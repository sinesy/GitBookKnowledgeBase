# enabling/disabling checkboxes in a grid

To change the value of the chekboxes in a grid or enabling/disabling them follow this steps:

* Go in your grid.
* Move in “Column Events” section.
* Create a new event: “Before Cell Click“.
* Hook an action like this to the event:

```javascript
var currentRecord = grid.getSelectionModel().getSelected().data;
if (currentRecord.fieldToTest == 1){
 /* Don't allow edits */
 return false;
}else{
 /* Allow edits */
 return true;
}
```

Return false will prevent the natural behavior of the checkbox, making it unable to change status.

