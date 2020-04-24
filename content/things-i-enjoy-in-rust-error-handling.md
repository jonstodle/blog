+++
date = "2019-04-21T12:00:00+02:00"
tags = ["Rust", "Things I Enjoy in Rust"]
title = "Things I Enjoy in Rust: Error Handling" 
+++
**Things I enjoy in Rust:**

1. [Enums](/things-i-enjoy-in-rust-enums)
2. **Error Handling**
3. [Immutability](/things-i-enjoy-in-rust-immutability)
4. [Impl](/things-i-enjoy-in-rust-impl)
5. [Macros](/things-i-enjoy-in-rust-macros)

I've already talked about how I like how enums are used in Rust. They make it easy to express multiple states *and* the state's related data. One place this is excellently utilized is error handling.

In general there are two types of errors that can occur in your program: errors you can recover from, i.e. your program can continue execution despite an operation failing; and errors you **can't** recover from, i.e. your program exits immediately.

A lot of popular programming languages don't have a good way to distinguish between these two kinds of errors, but in Rust this is done quite nicely. For any error you can't recover from, the `panic` macro is used. This forces your program to exit immediately. For all other errors, the ones you **can** recover from, an enum is used: `Result<T, E>`.

`Result<T, E>` has two cases: `Ok(T)` for when the operation succeeds and `Err(E)` for when the operation fails. There are two really nice side effects of doing error handling this way:

The first is that it's easy to see which functions might "throw" an error (or an exception, as they're known in some languages): they return `Result<T, E>`! This makes functions self-documenting, which again means you don't have to rely on (possibly) outdated or incomplete documentation.

The second side effect is that it forces you to think about how to handle possible errors. A function that returns `Result<T, E>`, forces you to write some kind of code to handle the possibility of an error. The simplest way to "handle" this is to call `unwrap` on the `Result<T, E>`. `unwrap` will make your program *panic* if the `Result<T, E>` is an error, or return the value of the `Ok(T)` if it's not an error.

More likely you'll resort to another way to handle possible errors by either using a `match` expression, or one of the convenience functions on `Result<T, E>`.

The function `unwrap_or`, for example, lets you specify a default value if the `Result<T, E>` is an error. This enables your code to be succinct when you don't want complex error handling. Let's compare two examples:

```
// C#
string value = "Default value";
try
{
		value = SomeOperationThatMightFail();
}
catch
{
		// Ignored
}


// Rust
let value = some_operation_that_might_fail()
		.unwrap_or("Default value")
```

Even if you compacted the formatting of the C# snippet, or made some helper function to clean up the code, the fact that the idiomatic Rust code is so succinct is something that really appeals to me.

The fact that I can assume that if I handle every `Result<T, E>` correctly my code will only crash when it *panics*, in which case there isn't anything I can do anyway, is something I enjoy.