# Filtering the grid content from a tree

In order to reload the grid content starting from the selection of a tree node, an action must be defined and linked to the tree \(to the node selection event\).

1. Create an action like this one:

```javascript
if (!node.isRoot){
   grid1239.store.baseParams.addSqlWhereClause = ' AND GEN01_CLUSTERS.FK_GEN10 = ' + record.progId; &lt;-- this is an example of an attribute to set 
   grid1239.store.load();
}else{
   /* click on the root node: any filtering condition must be removed */
   grid1239.store.baseParams.addSqlWhereClause = '';
   grid1239.store.load();
}
```

1. Link the action to the “node selected” event of the tree panel

Another typical task is to insert new rows into the linked grid and fill in data in new rows starting from data available in the selected tree node. In order to do that, you need to set a “default value in insert” for a cell in the grid to fill in with a value coming from the tree node. Within that cell, the default value to set can be filled with something like:

```javascript
treePanelXXX.getSelectionModel().getSelectedNode().attributes.&lt;attributeNameInTreeNode&gt;
```

