+++
title = "In Which I Claim Rich Hickey is Wrong"
date = 2020-11-30T22:45:00Z
[taxonomies]
tags = ["clojure", "types", "haskell", "rust", "f#", "commentary"]
+++

I recently watched a talk named *[Maybe Not](https://youtu.be/YR5WdGrpoug)* by Rich HIckey, the creator of Clojure. I can't claim to be on his level when it comes to language design, but I will postulate that his arguments at the [7:45 minute mark](https://youtu.be/YR5WdGrpoug?t=464) and about 2.5 minutes of watching, is wrong.

The subject he's covering is explicit "null" checks in languages that have an explicit type representing a missing value.

Languages like Haskell, F# and Rust express this through a type which may contain a value, or may not. You are then required to explicitly check whether the value is present before being able to use it. This is in contrast to languages like Java or C# which don't require you to do null checks, which might lead to null reference, or null pointer exceptions, respectively.

Back to Rich Hickey. He argues that changing an argument to a function from being explicitly non-null, to being possibly-null should not be a breaking change:

```rust
// From this
fn do_something(value: string) {}

// To this
fn do_something(value: Option<string>) {}
```

He argues that you are easing up on the requirements, and this shouldn't break existing callers: they are technically still passing valid values. He also argues the same for tightening the possible values for a return value:

```rust
// From this
fn return_something() -> Option<string> {}

// To this
fn return_something() -> string {}
```

Callers of this function might do a superfluous check for a value, but their code would still work.

[Watch the 2.5 minutes for a better explanation](https://youtu.be/YR5WdGrpoug?t=464).

# But You Are Breaking Callers

Rich Hickey argues that these are not breaking changes, and should not require the callers to change their code - just because the internal implementation of the function became a little better.

This is missing part of the point. **Why did the internals of the function change?**

Was it just because of an extra null check, or a new default fallback value? Has it become significant that you can pass a null value now? Is it significant that the operation now always seems to succeed, by never returning a null value?

You've changed the meaning of these values, and callers of this function should adapt to this new reality. Continuing to pass in the same values, or handle the same return values, in the same way as before should not be considered safe.

If there wasn't a change in behavior, why are you changing the contract?

You are changing the behavior of the function. **That is a breaking change.**
