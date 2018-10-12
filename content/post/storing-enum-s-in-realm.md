+++
date = "2018-01-24T11:00:00.0000000+00:00"
tags = ["Realm"]
title = "Storing enums in Realm"
+++
Ever since I came across Realm [https://realm.io], I've loved it. It's fast,
easy to use, no hassle to set up, easy migrations. Since the time I started
using it they've added fantastic, automatic syncing with a backend. And the next
thing on the horizon is a cloud service for the backend, so you don't have to
run it yourself (yay!).

While there's a lot to love about Realm, there are of course some limitations.
One of those limitations is that it's that you're only able to store primitive
types (like int, string, DateTimeOffset, etc). This has to do with the way Realm
stores and reads data from the database.

One of the types you're not able to store are enums. But as I've mentioned,
you're able to store ints in Realm, and an enum  is like an int  in a clever
disguise.

The values of an enum  are automatically assigned an int  value starting at 0.
The first value declared is assigned 0, the second is assigned 1, the third is
assigned 2  and so on. Like indexes in an array.

Let's define an enum:

public enum Color
{
  Blue, Green, Red, Yellow
}


Then you're able to explicitly cast between enums and ints like this:

Color color = Color.Blue;
int colorInt = (int)color; // == 0
colorInt = (int)Color.Green; // == 1
color = (Color)1; // == Color.Green


As we can easily cast between Color  and int, we can also store Color  as an int 
 in Realm.

Let's say we have a Paint  class on which one of the properties is the color of
the paint:

public class Paint : RealmObject
{
  // Other code
  public int Color { get; set; }
}


That works, but it's a bit clunky having to always cast the int  whenever we
want to use it. Not to worry though. We'll rename Color  to ColorRaw  to
indicate that it stores a raw value, instead of the desired one. We'll also add
a new property named Color  which casts the int  for us:

public class Map : RealmObject
{
  // Other code
  public int ColorRaw { get; set; }
  
  public Color Color
  {
    get => (Color)ColorRaw;
    set => ColorRaw = (int)value;
  }
}


As we've specified a custom getter and setter on Color, Realm will ignore the
property and not store it in the database.

Now we'll be able to use it (mostly) as we normally would. You won't be able to
data bind to Color, for instance. You can either make it send PropertyChanged 
notification yourself, or use a converter on your binding to convert between the
 int  and a Color.


--------------------------------------------------------------------------------

Hope this little trick helps you out.

Happy coding!
