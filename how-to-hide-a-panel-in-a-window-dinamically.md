# How to hide a panel in a window dinamically

A window is composed of a set of panels. You can hide any of these panes by invoing the hide\(\) javascript method on it. This instruction can be included in a javascript action linked to a "before render" event of the window. For instance, that instruction can hide or not a specific panel according to a role assigned or not the current logged user. The following example reports such a scenario. Content of the js action:

```javascript
if (userRoles.indexOf("ROLEXXX")==-1)

gridYYY.hide();
```

where ROLEXXX is the role identifier assigned or not the user. In order to retrieve the role identifier, open the global parameters list or the application parameters list and add the parameter "Show role id" and set it to Y. Once re-logged on to the system, the roles list will show the role identifier as well.

* If you need to hide a grid, add the instruction gridYYY.hide\(\)
* If you need to hide a form panel, add the instruction formPanelYYY.hide\(\)

Where YYY is the panel identifier found in the panel list \(e.g. grid1299, formPanel119\) By and large, the panel name can be found as the last column in the panels list.

