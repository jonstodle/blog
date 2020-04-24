+++
date = "2017-05-19T10:00:00.0000000+00:00"
tags = ["Xamarin","Xamarin Forms"]
title = "Xamarin products explained"
+++
I see questions about the different Xamarin solutions pop up from time to time, and I thought I’d write down my explanation here and link to this when people ask.

I’m going to go through the following:

* Xamarin.iOS
* Xamarin.Android
* Xamarin.Forms
 * Shared vs PCL

# Xamarin.iOS

Using Xamarin.iOS is basically the same as developing for iOS using Objective-C. The difference is that you’re using C# instead of Objective-C. All the APIs on the platform are the same, they are named the same and work the same. It’s like you’re developing natively for iOS, except you don’t have to learn Objective-C (or Swift).

Some aspects of the API have been C#-ified: The property names follow the C# standard of PascalCase, views are implemented as `partial` classes and events are used instead of delegates. You can learn more about this in the [Xamarin docs and guides](https://developer.xamarin.com/guides/ios/). With a little trial and error you should be able to look up an iOS question on Stack Overflow and understand how to do it in Xamarin.iOS.

# Xamarin.Android

This is pretty much the same thing as it is for Xamarin.iOS: the APIs are the same as in native Android development, but you’ll be using C# instead of Java to develop. There are som C#-ification (like the use of events instead of interfaces+classes), but with C# being so similar to Java, the differences are even smaller than with Xamarin.iOS.

# The Benefit of Xamarin.iOS and Xamarin.Android

You’ll be doing all the platform development as if you are doing it natively (but with C# instead), but you’ll be able to put business logic in a separate platform independent project and reuse it. That means all your model classes, network logic, view models (if you’re doing something MVVM-like) and (probably) more can be shared between the two platforms.

There’s also built-in support for dependency injection to use for platform specific implementations. For example: requesting access to the camera is different between iOS and Android, so you’ll have to write that code for each platform. You define an `interface` in your platform independent project which represents requesting access to the camera. In the platform specific projects you create a class that implements the interface and register it with the dependency service. At run time you request the platform specific implementation of the interface.

# Xamarin.Forms

First things first: Xamarin.Forms uses Xamarin.iOS and Xamarin.Android under the hood. What Xamarin.Forms provides is a way to describe UI in generic manner. You create the UI and Xamarin.Forms will use that information to generate a platform specific version of the controls. Let me explain with an example:

In Xamarin.Forms you have a type called `Button`. This type only describes properties of a hypothetical button. You can set what text it should display, what background color it should have and what code to run when a user taps on it. The `Button` though, does not know anything about iOS or Android.

When you run the app, Xamarin.Forms goes through all the UI code you’ve written (XAML or C#) and uses a `Renderer` to create platform specific versions of the `Button`. On iOS that means a `UIButton`, for example. It then looks through all the properties of the `Button` ( `Text`, `BackgroundColor`, tap handler, etc) and sets those values to the corresponding values on the `UIButton`.

This happens for each control in your UI when you run your app. You are in fact creating the same UI you would by using Xamarin.iOS and Xamarin.Android, but you don’t have to do the work twice.

You can even create your own `Renderer`s, either for existing controls (overriding the built-in ones) or for your own controls. You can do all the same things in Xamarin.Forms as you can with Xamarin.iOS and Xamarin.Android, but you don’t have to do the boilerplate stuff twice.

# Shared vs PCL project

This is probably the biggest confusion when it comes to using Xamarin.Forms. When setting up a Xamarin.Forms project, you are asked to choose: shared or PCL project. Here’s my attempt at explaining it:

# PCL project

I’ll start off with the PCL based project, because I think it’s the easiest to understand.

You write almost all of your code in the PCL project. This includes business logic and UI code. This project knows nothing about the specific platforms and you mostly refer to the Xamarin.Forms libraries.

To be able to use platform specific features (like requesting camera access), you can use the built in `DependencyService` as explained earier.

# Shared project

A shared project is something that was originally introduced back in the Windows 8.1 days. While it’s called a project, it’s not really a complete project on its own.

While you can add files and folder to the project, you can’t add references to other assemblies or add Nuget packages. This is because the shared project won’t be compiled on it’s own. When you compile any projects that references the shared project, the files in the shared project are copied into the referencing project and compiled as part of that project.

This means the shared project will have access to all the assemblies and Nugets that are added to the project that references the shared project. When you’re code is run, it will be inside the referencing project.

This means that platforms specific features are available in the shared project, but you’ll get an error when you try to compile an iOS project when there’s Android code in the shared project, and vice versa.

To use platform specific you can use compiler directives (`#if __IOS__` and `#if __ANDROID__`) to only include the platform specific code when you compile for that platform. It’s also possible to use partial classes where the platform specific code is in a part of the partial class inside the platform specific project.

# Conclusion

Which path you choose is totally dependent on your needs and there isn’t really a correct answer, but I hope I’ve managed to aid you to make the correct decision.

Happy coding!