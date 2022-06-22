---
description: Since 6.0.2
---

# Scrollable form list

If you want replace a grid with a list of editable forms you can use a Replicated Form.

In the editable panel detail you must define the panel like a replicated form.

![](<.gitbook/assets/image (19).png>)

Now, in your application, you can view an empty table panel container. When you load data with a list of records, the container will display an editable panel for each record.

![](<.gitbook/assets/image (20).png>)

For the replicable panel you can:

* load the data: you must specify the business component to call and the optional params

```
// XXX: id of editable panel
customPanelXXX.loadData(businessComponentId, { attribute: value });
```

* clear the data of one panel: you must specify the index of panel

```
customPanelXXX.clearData(index);
```

* ask the list of modified records: return an array of records

```
customPanelXXX.getModifiedRecords();
```

* ask the list of index modified records: return an array of index

```
customPanelXXX.getIndexModifiedPanel();
```

* ask the specified panel: return a panel

```
customPanelXXX.getFormPanel(index);
```

* set the data into specified panel

```
// index of panel
// values: javascript object with attribute and value to valorize the panel
// prefix for the inner pbject attribute
customPanelXXX.setFormValues(index, values, prefix);
```
