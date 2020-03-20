# using checkboxes to select rows in grid

It is possible to add a checkbox column anchored to the left side of a grid and use it to select multiple rows, when the grid is in read only mode. In order to do that, the “Multiselection in grid” event must be used: an action must be defined and linked to that event: that action could be empty, in case no specific custom logic is required. The action is helpful in case the selection should be ignored for specific rows, according to a custom logic or in case selected rows must be stored in some way each time a row is \(de\)selected.

When the whole selected rows are needed, these can be identified within another action using a javascript instruction like this one:

```javascript
var selections = grid939.getSelectionModel().getSelections();
//selections contains all  rows selected through the check box column.
```

