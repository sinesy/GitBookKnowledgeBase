# How to manage card panels

A card panel is a special kind of panel where any number of panels can be added to it, but only one of them can be showed at a time. The first added subpanel is the one showed at the beginning. Any other subpanel can be showed and replace the previous one, only by executing a special javascript method which can be invoked from a JS action. The method is accessible from within the window and has the following signature:

```javascript
setActiveItem("cardPanelIdentifier",panelIndex);
```

where cardPanelIdentifier represents the card panel name \(e.g. customPanel123\) and panelIndex represents the position \(starting from 0\) of the subpanel to show, within the card panel container. The method setActiveItem is always available inside any window.

