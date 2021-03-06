# Kinder

[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/rust-kinder/Lobby)
[![Build Status](https://travis-ci.org/KitFreddura/Kinder.svg?branch=master)](https://travis-ci.org/KitFreddura/Kinder)

*Algebraic structure and emulation of higher-order types for Rust*

Kinder provides some tools and traits that functional programmers use daily.

***Kinder is very much a work in progress. The idea is to make HOT's approachable to rustaceans and provide a series of macros that make implementation of custom HOTs as painless as possible***.

Updates: Moved all macros to lift.rs for ease of use.

**To Do**

1. ~~Finish implementation of Monad for std::collections~~

2. ~~Implement Applicative for std::collections~~

3. Implement Traverable for std::collections (~~implement Foldable first~~)

4. Work on macros which make deriving these traits for custom types easy

5. Figure out how to implement Applicative for structs requiring Ord or Hash (the apply method is the issue, e.g what is an ordered function?)

**The Lift Module**

The lift module defines the Higher struct which allows creation of higher kinded types.
It also exports the macro lift! which implements Higher for types of kind * -> *.

**The SemiGroup Module**

Implements SemiGroup for std::collections as well as String.
Provides a method add which takes two items of the same type and returns an element of the same type.

**The Monoid Module**

Implements Monoid for std::collections as well as String.
Provides and id method for SemiGroups such that x.add(T::id()) = x.

**The Foldable Module**

Implements Foldable for std::collections. Provides a method foldr which takes a starting value and a function and 
folds the Foldable using the function. See examples for more information.

**The Functor Module**

Implements Functor for std::collections and exports a macro functorize! which
makes a functor out of any lifted type which implements iter.

**The Applicative Module**

Implements Applicative for std::collections which supplies two methods.
Raise takes a T and raises it to be an A<T>, i.e Vec::lift(1) = vec!(1).
Apply takes an applicative, and a lifted functions and applies it, i.e vec!(1,2).apply(vec!(|x| x+1, |x| x*x)) = vec!(2, 4).

**The Monad Module**

Implements Monad for std::collections.
Monads have two functions, lift (normally return but return is reserved in Rust), and bind.
Lift takes and element and "lifts" it into the Monad, for example Option::lift(2) = Some(2).
Bind is similar to fmap except the mapping function has type: A -> M\<B> i.e i32 -> Option\<i32>.
Bind is often implemented using flat_map.

Example generic summing of Vectors:

```rust
extern crate kinder;
use kinder::lift::{Foldable, Monoid};

fn sum_foldable<B: Monoid<A=B>, T: Foldable<A=B>>(xs : &T) -> B
{
  xs.foldr(B::id(), |x, y| x.add(y))
}

fn main() {
  let ints = vec!(1,2,3);
  let floats = vec!(1.0,2.0,3.0);
  let strings = vec!(String::from("Hello"), String::from(", "), String::from("World!"));
  println!("{}", sum_foldable(&ints)); //prints 6
  println!("{}", sum_foldable(&floats)); //prints 6
  println!("{}", sum_foldable(&strings)); //prints "Hello, World!"
}
```
Run this example with:
```bash
cargo run --example fold-example
```

Example generic square function, credit to /u/stevenportzer on reddit for debugging and making the types work,
run with 
```bash
cargo run --example func-example
```

```rust 
extern crate kinder;
use kinder::lift::Functor;
use std::ops::Mul;

fn squares<A: Mul<Output=A> + Clone, T: Functor<A, B=A, C=T>>(xs: &T) -> T {
  xs.fmap(|&x| x*x)
}

fn main() {
  prinln!("{:?}", squares(&vec!(1,2,3)));  //will print [1, 4, 9]
}
```

![alt text](https://mir-s3-cdn-cf.behance.net/project_modules/disp/7a455b42774743.57da548c501ce.gif "Rustaceans")

[Logo source](https://www.behance.net/gallery/42774743/Rustacean)

