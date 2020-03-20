# How to get or set a value from the graphics control

4WS.Platform provides a utility method to retrieve a reference to a detail form panel or a filter panel:

```javascript
var filterPanel = getComponentByItemId('filterPanelXXX',win);
var formPanel = getComponentByItemId('formPanelYYY,win);
```

4WS.Platform provides a utility method to retrieve a reference to a graphics control, starting from a form/filter panel reference:

```javascript
var control = filterPanel.getForm().findField("attributeName")
var control = formPanel.getForm().findField("attributeName")
```

where attributeName identify the input control and it is expressed in the "camel" format \(see the detail form window to check out which is the name of an control\).

Starting from that control, you can:

* access to its value through the method getValue\(\).
* change its value through the method setValue\(valueToSet\).

In a single instruction:

```javascript
getComponentByItemId('formPanelYYY', win).getForm().findField('attributeName').getValue();
```

Note: this instruction will work only if the specified input control has been declared visibile, otherwise the instruction will fire an exception, due to the command findField which will return null. By and large, it is always possible to use this instruction, which will work on the data model, rather than the current content of the input control:

```javascript
getComponentByItemId('formPanelYYY',win).getForm().record.attributeName
```

A few shortcuts for very common scenarios:

```javascript
var field = getFieldByName(win,formItemId,name)
```

Find a field, starting from the containing window and the identifier \(itemId\) of the form \(form panel or filter panel\). Arguments:

* win window reference
* formItemId FormPanel or FilterPanel identifier \(itemId\)
* name name that identifies the required field

Returns the field contained in FormPanel/Filter that has the specified itemId or null if it doesn’t exist

```javascript
setFieldValueByName(win,formItemId,name,value);
```

Set a value into the specified field. Arguments:

* win window reference
* formItemId FormPanel or FilterPanel identifier \(itemId\)
* name name that identifies the required field
* value value to set

```javascript
var value = getFieldValueByName(win,formItemId,name);
```

Get a value into the specified field. Arguments:

* win window reference
* formItemId FormPanel or FilterPanel identifier \(itemId\)
* name name that identifies the required field

