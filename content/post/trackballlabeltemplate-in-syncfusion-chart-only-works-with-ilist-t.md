+++
date = "08/07/2017 10:00:00 +00:00"
tags = ["Xamarin Forms","Syncfusion"]
title = "TrackballLabelTemplate in Syncfusion chart only works with a list"
+++
After spending too many hours trying to figure this one out, I have to write it
down to help any unfortunate soul that should come across this undocumented
behavior. (Notice how I put all the important keywords in the title?)

While it's most often recommended to supply an IList<T>  when setting the 
ItemsSource  of a collection view in Xamarin.Forms or other XAML platforms, it's
not required. You can just as well supply an or IEnumerable<T>  and things will
work fine.

The benefit if passing a list is that the system can do random access of items.
In other words, it can use the mylist[42]  syntax. This is a lot more efficient
than trying to access a random item in an IEnumerable<T>.

You might think "but there's ElementAt()  on IEnumerable<T>". Yes, but that's
basically just skipping n - 1  items before accessing the desired item. 
myEnumerable.ElementAt(42)  is basically this: myEnumerable.Skip(42 - 1).First()
.

In the Xamarin.Forms documentation it actually explictly tells you to provide a
list  if you can, for performance reasons.

Back to SfChart  by Syncfusion:
Apparently there's a limitation in SfChart  when it comes to IEnumerable<T>  as
the ItemsSource: If you specify a custom TrackballLabelTemplate  for your chart,
it will be ignored if you don't use an IList<T>.

This had me stuck for quite a while.

I usually provide a list when binding to ItemsSource, but in this instance I was
using Where()  in the view code-behind to filter out some unwanted items. Since 
Where()  returns an IEnumerable<T>  things didn't work. Putting a ToList() 
after the Where()  clause fixed it all.

This wasted more hours than I want to admit to figure out...

If you stumble upon this post via Google; you're welcome :)

Happy coding!
