+++
title = "Spindle 0.1 is out!"
date = "2023-07-17"

[taxonomies]
tags = ["GPGPU", "ergonomics", "macros", "spindle", "Rust", "scientific computing", "CUDA", "parallel programming", "self compassion"]
+++

I am quite proud of the ergonomics. CUDA is always hard to deal, so I am using procedural macros to regulate what functions it *weaves*, starting from [Embarrassingly Parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel) problems and inducting up to [Data Parallel](https://en.wikipedia.org/wiki/Data_parallelism).

```rust
#[spindle::basic_range]
fn square_over_two(x: i32) -> i32 {
    (x * x) / 2
}

fn main() {
    let foo = unsafe { 33.square_over_two() }.unwrap();
    println!("{foo:?}");
    let bar: Box<[_; 33]> = unsafe { _square_over_two() }.unwrap();
    println!("{bar:?}");
}
```

<img height = "250px" src="/self/spindle-0-1.png" />
<img height = "400px" src="/self/spindle-linter.png" />

## Thanks 💖

I am especially grateful to friends and interested Rustaceans for their support! Feedback ranged from nodding in agreement, design suggestions, memes, and most importantly, helping me come up with the name! We can't not take inspiration from [`rayon`](https://docs.rs/rayon/latest/rayon/), the *lighter than silk* CPU parallelization crate. Honorable mentions to `serger`, `throstle`, `coreduroy` (*sigh*), and `curayon`.

## Scientific Computing in Rust

The [conference](https://scientificcomputing.rs/) was fantastic! The organizers did a fantastic job organizing the talks, communicated clearly, and facilitated a Gather Town social hour that was very hard to leave. It was heartwarming to see 100+ people show up for the first event. Special shoutouts to Gonzalo, Miguel, Ryan, and Sarah. Great job on your talks, everyone. It was a pleasure to meet you, and I look forward to collaborating 🔬.

Also, what a beautiful coincidence to be releasing the first draft of `spindle` after nearly 10 hours of directly or tangentially related talk! I am inspired to meet the need for ergonomic GPGPU parallelism in Rust.

## Goals

What do we want

```rust
#[spindle::my_fancy_macro]
fn foo(t: T) -> U { /* my fancy code */ }
```

to do? Some intrusive thoughts emerge.

> How should we handle generics? Trait bound? Arrays? Lifetimes? Shared static memory? Monomorphization? Crate dependencies? Recursive dependencies?
> Ooh! I have an idea for a HPC-empowered iterator! And a structure that handles filter maps! I bet we can map arrays, iterators, and vectors!
> Yeah, the ergonomics are a huge deal. How about a config struct parsed with `nom` or `serde` so the user has access to...
> Ooooooh wait! I have an idea to optimize allocations. It only involves some pointer recasting, then we'll just widen the  memory buffer... oh but now that means another `nullptr` check, but that's fine, we can just pack that into the error variant.
> I know a good name for the data structure... there's a probably trait with a method like... we should call it...

*breathes* Hey. Take it easy on yourself. Everything you just said is extremely hard. This is your first macro crate, and yes, macros are hard to write. GPGPU is hard. FFI is hard. Unsafe code is hard and *unsafe*. You got this, but please be kind to yourself.

What about just a range map that uses the GPU to map `n: i32` to `[foo(0), ..., foo(n-1)]`?

```rust
#[spindle::basic_range]
fn foo(x: i32) -> i32 { /* my basic function */ }
```

One final intrusive thought
> *that shouldn't be so hard 😉

Spoiler to nobody: it was.

## The basic_range macro

The [attribute macro](https://doc.rust-lang.org/reference/procedural-macros.html#attribute-macros) `basic_range` hides the hard work of safely(?) and correctly(?) interfacing with GPGPU APIs by:

1. regulating the signature of `square_over_two`
2. generating a ptx crate `square_over_two`
3. compiling the crate and emitting `$KERNEL/path/to/kernel.ptx`
4. defining the [GAT](https://blog.rust-lang.org/2022/10/28/gats-stabilization.html) `pub trait _SquareOverTwo`
5. implementing `_SquareOverTwo` for `i32` with `Return = Vec<i32>`
6. defining the standalone launcher `_square_over_two`

Almost every step here involves something tricky. Writing a macro severs the tie to many ergonomic qualities of Rust with a layer of indirection. Running arbitrary code at compile time and rewriting a `TokenStream` (uh, I think you mean `proc_macro2::TokenStream`) is hard! There are ways to claw some of them back, but it's another thing to learn. Don't reach for macros until you need them.

Let's dive in.

### Regulate `square_over_two`

We start with a simple mapping function that uses the GPU to map `0..n` with

```rust
fn square_over_two(x: i32) -> i32
```

Let's further restrict ourselves to a single `i32` parameter with a primitive number return type (`u32, f64, etc`). Launching `CUDA` kernels is inherently unsafe and the registers must be manually managed ourselves. Better to start simply and correctly here.

Generics? Unsupported. Monomorphization? Maybe for `1.1.0`. Lifetimes? *what does that even mean here???*

### Generate the `square_over_two` ptx crate

The `$NAME = square_over_two` crate lives at `$KERNEL = $PROJECT/kernels/square_over_two/` with boilerplate files `$KERNEL/{Cargo.toml, rust-toolchain.toml, .cargo/config.toml, src/lib.rs}`. The `toml` files configure the crate for ptx emission. In addition, `square_over_two` is copied to `$KERNEL/src/device.rs`, renamed as `device`.

```rust
// lib.rs
#![no_std]
#![feature(abi_ptx)]
#![feature(stdsimd)]
#![feature(core_intrinsics)]

mod device;
use core::arch::nvptx::*;

#[panic_handler]
fn my_panic(_: &core::panic::PanicInfo) -> ! {
    loop {}
}

#[no_mangle]
pub unsafe extern "ptx-kernel" fn kernel(output: *mut i32, size: i32)
{
    let thread_id: i32 = _thread_idx_x();
    let block_id: i32 = _block_idx_x();
    let block_dim: i32 = _block_dim_x();
    let grid_dim: i32 = _grid_dim_x();
    let n_threads: i32 = block_dim * grid_dim;
    let thread_index: i32 =  thread_id + block_id * block_dim;
    let mut i: i32 = thread_index;
    while i < size {
        let value = device::device(i);
        *output.offset(i as isize) = value;
        i += i.wrapping_add(n_threads);
    }
}
```

*Do you see why we started with `i32`?*

If you want to play with the ptx crate, `rust-analyzer` will be fully on-board thanks to the `toml` files! Note that any changes you make will be overridden by the macro at will, so copy it elsewhere for your experiments.

This code is deliberately not optimized for locality or performance. It performs unnecessary bounds checks. I am resisting the urge for untested and unbenchmarked changes. If you have suggestions, please submit them as issues and pull requests.

### Emit `$KERNEL/path/to/kernel.ptx`

We use the experimental `#![feature(abi_ptx)]` and `std::process::Command` to run

```bash
cargo +nightly -Z unstable-options -C $KERNEL build --release
```

Colored terminal output is logged and paired with a colored about the use of nightly Rust content:

```rust
#![no_std]
#![feature(abi_ptx)]
#![feature(stdsimd)]
#![feature(core_intrinsics)]
use core::arch::nvptx::*;
```

It's not perfect, but the warning should be visible to anyone compiling the code.

### Define the trait `_SquareOverTwo`

The  [GAT](https://blog.rust-lang.org/2022/10/28/gats-stabilization.html) `Return` is the desired return type, e.g., `Box<[i32]>` or `Vec<i32>`
The only method is

```rust
unsafe fn square_over_two(&self)
-> Result<Self::Returns, spindle::range::Error>;
```

### Implement `_SquareOverTwo` for `i32`

Here, `Return = Vec<i32>`.

Before interfacing with our ptx code, we [unsafely allocate uninitialized memory](https://doc.rust-lang.org/stable/std/alloc/fn.alloc.html), [manually check for a `nullptr`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.is_null), and [unsafely initialize a host `Vec`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.from_raw_parts).

We then use [`cudarc`](https://docs.rs/cudarc/latest/cudarc/) to initialize a [`CudaDevice`](https://docs.rs/cudarc/latest/cudarc/driver/safe/struct.CudaDevice.html), consume `kernel.ptx`, unsafely [`asyncrhonously launch`](https://docs.rs/cudarc/latest/cudarc/driver/safe/trait.LaunchAsync.html#tymethod.launch) a [`CudaFunction`](https://docs.rs/cudarc/latest/cudarc/driver/safe/struct.CudaFunction.html), and unsafely [reclaim data from the device](https://docs.rs/cudarc/latest/cudarc/driver/safe/struct.CudaDevice.html#method.sync_reclaim).

Hooray! We can

```rust
let foo = unsafe { 33.square_over_two() }.unwrap();
println!("{foo:?}");
```

### Define the standalone launcher `_square_over_two`

This free functions has signature

```rust
unsafe fn _square_over_two<const N: usize>()
-> Result<Box<[i32; N], spindle::range::Error>
```

It performs a comparable sequence of allocations.

We can now

```rust
let bar: Box<[_; 33]> = unsafe { _square_over_two() }.unwrap();
println!("{bar:?}");
```

## Error handling

The function signatures should tell the story and the `unwrap()`'s log our progress. I believe the memory management is correct, but I would love inspection from fresh eyes. Feedback, such as a github issue or PR, is welcome.

## Why `cudarc` over `rust-cuda`?

No specific reason, outside of familiarity. It would be fantastic to switch between them via feature flags. PRs welcome, but the [Minimum Viable Product](https://en.wikipedia.org/wiki/Minimum_viable_product) comes first.

## Other features?

Too many to list. No matter what we do, every feature should be ergonomic, correct, and safe, all tied as Priority #1. Next comes performance, as much as I love it.
