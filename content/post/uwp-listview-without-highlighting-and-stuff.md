+++
date = "2015-10-27T11:00:00.0000000+00:00"
tags = ["UWP"]
title = "UWP ListView without highlighting and stuff"

+++
When designing one of my apps I wanted to use a `ListView`, but not have the items highlighted when the mouse pointer was hovering over them. You can see the highlighting below:

![listview-with-effects](/uploads/listview-with-effects.gif)

After some googling I found that the highlighting happens in the `ListViewItemPresenter`, which turns out to be pretty hidden. It’s located inside the `ControlTemplate` of an `ListViewItem`, which is the `ItemContainer` for the `ListView`. The simplest way I’ve found to disable the effect is to simply override this `ControlTemplate`:

```
<ListView>
    <ListView.ItemContainerStyle>
        <Style TargetType="ListViewItem">
            <Setter Property="Template">
                <Setter.Value>
                    <ControlTemplate>
                        <ContentPresenter/>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
        </Style>
    </ListView.ItemContainerStyle>
    <TextBlock Text="List Item" />
    ...
    <TextBlock Text="List Item" />
</ListView>
```

By switching the `ListItemViewPresenter` for a `ContentPresenter` you may have to do some styling differently, but it shouldn’t be too much of a hassle. After all the Button uses a `ContentPresenter` to show it’s contents. After the switch you’ll get something like this:

![listview-without-effects](/uploads/listview-without-effects.gif)

The only “side effect” is that if the user taps or clicks on one of the items in the list, it will scroll the whole item into view (as far as it’s possible). I don’t think this necessarily is a drawback, as it makes it easier for the user to see the whole item if (s)he wants