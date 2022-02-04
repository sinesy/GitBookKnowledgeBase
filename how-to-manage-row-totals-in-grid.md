# How to manage row totals in grid

It is possible to add a row containing totals for each column, anchored at the bottom part of the grid. When scrolling grid, this row totals is still visible and locked to the bottom side.

In order to activate this row total, go to the grid definition panel and select the **"row totals" check box**. After doing it, a client-side javascript action is created and linked to the **"Update summary row" grid even**t.

This action will be automatically invoked every time the data model changes, e.g. when reloading it.

When reloading a grid, the action is invoked for each column twice: when the model is cleared up and when the model has been completely loaded.

This action receives as input several js objects:

* **gridxxx** - current grid, generating this event
* **colAttr -** attribute name of the current column: this action is invoked for each column defined in the grid
* **total** - a numeric value, corresponding to the current total for the current column
* **params** - a javascript object containing a few attributes
  * **css** - css class name that can be applied for the current column
  * **id -** column index for the current column
  * **style -** style for the current column
  * **attr\_**'ext:qtip - "Total no. of companies"' tooltip example
* &#x20;**record.data**: data to show in the last row
* **formattedValue**: current total, reformatted with the same format of the values in the column
* **col -** current column objects&#x20;

The action should returns a numeric value (or string value) to show in the cell (in the totals row) related to the current column. If no return is provided, the cell remains empty.

A typical scenario is:

```javascript
return formattedValue;
```

In this way, every column would provide a numeric value, even text type columns.

In order to limit the totals only for numeric type columns, the action could contain something like:

```javascript
if (params.style.align=="right") // numeric type columns have a right alignment...
  return formattedValue;
```

## Settings colors

It is also possible to dynamically set a foreground/background color to every cell, through the "params" js object, containing the "css" attribute, working at cell level.

If you have previously define a CSS class and save it in the \<webcontext>/css/xtheme.css file, you can refer it in this class.

For example, suppose to define a CSS class named "red", whose purpose is to set a red background color. You could test the current value for a column and set a red color for cells having a negative value:

```javascript
if (total<0) {
  params.css = "red"; // the CSS class name "red" must be defined in the xtheme.css file
}
return formattedValue;
```

## Settings totals reckoned on the server-side

Please note that these totals are calculated based on data loaded in grid. That means the grid should be loaded completely, otherwise what shown only represents a partial total, related to the portion of data loaded in grid.

However, it is not recommended to load all data in grid, especially if the result set is composed on hundreds or more records. So a grid should always be loaded partially.

A consequence of that is the need to fetch "real" totals from the server-side. A way to do it is used a utility js function provided by Platform (available since 5.3.2 version). This method will invoke a server-side js action which has to reckon all required totals and then get them back to the js function, in order to populate the totals row.

This utility function has the following syntax:

```javascript
summaryRow(grid,actionId,reqParams,colAttr,callback)
```

Required arguments are:

* **grid** - the current, containing the total row
* **actionId** - the id for the server-side action returning totals, one attribute for each column having a total
* **reqParams** - optional parameters to pass forward to the server-side action; can be set to null
* **callback** - js function invoked for each attribute; arguments passed to the function: callback(colAttr,num,params)

An example of usage is as follows:

```javascript
return summaryRow(
    gridxx,
    xyz, // actionId for the server-side js action
    {}, // req params
    colAttr,
    function(num) {
        if (colAttr=="docTotal")
          return num;
        else if (colAttr=="customerCredit") {
            if (num<0)
              params.css = "red";
            return num;
        }
    }
);
```

This function do all the job behind the scenes:

* invokes the server-side js action once
* gets back the js object and calls multiple times the callback

**The constraint to respect for the server-side js action is to provide a js object as a return value, containing as many attributes as the ones defined for the columns of the grid**: each attribute identifies a column in grid and provides the total for that column:

```javascript
var obj = {
  docTotal: 123.50,
  customerCredit: -20
};
utils.setReturnValue(JSON.stringify(obj));
```

The callback function is helpful to manage the total number for each column: it can redefine the total, format it or set an additional CSS for thast cell, dynamically.
