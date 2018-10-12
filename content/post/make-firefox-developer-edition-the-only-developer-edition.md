+++
date = "2018-01-04T11:00:00.0000000+00:00"
tags = ["Commentary"]
title = "Make Firefox Developer Edition the only developer edition"
+++
I recently switched from using Chrome as my main browser to Firefox. I actually quite like the new version 57 of Firefox (or Firefox Quantum as it's also called).

Mozilla (the creators of Firefox) has also started releasing a *Developer Edition* of Firefox. This version is supposed to be a bit ahead of the regular release of Firefox. You'll be able to test upcoming features, whithout being on the bleeding edge. It looks like it's going to be a kind of beta version.

This has got me thinking however. Firefox Developer Edition should be the only version of Firefox that has developer features. In other words, the regular version of Firefox should not include the developer tools.

I think this should be the case for all the major browsers. Let me explain.

I've find it a bit strange that web browsers targeted to regular end users include tools to debug and inspect the code of web pages. I don't see why they would need it or what they should use it for. When anyone non-technical asks me what they are for, I just reply something akin to "just click the little X and don't worry about it, it's some nerd thing".

Regular users have to pay a prize in installation package size, update size, resource use and UI complexity because of a small minority which use and depend on these features. I don't think we need to inlcude any more complexities to the web than the user already has to contend with.

The developer tools also include features to manipulate web pages. You can edit the content, the markup, the styling, the JS code, local storage, cookies, almost anything. Most of these are more or less harmless in the hands of non-technical users and semi-harmless in the hands of users who think they what they're doing, but don't.

Most regular users will probably try to close the tools as soon as possibly when they're opened. They're only seeing a complex UI with lots of buttons and text they don't understand.

But they might also be tricked to open the developer tools by another person and run commands in the JS console. Remember, this is CLI access to all code running on the site. They might be told to run something as harmless as `document.querySelector("html").style.opacity = 0` or something much more sinister. There's a reason Facebook shows this message when opening the developer tools:

Message in the developer tools console on facebook.com

Which leads me back to my original point: why are the developer tools there anyway?

I suggest removing the developer tools from the regular version of Firefox and only inlcude them in the Developer Edition. The people who need and want the developer tools will know how to get them. Regular users don't get the extra baggage and potential risks.

Also, do you know what other piece of consumer software that also includes developer tools as part of the "regular" edition? Microsoft Office. I don't think I have to convince you that wasn't a good idea...

Let me know what you think below in the comments or on [Twitter](https://twitter.com/jonstodle).