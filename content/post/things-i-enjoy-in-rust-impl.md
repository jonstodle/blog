+++
date = "2019-04-26T12:00:00.0000000+02:00"
tags = ["Rust", "Things I Enjoy in Rust"]
title = "Things I Enjoy in Rust: Impl"
+++
**Things I enjoy in Rust:**

1. [Enum](/things-i-enjoy-in-rust-enums)
2. [Error Handling](/things-i-enjoy-in-rust-error-handling)
3. [Immutability](/things-i-enjoy-in-rust-immutability)
4. **Impl**
5. Macros *(coming soon)*

Rust doesn't have classes or methods. Instead it uses structs and associated functions. To associate a function with a struct you use the `impl` keyword:

```
struct Point {
    x: isize,
    y: isize,
}

impl Point {
    fn new(x: isize, y: isize) -> Point {
        Point { x, y }
    }

    fn move_laterally(&mut self, amount: isize) {
        self.x + amount;
    }
}
```

The two functions are implemented for `Point` and they are now considered associated functions of `Point`.

The first function, `new`, creates a new instance of a `Point` by taking an `x` and a `y` coordinate. This function is much like a `static` method or class function in other languages. You use it like this:

```
let point = Point::new(0, 0);
```

The second function, `move_laterally`, has reference to `self`, which means it can be used as a method on an instance of `Point`. This is much like instance methods in other languages:

```
let mut point = Point::new(0, 0);
point.move_laterally(10);
```

What I really like about `impl` is the fact that anyone can implement function for a struct - even for structs not defined in the same project. Want to make a helper function to reverse strings?

```
impl String {
    fn reverse(&self) -> String {
        self.chars().into_iter()
            .rev()
            .collect()
    }
}

let text: String = "Allonsy".to_string();
let reverse = text.reverse(); // "ysnollA"
```

`String` is defined in the standard library, but you are still able to extend it with your own functionality. Neat!

You're even able to implement *traits* (Rust's near equivalent to *interface* in languages like C#, Java or Typescript) for structs not defined in your project. Even neater!

This flexibility of `impl` is something I enjoy.