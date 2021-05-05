# Theme customization

## Customizing a Filter Panel

Adding an icon to the left of a folder title

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







