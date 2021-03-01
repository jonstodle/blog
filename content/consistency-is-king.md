+++
title = "Consistency is King"
date = 2021-02-28
[taxonomies]
tags = ["commentary"]
+++

Code formatting and style is a subject programmers love to ~~argue over~~ discuss. Personally I think the details of the formatting and style is secondary to *consistency*. Consistency is for more important.

Whenever I work in a language, I prefer using the official style. A lot of newer langauges, like [Go](https://golang.org/cmd/gofmt/) or [Rust](https://github.com/rust-lang/rustfmt), even come with their own formatter enforcing these styles. I like when there's an official formatter, it means I don't have to consider to closely how to format my code while writing it, trusting the formatter to fix it for me.

For the languages that don't have an official style guide, like Javascript, I adore tools like [Prettier](https://prettier.io/). I install prettier in all my frontend projects, not even bothering to change the defaults. Instead of spending time considering how I want my code formatted, I trust the collective behind Prettier's defaults have some reason for the defaults being what they are. They might be silly reasons for all I know, but at least it's consistently the same silly reason.

Imagine a book changing line lengths, how it indicates dialogue and how it formats paragraphs every other page. Not impossible to read, just annoying and exhausting. Don't let your code be like this either. We spend more time reading code than writing it, let's not make it harder on ourselves.
