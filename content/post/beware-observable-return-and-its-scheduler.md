+++
date = "2017-03-30T10:00:00.0000000+00:00"
tags = ["Reactive Extensions"]
title = "Beware Observable.Return() and it’s scheduler"
+++
I love Reactive Extensions [http://reactivex.io/]. It makes async so much easier
and it makes handling continuous flows of data or event easy to handle.

While Reactive Extensions (Rx) is single threaded by default, it is very easy to
move between threads when needed. If you need to do some heavy work you can
easily tell Rx to move that work to a thread on the task pool, and then move
back to the UI thread in time to update the UI.

Some of the operators in Rx accept a so-called scheduler, which tells Rx where
to do the work of the operator. These operators often do work that might block
the thread an can therefore easily move their work to another thread.

Observable.Return()  also accepts a scheduler, which tells it on which thread to
return the object. While it does do that, there’s something it doesn’t do: It
doesn’t move the returned object to the requested thread until after it has
retrieved it.

This means it will block the thread you’re currently on if you fetch the value
to return the through a blocking function or method call.

Here’s an example. First we print the id of the current thread we’re on:

Console.WriteLine("Starting on thread ".PadLeft(26) + Thread.CurrentThread.ManagedThreadId.ToString());


Then we create a simple Func  which prints the thread it’s run on and then
returns a Unit, which is just an empty value. We will run this in the 
Observable.Return  to check which thread it’s on.

Func writeLineAndReturnUnit = () =>
{
    Console.WriteLine($"Inside Return on thread ".PadLeft(26) + Thread.CurrentThread.ManagedThreadId.ToString());
    return Unit.Default;
};


We call Observable.Return  and specify that it should return on a task pool
thread. We check the thread we’re on right after the Observable.Return  has
given us a value, and then again in the subscribe when we receive the value.

Observable.Return(writeLineAndReturnUnit(), TaskPoolScheduler.Default)
    .Do(_ => Console.WriteLine("Passing through on thread ".PadLeft(26) + Thread.CurrentThread.ManagedThreadId.ToString()))
    .Subscribe(_ => Console.WriteLine($"Received on thread ".PadLeft(26) + Thread.CurrentThread.ManagedThreadId));


This will output the following (the thread ids will, of course, vary).

       Starting on thread 11
  Inside Return on thread 11
Passing through on thread 10
       Received on thread 10


As you can see, the code inside the Return  is executed on the same thread that
calls it. If you call a function or method which blocks the thread inside Return
, like a file which might take some time to load from disk, you will block the
current thread. This might not be what you want if you’re calling it from the UI
thread.

Lesson is: don’t do any heavy lifting inside Observable.Return, it only moves to
the correct thread after it’s fetched the value to be returned. Preferably you
should only return “simple” values like string or int literals.
