# finding the right filter panel

In case there are multiple windows having similar grids/filter panels, it is possible to reuse the same logic and define it once. In case of a filter panel, this can be found inside an action by scrolling along all the panels of a window:

```javascript
/* navigate through the window children */
var count_Items = win.items.getCount();

var filterPanelId = '';
var idItemWin = null;

/*finding components filterPanel*/
for (var i_Items = 0; i_Items &lt; count_Items; i_Items++) {
idItemWin = win.findById(win.items.items[i_Items].id);
if (idItemWin != null &amp;&amp; idItemWin.itemId != null &amp;&amp; idItemWin instanceof Tinet.form.FormPanel) {
filterPanelId = idItemWin.itemId;
/*if the right panel has been found, the loop can be terminated */
if (filterPanelId.indexOf('filterPanel')) break;
}
}


var panel=getComponentByItemId(filterPanelId,win);
```

Once the right panel has been found, the presentation logic can be applied to it.

The same steps can be applied for grids too, using instructions like these ones:

```javascript
if (filterPanelId.indexOf('filterPanel')) break; in if (filterPanelId.indexOf('grid')) break;
```

Similarly, the right grid can be identified with a loop like the following one:

```javascript
if (filterPanelId.indexOf('filterPanel') || filterPanelId.indexOf('grid')) break;
```

