+++
title = "Things I Enjoy in Rust: Enums"
date = 2019-04-18T18:00:00.0000000+02:00
[taxonomies]
tags = ["Rust", "Things I Enjoy in Rust"]
+++
**Things I enjoy in Rust:**

1. **Enums**
2. [Error Handling](/things-i-enjoy-in-rust-error-handling)
3. [Immutability](/things-i-enjoy-in-rust-immutability)
4. [Impl](/things-i-enjoy-in-rust-impl)
5. [Macros](/things-i-enjoy-in-rust-macros)

I've been diving into Rust for the last couple of months, after my colleague started talking about it. I've been wanting to learn a lower level language, but C++ or something of the like have always seemed too daunting for me to even start.

I'd heard of Rust before, and great things too, but hadn't set apart time to look into it. I finally took the dive. And boy, am I glad I did.

A lot of people talk about the borrowing system of Rust, or how fast it, or the strict type system. All of which are great things, but it's not what I'm going to write about here. I'm excited about enums.

An enum, or enumeration, is a predefined set of values or cases. In C#, enums are mostly glorified `int`s. Enums in Java are somewhat similar, but not used very often. In Rust, however, they are also wrappers of other data.

*If you've come across enums in Swift or a lot of functional programming languages, like F#, you're probably familiar with how enums are used in Rust.*

In Rust you can define an enum as you would in other languages. Let's say we want to define the possible outcomes of a function that parses a string and tries to detect the type of data that's stored in the string:

    enum ParseResult {
    	Text, // The data is plain text
        Number, // The data is a number
        Boolean, // The data is a boolean value
        Error, // The data is unrecognized, parsing failed
    }
    
    fn parse_string(value: &str) -> ParseResult {
    	// Code
     }

This is nice, but we also want to return the value that was extracted from the string. With the enums in Rust, you can attach a value to the different cases of the enum. Let's redefine `ParseResult`:

    enum ParseResult {
    	Text(String),
        Number(f64),
        Boolean(bool),
        Error(String), // The error message is returned as a string
    }

It's now possible for `parse_string` to both return the type of data that's stored in the string *and* the data itself. To handle this result, you'll use `match`, which is like `switch` in a lot of other C-like languages, but has a lot more flexibility:

    match parse_string("42") {
    	Text(text) => print!("The content was some text: {}, text),
        Number(number) => print!("The content was a number: {}", number),
        Boolean(boolean) => print!("The content was a boolean: {}", boolean),
        Error(error) => print!("Unable to parse the string: {}", error),
    }

Enums are so useful, they're used to represent `null` in Rust. While a lot of C-like languages have some concept of a reference to a piece of memory with no value stored, Rust does not<sup><a href="#footnote1">1</a></sup>. If you want to express that value might be present, or it might not, you use an `Option<T>`. `Option<T>` is an enum that has two cases: `Some(T)`, which represents that the value is present; and `None`, which represents no value is present.

To use the value stored in an `Option<T>`, you have to account for the possibility it might not be present, i.e. `None`. Rust makes this easy by exposing multiple functions that let you *unwrap*, or extract the value from, an `Option<T>`. You're able to specify a default value, a function to execute or crash the program, to name a few, when there's no value.

Enums used like they are in Rust are super useful, and I enjoy them.

<a name="footnote1">1</a>: In general, Rust does not have null pointers, but they are possible with *unsafe* code.
