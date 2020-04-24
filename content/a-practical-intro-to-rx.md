+++
title = "A practical intro to Rx"
date = 2017-08-27T10:00:00.0000000+00:00
[taxonomies]
tags = ["Reactive Extensions","RxJS"]
+++
If you're trying to get started with programming with reactive extensions and observable programming, you are going to feel overwhelmed. You're trying to start thinking in a new way, and in some cases in a completely opposite way, than what you have been doing since you started programming.

In this intro, I will do my best to help you get in the right mindset. I will do this by trying to avoid using any of the fancy words you often see attached to Rx. I'll steer away from math theory and terms from research papers and use more common phrases as best I can.

In this intro I'm going to use JavaScript (and some HTML) and RxJS. At this point in time I think all programmers have been exposed to JavaScript at some point and this makes it more or less a universal language. If this is going to affect you, you can try and squint your eyes and pretend it's Java.

Now, let's start learning:

<details>
    <summary>Table of Contents</summary>
    <ul>
        <li><a href="#goodoldregularevents">Good, old, regular events</a></li>
        <li><a href="#observables">Observables</a></li>
        <ul>
            <li><a href="#signals">Signals</a></li>
            <ul>
                <li><a href="#nextsignals">Next signals</a></li>
                <li><a href="#errorsignals">Error signals</a></li>
                <li><a href="#completesignals">Complete signals</a></li>
            </ul>
            <li><a href="#creatinganobservable">Creating an observable</a></li>
        </ul>
        <li><a href="#observers">Observers</a></li>
        <ul>
            <li><a href="#next">next()</a></li>
            <li><a href="#error">error()</a></li>
            <li><a href="#complete">complete()</a></li>
        </ul>
        <li><a href="#theguaranteesofanobservable">The guarantees of an observable</a></li>
        <ul>
            <li><a href="#1anobservablewillproducezeroormorenextsignals">1. An observable will produce zero or more next signals</a></li>
            <li><a href="#2ifanerroroccursinsidetheobservableanerrorsignalwillbesentandnofurthersignalswillbesent">2. If an error occurs inside the observable, an error signal will be sent and no further signals will be sent</a></li>
            <li><a href="#3whentheobservablehasnomorenextsignalstoproduceitwillsendacompletesignalandnofurthersignalswillbesent">3. When the observable has no more next signals to produce, it will send a complete signal and no further signals will be sent</a></li>
        </ul>
        <li><a href="#subscribingtoobservables">Subscribing to observables</a></li>
        <li><a href="#hotandcoldobservables">Hot and cold observables</a></li>
        <li><a href="#subscriptions">Subscriptions</a></li>
        <ul>
            <li><a href="#automaticcleanup">Automatic clean up</a></li>
        </ul>
        <li><a href="#operators">Operators</a></li>
        <ul>
                <ul>
                <li><a href="#themodifiedobservablemodel">The modified observable model</a></li>
                <li><a href="#thepipelinemodel">The pipeline model</a></li>
            </ul>
            <li><a href="#operatorexplanations">Operator explanations</a></li>
            <ul>
                <li><a href="#map">map</a></li>
                <li><a href="#filter">filter</a></li>
                <li><a href="#combinelatest">combinelatest</a></li>
                <li><a href="#merge">merge</a></li>
                <li><a href="#mergemap">mergeMap</a></li>
                <li><a href="#switchmap">switchMap</a></li>
                <li><a href="#take">take</a></li>
                <li><a href="#catch">catch</a></li>
            </ul>
        </ul>
        <li><a href="#conclusion">Conclusion</a></li>
    </ul>
</details>


# Good, old, regular events

In Rx, it all starts with the *observable*. We, however, are going to start with events. More specifically, click events. Click events on a button like this in fact:

<p data-height="265" data-theme-id="0" data-slug-hash="wqoKvz" data-default-tab="html,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 01" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/wqoKvz/">Intro to Rx 01</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

This button doesn't do anything for us at the moment, but it does do something: it generates events. Every time you click the button a `click` event is fired. We haven't started listening to those events so we're not doing anything when those events fires, but they're still happening.

Even if no one is present to hear a tree fall in the forest, it will still make a sound. The same goes for the button: it will generate click events, even though no one is listening.

Let's start listening. With this button I've added a simple event listener to the click events:

<p data-height="265" data-theme-id="0" data-slug-hash="VzmvjZ" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 02" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/VzmvjZ/">Intro to Rx 02</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

The event listener might receive 1 event, or 2 events, or many events. It might even receive no events at all. The user might never click on the button. But when the user *does* click on the button, we have some instructions how to handle that.

We've now got a button, which is a *source of events*, an event listener, which let's us *get notified of those events*, and a handler function, which let's us *react to the new events*.

This is some basic stuff, you probably know this better than the back of your hand, but now for the plot twist: An observable is a *source of signals*, an observer let's us *get notified of those signals* and a next handler let's us *react to the new signals*.

Let's have a closer look at observables.


# Observables

I like to describe an observable as a producer of signals. A signal is just a generic term for something that is emitted by the observable. Just as a DOM element have different kinds of events, an observable has different kinds of signals.


## Signals

All signals carry with them a value. It's that value you're actually interested in. Just as an event contains a value with details about the event, a signals contains a value with some kind of data. The kind of data depends on the source of values.

I use the term signal in this intro, but you might see them called *values* or *events* in other places. I prefer to use the generic term signal for any kind of output from the observable, and only use values for *next signals* .


### Next signals

The *next signal* is the most common kind of signal you work with. It's called a next signal, or just *next* for short, because it represents the next value to be handled.

These signals contain the actual data you want. For observables based on events, this is the event data; for observable based on arrays, this is the values of the array; and for observables based on `Promise`s, this is the result of the promise.

An observable will produce a next signal whenever it has a new value ready.


### Error signals

The *error signal* is only produced when something goes wrong. If some kind of exception happens in an observable, it will produce an error signal.

The value of the error signal is the exception that was thrown.


### Complete signals

The *complete signal* is produced when the observable knows it's not going to produce any more next signals.

If the observable is based on an array, for example, and it has produced a next signal for each of the elements in the array, it will produce a complete signal to notify you that it is done.

The value of the complete signal is `void`, meaning it doesn't have a value. The complete signal doesn't have any data attached to it.


## Creating an observable

The source of the values that makes the observable produce the signals might vary, but you don't have to know the details about the source. The observable is a general abstraction on top of the actual source of values.

This means that you are able to work with signals from different sources in a general manner, and this is part of what makes Rx so powerful. Signals from a range of different sources all look and behave the same when they are delivered through the observable.

It's a bit like how you can iterate over an array, no matter the kind of contents it has. Array is a simple abstraction over a group of values and gives you a common set of methods to use on the items inside it.

While it is possible to create observables manually with the `create` factory method, I think that should be reserved as intermediate level topic. Not because it's very difficult, but because there are easier (and less error prone) ways to create observables.

While an observable can be it's own source of values, a common way to produce signals is to convert an existing source of values into an observable. Whenever a new value is available from the source, the observable will produce a new next signal for us. To convert a source of values to an observable, we use the family of `from*()` methods.

The first of these `from*()` factory methods we're going to look at is `fromEvent()`. This converts events from an event source to signals in an observable. You specify the source of the events and which event you want to listen to, and `fromEvent()` will convert those events to signals.

Here's how it's used:

```
Observable.fromEvent(button, "click");
```

This is roughly equivalent to this:

```
button.addEventListener("click");
```

Note the lack of a handler function in `addEventListener()`. This is because it exactly mirrors the call to `fromEvent()`. We're only announcing that we're interested in knowing when there is a new signal or event, but not what to do when we get that signal or event.

We already know how to add a handler to `addEventListener()`, so we're going to look at how to do it for observables. This is where the we get to observers.


# Observers

You very seldom work with observers directly, so I won't go into to too much detail. What you need to know is that an observer is a container for three types of handlers:


## next()

When an observable sends a next signal, it is to notify us that it has a new value we want. We receive the value of the next signal as an argument in the handler. We add a handler for next signals by calling `subscribe()` on the observable we created and pass in the handler:

```
Observable.fromEvent(button, "click").subscribe(event => {
    count++;
    output.innerText = "You clicked " + count + " times!";
  });
```


We call this *subscribing*; you have now subscribed to the observable. The above code is practically the same as this:

```
button.addEventListener("click", event => {
    count++;
    output.innerText = "You clicked " + count + " times!";
  });
```


## error()

When the observable sends the error signal, something has gone wrong. We handle this by adding a handler as the second argument to `subscribe()`. We receive the value of the error signal as an argument in the handler.:

```
Observable.fromEvent(button, "click").subscribe(event => {
    count++;
    output.innerText = "You clicked " + count + " times!";
  },
  error => console.log("Something bad happened: " + error);
```

The above code is kind of like this:

```
try {
  button.addEventListener("click", event => {
      count++;
      output.innerText = "You clicked " + count + " times!";
    });
} catch(error) {
  console.log("Something bad happened: " + error);
}
```


## complete()

When an observable is done producing next signals, it will send the complete signal. We can handle this by adding a handler as the third parameter. As complete's value is `void`, the handler doesn't take any arguments:

```
Observable.fromEvent(button, "click").subscribe(event => {
    count++;
    output.innerText = "You clicked " + count + " times!";
  },
  error => console.log("Something bad happened: " + error),
  () => console.log("I'm done."));
```


# The guarantees of an observable

When creating an observable, any observable, you're given a few guarantees. These guarantees relates to how the observable will behave, and the observable will *always* adhere to them. This makes observables easier and predictable to work with.


## 1. An observable will produce zero or more next signals

As I mentioned all the way at the top with events: you might not receive any events when adding an event listener, you're just listening in case one or more events are fired.

The same goes for observables: You might not receive any next signals when subscribing. You're just stating your interest in being told if there are any. In most cases you will expect to receive at least one value, but that's not guaranteed.

There are reasons why you might not get a next signal. These reasons are mentioned in the next rules.


## 2. If an error occurs inside the observable, an error signal will be sent and no further signals will be sent

When something goes wrong inside an observable, an *error signal* is sent. This includes both when we convert a source of values into an observable and when using operators (which we'll look at later).

The value of the error signal is the error that was generated inside the observable. In our examples so far, we've only looked at simple observable which won't cause an error to occur. If we were doing a web call inside an observable, the web call might error out. In that case we would receive an error signal containing the appropriate error.


## 3. When the observable has no more next signals to produce, it will send a complete signal and no further signals will be sent

When you subscribe, you don't know how many next signals you are going to receive (see guarantee #1). The observable will tell you if it knows there are no more next signals to be produced. It will then send a *complete signal*.

It's possible to create an observable from an array. When the observable has produced a next signal for each of the items in the array, it sends a complete signal to notify the subscriber that it's done producing values.

In the case of the event observable, you will never receive a complete signal. Events are never really done; there might always be one more. As the observable might produce more values, it will not send a complete signal either.


# Subscribing to observables

Remember that an observable is just an abstraction on top of a source that produces signals. In this case we've seen how to convert an event source to an observable, but as mentioned, the family of `from*()` methods can convert many different things to an observable.

* **`fromPromise()`** converts a JavaScript promise to an observable.
* **`from()`** takes an array, or an array-like object, and converts each item into a next signal in the resulting observable.

As all of these creates observables. This means you can also handle the next signals in a common way. Let's extract the handler from before into a separate function and change the output text:

```
function handleNext(nextValue){
  count++;
  output.innerText = "You have received " + count + " next signals!";
}
```

I've also changed the output to be a bit more generic. We can then pass it into the `subscribe()` method:

```
Observable.fromEvent(button, "click").subscribe(handleNext);
```

This will produce the same behavior. We're now able to reuse that handler for other observables too. Let's create an observable from an array:

```
const numbers = [1, 2, 3, 4, 5];

Observable.from(numbers);
```

We can put this together with our button handler and `handleNext()` from before:

```
Observable.fromEvent(button, "click").subscribe(handleNext);

Observable.from(numbers).subscribe(handleNext);
```

And here's the result:

<p data-height="265" data-theme-id="0" data-slug-hash="LjbpbK" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 03" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/LjbpbK/">Intro to Rx 03</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

You should see "You have received 5 next signals!" immediately. When converting the `numbers` array to an observable, the next signals are produced immediately. The method receives the array, goes through it, one item at the time, and produces a next signal for each of them. `handleNext()` is called for each next signal.

If you click the button, the count will continue as the same handler is used for the next signals produced by the click events on the button.


# Hot and cold observables

This leads us nicely into the concept of hot and cold observables. You'll see this mentioned a lot of places, especially people asking what the difference is. I hope that you, with your knowledge of the different observables we have created so far (the event observable and the array observable), will grasp this concept.

By default, an observable is cold. This means it will not produce any values before you call `subscribe()` on it. Just as a regular function won't return a value before you call it, an observable will not produce a signal before you subscribe to it.

This is how the array observable works. When we first call `Observable.from(numbers)`, no next signal is sent. We're just preparing the observable for later use. When we call `subscribe()` on the observable, it kicks into action and starts producing next signals for each of the items in the array:

```
const numbersObservable = Observable.from(numbers); // No next signal is produced
numbersObservable.subscribe(handleNext); // Next signals start being produced
```

A hot observable, on the other hand, produces values regardless whether you call `subscribe()` or not. This is how events work, for example. As I mentioned earlier, just because you're not listening to the click events on a button, doesn't mean they're not being fired.

When we call `Observable.fromEvent(button, "click")`, next signals are being produced every time we click the button. When we subscribe to it, we will receive all next signals produced from that point forward. All the next signals produced before that moment in time are lost. This is just like events.

In short, cold observables are like regular functions, which only produce values when called: they will not produce signals before `subscribe()` is called.  
Hot observables are like events, which produces values regardless if anyone is listening: they will produce next signals from the moment they are created, regardless if you call `subscribe()` or not.

While observables *should* be cold by default, you still get hot observables sometimes. 
This is often true when converting a source of values into an observable. This is the case with `fromEvent()` and `fromPromise()` for example.

A weakness in Rx is that there's no way to tell whether an observable is hot or cold by just looking at the declaration. You either have to know if it's hot or cold (as we know `fromEven()` observables are), or you have to subscribe to it and see what happens.

This sounds like a major drawback, and some say it is, but in 95% of the cases the outcome is predictable. Just be prepared to be surprised by a hot observable once in a while.


# Subscriptions

When subscribing to an observable, the `subscribe()` will return a token representing that subscription. This token has a single method you can call: `unsubscribe()`.

> **Note:** `unsubscribe()` is called `dispose()` in RxJS 4 and other implementations of Rx.

By calling `unsubscribe()` you tell the observable that you're no longer interested in new signals. This will make the observable shut down and clean up. If the observable has allocated objects they will be marked for clean up and asynchronous actions will be aborted.

`unsubscribe()` will not make the observable produce a complete signal. Complete signals are the observable's way of telling the subscriber it's done producing new values; `unsubscribe()` is the subscriber telling the observable that it's no longer interested in *any* signals.

Any time you know you're not interested in any more signals, you should always unsubscribe. This makes sure that the observable isn't doing any sort of work (potentially heavy work) in the background, that you're not interested in.

## Automatic clean up

When an observable has finished doing it's work, and sends a complete signal, it will also clean up after itself. That means if you receive a complete signal, you don't have to call `unsubscribe()`; the same clean up routine is done by the observable automatically.

If an error occurs inside an observable (or one of the operators) and it produces an error signal, the observable cleans up after itself the same way it does after a complete signal.


# Operators

Operators is probably the scariest and most overwhelming subject when getting started with Rx. In RxJS alone there are over 60 operators! That's a lot to swallow when starting out. I'm here to tell you two things:

First, you don't have to remember all the operators to get started with Rx. In reality you only need to concern yourself with a handful or two. Later, when you either need or want to, you can expand by looking into new operators.

Second, I'm going to go through the operators I see get used again and again. These are the ones you'll most likely need when you start using Rx.

Before we get into the operators, I'll explain how they work:

Up until now, we've only subscribed directly to the observable and handled the different signals. Operators let's us modify the observable before subscribing to it. We can filter out signals we're not interested in, or change the value of a next signal before we receive it in our next handler.

The official description of operators is a bit more technical, but I like to think of operators in two mental models. I'm going to explain them both below. I want you to ignore the operators themselves and just focus on the explanation. What the operators actually do will be explained in detail later.


### *The modified observable* model

This mental model considers an observable and all it's operators as a single unit. In the end you're left with an observable which you subscribe to.

Consider an observable that's based on the click events on a button:

```
const buttonClicks = Observable.fromEvent(button, "click");
```

We want to modify the next signal values produced by the observable. We add an operator:

```
const buttonClicks = Observable.fromEvent(button, "click")
  .map(myMappingFunction); // Don't worry about what this means. We'll get back to it
```

`buttonClicks` is still an observable, the operators return the original observable with the operator applied to it. To the subscriber it still looks like an observable without an operator attached to; all it sees is an observable of some kind.

The observable will still produce signals when the button is clicked. That has not changed. What has changed are the values of the next signals.

 Subscribing to the observable is exactly the same as before:

```
buttonClicks.subscribe(handleNext);
```

We could add even more operators to the observable to modify it even further:

```
const buttonClicks = Observable.fromEvent(button, "click")
  .map(myMappingFunction) // Again, ignore all the operators, we'll get back to all of this afterwords
  .filter(myFilteringFunction)
  .mergeMap(myMergeMappingFunction)
  .take(myTakeValue);
```

`buttonClicks` is still just observable. You subscribe to it as you normally would. The signals it produces has changed greatly, but the observable is still just an observable. 

As I mentioned earlier: *The observable is a general abstraction on top of the actual source of values*. In addition to abstracting away the source of the values, the observable can also modify the values before sending them to the `subscribe()`.

The operators let's you customise how the observable should modify the values.


### *The pipeline* model

This mental model considers the observable as a source of values, the operators as a pipeline and the subscribe as a handler.

Let's look at the same observable as before:

```
const buttonClicks = Observable.fromEvent(button, "click");
```

When working with operators, it's common practice to write each operator on a new line, aligning the dots. While this helps readability (especially with long operator chains), it also creates a nice visual for this mental model.

Operators can be applied to any observable, so let's show an alternate way of changing the next signal values before receiving them in the `subscribe()`.

```
buttonClicks
  .map(myMappingFunction)
  .subscribe(handleNext);
```

The pipeline model looks at it this way:

1. `buttonClicks` produces a value
2. The value is passed down to the next step in the pipeline: `map()`
3. `map()` changes the value
4. `map()` passes the value down to the next step in the pipeline: `subscribe()`
5. `subscribe()` handles the value

In this model, the value starts at the top and is passed down, one step at the time. Each step is an operator. The operator executes some operation and passes the value on. At the bottom the value is handled by `subscribe()`.

The pipeline model is probably the most common mental model of how operators work. You'll often see "the pipeline" referenced when talking about Rx. I think some might grasp it better with the modified observable model, however.

It's completely up to you how you choose to think about it; whatever works for you.


## Operator explanations

Most operators take a function as it's first argument. The function receives one value as an argument and returns a value. To put it in code, this is the function you pass in to an operator:

```
function(inValue) {
  // Do some work
  return newValue;
}

// OR

inValue => {
  // Do some work
  return newValue;
}
```

The `inValue` you receive is the value of a next signal. When you add an operator to an observable, the value of the next signal is passed to each of the operators in turn.

Inside each operator (with some few exceptions) you've supplied a function which takes the next signal's value, do some kind of operation or evaluation, and return a new value. The new value is then passed to the next operator in the pipeline.

To get a feel of how this works, let's look at an operator and how to use it.


### map

The map operator takes a function which receives a value and returns a value. You use this operator to convert values. The value you receive is the value of a next signal, and the value you return is often based on the value received.

Let's look at some code. Here's our good old `buttonClicks`:

```
const buttonClicks = Observable.fromEvent(button, "click");
```

The next signal values from observables based on events are an object containing information about the event.

Let's add this code to test it:

```
buttonClicks.subscribe(value => output.innerText += value + "\n");
```

<p data-height="265" data-theme-id="0" data-slug-hash="mMOeWV" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 04" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/mMOeWV/">Intro to Rx 04</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

On the event object there's a property called `target` which refers to the DOM object the event was fired by. We want to change our observable's next signal values to be the DOM object that fired the event. To do this, we use the `map` operator:

```
.map(event => { return event.target; })
```

We receive the next signal value from the observable, and simply return the `target` property on the event. Let's test this:

<p data-height="265" data-theme-id="0" data-slug-hash="prNjea" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 05" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/prNjea/">Intro to Rx 05</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

Still; this isn't very interesting information. Let's get the text of the button from the DOM object:

```
.map(event => event.target.innerText)
```

<p data-height="265" data-theme-id="0" data-slug-hash="Ojbymb" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 06" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/Ojbymb/">Intro to Rx 06</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

We can do pretty much whatever we want inside the `map` operator, as long as we return a value. We could for example split the text inside the button and return an array containing the lengths of the different words:

```
.map(event => {
  const text = event.target.innerText;
  const textParts = text.split(" ");
  let returnArray = [];
  
  for(let i = 0; i < textParts.length; i++) {
    returnArray.push(textParts[i].length);
  }

  return returnArray;
})
```

<p data-height="265" data-theme-id="0" data-slug-hash="prNjPa" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 07" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/prNjPa/">Intro to Rx 07</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

We can even disregard the value we receive entirely, and return anything we like:

```
.map(_ => "Something completely different!")
```

<p data-height="256" data-theme-id="0" data-slug-hash="GvNpMq" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 08" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/GvNpMq/">Intro to Rx 08</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

Note that we assign the value we receive to `_`. This is common practice in functional programming to signify that we don't care about the value and that we're not going to use it. Underscore is often called *discard* when used like this.


### filter

The `filter` operator takes a value and returns a boolean. If the function returns true, the value passed in will be passed on to the next operator in the chain. If the function returns false, the value passed in will **not** be passed to the next operator in the chain.

You can think of `filter` as an `if` statement. If true, continue; if false, stop.

To explain, we'll reuse the `buttonClicks` and the `map` operator we've just learned: we map each click to the current amount of milliseconds since epoch (01.01.1970), and then filter to only let signals with an even value through:

```
.map(_ => Date.now())
.filter(milliseconds => milliseconds % 2 == 0)
```

<p data-height="265" data-theme-id="0" data-slug-hash="vJyrGa" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 09" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/vJyrGa/">Intro to Rx 09</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

When you click the button, you'll only get a new value when you happen to click on an even amount of milliseconds since 01.01.1970. You may have to click a few times before getting a new value.

As with `map`, `filter` can be as simple or complex as you want it.


### combineLatest

To work with `combineLatest`, we're going to introduce a new observable:

```
const ticks = Observable.interval(1000);
```

This observable will produce a new next signal every 1000 milliseconds (e.g. every second). The value of the next signal is `0` for the first signal, `1` for second signal, `2` for the third, and so on.

<p data-height="265" data-theme-id="0" data-slug-hash="RZoJqO" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 10" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/RZoJqO/">Intro to Rx 10</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

With `combineLatest` you can combine the values of two observables. The resulting observable after `combineLatest` will produce a new signal every time either one of the two original observable produce a signal. The value of the next signal from `combineLatest` is an array containing the most recent value from each of the original observables:

<p data-height="265" data-theme-id="0" data-slug-hash="yoVEmX" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 11" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/yoVEmX/">Intro to Rx 11</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

As you can see, `combineLatest` will not produce any values until each of the original observables has produced at least one value. You will only start seeing a value when you click the button. The `ticks` observable have been producing new values regardless of you clicking on the button, which means the first value you see might not be `1,0`, but the most recent value produced by both observables.

`combineLatest` also supports a mapping function as the second argument. This function will receive two arguments; the two values from the array. We can then transform the two values into something more useful:

```
.combineLatest(ticks, (clickCount, tickCount) => `${clickCount} + ${tickCount} = ${clickCount + tickCount}`)
```

<p data-height="265" data-theme-id="0" data-slug-hash="rzWojX" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 12" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/rzWojX/">Intro to Rx 12</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

It is worth noting that it's preferable to supply the mapping function as an argument to `combineLatest`, instead of using a `map()` after it. There is a small performance benefit when doing this. Also, I think it expresses your intent better.


### merge

`merge` will literally merge the next signals of two observables into one observable. The resulting observable after the `merge` operator will produce the same next signals the two original observables produce, at the same time the original observables produce them.

Let's see how this works in practice. Here we combine the `ticks` and `buttonClicks` to create a new observable. This new observable will produce the same values that the two original observables produce:

```
.merge(ticks)
```

<p data-height="265" data-theme-id="0" data-slug-hash="yoVGrB" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 13" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/yoVGrB/">Intro to Rx 13</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

When `ticks` produces a new value, it will be displayed. When `buttonClicks` produces a new value, it will be displayed.


### mergeMap

This operator is actually a combination of a `map` and a `merge`, hence the name `mergeMap`. This operator was previously called, and is still called in other language implementations of Rx, `flatMap`.

The function you pass into this operator is a mapping function. You receive the next signal value as an argument and you return an observable. The observable you return will be merged into original observable.

For each of the next signal values you receive in the `mergeMap` you will produce a new observable. This new observable will produce entirely new next signal values. The resulting observable after the `mergeMap` will produce next signals when all of the observables returned inside the `mergeMap` produces next signals.

Let's remove the `ticks` observable and just focus on the `buttonClicks` observable and it's `map`:

```
buttonClicks
  .map(_ =>  {
    count++;
    return count;
  })
```

This observable will produce the values `1`, `2`, `3`, etc. for each button click.

Next, we're going to create a new interval observable based on the values produced by the `buttonClicks.map()` observable:

```
.mergeMap(clickCount => // 1
          Observable.interval(clickCount * 1000) // 2
          .map(value => `${clickCount * 1000}-interval has ticked ${value + 1} times`)) // 3
```

Let's go through this code:

1. We receive the clickCount from the `buttonClicks.map()` observable.
2. We create an interval observable which will tick every number of seconds equal to `clickCount` (if `clickCount` is `3`, it will tick every 3 seconds).
3. We then change the interval observable next signal value to a string telling us how many times that specific interval observable has ticked (remember that the interval observable starts at `0`, which is why we have to add `1`: `value + 1`).

This `Observable.interval().map()` observable will then be merged into the original observable - the original observable being the `buttonClicks.map()` observable.

Again, for each button click, `count` is incremented by `1` and passed to the `mergeMap`. `mergeMap` then produces an `Observable.interval().map()` observable which will produce a new next signal every number of seconds equal to the `clickCount`. The `Observable.interval().map()` observable is then merged into the `buttonClicks` observable.

All `Observable.interval().map()` observables ever produced inside the `mergeMap` (as a result of a next signal from `buttonClicks`) will be merged together. The resulting observable after the `mergeMap` will produce next signals every time one of the merged observables produces a next signal.

I'm repeating myself multiple times here on purpose. Getting a grasp on `mergeMap` is a very essential part of programming in Rx. If you manage to come up with solutions using `mergeMap` on your own, you've probably started to finally "get it"&trade;.

`mergeMap` is one of the toughest operators to "get". Partly because it's sometimes difficult to wrap your head around these "higher order" operators, but also because you always encounter it early on when learning Rx.

There are more operators that behave similarly to `mergeMap`, but you usually don't start using those before you've started "getting" Rx.

Having worked yourself through the whole `mergeMap` section, I'd encourage you to go through it once more to see if you understand it better the second time. Knowing what you now know, might help you understand the first text better.


### switchMap

`switchMap` is very similar to `mergeMap`: it takes a function which receives the next signal value as an argument and returns an observable.

The difference is that instead of merging all the observables produced inside it, `switchMap` will only produce next signals when the most recent observable returned produces signals. The second most recent observable will be unsubscribed to and discarded.

If we look at the same code as we did with `mergeMap`, but switch out the `mergeMap`:

```
.switchMap(clickCount => 
          Observable.interval(clickCount * 1000)
          .map(value => `${clickCount * 1000}-interval has ticked ${value + 1} times`))
```

<p data-height="265" data-theme-id="0" data-slug-hash="ZJBgYV" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 15" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/ZJBgYV/">Intro to Rx 15</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

The observable after the `switchMap` will only produce next signals when the most recent observable returned inside it produces a next signal. This is in contrast to `mergeMap` which produces a next signal every time *all* of the observables returned inside it produce a next signal.

In other words: every time you click the button, `switchMap` will call `unsubscribe()` on the previous observable it returned and only produce next signals when the new observable produces next signals.


### take

This operators will let you limit an observable to a maximum number of next signals. This is useful if you're working with an observable which never completes (like `Observable.fromEvent()`), but you only want a specific number of next signals.

When the limit is reached, `take` will unsubscribe to the observable it is called on, but the `subscribe()` will receive a complete signal from `take`.

Let's say we want to start an interval observable when the button is clicked and hide the button. We're only interested in *one* next signal from the button, so we use `take` to limit it. This will make sure that the subscription to `buttonClicks` is cleaned up when we don't need it anymore.

We then use a `switchMap` to subscribe to the single interval observable. Even though the `take` will cause `buttonClicks` to send a complete signal, the observable we return inside `switchMap` will continue to produce values and therefore keep the observable alive:

```
buttonClicks
  .take(1)
  .switchMap(_ => {
    button.style.opacity = 0;
    return Observable.interval(1000);
  })
```

<p data-height="265" data-theme-id="0" data-slug-hash="ZJLENq" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 16" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/ZJLENq/">Intro to Rx 16</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

### catch

If an exception is raised inside an observable, you'll be notified in the error handler inside your `subscribe()`. Sometimes though you'd rather want a next signal with an empty or default value instead.

`catch` takes a function which receives the exception and it returns an observable. The returned observable will **replace** the original observable.

The original observable has sent an error signal, which means it will not be able to send any more signals. `catch` will stop that error signal and pass it into the function. It then merges the returned observable into the original observable. In the subscribe it will look like the original observable stopped producing next signals and that the returned observable took over.

To explain `catch` we're going to use trusty old `buttonClicks` and `Observable.from()`:

```
const buttonClicks = Observable.fromEvent(button, "click");
const numbers = Observable.from([1, 2, 3, 4]);
```

We're then going to raise an exception on purpose inside a `map`:

```
.map(_ => {
  count++;
  
  if(count === 3) {
    throw "Count is 3!";
  }
  return count;
})
.catch(error => numbers)
```

<p data-height="265" data-theme-id="0" data-slug-hash="oeBXZE" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 17" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/oeBXZE/">Intro to Rx 17</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

When we click the button for the third time, an exception is raised. The `catch` will stop the error signal and replace it with the `numbers` observable.

You'll notice that the button stops working after the exception has been raised. This is because `buttonClicks` has produced an error signal. This is in keeping with the guarantees of an observable: when an error signal has been produced, no further signals will be produced.

This is seldom what you want, so what you do is use `catch` on an observable you return inside a `mergeMap` or a `switchMap`. When that returned observable raises an exception, `catch` will stop the error signal from merging into the original observable, and instead merge the observable that is returned inside the `catch`.

In the following example we return an interval observable inside a `switchMap`. The interval observable produces the same number of values as the number of clicks on the button. If the button has been clicked three times, the interval observable produces three values.

If the interval observable produces four values (`0`, `1`, `2`, `3`), the `map` will raise an exception when it sees the `3`. This makes the interval observable send an error signal, which `catch` stops and replaces with the `numbers` observable.

```
.switchMap(clickCount => 
          Observable.interval(100)
            .take(clickCount)
            .map(value => {
              if(value === 3) {
                throw "Value is 3!";
              }
              return value;
            })
            .catch(error => numbers))
```

<p data-height="265" data-theme-id="0" data-slug-hash="JyEYKm" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="Intro to Rx 18" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/JyEYKm/">Intro to Rx 18</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js">
</script>

# Conclusion

If you've read the whole thing through: Congratulations! 🎉

Getting through this considerable wall of text when you're getting started with Rx is tough. I sincerely hope you've gained some understanding of how to use Rx. I didn't want to get too technical and rather get you in a mindset where you can start to reason about how to use Rx in practice.

This intro won't show the solutions, but it (hopefully) shows how to use Rx to reach a solution.

I'm going to leave you with a suggestion:

**Read through the whole thing one more time. Maybe wait a few days - and read it again.**

This might seem a bit demotivating, but as I mentioned in the section on `mergeMap`: going back and reading the first parts with the new knowledge you now know, may help you understand those parts better; which again leads to understanding the last parts better.

If you still have any sort of question, feel free to ask in the comments or on [Twitter](https://twitter.com/jonstodle).

Happy coding!
