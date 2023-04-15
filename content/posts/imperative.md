+++
title = "Imperative to functional"
date = "2023-04-14"

[taxonomies]
tags = ["rust", "functional programming", "iteractory", "lazy evaluation"]
+++

## The float factory

```rust
fn to_floats(string: &str) -> Vec<f64> {
    let mut floats: Vec<f64> = vec![];
    todo!();
    return floats;
}
```

## Mild amounts of oxidization

```rust
fn to_floats(string: &str) -> Vec<f64> {
    let mut floats: Vec<f64> = vec![];
    for sub_string in string.split_whitespace() {
        todo!()
    }
    floats
}
```

## What's this Result thing? Must not be important

```rust
fn to_floats(string: &str) -> Vec<f64> {
    let mut floats: Vec<f64> = vec![];
    for sub_string in string.split_whitespace() {
        let float: f64 = sub_string.parse().unwrap();
        floats.push(float)
    }
    floats
}
```

## I guess match is neat

```rust
fn to_floats(string: &str) -> Vec<f64> {
    let mut floats: Vec<f64> = vec![];
    for sub_string in string.split_whitespace() {
        let float: Result<f64, _> = sub_string.parse();
        match float {
            Ok(float_but_im_afraid_to_reuse_variable_names) => floats.push(float_but_im_afraid_to_reuse_variable_names),
            Err(_) => panic!("errors never happen"),
        }
    }
    floats
}
```

## The first brave step

```rust
fn to_floats(string: &str) -> Result<Vec<f64>, String> {
    let mut floats: Vec<f64> = vec![];
    for sub_string in string.split_whitespace() {
        let float: Result<f64, _> = sub_string.parse();
        match float {
            Ok(float) => floats.push(float),
            Err(msg) => return Err(msg.to_string())
        }
    }
    Ok(floats)
}
```

## Letting go

```rust
fn to_floats(string: &str) -> Result<Vec<f64>, String> {
    let mut floats: Vec<f64> = vec![];
    for sub_string in string.split_whitespace() {
        match sub_string.parse() {
            Ok(float) => floats.push(float),
            Err(msg) => return Err(msg.to_string())
        }
    }
    Ok(floats)
}
```

## Mapping

```rust
fn to_floats(string: &str) -> Result<Vec<f64>, String> {
    let mut floats: Vec<f64> = vec![];
    for sub_string in string.split_whitespace() {
        let float = sub_string.parse()
            .map_err(|err: ParseFloatError| err.to_string()
        )?;
        floats.push(float)
    }
    Ok(floats)
}
```

## Unpeeling the onion

```rust
fn to_floats(string: &str) -> Result<Vec<f64>, String> {
    let mut floats = vec![];
    for sub_string in string.split_whitespace() {
        floats.push(
            sub_string
            .parse()
            .map_err(|err: ParseFloatError| err.to_string()
        )?)
    }
    Ok(floats)
}
```

## It's gone

```rust
fn to_floats(string: &str) -> Result<Vec<f64>, String> {
    string
        .split_whitespace()
        .map(|sub_string| 
    {
        sub_string
            .parse()
            .map_err(|err: ParseFloatError| err.to_string())
    }).collect()
}
```

## A perfect function

```rust
fn to_floats(string: &str) -> Result<Vec<f64>, ParseFloatError> {
    string
        .split_whitespace()
        .map(|sub_string| 
        sub_string
            .parse()
    ).collect()
}
```

## WAIT NO I THOUGHT I HATED CATEGORY THEORY

It was functors and monads all along.

Have fun on the [playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021).
