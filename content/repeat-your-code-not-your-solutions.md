+++
title = "Repeat Your Code, Not Your Solutions"
date = 2020-08-05T11:30:52Z
[taxonomies]
tags = ["commentary"]
+++

We've all probably heard about the DRY principle: **D**on't **r**epeat **y**ourself. The notion that you should not copy code, but instead put it in a shared location to be used by all that need it. It may be some kind of utility function, or some logic that needs to be run multiple places in your code base.

By moving this repeating code to a shared place you save yourself from some hassle. There's only one implementation of the code, meaning it's easier to debug; no need to hunt down the specific version which suddenly does something different. This also brings us to another reason code sharing like this is nice: if you need to update the code, there's a single place you need to update. No need to remember all the places where the code is used. Sooner or later you're bound to forget one of the places the code has been copied and you end up with the wrong behavior in one obscure code path.

Testing the function or logic also becomes easier when it's extracted to it's own thing. For something that's repeated multiple times, it's probably important, so having a few tests for it is not a bad idea. Testing is infinitely easier when there's not a load extra code surrounding the code you want to test.

Except, I don't think this is the right kind of process to use for DRY: DRY is to stop you from repeating your intention, not your code. Having the same exact code multiple places in your code base is completely fine, as long as their intent is different.

Just because to pieces of code looks the same, doesn't mean they _are_ the same. It doesn't mean they solve the same problem. Say you have a function which split's a string on commas. You might use this function multiple places: one time to parse a simple CSV line, and another where you separate a list of tags which are entered as a comma separated list.

```typescript
string split on ',', trim each part and return array
```

Say this is used in two places:

```typescript
parse csv line

split comma-seperated commas
```

By reusing the same function to solve these two problems you are effectively saying that the solution to these two problems will always be the same. That's the intention your code is stating: these problems have the same solution.

 The problems it solves are quite different - one is to get the fields of a line of csv vales, while the other gets a list of tags separated by commas. While they look the same now, they might not in the future. By tying the two problems together with a shared function, you're effectively saying that the two problems will always have identical solutions and that's plainly not true.

// changes
You might think to yourself this is not a problem, you'll just split it into two different functions once they need to be different. I'd argue that's a dangerous assumption. Never trust that you're going to remember details like that 2 years later. That's even assuming that you are the one making the change. To your future self, and especially anyone else, your intention behind this code will stress that these problems have the same solution.

// split when it changes, but you forget

// make code show intention
I always argue that your code should show your intent. If there are multiple solutions to a problem, make sure the solution you chose clearly shows your intent, what you expect and want the code to do. Instead of keeping a single function for doing two different tasks, create two individual functions, each with its own name clearly describing the Internet of the function - what it's supposed to do - and you'll have an easier time with your code.
