# How to open manually a popup window

If you want open a popup window you can set the window in popup mode

![](.gitbook/assets/image%20%285%29.png)

Now you can associated a javascript action to a button for open the window. Action example:

```text
var args = new Object();
args.parentComponentId = comp.id; //id of button
openWindow159(args);
```

The window opens next to the button in an automatically calculated position.  
If you want specify the position:

```text
var args = new Object();
args.parentComponentId = comp.id; //id of button
args.positionx = 100; //optional
args.positiony = 50; //optional
openWindow159(args);
```

You can open the popup window from any component.

