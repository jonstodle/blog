+++
date = "2019-04-23T12:00:00+02:00"
tags = ["Rust", "Things I Enjoy in Rust"]
title = "Things I Enjoy in Rust: Immutability" 
+++
**Things I enjoy in Rust:**

1. [Enums](/things-i-enjoy-in-rust-enums)
2. [Error Handling](/things-i-enjoy-in-rust-error-handling)
3. **Immutability**
4. [Impl](/things-i-enjoy-in-rust-impl)
5. Macros *(coming soon)*

Immutability is a term very commonly heard in relation to functional programming languages. In most C-like languages, immutability is mostly an afterthought and is usually implemented in a somewhat clumsy way.

In Rust, immutability is the default. If you assign a new value...

```
let myValue = 42;
myValue = 43; // <-- Not allowed
```

... it is immutable.

To enable immutability, you'll have to specify it explicitly:

```
let mut myValue = 42;
myValue = 43; // <-- Allowed
```

Rust also makes it easy to work with immutable structures. This is something that's often missing when working with immutable classes or structures in languages where immutability is not a main feature. Let's look at an example:

```
// Define a structure
struct Point {
	x: i32,
	y: i32,
};

// Create an instance
let point = Point {
	x: 2,
	y: 4,
};

// Change a value inside the structure
let point = Point {
	x: 4,
	...point,
};
```

Wait, what!?

Rust lets us create a value with the same name as a previous value. The second value *shadows* the first definition, effectively hiding it. This enables us to reuse the same name for the same thing, without having to come up with 3 ways to name it.

This also makes it easy to swap out an immutable structure, with an updated one, without having to change the name:

```
// Instead of this:
let point = Point { x: 2, y: 4 };
let updated_point = Point { x: 4, ...point };

// We do this:
let point = Point { x: 2, y: 4 };
let point = Point { x: 4, ...point };
```

Rust also has a spread operator which fills in the rest of the values in the structure with the values from an existing structure instance. The values we've defined in the new structure have the higher priority. Any value not assigned in the new instance of the structure will be filled with the corresponding value from the first structure.

Immutability as the default is something I truly enjoy.