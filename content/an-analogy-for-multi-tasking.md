+++
title = "An Analogy for Multi Tasking"
date = 2020-09-15T13:44:02Z
[taxonomies]
tags = ["tips", "commentary"]
+++

A lot of times the words *concurrency* and *parallelism* are used interchangeably or used incorrectly. I'd like to present this analogy as a way to understand the difference.

# Synchronous Work

Let's start with the most straight forward term: synchronous. In programming this means that work is done in a sequential order, no working on multiple tasks at once. In the context of code, this means each line is executed after the one before it (disregarding loops, and what not).

Imagine a carpenter making a simple wooden chair: They saw and cut the different pieces, sand them down, paint them, wait for the paint to dry, then assembles the pieces, creating a finished product.

All of this is done in sequence, one after the other. Even waiting for the paint to dry is done in sequence, meaning that no other actions are done while waiting - not even working on other chairs.

# Concurrency

Concurrency is a sort of time share. Instead of waiting for a process to resume work, we'll do something else in the meantime. Why sit around waiting for a mouse click from the user while there's other work to do? We can get back to handling mouse clicks once there are any mouse clicks to handle.

To continue the analogy with the carpenter: This time when the carpenter makes the chair the steps are the same, but multiple chairs are made at the same time. After sawing, sanding and painting the wooden pieces, instead of waiting idly while the paint is drying the carpenter starts working on a new chair.

When there's time to spare, and there's work to do, we can do that *concurrently*.

# Parallelism

Parallelism only became a thing when CPUs gained multiple cores. Now computers were truly able to do multiple things at the same time. Instead of jumping back and forth between tasks when there are a bit of spare time, parallelism is doing multiple things at the exact same moment.

This time, the carpenter has been cloned. There are now multiple copies of the carpenter, all making chairs at the same time. They saw, sand, paint, wait and assemble at the same time.

Remember that each individual carpenter is still capable of working concurrently. They don't need to wait idly while the paint dries. Each carpenter works *concurrently,* while working in *parallel* with the other carpenters.

---

Hope this helps clearing the terms up a bit.

Happy coding!
