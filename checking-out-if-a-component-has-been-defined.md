# checking out if a component has been defined

In case the same action is reused for different filters/grids, it is essential to figure out if a specific panel is defined in the current window where the action is applied. Such a test can be carried out with a snippet like this one:

```javascript
var grid=null;
/* Check if the grid is defined */
if (typeof (gridxxx) !== typeof(undefined)){
    grid = gridxxx
}else{
    /* Grid isn't defined. Terminate action */
    return false:
}

/* If this part of code is reached then the grid is defined and can be used */
grid. ...
```

Also check this page for more info about undefined check.

