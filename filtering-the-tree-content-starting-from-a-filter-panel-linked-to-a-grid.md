# Filtering the tree content, starting from a filter panel linked to a grid

In case the same window contains both a grid, a tree and a filter panel already connected to the grid, it is possible to filter the tree content too, starting from the same filter panel.

1. A client side javascript action must be created, containing instructions like these ones:

```javascript
treeLoaderXXX.baseParams.filterNames = filterNames;
treeLoaderXXX.baseParams.filterOps = filterOps;
treeLoaderXXX.baseParams.filterCaseSensitives = filterCaseSensitives;
treeLoaderXXX.baseParams.filterValues = filterValues;

var myMask = new Ext.LoadMask(Ext.getBody(), {
    msg : Ext.translate.Cache.translations.get("common.loadingprogress")
});
myMask.show();

var tree = getComponentByItemId('treePanelXXX',win); // &lt;-- note that this the the treePanel, NOT the treeLoader

treeLoaderXXX.load(tree.getRootNode(),function() { 
    tree.getRootNode().expand(); 
    myMask.hide();
});
```

1. Link the action to the event “when pressing the search button” of the filter panel

Notes:

* replace XXX with the right tree id
* use myMask if you want to show a loading waiting dialog over the tree, otherwise this instruction can be omitted

