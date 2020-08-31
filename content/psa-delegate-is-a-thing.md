+++
title = "PSA: delegate is a Thing"
date = 2020-08-31T21:35:46Z
[taxonomies]
tags = ["programming", "c#", "opinion"]
+++

This is just a short reminder to all C# developers defining and implementing interfaces with a single method:

> ### What you are looking for is `delegate`

A `delegate` is a way to define the signature of a method. It can then be implemented by a `Func`, an `Action` or class method, whichever fits the best.

They even work nicely with dependency injection: a `delegate` is just a definition, not an implementation. If you want to preserve testability, that's taken care of: in tests you can just pass in a dummy implementation by way of a `Func` or `Action`; no mocking framework needed.

Here's an example

```csharp
// Definition
public delegate void SendEmail(string recipient, string sender, string subject, string message);

// Registering with a DI system
services.AddTransient(_ => (recipient, sender, subject, message) => { /* Implementation */ });
// or
services.AddTransient(_ => StaticClassWithImplementation.StaticMethodImplementingDelegate);

// In testing
var systemUnderTest = new ClassThatNeedsToSendEmails((_, __, ___, ____) => { log.Info("Sent email"); });
```

Don't overcomplicate with interfaces and classes, what could simply be a delegate.
