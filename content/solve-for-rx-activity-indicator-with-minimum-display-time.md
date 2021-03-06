+++
title = "Solve for Rx: Activity indicator with minimum display time"
date = 2017-11-15T11:00:00.0000000+00:00
[taxonomies]
tags = ["Reactive Extensions","RxJS","Solve For Rx"]
+++
# Solve for Rx

I'm going to start a recurring series here on my blog called *Solve for Rx* in which I solve different problems using Rx. I think this will not only help me push my skills, but I also believe that others will find it helpful to see some practical examples other than a type-ahead search box example.

If you have any suggestions on problems to solve with Rx, I'd love to hear them! Leave a comment below or contact me on [Twitter](http://twitter.com/jonstodle).

# Activity indicator with minimum display time

Imagine you've made a fancy activity indicator for your app. You're going to show it every time the user has to wait for something to happen. During a web request perhaps. I've made a simple one like this:

![Pinwheel](/images/pinwheel.gif)

## First implementation

In this first implementation it simulates a web request when you click on the load button. Right now the response time of the simulated web request is half a second. The animation for the activity indicator takes 2 seconds to complete.

While it means I don't get to show my awesome animation, it also means that the user might not catch the fact that the action has completed - or even started in the first place. Sometimes it feels like a job is only properly done when you see it taking a little while.

    const Observable = Rx.Observable;
    const button = document.querySelector('#button');
    const pinwheel = document.querySelector('#pinwheel');
    function makeWebRequest() { // 1
        return Observable.timer(500);
    }

    const buttonClick$ = Observable.fromEvent(button, 'click').share(); // 2
    const webRequest$ = buttonClick$.switchMap(_ => makeWebRequest()).share(); // 3

    // Simulate handling web requests
    webRequest$.subscribe(); // 4

    buttonClick$
        .map(_ => true) // 5
        .merge(webRequest$.map(_ => false)) // 6
        .subscribe(spin => { // 7
            if (spin) {
                pinwheel.classList.add('spin');
            } else {
                pinwheel.classList.remove('spin');
            }
        });

<details>
  <summary>See the example on Codepen</summary>

<p data-height="375" data-theme-id="0" data-slug-hash="eGPPwZ" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="blog-50" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/eGPPwZ/">blog-50</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

</details>

1. This function simulates a web request by emitting a signal after 500 ms (this is a fast network).
1. This will emit a signal every time the button is clicked. The observable is shared between all the subscribers.
1. This will emit a signal every time the web request has returned. `switchMap` makes sure it only subscribes to the most recent request. `share` makes sure that the request isn't done once per subscriber, but shared among all subscribers.
1. This would usually be where you handle the web requests: store the data and/or update the UI. Here it's empty as it's not really relevant.
1. We map each button click to `true`. A `true` value means the activity indicator should be displayed.
1. We merge the `true`s from the button clicks with the `false`s from when the web requests are returned.
1. We add a class called `spin` if we receive `true` to make the pinwheel spin, and remove the class if we receive `false`

The result of the short response time is this:

![Pinwheel short](/images/pinwheel-short.gif)

## Fixing the implementation

To fix this we want the animation to display for at least 2 seconds before it stops. The code below is the same until we get to the numbered comments:

    const Observable = Rx.Observable;
    const button = document.querySelector('#button');
    const pinwheel = document.querySelector('#pinwheel');
    function makeWebRequest() {
        return Observable.timer(500);
    }

    const buttonClick$ = Observable.fromEvent(button, 'click').share();
    const webRequest$ = buttonClick$.switchMap(_ => makeWebRequest()).share();

    // Simulate handling web requests
    webRequest$.subscribe();

    buttonClick$
        .switchMap(_ => Observable.combineLatest( // 1
            webRequest$.take(1), Observable.timer(2000), // 2
            (_, __) => false) // 3
            .startWith(true)) // 4
        .subscribe(spin => {
            if (spin) {
                pinwheel.classList.add('spin');
            } else {
                pinwheel.classList.remove('spin');
            }
        });
        
<details>
    <summary>View on Codepen</summary>
    
<p data-height="379" data-theme-id="0" data-slug-hash="YrRjqa" data-default-tab="js,result" data-user="jonstodle" data-embed-version="2" data-pen-title="blog-50-2" class="codepen">See the Pen <a href="https://codepen.io/jonstodle/pen/YrRjqa/">blog-50-2</a> by Jon Stødle (<a href="https://codepen.io/jonstodle">@jonstodle</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

</details>

1. At every button click we create a new observable, and we only want to subscribe to the most recently returned.
1. We combine the result of the web request with a timer which will emit a signal after 2 seconds (the length of the animation). We also use `take(1)` to make sure we only subscribe to `webRequest$` for as long as is strictly needed.
1. `combineLatest` will only emit a signal when each of the observables (`webRequest$` and `Observable.timer(2000)`) has emitted at least one signal each. When they've both returned a signal, we'll ignore those values and return `false` instead; this is to stop the spinning (as before)
1. We make the observable emit a `true` signal immediately when it's subscribed to.

The new result is this:

![Pinwheel](/images/pinwheel.gif)

---

As mentioned this technique will work both for waiting for animations to finish, and to make sure a loading animation is show a minimum amount of time. The latter can be really useful for users as they have time to notice that the load has happened.

Happy coding!
