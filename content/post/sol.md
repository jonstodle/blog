+++
date = "01/14/2018 11:00:00 +00:00"
tags = ["Reactive Extensions","Solve For Rx","RxJS"]
title = "Solve for RxJS: Showing messages in sequence"
+++
Solve for Rx
Solve for Rx  is a series in which I solve different problems using Rx. I think
this will not only help me push my skills, but I also believe that others will
find it helpful to see some practical examples other than a type-ahead search
box example.

If you have any suggestions on problems to solve with Rx, I'd love to hear them!
Leave a comment below or contact me on Twitter [http://twitter.com/jonstodle].

Showing messages in sequence
A common feature in UIs is showing a message to the user. It might be that some
background task has finished and you want to notify the user. In some cases you
might get mulitiple messages in quick succession. What do you do in those
situations?

You might build some kind of queue where you push new messages. You display the
message immediately if there's no message being displayed. If there's a message
that's being displayed you do nothing. When a message is done being displayed
you check for new messages in the queue and display the next message. This might
get quite complex very quickly.

What I want, is something like this:



To demonstrate I've put together this simple HTML and styled it a bit:

<div id="container">
  <input type="text" id="message-input">

  <h1 id="message-container"></h1>
</div>


In the JavaScript I've made a reference to the two elements I'm interested in:

const messageInput = document.querySelector("#message-input");
const messageContainer = document.querySelector("#message-container");


I'm interested to know when the user presses the Enter  key when typing in the 
input. This will submit the current message. I create an observable for the 
keydown  event and let signals through when the Enter  key has been pressed:

Rx.Observable.fromEvent(messageInput, "keydown")
  .filter(event => event.keyCode === 13)


Next I create an observable that represents the lifetime of displaying a
message. It sets the new text inside the #message-container, displays 
#message-container, waits for a little while and then hides #message-container 
again.

I start by mapping the event into an observable which sends a single next signal 
 containing the message:

.map(event =>
  Rx.Observable.of(event.target.value)


Set the text of #message-container:

.do(message => messageContainer.innerText = message)


To display and hide #message-container, I use a CSS class that overrides 
#message-container  opacity from 0  to 1. I've also added a transition that
lasts for half a second.

.do(_ => messageContainer.classList.add("visible"))


By switchMaping to an observable that produces a signal in three seconds, I
effectively halt the "execution" of the observable chain for three seconds. This
lets the user have time to read our message:

.switchMap(_ => Rx.Observable.timer(3000))


Remove the CSS class that overrides the opacity of #message-container:

.do(_ => messageContainer.classList.remove("visible"))


Again I switchMap  to an observable that will produce a signal in the future.
While the transition only takes 500 milliseconds, I wait for 750 milliseconds to
have a little room between showing the messages:

.switchMap(_ => Rx.Observable.timer(750)))


After having mapped the event to an observable describing the lifetime of the
display of the message, I no longer need the value of #message-input:

.do(_ => messageInput.value = "")


concatAll  works on an observable that contains observables. It will subscribe
to each of the inner observables in turn. This means it won't subscribe to a new
observable until the previous one has finished:

.concatAll()


As each of our inner observables describe the entire lifecycle of displaying a
single message and concatAll  will not subscribe to a new observable until the
previous one has finished, message will be displayed in sequence after each
other.

At the end I subscribe to make the whole thing work:

.subscribe();


You can see it in action in this CodePen:

See the Pen Showing messages in sequence 01
[https://codepen.io/jonstodle/pen/KZaZqJ/]  by Jon Stødle (@jonstodle
[https://codepen.io/jonstodle]) on CodePen [https://codepen.io].

Taking it a step further
Wouldn't it be nice if it was possible to dismiss messages by clicking them?
Something like this:



After the switchMap  that waits for three seconds I add a takeUntil  which will
make the observable finish when I click #message-container:

.switchMap(message => Rx.Observable.timer(3000).map(_ => message))
.takeUntil(Rx.Observable.fromEvent(messageContainer, "click"))


I put the part that removes the CSS class and waits for the message to hide
inside a concat:

.concat(Rx.Observable.of(null)
    .do(_ => messageContainer.classList.remove("visible"))
    .switchMap(_ => Rx.Observable.timer(750))))


concat  will append the given observable to observable concat  is called on.
When the first observable finishes, the appended one starts. To the subscriber
it looks like one observable.

This concat  makes sure the hiding of #message-container  is done whether the
three second timer finishes normally, or it finishes early by takeUntil  firing.

You can see it in action in this CodePen:

See the Pen Showing messages in sequence DONE
[https://codepen.io/jonstodle/pen/wpgpQQ/]  by Jon Stødle (@jonstodle
[https://codepen.io/jonstodle]) on CodePen [https://codepen.io].


--------------------------------------------------------------------------------

Happy coding!
