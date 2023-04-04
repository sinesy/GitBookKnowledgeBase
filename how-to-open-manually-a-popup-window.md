# How to open manually a popup window

If you want to open a popup window you can set the window in popup mode

![](<.gitbook/assets/image (5) (1).png>)

Now you can associate a javascript action to a button for opening the window.&#x20;

An example of action:

```
var args = new Object();
args.parentComponentId = comp.id; //id of button
openWindow159(args);
```

The popup window will be opened next to the button in a position automatically calculated.\
If you want to specify the position:

```
var args = new Object();
args.parentComponentId = comp.id; //id of button
args.positionx = 100; //optional
args.positiony = 50; //optional
openWindow159(args);
```

You can open the popup window from any component.
