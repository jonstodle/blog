+++
title = "Experiment in a Git branch"
date = 2017-11-05T11:00:00.0000000+00:00
[taxonomies]
tags = ["Git"]
+++
When programming you often come across code you'll want to change or refactor. After some tinkering the improvement turns out to not be such a great improvement after all.

Your first instinct might be to *Ctrl+Z* all the way back to safety. If you've been committing the changes to git, you might discard the changes, revert the commit(s) containing the changes or even reset the head.

I'm here to tell you, do none of those things. Put that work in a branch instead.

Branches are cheap and they make it possible to stuff away the code and pretend it didn't happen. Should you want to go back to that code however, that's still fully possible. There might be some work in there worth salvaging.

You can even reapply those changes to your current code with a rebase of the branch. That way you can continue right where you left off.

At some point in the future you might refactor that piece of code properly and never actually need that old branch of unused code. In that case you can simply delete the branch all together.

I often find myself trying out something new, that I think is a good idea. After sinking in maybe an hour or two to the idea, I realize it's not such a good idea after all. I always put that code in a branch. Should I ever come up with a better solution, I can reuse parts of that code easily.

Happy coding!
