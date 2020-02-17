---
title: "If Statements With Nullable Bools"
date: 2020-02-17T20:04:56+01:00
draft: false
---

Nullable `bool` in C# might sometimes feel a bit of a hassle to work with.
I'm here to give you a few tips that took me embarrassingly long to pick up on.
I'm hoping this saves you some hassle too.

# `HasValue` and `Value`

These properties are present on `Nullable<T>` and have been since generics where introduced.
It took me too long to notice them, so here they are.
No need to write

```
bool? nullableBool = true;
bool regularBool;

if (nullableBool != null)
{
    regularBool = (bool)nullableBool;
}
```

when you can write

```
bool? nullableBool = true;
bool regularBool;

if (nullableBool.HasValue)
{
    regularBool = nullableBool.Value;
}
```

`HasValue` returns `true` if `nullableBool` is not `null` and `Value` returns a regular `bool` if `nullableBool` is not null.

`Value` also works a lot better when chaining methods:

```
bool? nullableBool = true;

((bool)nullableBool).ToString();
// or, simply
nullableBool.Value.ToString();
```

I think the second option is a lot more readable.

# `GetValueOrDefault`

This method on `Nullable<T>` might not always save characters, but it's very clear in it's intent, which I believe accounts for a lot.
`GetValueOrDefault` makes it clear what your code does.
So instead of the generic _null conditional_

```
bool? nullableBool = true;
bool regularBool;

if (nullableBool ?? false)
{
    regularBool = nullableBool.Value;
}
```

you can explicitly say that you want a default value

```
bool? nullableBool = true;
bool regularBool;

if (nullableBool.GetValueOrDefault(false))
{
    regularBool = nullableBool.Value;
}
```

As I said, `GetValueOrDefault` doesn't necessarily save on characters, but it's clearly states what you want.
This too works better when chaining methods:

```
bool? nullableBool = true;

(nullableBool ?? false).ToString();
// alternatively
nullableBool.GetValueOrDefault(false).ToString();
```

# Direct variant checking

Again, this is one of those things that took me too long to figure out, so here goes.
A simple boolean check might look like this:

```
bool regularBool;

if (regularBool)
{
    // some code...
}
```

but that doesn't work with a `bool?`, so I used to do something like this

```
bool? nullableBool = true;

if (nullableBool.HasValue && nullableBool.Value)
{
    // some code...
}
```

or

```
bool? nullableBool = true;

if (nullableBool ?? false)
{
    // some code...
}
```

Turns out, you can just do this instead:

```
bool? nullableBool = true;

if (nullableBool == true)
{
    // some code...
}
```

`nullableBool == true` will evaluate to `false` if `nullableBool` is either `false` or `null`, in other words: _not true_.
It seems so obvious in retrospect, but I'd become so accustomed to writing `if (regularBool) ...`, instead of `if (regularBool == true) ...`, that it didn't occur to me to write it out in full for `bool?`s.

# The other `Nullable<T>`s

This does work for the other nullable primitive types too.
You can for example shorten this

```
int? nullableInt = null;
int regularInt;

if (nullableInt.HasValue && nullableInt == 100)
{
    regularInt = nullableInt.Value;
}
```

to

```
int? nullableInt = null;
int regularInt;

if (nullableInt == 100)
{
    regularInt = nullableInt.Value;
}
```

There's also this case:

```
int? nullableInt = null;
int regularInt;

if (nullableInt < 100)
{
    regularInt = nullableInt.Value;
}
```

`nullableInt < 100` will evaluate to `true` if `nullableInt` has a value, and that value is less than `100`.
It will however evaluate to `false` if `nullableInt` is `null`.
Which one could argue is correct, as `nullableInt` isn't a number lower than `100`, it's `null` (which isn't a number at all).

---

I hope you found this interesting, or at least helpful.

Happy coding!
