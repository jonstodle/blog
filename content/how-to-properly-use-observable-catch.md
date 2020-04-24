+++
date = "2017-04-19T10:00:00.0000000+00:00"
tags = ["Reactive Extensions"]
title = "How to properly use Observable.Catch()"
+++
When being new to [Reactive Extensions](http://reactivex.io/) there’s a lot to take in and a lot of things are confusing. After a while, you start getting into the flow and things are going better. But then, all of a sudden, you get snagged when trying to use [Observable.Catch()](http://reactivex.io/).

Whenever an exception is thrown inside of an observable chain, it collapses the whole chain. That means that if the source of the observable was an event and that event continues to fire, your observable is not listening to it.

“Aha!” you might think, “I’ll just put a Catch() in there and that solves it”. So you try to do this:

```
Observable.FromEventPattern(window, nameof(Window.Activated))
    .SelectMany(_ => LoadSomethingFromTheWeb()) // .flatMap() in non-C# Rx
    .Catch(Observable.Return("")) .Subscribe(x => { /* Do something */ });
```

That doesn’t solve your problem though. The `Catch()` operator only replaces the previous observable, it doesn’t preserve or restore it in any way. The original subscription to the event is gone.

What you meant to do is actually this:

```
Observable.FromEventPattern(window, nameof(Window.Activated))
    .SelectMany(_ => LoadSomethingFromTheWeb().Catch(Observable.Return("")))
    .Subscribe(x => { /* Do something */ });
```

But how is this different?

Remember that `Catch()` replaces the observable that threw an exception. You don’t want to replace the observable that starts with `Observable.FromEventPattern()` when there’s an excption, you want to replace the result which failed to load from the web.

By putting the `Catch()` on the `LoadSomethingFromTheWeb()` observable you’re saying “Load something from the web and return the result in an observable. Should that fail, replace that observable sequence entirely with this new one.”

In the example above we replace the failing observable with a new one which just returns a single value: an empty string.

While the `SelectMany` makes it look like the observable returned inside it disappears into the parent observable, it’s actually still there. The `LoadSomethingFromTheWeb()` observable is still present, it’s just that all it’s items are passed directly on to the parent observable.

Understanding how `Catch()` works will save you a lot of headache and make it much easier to handle any errors or exceptions in you observable pipelines. Leave a comment below if you have any questions, I’ll happy to answer them 😊.

Happy coding!