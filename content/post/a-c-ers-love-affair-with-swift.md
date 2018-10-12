+++
date = "2017-04-09T10:00:00.0000000+00:00"
tags = ["C#","Swift"]
title = "A C#-er’s love affair with Swift"
+++
My language of choice is C#. This is partly because it’s the first language I learned properly, partly because it’s familiar and comfortable, and partly because I like the syntax.

Back in September or October I read someone suggesting that it’s a good practice to learn a new language each year. At first thought that it sounded excessive, I now believe it to be an excellent practice. This is in part because I ended up learning Swift.

I wanted to develop a native iOS application now that I don’t have to use Objective-C. I could do this with [Xamarin](https://www.xamarin.com/) of course, but I wanted to try it the Apple way.

Having spent a few months getting familiar with Swift and learning how Swift does things, there are some things I really liked. I wanted to go through some of these features. Some of these would be nice to have in C#, some doesn’t make sense in C# (and that’s OK).

In the text below I’ll be using the C# names for the different concepts, instead of the correct names they have in Swift.

# Class Extensions

These are very heavily used in Swift and for a very good reason; they’re very handy. They’re somewhat similar to partial classes, but for all classes and not just your own. I’ll show below how they’re different.

A class extension in Swift lets you add extra code to an existing class. In the extensions you can add new static and instance methods, getter properties and new constructors. You can also make a class implement an interface (or protocols as they call them in Swift).

While we can add instance methods in C# with extension methods, it would be nice to be able to add some convenience methods as static methods to a class or a simplified overload of a constructor.

Class extensions are not limited to your own classes; you can extend any class. That includes sealed classes inside system frameworks or classes in libraries you’ve added. You can’t override anything, but it’s fully possible to overload members. If you come across a class which it would be really nice if it implemented `IEnumerable<T>` to make Linq available, you could make that happen.

# Enum Shorthand

One thing that stomped me when I first started learning C# and Silverlight (oh, the days), was setting the visibility of UI elements was done like this:

```
MyControl.Visibility = Visibility.Visible;
```

“Why do I have to write Visibility twice?”. While it’s obvious to me now (and it’s been for a while 😛) that it’s an enum, I’ve never liked the verbosity of it.

In Swift you can reference an enum member by omitting the enum name. The example above could then be written like this:

```
MyControl.Visibility = .Visible;
```

To me it not only feels natural, but I think it’s more readable. You could of course write the whole thing out if you wanted to, but when it’s obvious you can shorten it down.

In C# 7 you can do some nice `switch`ing with the new pattern matching:

```
switch (MyControl.Visibility)
 {
    case Visibility.Visible: return true;
     case Visibility.Hidden: return false;
     case Visibility.Collapsed: return false;
     default: false;
 }
```

But it could be nicer with:

```
switch (MyControl.Visibility)
{
    case .Visible: return true;
    case .Hidden: return false;
    case .Collapsed: return false;
    default: false;
}
```

A lot of repetition gone and increased readability.

This concept is actually quite like the `using static` that was introduces in C# 6. I think it’s actually might be possible to get enum shorthand into C# at some point.

# Optional Try

Swift has an interesting way of handling exceptions (or errors as they’re called in Swift). Whenever you’re calling a method that may throw an error, you have to put a `try` clause in front of it:

```
try aThrowingMethod();
```

I actually like this as it makes it really clear to you, and others that might read your code, when you have to account for possible exceptions. How you choose to handle the potential exception, though, is the nice part:

If you put in a `try` like the one above, you have to add a `catch` clause to handle the potential exception.

You can also tell Swift that you guaranty the method won’t throw an exception by appending a `!` to the `try` clause:

```
try! aThrowingMethod();
```

If it does throw it will cause your application to crash. This is equivalent to when you don’t catch exceptions in C#.

The third way, and my favorite, is when you append a `?` to the `try` clause. Whenever you put a `?` after something in Swift you indicate that it might be null. Using this with a `try` clause will cause the resulting value to be null if it throws an exception:

```
var thisWillBeNull = try? thisMethodWillThrow();
```

I find this a nice and succinct way to do a `try/catch`. You can then do a simple null check to see if the operation was successful.

# Explicit Parameter Names

This is probably the thing I found most annoying when starting to learn Swift, but I’ve ended up missing when programming in C#. These are incredibly verbose in Objective-C, but are better in Swift 3+. Below is an example of a method in C# and how I’d write the method in Swift

```
// C#
public Person FindPersonById(int id) { }

// Swift
func findPerson(by id: Int) -> Person { }
```

For C# eyes the Swift version looks weird and wrong, but here’s how it works: `findPerson` is of course the name of the method. `by` is the name of the parameter for the consumer of the method. `id` is the name of the parameter inside the method block.

When the method is called it’ll look like this:

```
findPerson(by: 1234);
```

Swift methods are supposed to read like a sentence and this becomes especially nice when you have multiple parameters:

```
// Declaration
func scheduleNotification(for person: Person, at date: Date = Date()) { } // No return value

// Consumption
scheduleNotification(for: myPerson, at: someDate);
```

This makes it easy to see what all the parameters do; both when you’re first calling the method and when you read your code later.

As you expect, the IDE helps you with the types for each of the parameters, so it’s not difficult to know what you’re supposed to pass in.

# In General

At the end here, I just want to comment on a few things I like in Swift, but don’t merit their own header.

I actually really like how Swift forces you to think about possible null values. If a variable isn’t marked as optional with an appended `?` (like this: `nullableValue?`), you can’t assign null to it at all. And you don’t have to check for null either with these values. This is the case for both primitive types and reference types.

As I mentioned, Swift also forces you to use a `try` clause when using throwing methods, making you aware when you have to think about possible exceptions. And it also forces you to mark methods that might throw an exception as `throwing`.

Swift also has constructors that might fail to instantiate a new instance. These are specially marked and return null when they fail. You’re still able to have constructor that fail and blow up, but there’s a way to be nice about it if there’s a reasonable possibility for the constructor to fail.

# Closing Thoughts

I was surprised when I started liking Swift and even more so when I missed some things when going back to C#. I think it just shows that there’s some really good ideas outside the bubble of your preferred language.

Happy coding.