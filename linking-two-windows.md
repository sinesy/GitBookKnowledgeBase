# Linking two windows

In order to open a window from another window, a javascript action must be defined and linked to an event of the first window. That action can contain a js instruction openWindowXXX in order to open the second window, where XXX is the window identifier. This method requires also an argument which can be js object where additional values can be set into, as in this example

```javascript
var args = new Object();

args.parY = vo.parY;

openWindowXXX(args);
```

For instance, if the two windows contain both a grid and the grid contained in the second window should be filtered according to the selected row in the first one, am action can be defined to open the second window and linked to the “double click” event of the first grid. In that case, the action would contain something like:

```javascript
var args = new Object();
args.idElaborazioni = vo.idElaborazioni;
openWindow679(args);
```

In the second window, a new input parameter has to be defined for the window:

