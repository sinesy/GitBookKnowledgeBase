# Configuring grid exports

The grid export task can be customized using the application parameter named “grid export sorting”.  
The parameter value should contain a list of export formats separated by the \| symbol.  
These export formats will be the only ones showed and in the specified order.  
Allowed values are:

* \[‘XLS’,”Excel”\]
* \[‘Extended XLS’,”Extended Excel”\]
* \[‘CSV \(;\)’,”CSV \(;\)”\]
* \[‘CSV \(,\)’,”CSV \(,\)”\]
* \[‘HTML’,”HTML”\]
* \[‘PDF’,”PDF”\]
* \[‘RTF’,”RTF”\]
* \[‘XML \(small format\)’,”XML \(small format\)”\]
* \[‘XML \(large format\)’,”XML \(large format\)”\]

For example, if the application parameter is set to “Extended XLS\|XLS”, then the only allowed export formats will be Extended Excel and Excel and showed in the combo box in that order:

* “Excel Extended” 
* “Excel”

If the application parameter is not set, then the default order is the one reported in the list above.

Pay attention to the “Available in the client” checkbox for that parameter: it must be checked: only in that case the parameter will be available on the front-end. Moreover, in order to make it working, the user has to logon again in the application.

There is an grid event named "**before export**" you can use to customize the export experience. You can link to this event an client-side javascript action you can use for:

1 - block the export process, by returning false

2 - pass forward additional settings, by returning a javascript object which can contain the following optional attibutes:

* cols - a javascript array containing columns descriptors, each containing: dataIndex, header, width, selected attributes
* defaultExportFormat - the default export format; allowed values are reported above
* enqueue - a true\|false value; if not specified the export is never enqueued; if set to true, the export activity is postponed in case an export is already in progress: the current one will automatically start at the end of the previous one; there is a timeout of 30 minutes, after them the export waiting is cancelled. Moreover, a dialog is prompted to the user to inform him about the export suspension because there is already another export in progress and ask him for waiting or cancel the export process

Example of enqueued export \(available from 5.3.2 version of Platform\):

```javascript
return { enqueue: true };
```

Finally, there is a global parameter in the EXPORT section named "**Max.nr. of concurrent exports beforeenqueuing them**", that you can use to tune the max number of concurrent exports: if not specified, it is set to 1, i.e. only one export can be executed at the same time, all the others are enqueued.

It is strongly recommended to not increase that value if the application under development contains very large data to export.

