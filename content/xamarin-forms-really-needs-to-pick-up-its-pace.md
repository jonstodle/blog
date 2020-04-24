+++
title = "Xamarin.Forms really needs to pick up it's pace"
date = 2018-03-15T11:00:00.0000000+00:00
[taxonomies]
tags = ["Xamarin Forms","Xamarin","Commentary"]
+++
I recently tweeted this:

<blockquote
 class="twitter-tweet"><p lang="en" dir="ltr">If they 
don&#39;t show anything properly exciting regarding <a 
href="https://twitter.com/hashtag/Xamarin?src=hash&amp;ref_src=twsrc%5Etfw">#Xamarin</a>
 (Forms) at <a 
href="https://twitter.com/hashtag/Build?src=hash&amp;ref_src=twsrc%5Etfw">#Build</a>
 this year, I&#39;m probably going to have to drop it in favor of 
something more fresh. Feels a bit stagnated and <a 
href="https://twitter.com/flutterio?ref_src=twsrc%5Etfw">@flutterio</a>
 looks promising <a 
href="https://t.co/5IVhlvMvw1">https://t.co/5IVhlvMvw1</a></p>&mdash;
 Jon Stødle (@jonstodle) <a 
href="https://twitter.com/jonstodle/status/970950569669120000?ref_src=twsrc%5Etfw">March
 6, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" 
charset="utf-8"></script>

This was a comment on a tweet by [Morten Nielsen](https://mobile.twitter.com/dotMorten/status/970842655734226944), in which he stated that what Microsoft was showing their MVPs at the MPV Summit was pretty exciting. My hope is that it has something to do with Xamarin. I fear it's unlikely however, as Xamarin.Forms is open source and it would be truly bad form to spring something unexpected on the project.

I've been working with Xamarin.Forms for a few years and while I was onboard with *Native Look And Feel*, it started to sour a while back. There's just too many problems trying to make different platforms and UI stacks behave in the same fashion.

While something like Xamarin.Forms gives me the actual native widgets produces by UIKit, Android Widgets and Windows.UI, it's less and less what I actually want. What I really want is for my list view to work equally well on all platforms. Tall order? Yes. Impossible? No.

[Adam Pedley recently wrote](https://xamarinhelp.com/flutter-xamarins-next-big-competitor/) that Xamarin.Forms gives a *"Native Look And Feel. A commonly spoken phrase, that used to be the shining selling point of Xamarin … This is fast losing relevance, among Xamarin.Forms applications. Today clients are less focussed on making an app look native … they want the app to look like this on ALL platforms "*. To me this has become more and more obvious.

I've been looking into Flutter on and off for the last few months, and with the recent move to beta, it's really starting to look promising. Flutter throws native UI out the window and does it all itself. Underneath is Skia, a very powerful 2D rendering engine used by, among others, Chrome.

On top is Flutter's reimplementation of all the necessary UI components. That includes both the visual ones, like buttons, labels and text fields, but also touch events, layout and animation. Usually I'm very sceptical of this kind of approach, but it turns out it actually works. And it works well.

At the moment I'm using Xamarin at work to build our mobile app, but I'm going to keep a close eye on Flutter for a possible change in the future. In the meantime, I'm going to play around with Flutter on some personal projects.
