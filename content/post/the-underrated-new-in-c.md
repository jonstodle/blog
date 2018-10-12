+++
date = "07/18/2017 10:00:00 +00:00"
tags = ["C#"]
title = "The underrated new[] { } in C#"
+++
17 years into it's life, C# has a lot of language features. This means that some
of them are likely to go unnoticed by some people, maybe even a lot of people.

A feature which isn't get the love I think it deserves is implicitly typed
arrays, or the new[] { }  syntax. It's a shorthand for writing arrays. The type
of the array is inferred from the contents you give it:

var a = new[] { 1, 10, 100, 1000 }; // int[]
var b = new[] { "hello", null, "world" }; // string[]


"That's nice and all", you might think. "But who uses arrays in C# these days?".
While it's true there are a lot of other types of collections in .Net which are
far better for a lot of use cases, I think implicitly typed arrays still has a
very nice use case.

Creating arrays in this manner is only useful when you know the elements you
want to put into it. This means it's great for passing a few values together. It
becomes infinitely more useful when you realize that arrays implement IList<T>.

For any method that takes an IEnumerable<T>  or IList<T>  as a parameter you can
pass an implicit array if you only have a small set of values to pass:

GiveMeAnIEnumerableOfString(new[] { "Ten", "Eleven", "Twelve", "Nine" });


It's also quite handy when you're writing a method which should return an 
IEnumrable<T>  or IList<T>:

public IEnumerable<string> GetFourSpecificNames() => new[] { "Martha", "Rory", "Bill", "Rose" };


Which I prefer to this:

public IEnumerable<string> GetFourSpecificNames() => new List<string> { "Martha", "Rory", "Bill", "Rose" };


I prefer it to be implicit, just like I prefer to use the var  keyword when
creating local variables.

AFTER NUMEROUS SUGGESTIONS I'VE CHANGED THIS EXAMPLE

Which I think looks better than this:

public IEnumerable<string> GetFourSpecificNames()
{
    var list = new List<string>();
    list.Add("Amy");
    list.Add("Rory");
    list.Add("Bill");
    list.Add("Rose");
    return list;
}


Lately I've found myself using this syntax a lot when I need to pass a
collection of known items.

Happy coding!
