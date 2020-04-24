+++
date = "2019-04-30T12:00:00.0000000+02:00"
tags = ["Rust", "Things I Enjoy in Rust"]
title = "Things I Enjoy in Rust: Macros"
+++
**Things I enjoy in Rust:**

1. [Enum](/things-i-enjoy-in-rust-enums)
2. [Error Handling](/things-i-enjoy-in-rust-error-handling)
3. [Immutability](/things-i-enjoy-in-rust-immutability)
4. [Impl](/things-i-enjoy-in-rust-impl)
5. **Macros**

I had never used a programming language with macros before Rust. I've heard about it, and seen some rave about it, but I've never actually looked into what they do. Now that I know - I love them!

Macros in Rust enables what is sometimes called *meta programming*: code writing other code. When you use a macro in Rust, you're actually calling a function which takes the input and generates more code based on that input. The result of this is what's fed to the compiler.

These macros are much like keyboard macros or programmable macros in some software: they let you automate repetitive tasks.

Granted, I haven't written any macros myself, but I love that I'm able to use convenience macros from both the standard library and from external crates.

Let's take this example from [*The Book*](https://doc.rust-lang.org/stable/book/) which shows how the `vec!` macro works:

```
// vec! macro defined
#[macro_export]
macro_rules! vec {
    ( $( $x:expr ),* ) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
} 

// vec! macro in use
let v: Vec<u32> = vec![1, 2, 3];
```

The `macro_rules!` defines the kind of input the `vec!` macro accepts (a variable number of inputs), it creates a new `Vec` instance, and `push`es the inputs into the `Vec` and returns it.

In addition, as of the 2018 edition of Rust, it's been possible to create macros for use with `#[derive()]`, which makes it possible for anyone to create macros that can act on `struct`s. Just like how `#[derive(Clone)]` makes a struct implement the `Clone` trait, you can make a macro that automatically implements your own traits for any struct.

Hate writing tons of boiler plate code to convert your structs to JSON or convert JSON to structs? The `serde` crate has macros for that! With a simple `#[derive(Serialize, Deserialize)] your struct support being serialized and deserialized to JSON and back. That's just about as simple as it gets.

With macros, there's much to enjoy.