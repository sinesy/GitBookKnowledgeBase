# Adding a where clause to a business component linked to grid

In case an additional where clause must be appended to the base filtering conditions defined in a business component linked to a grid, a client side js action can be defined and linked to the grid \(e.g. linked to a “before load” event\). The addSqlWhereClause property can be used for that purpose.

## Example

```javascript
var filtervalue = getComponentByItemId('xxx', win).getForm().findField('filterName').getValue();

var strWhere = " ...";

store.baseParams.addSqlWhereClause = strWhere;
```

