+++
date = "2016-11-26T11:00:00.0000000+00:00"
tags = ["Tip"]
title = "Visual Studio Code Completion Shortcut"
+++
I’ve never appreciated Intellisense in Visual Studio as much as I do now.
Intellisense is the code completion technology present in the Visual Studio
family of products. After using Xcode for a little while, and it’s atrocious
code completion, I can truly appreciate Intellisense in it’s full glory.

Anyway, back to the shortcut with Intellisense: If you’re working with an API
that has a lot of classes starting with the same name (I’m looking at you 
ReactiveUI [http://reactiveui.net/]); or you’ve ended up with at lot of fields
or properties starting the same, you might have come across the situation where
you end up typing the same thing over and over to get the right element.

Let’s say you have this list of fields:

 * NameContainerGrid
 * NameTextBlock
 * NameTextBox
 * NameCheckButton

You could type “name” every time you access these fields:



Or you can just type the capital letters of the name of the field (or
property/class) and skip over the lowercase letters to get to the part of the
name that’s unique:



In Visual Studio 2017 this is made even better as Intellisense highlights the
part of the name it’s matched to your input:



This means you can reach MainWindowViewModel quickly by typing “MWVM” and
hitting enter/space or any of the other characters which completes the
suggestion.

Happy coding!
