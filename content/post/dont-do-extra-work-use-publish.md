+++
date = "04/29/2017 10:00:00 +00:00"
tags = ["Reactive Extensions"]
title = "Don’t do extra work, use Publish()"
+++
When using Reactive Extensions [http://reactivex.io/]  you sooner or later
encounter a point where you end up re-using the same source observable to build
two different observables. Something like this perhaps:

var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))
    .SelectMany(_ => LoadSomethingFromTheWeb());
 
somethingsFromWeb
    .Where(x => x.Length > 10)
    .Subscribe(x => { /* Do something */ });
 
somethingsFromWeb
    .Where(x => x.Length <= 10)
    .Subscribe(x => { /* Do something else */ });


What you might not realize is that you’re actually doing double work here. For
each time you call Subscribe()  on an observable, you’re actually creating a
whole new observable pipeline. Even though somethingsFromWeb  are shared between
the two subscribers when creating the observable pipeline, it’s not shared when
subscribed to by calling Subscribe().

One way to put it is that somethingsFromWeb  is copied to each subscriber that
use it. The code above is somewhat equivalent to the following code, using good
ol’ .Net events:

window.Activated += async (sender, args) =>
{
    var somethingFromWeb = await LoadSomethingFromTheWeb();
    if (somethingFromWeb.Length > 10) { /* Do something */ }
}
 
window.Activated += async (sender, args) =>
{
    var somethingFromWeb = await LoadSomethingFromTheWeb();
    if (somethingFromWeb.Length <= 10) { /* Do something */ }
}


When putting it like this, it’s very easy to see what’s happening: you’re
clearly doing the same work multiple times for each event.

There is of course a correct way to do this in Reactive Extensions and it’s the 
Publish()  operator. This makes somethingsFromWeb  in the first example behave
like what you’d expect: it shares the source observable between all the
subscribers.

By doing this…

var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))
    .SelectMany(_ => LoadSomethingFromTheWeb())
    .Publish();


… everything before Publish()  is done once and then passed to all subscribers
using somethingsFromWeb  as a source.

When using Publish()  on an observable you also stop it from producing any
elements. It won’t emit anything until you tell it to. This may seem a bit
weird, but it’s really useful in cases where you want to be sure multiple
subscribers using it as a source are ready before it starts emitting elements.

To make it start you call Connect()  on the “published” observable (technically
it’s called a “connectable observable”):

var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))
    .SelectMany(_ => LoadSomethingFromTheWeb())
    .Publish();
 
somethingsFromWeb
    .Where(x => x.Length > 10)
    .Subscribe(x => { /* Do something */ });
 
somethingsFromWeb
    .Where(x => x.Length <= 10)
    .Subscribe(x => { /* Do something else */ });
 
somethingsFromWeb.Connect();


Here it’s called after the two subscribers are set up, but you can also call it
right after you’ve published the observable. It will now start emitting element
immediately:

var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))
    .SelectMany(_ => LoadSomethingFromTheWeb())
    .Publish();
somethingsFromWeb.Connect();
 
somethingsFromWeb
    .Where(x => x.Length > 10)
    .Subscribe(x => { /* Do something */ });
 
somethingsFromWeb
    .Where(x => x.Length <= 10)
    .Subscribe(x => { /* Do something else */ });


There is also a nice sibling to Publish()  and Connect()  called RefCount(). 
RefCount()  makes sharing easier. You call it directly after Publish()  and you
don’t have to call Connect(), RefCount()  does that for you.

var somethingsFromWeb = Observable.FromEventPattern(window, nameof(Window.Activated))
    .SelectMany(_ => LoadSomethingFromTheWeb())
    .Publish()
    .RefCount();
 
somethingsFromWeb
    .Where(x => x.Length > 10)
    .Subscribe(x => { /* Do something */ });
 
somethingsFromWeb
    .Where(x => x.Length <= 10)
    .Subscribe(x => { /* Do something else */ });


RefCount()  works like this: When the “refcounted” observable receives it’s
first subscriber, it calls Connect()  on the published observable. The
refcounted observable then works as a regular published observable, sharing
everything before Publish()  between all it’s subscribers.

RefCount()  always keeps a count of how many subscribers it has. When the
different subscribers stop using the refcounted observable, by disposing the
subscription, it checks whether it has any subscribers left. If it has zero
subscribers it disposes the published observable, as it’s no longer used by
anyone.

Now go fix your code 😊.

Happy coding!
