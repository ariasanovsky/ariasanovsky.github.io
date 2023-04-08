+++
title = "Becoming a better dev"
date = "2023-04-02"

[taxonomies]
tags = ["rust", "multithreading", "agile"]
+++

## What software development means to me

I'm a competent mathematical problem-solver, but what does that mean for software development?
This week I've been exploring

- [agile simplified](https://youtu.be/9K20e7jlQPA): do what is valuable, make a working product quickly without interruption, trash it if necessary, and collaborate
  - use right tools for the job? prioritize working code over methodologies and documentation (though Rust does the latter so well by default)
  - `ripgrep` has been the best `grep` for years, but `grep` is best for projects with mixed encodings
  - CI/CD is actually pretty easy (this site is also on a pipeline!)
  - only commit working code that is better than the previous version
- [monads](https://youtu.be/giWCdQ7fnQU): I'm finally enjoying category theory!
  - this means your type system has some consistency and structure that you probably want desperately
  - Rust's `Enum` handles this beautifully, I'll explain more later
  - I'll get around to Haskell eventually 😇
  - There is no happy/sad path, only valid and invalid states
- `Box, Rc, Arc` are [alien](https://youtu.be/CTTiaOo4cbY) at first, but so is anything novel
- [constexpr is overrated and a bit messy](https://youtu.be/NNU6cbG96M4): is the design is wrong?
  - is Circle the savior of C++?
  - static analysis will not save us. That's your compiler's job
- side-effects: your function signature should tell the whole story
  - but this is not enough!
  - when you want to multithread, your functions should minimize side-effects
  - when you want to maintain, `mut` is the correct default
  - `maps`, `iterators`, `collecting`

## Chore list

I want to

- clean up my homepage
- parallelize my `MCTS Ramsey` project

```rust
todo!("thread-local argmax graph");
todo!("immutable ScoreKeeper");
todo!("n_(a,g) -> n_ag"); /* almost there! */
todo!("clippy compliance"); /* so fun! */
todo!("open pictures in UI");
todo!("do I have enough g6 tests?"); /* I saw the g6 libraries! maybe use them! */
todo!("on-the-fly tikz with wsl?!");
todo!("optimize the guess graph");
```

- retool for a neural network
  - with the new architecture, we abandon the action queue
  - sign the count matrix to indicate edges
  - offset so that edges are nonzero
  - `f16` with [cudarc](https://github.com/coreylowman/cudarc)

- community tools
  - `cudarc` for Rust GPU-accelerated (split-)seed serach
  - markdown guides instead of pdf
  - simpler tool for resetting save files
  - better ergonomics & lowering the barrier to (re)entry
  - regex tool for tabulating submitted run data
  - auto seed searching (the dream)
