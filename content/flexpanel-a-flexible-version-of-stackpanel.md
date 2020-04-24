+++
title = "FlexPanel: A Flexible version of StackPanel"
date = 2016-12-05T11:00:00.0000000+00:00
[taxonomies]
tags = ["WPF","UWP"]
+++
`Panel` is a seriously underrated feature of Microsoft’s XAML platform. Panel is the base class for basically all the controls in XAML which lay out children. How the children are laid out is up to the implementation of two simple methods in the sub classes.

I’ve [previously written](/putting-dlls-into-a-single-wpf-exe) about a foray into the `Panel` class in UWP, but this time I’ve done it for a WPF app I’m building.

The problem I’m trying to solve is this: I find `Grid` very verbose and tedious to maintain. Changing how child elements should be laid out is a bit weird and it’s very unflexible if you want to make a small change after the fact.

In Xamarin.Forms there’s a StackLayout which behaves much like a regular `StackPanel` in MS-XAML, but with one nice difference: you can make it expand and use the whole length or width available. One of the children of the layout can then be set to use the left over space, making it expand as much as possible.

For those who’s expiremented with `FlexBox` in CSS might be familiar with the concept.

Lending some inspiration from both of these technologies, I’ve come up with my own WPF version. You can set one or more of the child controls to “Flex”, which will make them use the remaining space after all the other controls are done consuming space. Additionally you can set the “FlexWeight” of each control to make it use more or less of the space left, relative to the other flexing elements.

Let’s see how looks when in use:

![flexpanel_1.gif](/images/flexpanel_1.gif)

The implementation is pretty straight forward, but first let me quickly explain how a `Panel` works:

When first setting up the layout, the layout system will ask all the controls how much space they want. They’re all passed how much space is available and report back their desired size. Some controls only ask for the minimum size they need; think of how a `StackPanel` is only as long as it’s internal content. Some controls will ask for all the available space, like `Grid`.

After all the controls have reported their desired size, the layout system will ask them to arrange themselves. This time, they will be passed how much space they are actually given. This might differ from how much space was available when asked about their desired size.

This is implemented in the `Panel` sub classes through two override methods: `MeasureOverride` for reporting how much size the `Panel` wants and `ArrangeOverride` for doing the actual arranging and layout of the children.

Let’s start looking at `FlexPanel`:

```
// Attached property: Should a child control flex
public static bool GetFlex(DependencyObject obj) => (bool)obj.GetValue(FlexProperty);
public static void SetFlex(DependencyObject obj, bool value) => obj.SetValue(FlexProperty, value);
public static readonly DependencyProperty FlexProperty = DependencyProperty.RegisterAttached("Flex", typeof(bool), typeof(FlexPanel), new PropertyMetadata(false));

// Attached property: How many parts of the available space does the child control want
public static int GetFlexWeight(DependencyObject obj) => (int)obj.GetValue(FlexWeightProperty);
public static void SetFlexWeight(DependencyObject obj, int value) => obj.SetValue(FlexWeightProperty, value);
public static readonly DependencyProperty FlexWeightProperty = DependencyProperty.RegisterAttached("FlexWeight", typeof(int), typeof(FlexPanel), new PropertyMetadata(1));

// Dependency property: Which way should child elements stack
public Orientation Orientation
{
    get => (Orientation)GetValue(OrientationProperty);
    set => SetValue(OrientationProperty, value);
}
public static readonly DependencyProperty OrientationProperty = DependencyProperty.Register(nameof(Orientation), typeof(Orientation), typeof(FlexPanel), new PropertyMetadata(Orientation.Vertical));
```

I’ve first defined two attached properties. These are properties defined in the `FlexPanel` class, but are set on other controls. You’ve been using these with `Grid`'s: `Grid.Colum` and `Grid.Row`. You use these to tell the `Grid` where to put the control, but the controls themselves are not aware of these properties.

The `Flex` property tells whether the control should flex/expand to use the remaining available space. By default this is set to false making it use as little space as possible. The `FlexWeight` property tells the weight of the control. The weight is in proportion to the other flexing controls: a control with twice the weight of another control will use twice as much of the available space.

Finally I have a regular `DependencyProperty` which sets the orientation to stack the child controls. This makes the whole panel act as a `StackPanel` if none of the child controls flex.

Let’s look at the two important methods:

```
protected override Size MeasureOverride(Size availableSize) // The panel's passed the space available to it
{
    var desiredSize = new Size(); // For tracking the minimum size of the panel
    foreach (UIElement child in Children)
    {
        child.Measure(new Size(double.PositiveInfinity, double.PositiveInfinity)); // Make it report smallest desired size

        if (Orientation == Orientation.Vertical)
        {
            desiredSize.Height += child.DesiredSize.Height; // Add the child control's height to the minimum desired height
            desiredSize.Width = Math.Max(desiredSize.Width, child.DesiredSize.Width); // Check if this child control is the widest yet
        }                                                                                                                                        // and if it is, assign it the minimum desired width
        else
        {
            desiredSize.Width += child.DesiredSize.Width; // Add the child control's width to the minimum desired width
            desiredSize.Height = Math.Max(desiredSize.Height, child.DesiredSize.Height); // Check if this child control is the highest yet
        }                                                                                                                                            // and if it is, assign it the minimum desired height
    }

    if (double.IsPositiveInfinity(availableSize.Height) || double.IsPositiveInfinity(availableSize.Width)) return desiredSize; // If the available size is infinite, return the minimum size
    else return availableSize; // Return that it wants to use all the space available
}
```

We ask all the child controls to report their desired size. By passing them `double.PositiveInfinity` they’ll report the minimum size they need. This is because no control should return positive infinity if it’s passed that value.

To prevent the `FlexPanel` from returning `double.PositiveInifinity` we calculate the minimum size need to layout all the child controls in the desired orientation.

```
protected override Size ArrangeOverride(Size finalSize)
{
    var currentLength = 0d; // The offset to lay out the next control
    var totalLength = 0d; // The length used by the non-flexing controls
    var flexChildrenWeightParts = 0; // How many parts to divide the left over space into

    if (Orientation == Orientation.Vertical)
    {
        foreach (UIElement child in Children)
        {
            if (GetFlex(child)) flexChildrenWeightParts += GetFlexWeight(child); // If the control should flex, add it's weight to the number of parts to divide the remaining space into
            else totalLength += child.DesiredSize.Height; // For non-flex controls, add their height to the total length used by non-flexing controls
        }

        var flexSize = Math.Max(0, (finalSize.Height - totalLength) / flexChildrenWeightParts); // Define how large one part of the remaining size is

        foreach (UIElement child in Children)
        {
            var arrangeRect = new Rect();
            if (GetFlex(child)) arrangeRect = new Rect(0, currentLength, finalSize.Width, flexSize * GetFlexWeight(child)); // If the control should flex, make it's height the flexSize times it's FlexWeight
            else arrangeRect = new Rect(0, currentLength, finalSize.Width, child.DesiredSize.Height); // For non-flexing controls, make it only as high as it needs.

            child.Arrange(arrangeRect); // Tell the child to position itself according to the Rect
            currentLength += arrangeRect.Height; // Keep track of where to position the next control
        }
    }
    else
    {
        foreach (UIElement child in Children)
        {
            if (GetFlex(child)) flexChildrenWeightParts += GetFlexWeight(child); // If the control should flex, add it's weight to the number of parts to divide the remaining space into
            else totalLength += child.DesiredSize.Width; // For non-flex controls, add their width to the total length used by non-flexing controls
        }

        var flexSize = Math.Max(0, (finalSize.Width - totalLength) / flexChildrenWeightParts); // Define how large one part of the remaining size is

        foreach (UIElement child in Children)
        {
            var arrangeRect = new Rect();
            if (GetFlex(child)) arrangeRect = new Rect(currentLength, 0, flexSize * GetFlexWeight(child), finalSize.Height); // If the control should flex, make it's width the flexSize times it's FlexWeight
            else arrangeRect = new Rect(currentLength, 0, child.DesiredSize.Width, finalSize.Height); // For non-flexing controls, make it only as wide as it needs.

            child.Arrange(arrangeRect); // Tell the child to position itself according to the Rect
            currentLength += arrangeRect.Width; // Keep track of where to position the next control
        }
    }

    return finalSize; // Always use as much space as possible
}
```

This is where the magic happens: we start by checking if any of the child controls are supposed to flex. If so, I add their weight to the counter. If a control has a weight of “2”, it should get two parts of the total space remaining after all the non-flexing controls are laid out. If the current control is not flexing, I add it to the counter keeping track of how much space the non-flexing controls will use.

Then we finally do all the arranging of all the child controls. All non-flexing controls are assigned only the size they need in the stacking direction and as much space as is available in the direction perpendicular to the stacking direction. The flexing controls however are given their part of the total remaining space and asked to arrange themselves into that space.

By using a comibination of flexing and non-flexing controls and different weights of the flexing controls, you can get som really nice layouts.

`FlexPanel` where the yellow rectangle has `FlexWeight = 1` and the red rectangle `FlexWeight = 2`

![flexpanel_2.gif](/images/flexpanel_2.gif)

`FlexPanel` with a child `FlexPanel` which flexes and which itself has a flexing child control

![flexpanel_3.gif](/images/flexpanel_3.gif)

The whole thing is on [Github as a gist](https://gist.github.com/jonstodle/be45983fb4d597dd195c347280258e7e). Go grab if you want to use it.
