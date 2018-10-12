+++
date = "2017-07-08T10:00:00.0000000+00:00"
tags = ["Reactive Extensions"]
title = "FlatMap explained"
+++
I was recently helping an acquaintance out with understanding flatMap  (or 
SelectMany  as it's known in Rx.NET). After helping him set up his observables
he noted that flatMap  is almost like magic.

I remember when I was first learning Reactive Extensions that flatMap  was one
of the operators that I didn't really understand. When I used it, it was mostly
because I'd been told (or read somewhere) that's what I had to use.

In this post I'm going to take a stab a explaining how flatMap  works (from a
user perspective) and how to use it in your Rx code.

Map
First we have to talk a little bit about the map  operator: map  returns a
value. That's it's whole job.

The current value is passed in as an argument. This way the current value can be
used to influence the returned value in some way, but that's completely up to
you as the programmer.

Here are a few examples of how map  can be used:

Multiply a the current value
myObservable // This outputs 1--2--3--4--5--6--
  .map(currentValue => currentValue * 2) // Multiply by 2
  .subscribe(console.log);

OUTPUT:
2
4
6
8
10
12


On JS Bin [http://jsbin.com/buqemim/2/edit?js,console]

Convert the current value
myObservable // This outputs 1--2--3--4--5--6--
  .map(currentValue => currentValue + "") // Convert to string
  .subscribe(console.log);

OUTPUT:
"1"
"2"
"3"
"4"
"5"
"6"


On JS Bin [http://jsbin.com/kezamo/3/edit?js,console]

Convert to a constant value
myObservable // This outputs 1--2--3--4--5--6--
  .map(currentValue => "It has happened") // Always return the same value
  .subscribe(console.log);

OUPUT:
"It has happened"
"It has happened"
"It has happened"
"It has happened"
"It has happened"
"It has happened"


On JS Bin [http://jsbin.com/diwayeg/2/edit?js,console]

Merge
merge  let's you take multiple observables and merge their ouput into one
observable. To merge observables they have to output the same type of values.

If you have three buttons producing the values 1, 2 and 3 in three different
observables, you can use merge to put all the values into a single observable:

Rx.Observable.merge(
    button1Click$.map(_ => 1),
    button2Click$.map(_ => 2),
    button3Click$.map(_ => 3))
  .subscribe(console.log);

OUTPUT:
1
2
3
3
2
1


On JS Bin [http://jsbin.com/wafeguy/2/edit?html,js,console,output]

You could merge the observables of two buttons and map the clicks to +1  and -1,
making a simple counter:

Rx.Observable.merge(
    minusButtonClick$.map(_ => -1),
    plusButtonClick$.map(_ => +1))
  .startWith(0)
  .scan((aggr, curr) => aggr + curr, 0)
  .subscribe(console.log);

OUTPUT:
0
1
2
3
2
1


On JS Bin [http://jsbin.com/vomijej/edit?html,js,console,output]

FlatMap
At last we're getting to flatMap  itself. The reason we've taken the roundabout
way by map  and merge  is because flatMap  is mostly a combination of the two.

When you use flatMap, two things happen: You provide a function that returns an
observable and RxJS merges that observable with the observable the flatMap  is
part of.

buttonClick$
  .flatMap(_ => Rx.Observable.timer(1000).map(_ => "Delayed click"))
  .subscribe(console.log);

OUTPUT:
"Delayed click"
"Delayed click"
"Delayed click"
"Delayed click"
"Delayed click"
"Delayed click"


On JS Bin [http://jsbin.com/huxunel/2/edit?html,js,console,output]

flatMap  is basically just a regular map  but it returns the values of an
observable, instead of a value directly.

Whenever you feel the need to subscribe to an observable inside an observable,
you'll want flatMap.

Conclusion
I hope I've managed to clear up flatMap  a bit (and not make you more confused).
 flatMap  is a powerful tool and understanding it makes you more proficient at
Rx.

Happy coding!
