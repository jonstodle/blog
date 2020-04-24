+++
title = "To Split or Not to Split"
date = 2019-10-17T18:43:37.951Z
[taxonomies]
tags = ["commentary"]
+++
Some people encourage smaller functions rather than larger ones. "If a function does more than one thing, break it into smaller ones", they say. Others say that breaking functions into smaller functions like that reduces readability. "Keep everything in one place if it belongs together. Don't make me jump around the code to find out what's happening".

Personally, I'm leaning towards the first opinion. I feel calling smaller helper functions inside a larger one assists readability. The larger function describes the overall process, while the smaller ones abstract away the nitty gritty of each task, making the whole flow easier to read.

I think there's an important point to make about these helper functions: **I always keep them pure**. I don't want these helper functions to modify local state of any kind. They take some kind of input and return a result. That's it.

I think some of the people who dislike splitting up functions would let helper functions mutate state, which I agree would create a lot of mess. By keeping the helper functions pure, I think this confusion is avoided.

Just a thought.

Happy coding!
