+++
date = "02/13/2018 11:00:00 +00:00"
tags = ["Commentary","Swift","PHP","Dart","Python"]
title = "Learn a new programming language"
+++
I remember reading a tweet saying "you should learn a new programming language
each year" about two years back. My first thought was that I didn't need a new
programming language, I was quite happy with C#. I had done some small projects
in JavaScript and TypeScript, and even a tutorial on PHP, but didn't get hooked.

The quote stuck with me however, and I ended up learning Swift 3 about one and a
half year back, while making an iOS app. The lessons I learned from learning
Swift 3 has made me a believer of the "learn a new programming language each
year" mantra.

Learning new languages has helped me get a new perspective on the languages I
already know. It's made me appreciate C# even more, taught me new programming
patterns and made me see that C# is far for perfect.

Here are some things I came across when working with new languages:

Swift (3)
Swift almost became my new favorite language. It felt very much like C#,
designed 10 year later. A lot of features has been added to C# in the years
since it was first released, and in Swift they've come into their own.

Type inference, for example. You only have to declare the type of a variable
when the compiler can't figure it out. You can always add a type declaration for
clarity, but that's up to you.

In C#, there's extension methods. They let you add new functionality to an
existing class. In Swift on the other hand, you can extend classes with new
properties, new methods and make classes implement protocols (interfaces).

Swift also requires methods that throw exceptions to be marked with a keyword.
Whenever you call these methods, you have to use a try  clause. It felt verbose
in the beginning, but helps you avoid uncaught exceptions crashing your
application.

I also ended up liking the explicit parameter names, despite disliking them at
first. They help code be self documenting, and makes methods and functions
easier to read.

Also, Swift's enums are awesome.

PHP
I used to hate PHP because it was cool, now I dislike PHP because I've used it.
I don't have to many nice things to say about PHP, other than it's an adventure.

I try to do something that should be obvious; like getting the length of an
array. I look for a property called something like length  or count, but no
luck. What I was looking for was the global function sizeof, which is an alias
of the global function count. There's probably some weird history there.

Oh, and arrays in PHP are actually dictionaries, or maps. This has a nice side
effect of messing up indexes if you remove an item. If you've got an array with
three items, with index 0, 1  and 2, and remove the second item (1), you've now
got an array with two indexes: 0  and 2.

At some point I learned to smile , shrug my shoulders and say "it's PHP, so why
not?" when I came across these things.

Python
I did a small project in Python in 2017. While I didn't write more than a couple
of hundred lines of code, I found an appreciation for a proper argument parser.
Python's library for parsing command line arguments made me never want to do
that by hand again.

Dart
I'm trying out some Dart at the moment, and I've already found a couple of
things I like about the language.

All classes implement an interface which is identical to the class. This makes
it possible to swap out implementations when testing your code or you can make
your custom class conform to another class, making them interchangeable.

Dart also drops access modifiers. No private  or public. If you define a class,
method or property with a leading underscore (_myProperty), it's private.


--------------------------------------------------------------------------------

I encourage you to try out a new language – you might find something you like.
The worst case scenario is that you end up liking your favorite language even
more.

Happy coding!
