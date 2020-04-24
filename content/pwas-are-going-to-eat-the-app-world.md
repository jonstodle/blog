+++
date = "2018-04-15T10:00:00.0000000+00:00"
tags = ["Commentary","PWA","WASM","Electron"]
title = "PWAs are going to eat the (app) world"
+++
There's been a lot of talk around Progressive Web Apps (or PWAs) for a time now. Some people are condemning them and don't want to give the browser even more access to data and sensors, while others are heralding the end of the native apps. While these are extremes, I think there are some interesting ramifications that the PWAs are bringing with them.

PWAs are going to be versatile enough and robust enough that they're going to supplant some of the native apps you might have on your phone (or computer) today.

While some apps will no doubt have a benefit by running directly on the metal of your phone or computing device, a lot of apps don't really need all that computing power. These apps can be swapped for PWAs. And with PWAs having the ability to be added to your homescreen or desktop as if it's a regular app, there's going to be no difference for a lot of users.

If you want a good example of a PWA at work, check out mobile.twitter.com.

Users are going to have the benefit of being able to try a PWA before installing it - just navigate to it's location on the web. If they want to install the app, they can add it to their home screen. Another great benefit is that PWAs (and single page applications) are naturally inclined to only download parts of the application at a time - no need for a large initial download before even using the app. When the app is "installed" by adding it to the home screen, the rest of the application can be downloaded in the background.

One can't talk about web development without mentioning Javascript. A lot of native developers are very sceptical to Javascript. While there have been built massive applications with Javascript, there's still a stigma that it's not a good language to build large, heavy apps in.

This is where Web Assembly (or WASM for short) is going to make a difference. WASM let's developers use almost any language and compile it down to WASM. The compiled WASM code will run in the browser with far superior performance to Javascript.

With the ease of install of PWAs and high performance of WASM, I think we're also going to see some Electron apps moving to be PWAs. Some Electron apps genuinely need the higher level of system access Electron (and nodejs) gives them, but some Electron apps are mostly Electron apps because a "native version" of a web app is desired. And apps in this last category are going to make the switch.

I don't think Adam Pedley was far off when he recently said that PWAs are going to make up 70% of apps in the future. I believe it's less work to maintain a PWA across multiple browsers, than it is to maintain a native app across multiple operating systems. And that's going to make a lot of developers jump on-board.
