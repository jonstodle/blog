+++
title = "There's no such thing as over-reliance on tools"
date = 2020-07-27T17:53:15Z
[taxonomies]
tags = ["programming", "vim", "commentary"]
+++

There's something I see mentioned sometimes when discussing programming languages, and that's the reliance on tooling and IDEs. More specifically; the (perceived)  "over reliance" on tooling in some ecosystems or in some part of the community of a programming language.
`
People claiming there's an overreliance on tooling will typically claim that someone or a whole community is too reliant on a specific set of tools - or tools in general - and claim that it's a bad thing. Not necessarily because the tool is bad or not optimal, but out of principal. The fact that you need the tool in the first place is wrong.

One example of this was in a thread on Reddit where a programmer was bemoaning the fact that you "have to" use Cargo to work with Rust. Strictly speaking you don't have to, but you're going to have a better time by using Cargo, and this person took this as a bad thing. The person had the opinion that this made Rust more complex than needed and Cargo introduces an extra point of failure in the overall toolchain.

Another example that comes to mind is someone who disliked the way Go does access modifiers for structs and their fields. By uppercasing the first letter in a struct's name or the first letter in a field's name, you make the struct or field public. Otherwise it's private and not accessible to anyone outside fo the module. The person was annoyed that changing visibility (or access) to a struct or field meant that you hade to rename the references to the struct or field everywhere in your code base. Without and IDE or other editor with rename refactoring tools this is a _very_ tedious task. Go's implicit reliance on an IDE was a problem in their mind.

This whole discussion feels like a repeat to me. This sounds a bit like the people who didn't like comiled langauges because they didn't allow you manipulate memory in the same way assembly did. These people are complaining about having to use modern tooling, while claiming that Make is not tooling. They sound very much like grumpy old men complaining about today's youth.

Are they always wrong. No. But complaining about having to use IDEs feels a bit like 1989 to me.
