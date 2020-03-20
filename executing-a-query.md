# executing a query

In order to execute a SQL query from within a server side JS action, the following snippet can be used:

```javascript
var query = "SELECT * from TABLE where site_id = ? and company_id = ?";

var rows = utils.executeQuery(
  query,
  null, /* additional datastore ID, null for main Database */
  false, /* do it on a separated transaction */
  true, /* fire an Execution in case of error */

  siteId,
  companyId /* Parameters list separated by commas */
);
rows = JSON.parse(rows);
```

NOTE: The second parameter can be number representing the additional datastore ID or null to execute the query on the main DB.

Remember to use camelCase to access fields data, so the field called COMPANY\_ID in the database can be accessed as rows.companyID after the query execution.

