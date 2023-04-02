+++
title = "Rust's `match` is liberating"
date = "2023-02-15"

[taxonomies]
tags = ["rust"]
+++

## Have you met `foo` before?

```rust
fn foo(n: usize, k: usize) -> usize {
    match (n, k) {
        (_, 0) => 1,
        (0, _) => 0,
        _ => foo(n-1, k-1) + foo(n-1, k)
    }
}
```

## I bet you have!

```rust
fn bar(x: f64, k: i32) -> f64 {
    use std::cmp::Ordering::*; /* types don't exist at runtime */
    match k.cmp(&0) {
        Less  => 0.,
        Equal => 1.,
        Greater => (0..k)
            .map(|i|
                (x - i as f64) / 
                (k - i) as f64
            ).product()
    }
}
```

## Solve problems without interruption

Have fun on the [playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021).
