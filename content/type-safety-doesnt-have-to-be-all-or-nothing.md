+++
title = "Type Safety Doesn't have to be All or Nothing"
date = 2020-04-27T21:51:38+02:00
[taxonomies]
tags = ["rust", "commentary"]
+++

One argument I sometimes hear against Rust is that if you have to use `unsafe`, there's no point in using Rust, you might as well go with the old familiar C++, or some other performant/preferred language of choice.

This argument always annoys me. As if the fact that you sometimes need (or want) to go outside of the safety net of the compiler, means that all the other benefits of the Rust type system, borrow checker and compiler is null and void.

`unsafe` is kind of a misnomer, because what the keyword gives you is not necessarily an unsafe block of code. What it does give you is a block of code that has been error checked by a human, not the compiler. `unsafe` is more appropriately `compiler, I know what I'm doing, skip checking this code`. This is similar to how an entire program might be written in other languages.

The huge benefit with Rust, on the other hand, is that the code that's manually checked by a human is contained within clearly sectioned blocks, and they are usually (but might not be) fairly small. Most Rust programmers prefer not to spend too much time outside the safety of the compiler checks.

Another way to put this, in a crass way, is saying that wearing a seat belt has no benefit; you might die from a crash anyways, so what's the point of the hassle of putting it on?

---

This short post was inspired by a person's colleagues not seeing the benefit of strict checking of 90% of the program, because 10% had to be manually checked.

Happy coding!
