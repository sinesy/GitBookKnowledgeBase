# How to open manually a window from another window

Starting from an already opened window, it is possible to open another window. In order to do that, you have first to retrieve the window id, which can be found by opening the windows list, where the id for each window is showed. Once retrieved it, create a javascript type action and link it to a specific event, such as button aded to a grid/form toolbar or a double click of a grid included in the parent window. The simplest way to open a window is by adding the following snippet to the js action:

```javascript
var args = new Object();

openWindowXXX(args);
```

The window to open is identified by the XXX number, which is the window id found inthe windows list. If the window to open requires specific parameters, these must be provided through the args js object. For instance, if this action is linked to a grid event such as a row click or double click, then a "vo" variable is available: that js object represents the data of the current selected row in grid. If you need to pass forward part of this data, yuo can do it through the "args" object.

## Example

```javascript
var args = new Object();

args.parY = vo.parY;

openWindowXXX(args);
```

Suppose you need to pass forward a reference to the parent window of a panel within it, such as a grid, in order toforce its reloading when an updating is carried out in the child window. You cannot pass forward directly the window or grid reference: this is not allowed, since allowed parameters can be basic data only, such as text, numbers or js data objects. A parent grid reference can be passed forward using its panel id, which is a text valuue.

## Example

```javascript
var args = new Object();

args.parentGridId = gridZZZ.id;

openWindowXXX(args);
```

That grid id can then be used to fetch the grid panel, using the Ext JS API:

```javascript
var gridPanelId = args.parentGridId;

var parentGrid = Ext.ComponentMgr.get(gridPanelId);

parentGrid.store.reload();
```

