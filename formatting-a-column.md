# formatting a column

In case a **numeric column needs to be formatted in a specific way,** it is possible to use the “data format” setting, available in the “columns” folder of the grid panel definition window.

[![format](http://4wsplatform.org/wp-content/uploads/2015/12/format-1024x241.jpeg)](http://4wsplatform.org/wp-content/uploads/2015/12/format.jpeg)

The example reported above shows how to format a numeric column where both the decimal and thousand settings have been set, using the pattern **0,000.00**

In case of a simple **number formatted with a currency value**, the easiest way to do that with 2 decimal digits is to set the “Currency symbol”: in that case, the number will be formatted as: symbol **0,000.00**

In case a **cell must be colored/reformatted according to a specific logic**, it is possible to set font, background/foreground colors by working on the “**Renderer**” property available in the “Advanced mode” \(press the Advanced button on the grid’s toolbar\).

In this cell you can define a series of javascript instructions.

All these instructions have access to the following properties:

* value – current value that would be showed
* metaData – a javascript object containing several settings related to color, font, etc
* record – the whole record linked to the grid’s row
* rowIndex – current row index
* colIndex – current column index
* store – data store linked to the whole grid, containing data for all the rows

Starting from these properties, it is possible to change the default representation of the value passed in input and reformat it.

For instance, to change the color, you have first to the define a CSS class in your custom theme, then refer it as in the following snippet:

```javascript
if (value == 'whatever') {
  //metaData.css : String : A CSS class name to add to the TD element of the cell.
  //metaData.attr : String : An html attribute definition string to apply to
  // the data container element within the table
  // cell (e.g. 'style="color:red;"').
  metaData.css = 'name-of-css-class-you-will-define';
}
// if needed: 
// return anothervalue;
```

Another way is to explicitelly set the CSS style using the style property:

```javascript
if (value == 'whatever') {
  metaData.style = metaData.style+'background: red;';
}
// if needed: 
// return anothervalue;
```

