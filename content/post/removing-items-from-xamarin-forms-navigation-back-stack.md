+++
date = "2016-07-11T10:00:00.0000000+00:00"
tags = ["Xamarin", "Xamarin Forms"]
title = "Removing items from Xamarin.Forms navigation back stack"

+++
I’m currently hacking together a small utility/calculator app for my girlfriend and wanted to enable navigation from one page to another, but remove the page I was navigating from from the navigation back stack. Like I’ve masterly illustrated below:

![remove-from-navigation](/uploads/remove-from-navigation.png)

Navigating backwards from page 3 should send the user directly to page 1 and skip page 2 entirely.

My first instinct was to just remove an element from the back after navigating to the next page, but in Xamarin.Forms the back stack is an `IReadOnlyList` and can’t be edited.

After some googling I found a simple solution in the [Xamarin forums](https://forums.xamarin.com/discussion/comment/125273/#Comment_125273): Insert the next page before the current page in the stack by using `InsertPageBefore()` and then navigate backwards:

```
Navigation.InsertPageBefore(new Page(), Navigation.NavigationStack[Navigation.NavigationStack.Count -1]);
await Navigation.PopAsync();
```

I’m guessing there are some weird cross platform compatibility issue with removing stuff from the back stack, but here’s a simple workaround at least 🙂