# Theme customization

## Customizing a Filter Panel

### Adding an icon to the left of a folder title

In order to set an icon to the left of a folder title, in case the folder contains a filter panel:

* add to xtheme.css file something like

```text
.x-tab-strip .x-tab-with-icon span.x-tab-strip-text.myclassename {
	background-image: url(../images/menu/myiconname.png) !important;
}
```

where "myclassname" represents the name of a CSS class referring the icon to show for this specific panel

* set to the "Additional Settings" available in the Filter Panel definition pane something like

```text
iconCls: "myclassname"
```

### 

### Adding additional graphical settings to a filter panel

You can include more settings for a filter panel using the "Additional Settings" available in the Filter Panel definition pane something like.

See [https://docs.sencha.com/extjs/3.4.0/\#!/api/Ext.form.FormPanel](https://docs.sencha.com/extjs/3.4.0/#!/api/Ext.form.FormPanel) to get a whole description of available settings.

## Customizing a Grid Panel

### A grid having rows with different heights

It is not needed to set any additional setting to have rows with different heights: you have simply to set a cell content having multiple lines. Lines can be expressed in HTML format and a new line is represented by the &lt;br&gt; tag.

A good approach can be to bind a server-side javascript business component and use it to fill in the cell content: through javascript you can easily create complex HTML  content, using for example the tags &lt;table&gt; &lt;tr&gt; &lt;td&gt;.

An alternative approach is to do it on the web layer, through the renderer property of a column, where you can define HTML content, for example using the tags &lt;table&gt; &lt;tr&gt; &lt;td&gt;:

```text
value = 
'<table height=200 width=200>'+
'<tr><td>ID  :</td><td>'+record.data.id+'</td></tr>'+
'<tr><td>PIN :</td><td>'+record.data.pin+'</td></tr>'+
'<tr><td>Address :</td><td>'+record.data.address+'</td></tr>'+
'</table>';
```

In this example, the record passed to the row must contains attributes named id, pin and address.



### Adding additional graphical settings to a grid panel

You can include more settings for a filter panel using the "Additional Settings" available in the Grid Panel definition pane something like.

See [https://docs.sencha.com/extjs/3.4.0/\#!/api/Ext.grid.GridPanel](https://docs.sencha.com/extjs/3.4.0/#!/api/Ext.grid.GridPanel) to get a whole description of available settings.

## Customizing a Form Panel

### Adding additional graphical settings to a form panel

You can include more settings for a filter panel using the "Additional Settings" available in the Grid Panel definition pane something like.

[/docs.sencha.com/extjs/3.4.0/\#!/api/Ext.form.FormPanel](https://docs.sencha.com/extjs/3.4.0/#!/api/Ext.grid.GridPanel) to get a whole description of available settings.





