# How to display the data not found message in a grid

If you want to display the data not found message in a grid you can set the "Add. view config." in a panel grid.

This is the attributes to set

```
deferEmptyText: true,
emptyText: '<div class="grid-data-empty"><div data-icon="/" class="empty-grid-icon"></div><div class="empty-grid-headline">Data not foud</div></div>'

```

You can customize the message with

```
Ext.translate.Cache.getTranslation("panels.data not found")
```

Example

```
deferEmptyText: true,
emptyText: '<div class="grid-data-empty"><div data-icon="/" class="empty-grid-icon"></div><div class="empty-grid-headline">' + Ext.translate.Cache.getTranslation("msg.data not found") + '</div></div>'

```

If you want load the key of the message, with default message, for all grid panels you can set "Message for empty panel" application parameter.
