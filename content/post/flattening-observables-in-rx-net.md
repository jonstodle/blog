+++
date = "10/26/2017 10:00:00 +00:00"
tags = ["Reactive Extensions"]
title = "Flattening observables in Rx.NET"
+++
Quite often when working with Rx, you'll end up with an observable of
observables: IObservable<IObservable<int>>  for example. When you were learning
Rx, you were probably told to use SelectMany  to flatten  the observable:

obs1
    .SelectMany(_ => Observable.FromAsync(() => DoWebCall()))
    ...


This flattens the IObservable<IObservable<int>>  to IObservable<int>. "Case
closed" you might think, but here's something to consider: SelectMany  will
merge all the observables returned inside the operator. Let's look a bit into
that.

Merge
To better understand SelectMany  we'll start by having a look at Merge.

I think the most common uses of Merge  is either as a static method on 
Observable  or as an operator taking an argument:

// As a static method
Observable.Merge(
    obs1, obs2
)
...

// As an operator taking an argument
obs1
    .Merge(obs2)
    ...


Merge  can also be used as an operator taking no arguments:

obs1
    .Merge()
    ...


This Merge  only works on IObservable<IObservable<T>>, or an observable of
observables. Observables of observables are a bit hard to grasp, so here's one
way to think about it:

An IObservable<T>  is something that will happen some time in the future. That
future might be 1ms away or 10 minutes, doesn't matter when - you'll get a
signal when it happens. The signal will contain 1 value of type T. An 
IObservable<IObservable<T>>  is something that will signal when something is
scheduled to happen in the future. When that scheduled thing happens, you'll get
a signal (and that signal will contain 1 value of type T).

This is can very quickly become very complex, which is why merge these
observable. Instead of being told that a new observable has been added, we
automatically subscribe to the new observable and get notified about the result
in our existing observable.

This is what Merge  does. You give it an IObservable<IObservable<T>>  and you'll
get back an IObservable<T>. Whenever there's a new inner observable, Merge  will
automatically subscribe to it and pass Next  and Error  signals to the outer
observable.

To use Merge  in this manner, we can rewrite the first example like this:

// First example
obs1
    .SelectMany(_ => Observable.FromAsync(() => DoWebCall()))
    ...

// Rewritten
obs1
    .Select(_ => Observable.FromAsync(() => DoWebCall()))
    .Merge()
    ...


SelectMany
You've just seen how SelectMany  basically works:

 1. Transform a signal to an observable (Select)
 2. Merge that signal into the original/outer observable (Merge)

Imagine however that the observable that is returned inside the SelectMany 
emits multiple signals. In some cases this is what you want, but when doing web
requests you're most likely only interested in the most recent request sent. 
SelectMany  will pass on any signal that is returned, regardless whether they
are in order or not.

This is perhaps best explained visually. This shows an Observable.Interval 
emitting a signal every 2 seconds (these signals are again transformed to
strings to more easily distinguish them). That signal is transformed to a new 
Observable.Interval  which will emit signals half a second apart.



Notice how the signals from the different Observable.Intervals mix with each
other. That might not be what we want.

Switch
Enter Switch. Switch  also works on IObservable<IObservable<T>>, but in contrast
to Merge, it switches  to the most recent IObservable<T>  inside the 
IObservable<IObservable<T>>.

When a new IObservable<T>  is emitted by IObservable<IObservable<T>>, Switch 
will unsubscribe to previous IObservable<T>  and subscribe to the new one. That
way you won't get any signals from the previous observable if it should happen
to send a signal after you've received the new observable.

This example uses a Select().Switch()  combo. Notice how we only receive signals
from the most recent Observable.Interval.



Concat
The last operator I'm going to cover is Concat. Concat  also only allows one 
inner  observable produce values, but it will wait for the previous to finish
before it subscribes to the new one. When a complete  signal is sent by the
previous observable, Concat  will subscribe to the next and let that finish
before subscribing to the next one.

Let's look at a gif:



Each observable emits 4 signals, and when that's done Concat  let's the next
observable emit it's 4 values.


--------------------------------------------------------------------------------

The tool I used for these examples is Linqpad [http://www.linqpad.net/]. It's an
excellent tool if you're doing any kind .Net development.

Happy coding!
