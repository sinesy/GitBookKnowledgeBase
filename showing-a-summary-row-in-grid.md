# showing a summary row in grid

In case a grid contains one or more numeric columns and there is the need to sum the numeric values of a specific column \(or more than one\), a javascript action must be defined and linked to the ‘Update summary row’ event of the grid.

That action can then report code like the following one, where formattedValue contains the value of that sum.

Note that the sum is applied to the values loaded in grid, so it should be used for grids where all the content has been loaded.

