+++
date = "2017-09-26T10:00:00.0000000+00:00"
tags = ["C#"]
title = "Overloading operators in C#"
+++
C# is a mature language and at this point there are some features that are forgotten, or not even discovered, by users of the language. I've previously written about one of my favorite "obscure" features: the [implicitly types array syntax](/the-underrated-new-in-c/).

When creating your own classes (as you very often do), you have the ability to set how the different operators in C# should work with the class. While you're not allowed to create new operators, you can overload most of the existing ones.

Let's start with a simple class which represents a point in a two dimensional space:

```
public class Point
{
    public double X { get; set; }
    public double Y { get; set; }
}
```

Let's declare two points, to have something to work with:

```
var point1 = new Point { X = 1, Y = 4 };
var point2 = new Point { X = 2, Y = 3};
```

If we wanted to add those two points to get `point3`, we'd have to do something like this:

```
var point3 = new Point
{
    X = point1.X + point2.X,
    Y = point1.Y + point2.Y
};
```

This is very verbose and clunky. Especially if you are going to do this operation a lot.

Let's make it simpler by overloading the plus operator. Let's add this method to the class definition of `Point`:

```
public static Point operator +(Point lhs, Point rhs)
    => new Point
    {
        X = lhs.X + rhs.X,
        Y = lhs.Y + rhs.Y
    };
```

We define a static method that will return a `Point`, but instead of giving it a name as usual, we say it's an operator and then which operator it is. The parameters are the two points that are to be added. We then return the new point.

It's worth noting that it's only required for one of the parameters to be the same type as the containing type; meaning, you can define an overload for the plus operator between a `Point` and a `double` for example:

```
public static Point operator +(Point lhs, double rhs)
    => new Point
    {
    	X = lhs.X + rhs,
    	Y = lhs.Y + rhs
    };
```

You have the ability to go crazy here.

We can now declare `point3` more succinctly:

```
var point3 = point1 + point2;
// X: 3
// Y: 7
```

And we can add `2` to both `X` and `Y` by adding a `double` to `point3`:

```
var point4 = point3 + 2;
// X: 5
// Y: 9
```

Some of the other binary operators (operators between two operands) that can be overloaded are `-`, `*`, `/`, `%`. You can check out the comprehensive list in the [docs](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/overloadable-operators).

Next, let's add an overload for the `==` operator. This makes it easier to check whether two points are in the same place.

When overloading `==`, you also have to overload `!=`. In addition it's also **strongly** recommended to override `Equals()` and `GetHashCode()`. 

These two operators and two methods all relate to equality and how to determine whether two objects are to be considered equal. By overriding all of them, we ensure that they all calculate equality the same way.

Let's start with the `==` operator:

```
public static bool operator ==(Point lhs, Point rhs)
    => lhs.X == rhs.X && lhs.Y == rhs.Y;
```

As you can see, this is pretty much the same as before. We receive to points and return true if the `X` and `Y` values are equal. This also makes the `!=` operator easy to implement:

```
public static bool operator !=(Point lhs, Point rhs)
    => !(lhs == rhs);
```

It's just a negated `==`.

Next, to appease the C# gods ([Jon Skeet](https://www.quora.com/Who-is-Jon-Skeet-and-how-did-he-become-famous-on-Stack-Overflow) included), let's implement `Equals()` and `GetHashCode()`:

```
public override bool Equals(object obj)
{
    if (obj is Point point)
    {
        return this == point;
    }

    return false;
}

public override int GetHashCode() => new { X, Y }.GetHashCode();
```

The `GetHashCode()` implementation is from an [Stack Overflow answer](https://stackoverflow.com/a/4630550/1339804) relating to how `GetHashCode()` is best implemented.

Now we can easily check whether to points are equal. Let's declare `point5` which has the same `X` and `Y` values as `point4`:

```
var point5 = new Point { X = 5, Y = 9 };
```

And then we can do some tests:

```
point4 == point5 // true
point4 != point4 // false
point4 == point3 // false
```

Not a very through test, but it'll do for this demonstration.

You can of course overload other equality operators too: `<`, `>`, `<=`, `>=`. You can see more about this in the [docs](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/overloadable-operators).

The last kind of operator I want to show how to overload, are the conversion operators. These are the operators used when you do implicit and explicit conversion from one type to another.

For example, you can *implicitly* cast an `int` to a `double` by writing this:

```
double myDouble = 1;
```

While the `1` is an `int`, the `double` type knows how to create a `double` from an `int` and can do so for you automatically.

Going the other way, though, is not as automatic. You have to specify that you want to force the `double` to become an `int` by doing an *explicit* cast:

```
int myInt = (int)myDouble;
```

`int` knows how to create an `int` from a `double`, but doing so means you can loose some information, namely the digits after the dot, and forces you to explicitly tell it to do the conversion.

It's up to each type whether a conversion from another type should be implicit or explicit, but the rule of thumb is that implicit conversion should only be done when you can ensure no loss of information. Explicit conversion is for when you can say "you could do this, but...". The *but* indicates an explicit conversion.

Now let's have a look at how to implement this dandy feature in our `Point` class. To make it easy to create a `Point` with the same `X` and `Y` value, we should be able to just provide a `double` and implicitly cast that to a `Point`:

```
public static implicit operator Point(double d)
    => new Point { X = d, Y = d };
```

As you can see, it looks very much like the previous overloads. The difference is that the "return type" becomes `implicit` and the name of the method is the type that is returned after the conversion. It's a bit strange how they're switched around, but there you go.

This means that we can now do this:

```
Point point6 = 6;
// X: 6
// Y: 6
```

This is actually a bit more clever than you might think: The `6` is actually an `int`, but our `Point` class only support conversion from a `double`. However, since `int` can be *implicitly* converted to a `double`, which is supported by `Point`, C# does the conversion automatically.

To demonstrate explicit conversion, we're going to provide a conversion from `Point` and back to `double`. When doing this conversion, the `double` is going to represent the length of the line between the `Point` and the origin, the `0,0` coordinate. We want this conversion to be explicit as there is a loss of information:

```
public static explicit operator double(Point p)
    => ((p.X * p.X) + (p.Y * p.Y)) / 2;
```

Now can we do this:

```
double length = (double)point5;
// length: 53
```

By overloading conversion operators you're able to provide an easy way to convert between your custom class and any other class.

While our `Point` class should probably been a `struct`, I purposely used a `class` to demonstrate the equality operator overloads. In structs these are handled differently: they check whether each of the properties on the two structs being compared are equal. If they are all equal, the structs are considered equal. While our `Point` class does the same thing, I just wanted to show how it's done manually.

<details>
  <summary>
The complete code for the `Point` class can be found by expanding this.
  </summary>

```
public class Point
{
    public double X { get; set; }
    public double Y { get; set; }

    // Plus operator overload
    public static Point operator +(Point lhs, Point rhs)
        => new Point
        {
            X = lhs.X + rhs.X,
            Y = lhs.Y + rhs.Y
        };

    public static Point operator +(Point lhs, double rhs)
        => new Point
        {
            X = lhs.X + rhs,
            Y = lhs.Y + rhs
        };

    // Equality operator overload
    public static bool operator ==(Point lhs, Point rhs)
        => lhs.X == rhs.X && lhs.Y == rhs.Y;

    public static bool operator !=(Point lhs, Point rhs)
        => !(lhs == rhs);

    public override bool Equals(object obj)
    {
        if (obj is Point point)
        {
            return this == point;
        }

        return false;
    }

    public override int GetHashCode() => new { X, Y }.GetHashCode();

    // Conversion operator overload
    public static implicit operator Point(double d)
        => new Point { X = d, Y = d };

    public static explicit operator double(Point p)
        => ((p.X * p.X) + (p.Y * p.Y)) / 2;
}
```

</details>

Now go forth, and overload operators!

Happy coding!