# Adding filter conditions to a grid

In order to set filtering conditions defined in a filter panel to a grid, these must be set in the baseParams object contained in the grid’s store object:

```javascript
var filterValue = getComponentByItemId('xxx', win).getForm().findField('filterName').getValue();

store.baseParams.nome_colonna_camelcase=filterValue;

store.load();
```

Pay attention that this filtering conditions are passed to the grid’s business components but they are ignored by it if they have not been defined as variables inside the business component \(e.g. where field = :NOME\_COLONNA\).

An alternative way is to usequickFilterNames,quickFilterOps equickFilterValues parameters: these do not require that the parameter is declared within the business component: they add that condition in AND to al the others \(which is not always acceptable\).

## Example

```javascript
store.baseParams.quickFilterNames += ",nomeOggetto.nome_campo_camelcase";
store.baseParams.quickFilterOps += ",=";
store.baseParams.quickFilterValues += "," + valore;
```

* quickFilterNames: list of attributes, separated by a comma; they will be replaced on the server side with equivalent field names \(e.g. atttributeName -&gt; ATTRIBUTE\_NAME\)
* quickFilterOps: list of operators, separated by a comma, one for each attribute defined through the previous parameter; allowed values:=,&lt;&gt;, like etc.
* quickFilterValues: list of attribute values, separated by a comma

Notethat if you need to have aIN filter,the commas must be replaced by a single pipe \(\|\) in the quickFilterValues.

```javascript
store.baseParams.quickFilterNames += ",field";
store.baseParams.quickFilterOps += ",IN";

/* In case of array -&gt; arr = [1, 2, 3, 4, 5, 6, ... ] */
store.baseParams.quickFilterValues += "," + arr.join('|');


/* In case of string -&gt; str = "1, 2, 3, 4, 5, 6, ... */
store.baseParams.quickFilterValues += "," + str.replace(/\,/g, '|');
```

Adding further filtering conditions to the ones already available through a filter panel

In case a filter panel is linked to a grid, all its filter controls are automatically passed to the grid, when loading it. However, additional conditions can be added too. The filter panel event “before search” can be used to attach a js action where adding other filters:

```javascript
filterNames += 'nomeOggetto.filterName,';
filterOps += '=,';
filterCaseSensitives += 'true,';
filterValues += 'value,';
```

Pay attention to concatenatethese additional conditions to the ones already existing: do not replace them! In case the filtering condition to add is related to a SQL field not belonging to the main table, the secondary table must be specified too, in terms of “object” \(camel notation\)

## Example

Finally, it is possible to use all the SQL operators: =, &gt;= &lt;= != o &lt;&gt; The “in” operator is also supported; when using it, all its values must be separated by the \| symbol

