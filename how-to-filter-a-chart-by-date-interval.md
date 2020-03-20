# How to filter a chart by date interval

In case you have configured a chart panel, what you have already done is:

* define a business component, usually a "SQL based component for a grid" to fill in the chart
* define a window containing the chart panel and bind that business component in the chart panel definition

After that, you have a dynamic chart, whose content depends on the bind variables defined in the SQL business component, which could be something like:

```text
SELECT ... FROM ... WHERE FIELD1 = :COMPANY_ID
```

where :XXX are input parameters which can be automatically resolved by Platform in case of predefined variables \(e.g. COMPANY\_ID, SITE\_ID, USERNAME, ...\) o_r_ can come from the UI.

**A window composed of a filter panel + chart panel**

When you need for dynamic content in a chart, it is a good practice to include in the window a filter panel as well.

That means you have to manually add filter components to the filter panel, whose name must be **exactly** as a database field which can be appended to the base SQL statement defined in the business component. These filter components must also have selected the "**Add to filter**" flag, in order to automatically pass forward such filter conditions.

When the end user presses the Search button in the filter panel, Platform will pass forward to the server-side these request parameters:

* **filterNames** - a String composed of a list of attribute names separated by a comma; example: "field1,field2,"
* **filterOps** - a String composed of a list of operators to apply to the filters defined above and therefore they must have the same number of tokens, separated by a comma; example: "=,&gt;=,"
* **filterCaseSensitives** - a String composed of a list of boolean flags, separated by a comma, one for each filter defined above, indicating whether a text valu filter must be CASE sensitive or not; example: "false,false,"
* **filterValues** - a String composed of a list of attribute names separated by a comma; example: "ABC,2,"

Suppose you have defined a filter component having field name: "FIELD3", with operator "like".

When such a filter has been filled on the web UI and the user has pressed the Search button, Platform will pass forward something like:

* **filterNames** = "field3,"
* **filterOps** = "like,"
* **filterCaseSensitives** - "false,"
* **filterValues** - xyz

On the server side, the base SQL statement will be enriched with this additional condition:

```text
SELECT ... FROM ... WHERE FIELD1 = :COMPANY_ID AND FIELD3 LIKE xyz
```

**Filling additional bind variables**

A more complex scenario is when you have defined a SQL statement in the business component requiring bind variables not directly solved by Platform \(not predefined variables\).

Suppose you have defined a SQL statement like the following:

```text
SELECT ... FROM ... WHERE FIELD1 = :COMPANY_ID AND MY_DATE >= :DATE1 AND MY_DATE <= :DATE2
```

For this example, two filter components must be defined, which can be called for example DATE1 and DATE2; pay attention to **NOT** select the flag "Add to filter", otherwise Platform will automatically pass these filter conditions to the business component, where the base SQL would be enriched with:

```text
AND DATE1 = ... AND DATE2 = ...
```

but DATE1 and DATE2 are NOT real database fields and a database error would be fired!

The bind variables required by the business component linked to the chart panel \(DATE1 and DATE2\) can be filled by passing them forward through the filter panel, using a "before search" event linked to the filter panel.

In such client-side javascript action, you can define or even by-pass completely the filter conditions to pass to the business component: filterNames, filterOps, filterCaseSensitives, filterValues.

You can append additional filters to these pre-filled variables, as well as completely redefine them.

You can also pass forward additional bind variables, not defined through a filter component.

The right content for such js action could be:

```javascript
var date1 = getComponentByItemId('filterPanelxxx',win).getComponent('controlDATE1');
var date2 = getComponentByItemId('filterPanelxxx',win).getComponent('controlDATE2');
if (date1.getValue()!=null && date2.getValue()!=null) {
    baseParamsChartPanelyyy.date1 = date1.getValue();
    baseParamsChartPanelyyy.date2 = date2.getValue();
}
```

Through this scriptlet, the two dates are read from the filter components and passed forward to the server-side as 2 request parameters named date1 and date2.

Please note that yyy represents the id of the chart panel, whereas xxx represent the id of the filter panel.

**Important note**: bear in mind that you have always to set the right input parameter type in the business component: when you edit the business component content, by adding bind variables like :DATE1 and :DATE2, Platform will automatically identify them and include them in the "Component input parameters" folder. Here you have to set the right parameter type. For the example above, you have o change the parameter type to Date for DATE1 and DATE2, otherwise the 2 strings passed from the UI related to the dates, will not be correctly converted to dates!

