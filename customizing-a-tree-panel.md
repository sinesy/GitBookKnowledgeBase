# Customizing a Tree Panel

A Tree panel can be fill in with a single business component or through a series of business components, one for each tree level.

It is possible to link a "Node expanded" event to a tree and use this event to customize the tree content, in terms of:

* which sub-tree nodes are leaves
* icon and by and large CSS formats for nodes

The javascript callback invoked by Platform through the "Node expanded" event has the following signature:

```
function(comp,record,rowIndex,node) {
}
```

where

* **comp** is the Tree Panel component
* **record** is the current expanded node content (not the node, the data coming from the server, linked to that node)
* **rowIndex** is the current node index, starting from the root node
* **node** is the current expanded node

The last object is the most helpful, since it contains methods which can be used for a variety of different usages:

* **node.childNodes** - a javascript Array containing the nodes retrieved from the server and already added to the current expanded node; you can scroll through them and change some of their properties, if needed; for example the CSS to add to them of whether they are leaves or not
* **int getDepth()** - the current node depth (starting from 0, the root node) - helpful to figure out which children nodes are leaves
* **attribute** - contains the record binded to the current node, helpful along with the depth to figure out if the children nodes are leaves
* **setLeaf(boolean isLeaf)** - used to mark the node as a leaf
* **setCls(String css)** - a method used to add a CSS class to the node; through it you can for example override the default rendering of the node (size, background, icon, etc.)
* **setIconCls(String css)** - a method used to define the CSS class used for the icon of the node
* **setIcon(String imageName)** - a method used to define the icon for the node

**Example of "Node expanded" action content, for a node having 3 levels of nodes**

```
if (node.getDepth()==2) { 
    // here I could also include additional conditions
    // in case the tree has different depths, according to the subtree:
    // a good test would be: 
    // && node.attributes["attrNameToTest"]=="MYSPECIFICSUBTREENODEVALUE"...
     
    // all children nodes are leaves: let's mark them as leaves!
    for(var i=0;i<node.childNodes.length;i++) {
        node.childNodes[i].setLeaf(true);
    }
    node.expand();
}
```

