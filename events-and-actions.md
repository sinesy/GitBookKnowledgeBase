# Events and Actions

According to the MVC paradigm, the data model and the views must be decoupled: this philosophy has been respected on the whole product. Between model and view, a controller should be included, in order to manage operations to apply on the views and to listen to events coming from the views. Actions in 4WS.Platform represent the controller: they are invoked by events fired by views \(a grid, a form, a tree, a column, a control, a filter control, a tree node, a button\) and they perform a presentation logic on the GUI or a business logic on the server side. Actions have a format:

* JavaScript– in this case the code will be executed on the GUI
* SQL – in this case the code will be run on the server side; it can include more than one SQL statement, separated by ";"
* Web service – the web service is expressed as an URL which can include variables \(:VARNAME\); this web service will be always called on the server layer, so there will not be any limit related the host to invoke.

By means of actions, it is possible to enhance and customize the standard behavior of grids, forms, trees and the other components, by injecting custom code invoked by specific events.

Events and actions work closely: an event provides inputs that an action can use and evaluate; output from actions can represent a feedback for events and can influence the way the component behaves after the event completion. So, for example, it is possible to prevent the data saving when pressing the save button in a grid, using an action linked to a "before saving on insert" event.

It is also possible to create a chain of actions to execute in sequence, starting from a single event: through the action detail form, it is possible to define an optional action to perform at the end of the current one. In case of server-side javascript actions, it is also possible to define an action to concatenate before the current action: in this way, a unique action composed of these two is execute on the server side. This feature allows to reuse code defined on the server by multiple actions, in a similar way to classes linked to a inheritance relationship: a base action can contain reusable code, expressed as declaration of functions, whereas other classes can invoke that code, if they inherit the base action, by declaring it as the class to execute before.

## Example

```javascript
var baseMethod = function(argument1, argument2, ..) {...}
```

## Example

```javascript
baseMethod(parameter1, parameter2, …);
```

This feature is available only for server-side javascript actions. If inheritance is nedded on client-side javascript actions, this is already possible by defining an action linked to the whole application \("action after render" property defined in the application detail form\), containing the reusable code \(reusable functions\); this code is global, so it can be accessed from any part of the client-side application, so to any client-side javascript action.

## Example

```javascript
window.baseMethod = function(argument1, argument2, ..) {...}
```

## Example

```javascript
baseMethod(parameter1, parameter2, …);
```

Events are defined at panel level and at component level \(column, control, tree node, button\). In the following table, a list of inputs and outputs for actions are reported, according to the event type, related to grid panels.

| Event | Action type | Inputs | Outputs |
| :--- | :--- | :--- | :--- |
| before grid opening | SQL/Javascript/Web service | gridXXX \(current grid\)  vo \(current record\) |  |
| after data loading | SQL/Javascript/Web service | gridXXX \(current grid\)  vo \(current record\) |  |
| click of a grid row on tree node | SQL/Javascript/Web service | comp \(current grid\)  record \(current record\)  rowIndex \(current row index\) |  |
| double click on a grid row | SQL/Javascript/Web service | comp \(current grid\)  record \(current record\)  rowIndex \(current row index\) |  |
| before cell editing in grid | SQL/Javascript/Web service | comp \(current grid\)  record \(current record\)  rowIndex \(current row index\)  node \(current column index\) | return false will block the operation |
| after cell editing in grid | SQL/Javascript/Web service | comp \(current grid\)  record \(current record\)  rowIndex \(current row index\)  node \(current column index\) |  |
| before saving data in insert mode | SQL/Javascript/Web service | gridXXX \(current grid\)  vo \(current record\) | return false will block the operation |
| before saving data in edit mode | SQL/Javascript/Web service | gridXXX \(current grid\)  vo \(current record\) | return false will block the operation |
| before deleting record | SQL/Javascript/Web service | gridXXX \(current grid\)  vo \(current record\) | return false will block the operation |
| before rendering the grid | SQL/Javascript/Web service | rec \(current record\) |  |
| when pressing insert button | SQL/Javascript/Web service | gridXXX \(current grid\)  vo \(current record\) | return false will block the operation |
| when pressing edit button | SQL/Javascript/Web service | gridXXX \(current grid\)  vo \(current record\) | return false will block the operation |
| when pressing delete button | SQL/Javascript/Web service | gridXXX \(current grid\)  vo \(current record\) | return false will block the operation |
| after saving data in insert mode | SQL/Javascript/Web service In case of SQL statements, these are executed on the server side and are part of the same saving transaction. | gridXXX \(current grid\)  vo \(current record\) |  |
| after saving data in edit mode | SQL/Javascript/Web service In case of SQL statements, these are executed on the server side and are part of the same saving transaction. | gridXXX \(current grid\)  vo \(current record\) |  |
| after deleting record | SQL/Javascript/Web service In case of SQL statements, these are executed on the server side and are part of the same saving transaction. | gridXXX \(current grid\)  vo \(current record\) |  |
| update summary row | Javascript | total \(current total for the current column\)  params.css \(css class name that can be applied for the current column\) params.id \(column index for the current column\) params.style \(style for the current column\)  params.attr \(‘ext:qtip=”Total no. of companies”‘ tooltip example\) record.data \(data to show in the last row\)  formattedValue \(current total, reformatted with the same format of the values in the column  colAttr \(attribute nameof the current column\)  col \(current column object\) | return total;  or  return formattedValue; |
| column headers | Javascript |  | return array with column headers  example: return \[ \[{header: ‘4 Cols’, colspan: 4}, {header: ‘7 Cols’, colspan: 7}\], \[{header: ‘Goup1’, colspan: 2},{header: ‘Group2’, colspan: 2},{header: ‘Group3’, colspan: 7, align: ‘center’}\] \];  Important note: be sure to include hidden columns in the colspan counting, as well as an additional empty column always automatically added on the right of the grid. |

The grid name \(gridXXX\) can be retrieved by opening the list of panels and identying the current pane: the last column in the list \(javascript name\) reports the grid name to use.

The local variable vo represents the javascript object linked to current grid row; if you need to read the value of a specific cell, use the related attribute name; example: vo\["attribute1.attribute2"\]

The local variable rec/record represents a wrapper of the javascript object linked to current grid row; starting from this wrapper, you can access so some utility methods/properties: record.data – the javascript object linked to the current row; if you need to read the value of a specific cell, use the related attribute name; example: record.data\["attribute1.attribute2"\] record.set\("attributeName1.attributeName2", valueToSet\] – method used to change the value of a specific cell, identified by the specified attribute name

In javascript actions, you can also perform common operations:

* gridXXX.store.getCount\(\) – number of rows in grid
* gridXXX.store.baseParams.parameter = value; – pass to HTTP request the specified parameter; in order to read that parameter on the binded business component on the server side, you have to specify it in the where clause of the business component; bear in mind that if the variable in the where clause is :VAR\_NAME, then the parameter passed through baseParams must be expressed in "camel" format, i.e. varName
* gridXXX.store.reload\(\); – force grid reloading
* gridXXX.store.removeAll\(\); – clear up the grid content
* gridXXX.mode – current grid mode;you can use it to check out the current grid mode and then perform custom logic.It can have 3 different values:
* READONLY
* INSERT
* EDIT
* gridToolbarXXX.enable\(\) or gridToolbar.disable\(\) – enable/disable all buttons in the toolbar
* gridToolbarXXX.insertButton.enable\(\) or disable\(\) – enable/disable the insert button of the grid’s toolbar
* gridToolbarXXX.updateButton.enable\(\) or disable\(\) – enable/disable the update button of the grid’s toolbar
* gridToolbarXXX.detailButton.enable\(\) or disable\(\) – enable/disable the detail button of the grid’s toolbar
* gridToolbarXXX.reloadButton.enable\(\) or disable\(\) – enable/disable the reload button of the grid’s toolbar
* gridToolbarXXX.deleteButton.enable\(\) or disable\(\) – enable/disable the delete button of the grid’s toolbar
* gridToolbarXXX.exportButton.enable\(\) or disable\(\) – enable/disable the export button of the grid’s toolbar

In the following table, a list of inputs and outputs for actions are reported, according to the event type, related to form panels.

| Event | Action type | Inputs | Outputs |
| :--- | :--- | :--- | :--- |
| before saving data in insert mode | SQL/Javascript/Web service | formPanelXXX \(current form\)  vo \(current record\) | return false will block the operation |
| before saving data in edit mode | SQL/Javascript/Web service | formPanelXXX \(current form\)  vo \(current record\) | return false will block the operation |
| before deleting record | SQL/Javascript/Web service | formPanelXXX \(current form\)  vo \(current record\) | return false will block the operation |
| when pressing insert button | SQL/Javascript/Web service | formPanelXXX \(current form\)  vo \(current record\) | return false will block the operation |
| when pressing edit button | SQL/Javascript/Web service | formPanelXXX \(current form\)  vo \(current record\) | return false will block the operation |
| when pressing delete button | SQL/Javascript/Web service | formPanelXXX \(current form\)  vo \(current record\) | return false will block the operation |
| after saving data in insert mode | SQL/Javascript/Web service In case of SQL statements, these are executed on the server side and are part of the same saving transaction. | formPanelXXX \(current form\)  vo \(current record\) |  |
| after saving data in edit mode | SQL/Javascript/Web service In case of SQL statements, these are executed on the server side and are part of the same saving transaction. | formPanelXXX \(current form\)  vo \(current record\) |  |
| after deleting record | SQL/Javascript/Web service In case of SQL statements, these are executed on the server side and are part of the same saving transaction. | formPanelXXX  \(current form\)  vo \(current record\) |  |
| click on any additional button | SQL/Javascript/Web service | formPanelXXX |  |

The form name \(formPanelXXX\) can be retrieved by opening the list of panels and identifying the current pane: the last column in the list \(javascript name\) reports the form name to use.

The local variable vo represents the javascript object linked to current form row; if you need to read the value of a specific control, use the related attribute name; example: vo.attribute1.attribute2

In javascript actions, you can also perform common operations:

* formPanelXXX.baseParams.parameter = value; – pass to HTTP request the specified parameter; in order to read that parameter on the binded business component on the server side, you have to specify it in the where clause of the business component; bear in mind that if the variable in the where clause is :VAR\_NAME, then the parameter passed through baseParams must be expressed in "camel" format, i.e. varName
* formPanelXXX.reload\(\); – force form reloading
* formPanelXXX.mode – current form mode. You can use it to check out the current form mode and then perform custom logic. It can have 3 different values:
* READONLY
* INSERT
* EDIT
* formToolbarXXX.enable\(\) or disable\(\) – enable/disable all buttons in the toolbar
* formToolbarXXX.insertButton.enable\(\) or disable\(\) – enable/disable the insert button of the form toolbar
* formToolbarXXX.updateButton.enable\(\) or disable\(\) – enable/disable the update button of the form toolbar
* formToolbarXXX.detailButton.enable\(\) or disable\(\) – enable/disable the detail button of the form toolbar
* formToolbarXXX.reloadButton.enable\(\) or disable\(\) – enable/disable the reload button of the form toolbar
* formToolbarXXX.deleteButton.enable\(\) or disable\(\) – enable/disable the delete button of the form toolbar
* formToolbarXXX.exportButton.enable\(\) or disable\(\) – enable/disable the export button of the form toolbar
* formToolbarXXX.show\(\) or hide\(\) – hide/show toolbar

In the following table, a list of inputs and outputs for actions are reported, according to the event type, related to tree panels.

| Event | Action type | Inputs | Outputs |
| :--- | :--- | :--- | :--- |
| before tree rendering | SQL/Javascript/Web service | treePanelXXX \(current tree\) |  |
| click of a grid row on tree node | SQL/Javascript/Web service | treePanelXXX \(current tree\)  vo \(record linked to current node\)  node \(current node\) |  |
| when expanding a node | SQL/Javascript/Web service | treePanelXXX \(current tree\)  vo \(record linked to current node\)  node \(current node\) |  |
| when selecting a checkbox related to a node | SQL/Javascript/Web service | treePanelXXX \(current tree\)  vo \(record linked to current node\)  node \(current node\) |  |

The tree name \(treePanelXXX\) can be retrieved by opening the list of panels and identying the current pane: the last column in the list \(javascript name\) reports the tree name to use.

In the following table, a list of inputs and outputs for actions are reported, according to the event type, related to components.

| Event | Action type | Inputs | Outputs |
| :--- | :--- | :--- | :--- |
| cell value changed | SQL/Javascript/Web service | comp record rowIndex node control formPanel filterPanel |  |
| before validation | SQL/Javascript/Web service | control formPanel filterPanel |  |
| focus grab and lost | SQL/Javascript/Web service | comp record rowIndex node control formPanel filterPanel |  |
| value changed | SQL/Javascript/Web service | comp record rowIndex node control formPanel filterPanel |  |
| value correctly validated | SQL/Javascript/Web service | comp record rowIndex node control formPanel filterPanel |  |
| value not correctly validated | SQL/Javascript/Web service | comp record rowIndex node control formPanel filterPanel |  |
| row/node selected | SQL/Javascript/Web service | comp record rowIndex node control formPanel filterPanel |  |
| button clicked | SQL/Javascript/Web service | comp record control formPanel filterPanel |  |
| enter key pressed | SQL/Javascript/Web service | comp record control formPanel filterPanel |  |

In the following table, a list of inputs and outputs for actions are reported, according to the event type, related to grid columns.

| Event | Action type | Inputs | Outputs |
| :--- | :--- | :--- | :--- |
| before lookup code validation before lookup grid opening | SQL/Javascript/Web service | lkupds lkupds.baseParams grid rec |  |
| after lookup code changed | SQL/Javascript/Web service | grid rec value |  |
| before upload button | SQL/Javascript/Web service | grid rowIndex colIndex rec |  |
| after editing cell | SQL/Javascript/Web service | grid rowIndex columnIndex fieldName |  |
| cell selected | SQL/Javascript/Web service | record index control |  |
| before combo selection | SQL/Javascript/Web service | combo |  |

A special kind of javascript action is the one where the written code, executed on the GUI layer, is used to call the server side, via an Ajax call. There are 2 ways to invoke the server side: invoking a resource class on the server side, e.g. using the utility method

```javascript
var response = new SyncRequest().send(uri[,method[,data]]);
```

invoking a predefined resource class, used to invoke a business component having the task to execute a SQL statement \(contextPath+"/executesql"\) or a web service from the server layer \(contextPath+"/executesql/executewebservice"\).

Another javascript function provided by platform that you could include in your client-side javascript actions is the one used to manually manage a file upload:

```javascript
uploadFile(title,enabled,fileName,dirId,beforeUploadCallback,afterUploadCallback,errorOnUploadCallback,settings);
```

The syntax for this function includes the following arguments:

* **title**; can be null, in that case, a default title is shown 
* **enabled** true: upload button is enabled, false: upload button is disabled 
* **fileName** file name to set in the input field; if it is not empty, then a “Download” and “Preview” buttons are shown too 
* **dirId** directory id on the server side where saving the file to upload 
* **beforeUploadCallback** callback function invoked just before the uploading; format: 

```javascript
function({ filename: “…” }) { 
  return true|false 
};   
// false can block the uploading
// you can change the file name by working on the argument 
```

* **afterUploadCallback** callback function invoked after the uploading; format: 

```javascript
function({ fileName: "….", success: true|false, message: "…" }) 
// The popup window is automatically closed just before this callback is invoked
```

* **errorOnUploadCallback** callback function invoked in case of errors on uploading; format: 

```javascript
function({ error: “…” }) 
// The popup window is automatically closed just before this callback is invoked
```

* **settings** - optional javascript object containing additional settings, expressed as attribute-value:
  * **acceptMimes**: a string containing a list of mime types, separated by comma; example: "image/jpg,image/png" - used to filter the content to upload 
  * **encriptAttachments**: true\|false - specify if content to upload must be encripted on the server side; in such a case, content must be undecripted when reloading it leater
  * **panelId**: ... // panel id related to this upload process

Through this arguments you can for instance show a modal dialog prompting the user to select a local file and upload it in the specified folder identified by dirId. After that, you could use the same modal dialog to show a previously uploaded file, through the “Download” or “Preview” buttons, in case you have filled the “fileName” arguments.

## Example

```javascript
uploadFile(
    "My upload", // title,
    true, // upload enabled
    "", // fileName,
    19, // dirId
    function(args) {
        var filename = args.filename;
        return true; // do not block file uploading!
    }, // beforeUploadCallback
    function(args) {
        var filename = args.filename;
    }, // afterUploadCallback
    function(args) {
        var error = args.error;
    }, // errorOnUploadCallback
    {
      acceptMimes: "image/jpg,image/png,image/gif"
    } // settings
);
```

## Example

```javascript
var yourFileName = "...";
uploadFile(
    "My upload", // title,
    false, // upload NOT enabled
    yourFileName, // fileName,
    19 // dirId
);
```

