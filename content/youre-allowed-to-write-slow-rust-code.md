+++
title = "You're Allowed To Write Slow Rust Code"
date = 2020-10-01T06:31:19Z
[taxonomies]
tags = ["rust", "commentary"]
+++

There's a thing that comes up from time to time in the Rust community: people wanting to optimize their code. Make the code run faster. Make it allocate less memory. These are worthy goals, but maybe not necessary for all projects. Just because your program is written in Rust, it doesn't have to be optimized to the moon and back to do it's job. In a lot of cases, you'll be fine using `.clone()`.

There's a fantastic quote from a discussion on the [Rust user forums](https://users.rust-lang.org/) which I always think of when these discussions emerge:

> Just because Rust allows you to write super cool non-allocating zero-copy algorithms safely, doesn't mean every algorithm you write should be super cool, zero-copy and non-allocating.  
> -- [trentj on The Rust Programming Language Forum](https://users.rust-lang.org/t/feeling-rust-is-so-difficult/29962/15)

Of course there's a time and place for *super cool no-allocating zero-copy algorithms*, but very often it's not the time, nor the place. You can write fantastically effective code with Rust, but **you don't have to**! A lot of the time it's entirely fine to just write code that works with minimal effort. You don't have to spend 4 days trying to figure out how lifetimes work just to avoid calling `.clone()` a few times.

Don't spend time trying to make micro optimizations now. Take the easy route. You'll probably come back to that piece of code in a few months time, smile, and change that `.clone()` to something more efficient.

Happy coding!

<small>NB: Watch this [video by Jon Gjengset](https://youtu.be/rAl-9HwD858) if you want a good, pragmatic walk through of lifetimes in Rust.</small>
