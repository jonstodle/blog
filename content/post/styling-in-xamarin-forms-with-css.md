+++
date = "2017-12-25T11:00:00.0000000+00:00"
tags = ["Xamarin Forms","CSS"]
title = "Styling in Xamarin.Forms with CSS"
+++
Less than a week ago, CSS support was merged into the master  branch of
Xamarin.Forms. I've seen some people wonder what the point of CSS support is and
how it may be useful. Personally I'm excited for CSS support. I want to tell you
why.

I've never really been a fan of XAML styling. Not in Xamarin.Forms, nor in WPF
or UWP. I've always thought it's too limited. A style can only be specified for
a single type of control at a time for example.

Want to specify a default margin for all child items of a StackLayout? You'll
have to create a style for each type of control inside that StackLayout.

With CSS you'll be able to define a property on a container element and have
that style "trickle down" to its children. This means you'll be able to set the
text color on a Grid  and all it's children will use that text color: Labels, 
Buttons, Editors, etc. Best of all, it can be reused on a StackLayout  later for
the same effect.

It's possible to make selections based on the parent of a control too. Say you
want the children of all Frames to have a default margin of 10. You can easily
specify this too with the following selector: Frame > *. In other words, "any
kind of control that's a direct child of a Frame.

If you want all the children of the children, and their children, and their
children, all the way down (all descendants in other words), you can specify
that too: Frame *. "All controls that are inside a Frame.

Xamarin.Forms will also support a special kind selector which is not present in
regular CSS. The ^  selector. This selector will match a class and all it's
subclasses. ^ContentPage  will match any ContentPage  and any subclass of 
ContentPage  you may have created. This means you'll be able to style of the
pages in your app in one place.

This makes building themes a lot easier. Imagine defining a style that will
change the all the included controls and pages, and  your custom controls and
pages. If you want to support multiple themes in your app, you'll be able to
swap out the loaded CSS file and completely change the way the app looks.

I think CSS will help Xamarin.Forms look better. It will be easier to create a
custom look for your app, which I think is something Xamarin.Forms really needs.
As theming has been a bit difficult in the past, a lot of Xamarin.Forms apps
look alike. While it's not a bad thing that they look like the native apps on
ther respective platfomrs, it's often desirable to have a color scheme in the
app that complements a brand or have a unique color scheme for the app.

I know a lot of people don't like CSS, but I personally love it. It's a simple
syntax to learn, but it has a lot of possibilities when it comes to more
advanced scenarios. The initial support for CSS won't support some of the more
advanced selectors, but I think they're not really needed for most scenarios
anyways.

If you want all the technical details you can read up on the Github issue
[https://github.com/xamarin/Xamarin.Forms/pull/1207], or Adam Pedley's write up
[https://xamarinhelp.com/css-xamarin-forms/].

Happy coding!
