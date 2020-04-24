+++
title = "Task.ToObservable() != Observable.FromAsync(() => Task)"
date = 2017-05-29T10:00:00.0000000+00:00
[taxonomies]
tags = ["Reactive Extensions"]
+++
When converting tasks to observables, writing something like this is very common:

```
Observable.FromAsync(() => ThisReturnsATask());
```

At some point you may discover that there’s actually an extension method for tasks which turns it in to an observable:

```
ThisReturnsATask().ToObservable();
```

But that is not the same as the first conversion.

When calling `ThisReturnsATask()` it will start the task immediately, as is usual. The task it returns can be stored in a variable…

```
var task = ThisReturnsATask();
```

… which can be awaited later to retrieve the value:

```
var result = await task;
```

If the task is already done, it will return the result immediately. If it’s not done the execution will wait for it to be done. It behaves exactly the same as it does when you do this:

```
var result = await ThisReturnsATask();
```

When calling `ThisReturnsATask().ToObservable()` it will immediately start the task and convert the result into a signal in the observable. If you haven’t subscribed to the observable, it will store the value until you’re ready for it.

This is in contrast to `Observable.FromAsync(() => ThisReturnsATask());` which defers calling the inner `Func<Task>` until you subscribe. This means that the task returned from `ThisReturnsATask()` will not be started until you subscribe to the observable.

Here’s an example:

```
async Task DoWork()
{
    System.Diagnostics.Debug.WriteLine("I did work");
}
 
var obs1 = Observable.FromAsync(() => DoWork());
var obs2 = DoWork().ToObservable();
```

If you run this code, you might expect that nothing should be printed in the output window, but what you’ll see is

```
I did work
```

That is because when you assign `obs2`, `DoWork` is called and it prints to the debug window. `obs1` will not print anything until you subscribe.

As a side note, you can make `ToObservable()` behave like `FromAsync()` with this “workaround”:

```
Observable.Defer(() => ThisReturnsATask().ToObservable());
```

It feels a bit heavy-handed to me, so I prefer the `FromAsync()` personally.

Happy coding!
