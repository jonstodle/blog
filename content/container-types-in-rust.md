# Container types in Rust

## Cell

`Cell` is borrow checking at runtime. The inner value is copied out. If you try to read while it’s being mutated, you’ll get a panic?

`RefCell` put something on the heap. Cloning this will return a new pointer to that same head location. This shared an instance of a struct. This is how C# works. 

`Box` put something on the heap. Cloning this will clone the data on the heap and return a pointer to that new copy. 

`Rc`

`Arc`