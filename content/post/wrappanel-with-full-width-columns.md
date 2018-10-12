+++
date = "2015-10-28T11:00:00.0000000+00:00"
tags = ["WPF","UWP"]
title = "WrapPanel with full width columns"
+++
I wanted to have a grid of elements where the elements spanned the whole
available width. Basically I wanted the same result you get when you set the 
ColumnDefinitions  of a Grid to have equal width:

<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition/>
        <ColumnDefinition/>
        <ColumnDefinition/>
    </Grid.ColumnDefinitions>
</Grid>


However, I needed it to be more dynamic than a Grid. I wanted to be able to set
a number of columns and then just supply a set of items. Kind of like a GridView
, but with elements always filling the whole width.

To get this working I made a custom panel which takes a number of columns and
then arranges all the children within the available width, stretching them
equally.



I made a new panel which inherits from the Panel  class. I called it 
UniformGridWrapPanel. I made a DependencyProperty  called MaximumColumns, which
is an int that keeps track of the desired amount of columns. I then overrode the
 MeasureOverride:

protected override Size MeasureOverride(Size availableSize)
{
    // Set it to use whole available width
    var finalSize = new Size { Width = availableSize.Width };
    var columnWidth = availableSize.Width / MaximumColumns;

    var rowHeight = 0d;
    var rowChildCount = 0;
    foreach (var child in Children)
    {
        child.Measure(new Size(columnWidth, availableSize.Height));
        if (rowChildCount < MaximumColumns)
        {
            // Get the talles element in the row to make sure the next row is below all elements on this row
            rowHeight = Math.Max(child.DesiredSize.Height, rowHeight);
        }
        else
        {
            // New row
            finalSize.Height += rowHeight;
            rowHeight = child.DesiredSize.Height;
            rowChildCount = 0;
        }
        rowChildCount++;
    }

    // Add height of last row
    finalSize.Height += rowHeight;
    return finalSize;
}


I make the panel use the whole available width and the calculate the width of
the columns. I also keep track of the row height, which is the height of the
tallest element in the current row. This makes sure that the row below doesn’t
overlap the one above it.

If the child count of the current row exceeds the number of columns, I make a
new row by adding the height of the current row to the final size of the panel.
I set the new row height to be the current element (since it’s the tallest
element on the new row) and reset the child count. Last I add the final row’s
height to the final size of the panel and return it.

I did the ArrangeOverride  like so:

protected override Size ArrangeOverride(Size finalSize)
{
    var columnWidth = finalSize.Width / MaximumColumns;
    var posY = 0d;

    var rowHeight = 0d;
    var rowChildCount = 0;
    foreach (var child in Children)
    {
        if (rowChildCount >= MaximumColumns)
        {
            // New row
            rowChildCount = 0;
            posY += rowHeight;
            rowHeight = 0;
        }

        child.Arrange(new Rect(columnWidth * rowChildCount, posY, columnWidth, child.DesiredSize.Height));
        // Get the height of the row, based on the talles row child
        rowHeight = Math.Max(child.DesiredSize.Height, rowHeight);
        rowChildCount++;
    }

    return finalSize;
}


I do much the same here as in the MeasureOverride. Here I use the row child
count to multiply it by the column width: the first child has index 0, which
when multiplied will give an x coordinate of 0. The next child will be moved 1
column width to the right. Apart from that it’s much the same.

In UWP apps you can use an AdaptiveTrigger  to set the column count on the panel
based on how much room you’ve got.



The whole class can be found on GitHub
[https://gist.github.com/jonstodle/ff4621c4e78ecebe1466].

Update: Just as a warning – the panel does not use virtualization. If you put a
lot of elements inside, the performance is not going to be good.
