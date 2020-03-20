# Enquiring a table belonging to the Platform repository

Inside a server side js action it is possible to execute SQL queries through utils.executeQuery function. In case of a SQL query enquiring a table belonging to the Platform repository, the datastore id must be set to null.

```javascript
var rows = utils.executeQuery(
query,
 null, /* additional datastore */
false, /* do it on a separated transaction */
true, /* fire an Execution in case of error */ 
                [elenco dei parametri separati da virgola]
);

rows = JSON.parse(rows);
```

