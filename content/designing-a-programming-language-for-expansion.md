+++
title = "Designing a Programming Language For Expansion"
date = 2021-04-06
[taxonomies]
tags = ["commentary"]
+++

I recently watched a talk called *[Growing a Language](https://youtu.be/_ahvzDzKdB0)* by [Guy Steele](https://en.wikipedia.org/wiki/Guy_L._Steele_Jr.), a productive fellow involved in writing specifications for multiple programming languages - including Java. In the talk he makes the case that you can’t design an extensive language from the start, but must start simple and expand over time. By expanding as needed, users are able to give feedback and field suggestions on what’s needed in the language. This in turn makes users invested in the language and keep favoring it.

Not being able to expand the language will make it stagnate and fall out of favor, Steele argues. Programming languages today, and in the ‘90s when the talk was given, have to solve large and complex problems, and should be designed to accommodate that (over time). A language lacking more advanced tools makes it unnecessarily harder for users of the language to use it in a modern setting.

Throughout the talk he speaks of expanding the *programming language*. I would prefer to split what he refers to into two parts: the *actual programming language* (I’ll call it *the syntax* from here on to distinguish it from Steele’s compound definition), and the *standard library*.

The syntax dictates how you express yourself in the language. It defines the reserved keywords and how to compose them. The standard library provides pre-built types and functions which are included by default when using the language.

I make this distinction because there are two ways to allow a programming language to expand: by expanding both the syntax and the standard library **or** by only extending the standard library, leaving the syntax alone.

Languages like Python, C# or Rust are continually expanding both their standard libraries with new types and functions and adding new syntax to their languages. They introduce types which represent new concepts and syntax to make it easier to work with these types.

An example that’s been making the rounds in a lot of popular programming languages is `Task`s, `Future`s or `Promise`s, which represent a result which will be produced at some point in the future. In addition to introduce types representing this concept, the languages also received new syntax, `async` and `await`, to make working with these types more streamlined.

On the other side we’ve got Go. Go has not changed it’s syntax since it’s 1.0 release in 2011. They’ve even made it a point to keep the syntax light. The standard library however, has been expanded greatly since then. How you express yourself in the language has remained unchanged.

This prevents the language being able to streamline usage of new types representing new concepts, but it comes with a great benefit: There’s no need learning new syntax and beginners to the language benefits only having to learn the same syntax as those that picked it up at first release. All resources online are still relevant syntax wise, still a decade later!

Rust is one of my favorite programming languages, and it’s ability to add syntax later in it’s lifetime is undoubtedly a strength. However, the fact that Go’s syntax has remained the same for a decade is also undoubtedly a strength. Go’s willingness to expand it’s standard library has made it incredibly comprehensive, meaning you’re able to get *very* far without needing to pull in extra code. They’ve managed to be comprehensive, without becoming complex.

I’m not going to declare one strategy superior to another, but I think it shows, a bit contrary to Steele’s claims (at least in the ‘90s), that being able to extend the syntax is not necessarily a hinderance in a language being able to expand and thrive over time.

---

### A note on Go 2:

Version 2 of Go will include new syntax for working with, among other things, generics. It could be argued that it shows that it’s a requirement to expand syntax to be able to continue to evolve. I’m not going to wholly dispute that, but I think it’s worth noticing how they are approaching the change: as something extraordinary and as a rare occurrence.

Changing or expanding the syntax is something they don’t take lightly and only do when it’s strictly necessary, not out of convenience or as part of following a trend. By changing the syntax every decade and a half it feels more like designing a new language, extending an existing one. That, I think, is not in the vein of what Steele talks about.
