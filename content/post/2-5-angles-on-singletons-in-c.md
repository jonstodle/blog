+++
date = "2017-03-20T11:00:00.0000000+00:00"
tags = ["C#"]
title = "2.5 angles on singletons in C#"
+++
This is not a thorough walk through of the technical pros and cons of different
singleton patterns in C#. This is just me philosophizing over the three
approaches I’ve used in my code.

If you want a proper, thorough, technical, accurate and intelligent walk through
of singleton patterns in C#, check this
[http://csharpindepth.com/Articles/General/Singleton.aspx]  out. That’s done
properly and by a guy who knows his stuff: Jon Skeet, the C# god of Stack
Overflow [http://stackoverflow.com/users/22656/jon-skeet].

Anyway. Here are my three implementations and thoughts:

public static MySingleton Current { get; } = new MySingleton();


This is one approach I’ve used a couple of times recently. It’s very succinct,
but it has one flaw which I didn’t consider until it got me into trouble; it
instantiates the class at first access to the MySingleton  class.

If your only static property on MySingleton  is Current, this is not a problem.
However, I wanted to access another static property on the class before using 
Current: MySingleton.IsAvailable. If IsAvailable  is false, I can’t instantiate 
MySingleton, but that happens automatically when I first access MySingleton.

public static MySingleton Current { get; }

static MySingleton()
{
    Current = Current ?? new MySingleton();
}


This approach is more or less the same as the one above. It tries to assign 
Current  to itself, unless it’s null, in which case it sets it to a new instance
of MySingleton. This still suffers from the problem mentioned above; Current  is
instantiated at first access to the class.

One additional trap is that a static constructor might be invoked before the
class is even used. If the CLR (which runs the code on the computer) decides it
has some spare time, it might start invoking static constructors in case they’re
going to be used at a later time. This is done to prevent delays when accessing
the class for the first time later.

private static MySingleton mySingleton;
 public static MySingleton Current
 {
     get
     {
         if(mySingleton == null) mySingleton = new MySingleton();
         return mySingleton;
     }
 }


What you want is this. A lazy instantiation of the Current  property, i.e. it’s
not instantiated before the property itself is used. Even if you use the rest of
the class, Current  will not instantiate a new MySingleton  instance until truly
needed.

Which one of the three you end up using might depend on your needs. Sometimes it
might be beneficial if the CLR instantiates the singleton before it’s truly
needed (if it has some CPU time to spare anyways). Other times however, it might
be more desirable with a lazy property.

Choose wisely and happy coding 🙂
