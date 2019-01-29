+++
date = "2016-09-08T10:00:00.0000000+00:00"
tags = ["CSharp"]
title = "Private fields with underscore prefixes, or not?"
+++
Coding styles is always a very volatile topic. Everyone has their own interpretation on how it should be. In this post I’m going to tell you about my journey of preferring private fields with an underscore, then to not using them and then, recently, coming back again.

When I first started to learn programming, the example code mostly had underscores prefixing private fields in classes, like so: `private int _myField;`. Not knowing any better and doing what I was taught, I followed this practice for a good long while.

I started disliking having to write all those underscores everywhere, I found unnatural to do on my keyboard (a [Norwegian one](https://en.wikipedia.org/wiki/QWERTY#/media/File:KB_Norway.svg)). I was wondering why it was necessary with all these extra characters repeated all over the place in my code. It was extra typing and it wasn’t very intuitive to type on the keyboard either.

Realizing that I could code in my own style, I started dropping the underscores in my code. It felt better; it was easier to type, I thought it looked better and it made sense to me.

Very recently though, I came across two things that made me switch back to using underscore prefixes:

1. When reading some code on Github, which didn’t use underscores, I realised that it became hard to keep track which variables were fields of the class and which were local variables of the block. When you’re the one writing the code or know the style of the one who wrote it, it might be easy to keep track. As an outsider trying to figure out what was happening, it was harder to keep track of which was which.
2. Recently [Lance McCarthy](https://twitter.com/lancewmccarthy) mentioned he was seeing a resurgence of private fields with underscore prefixes and [Neil Turner](https://twitter.com/theothernt) linked to the [DotNetCore coding guidelines](https://github.com/dotnet/corefx/blob/master/Documentation/coding-guidelines/framework-design-guidelines-digest.md), which uses this style.

This made me decide: I’m going back.

After going back to underscore prefixes I’ve found that I’ve gotten better at finger contortion and actually can do the underscore combo easier than before. It’s also nice having sort of a filter in the Visual Studio Intellisense that only shows me private fields when I start typing `_`.

The biggest benefit however, is the reduce in variable name conflicts: While Visual Studio Intellisense and the C# compiler is clever enough to understand that these two are not the same variable:

```
private int myInt = 1;

private int ReturnNewInt()
{
    var myInt = 3;
    return myInt;
}
```

But for a human being, this becomes very confusing, very fast. You can of course get around this by not using short field names and by truncating local variables. This gets very cumbersome a lot of the time and I’m guessing you’re going to hit a conflict sooner or later anyway. While it might technically work, it might not work intellectually.

The most important thing in the end though is to use a style comfortable to you.

Happy coding 🙂