# How to change CSS settings for a grid row

A way to redefine the default settings for a cell in a grid is through the "renderer" property of each column.

Here you can specify CSS styles for a specific cell.

Anyway, if you need to set the same CSS styles for all cells of a row, it would not be a good idea to use the "renderer" property for every cell, since it would be a long task. A best way is to define such a setting at row level.

Row level CSS settings can be defined using the "Additional view grid" settings, available in the Grid definition panel.

Through this multiline field, you can override or extend the default properties per row.

Example:

```text
   getRowClass: function (record, rowIndex, rp, store) {
        if (record.get("rowRead")=="N")
          return "row-bold";
   }
```

In this example, a new CSS class named "row-bold" is added to every row where there is a cell named "rowRead"\(column header\) having value "N".

To complete the example, you have also to include in the xtheme.css file an entry for such a CSS class, something like:

```text
.row-bold {
  font-weight: bold;
}
```

