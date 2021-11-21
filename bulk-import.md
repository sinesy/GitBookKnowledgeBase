# Bulk import

A "bulk import" is a process involving a large amount of data to write in one or more connected tables, usually starting from a file.

Writing data means inserting records in table or updating them.

Typical problems to deal with when importing data are:

* how to speed up the import process (e.g. loading 1M rows from a file in a few minutes and not in days or weeks)
* updating already existing records, where a specific record can be identified by a set of fields which are not the PK but they can be used to identity a single record (e.g. COMPANY\_ID + CODE instead of a PK based on a single numeric field like PROG\_ID)
* validating input data in terms of: **NOT NULL** constraints, **type** validation (e.g. an integer, e decimal number, a date expressed as yyyy-MM-dd, a date+time expressed as yyyy-MM-dd HH:mm), **length** validation (e.g. a text no more larger than x characters, an integer having no more than N digits, a decimal number having no more than N/M int/dec digits)
* validating input data with respect to other tables, i.e. through a **foreign key **(e.g. a CODE passed in input to validate using another table where CODE is one of its fields to check out and then fetch its PROD\_ID primary key)

The best way to manage all these issues is by splitting them into a sequence of steps:

1. **loading a CSV file to a border table **"**x\_**_**csv**"._ This table must have a simple structure: a CLOB field hosting the whole CSV raw, a numeric PK (e.g. ROWID), other fields to partition data per tenant (e.g. COMPANY\_ID). The aim of this table is to forget the file and work only on a database level, which is way faster than working with files. **A grid can be defined to show and edit the CLOB content, splitted in many columns, one for each CSV column. ** Editing is helpful to fix input data through the grid, in case of validation errors. Additional fields can be added:&#x20;
   * ERRORS - text field to store a list of errors (e.g a JSON list of errors)
   * CONTAINS\_ERRORS - char field used to store an error condition (Y/N)
2. **validating  data in terms of mandatory, type and length, by loading a second border table**; a second table is now needed in order to host each field; consequently a mapping is needed between the CLOB field in the first border table to N fields in the second border table. During this step, the validation is performed. In case of errors, these are stored in the first border table: fixes can be applied in the first border table and then the validation can be re-executed.&#x20;
3. **validating data in terms of foreign keys, by using destination tables to check out values (FKs)** - destination tables are needed to checkout a CODE and get the PK (e.g. PROG\_ID); the second border table must contain not only CODE fields but also the FK (PROG\_ID). In order to speed up this process, a limited amount of UPDATES must be carried out.
4. **loading data from the border table to destination tables** - the second border table contains all data required to fill in any destination table: only INSERT or UPDATE operation are now needed, except for the calculation of the PK for a destination table, in case this is based on an UUID or a counter (reckoned through a counter table). In order to speed up the calculation of counters, a unique UPDATE must be carried out: in this way, a lock is performed only once and one only update operation is performed.

The steps reported above can be executed using a few utility methods:

**1.loading a CSV file to a border table **"**x\_**_**csv**"_

```javascript
utils.readCsvAndLoadTable(settings); 
```

where settings is a javascript object containing the following attributes:

Example of x\_csv border table:

```
create table BT01_ITEMS_CSV( 
 COMPANY_ID CHAR(5) NOT NULL, 
 ROW_ID NUMERIC(12) NOT NULL, 
 ROW_CONTENT MEDIUMTEXT, 
 ERRORS VARCHAR(4000), 
 CONTAINS_ERRORS CHAR(1), 
 PRIMARY KEY(COMPANY_ID,ROW_ID) 
);
```

****

**2. validating  data in terms of mandatory, type and length, by loading a second border table**

```javascript
utils.mapClobFieldToTable(settings);
```

where settings is a javascript object containing the following attributes:





: null, : "BT01\_ITEMS\_CSV", : null, : "BT02\_ITEMS\_DATA", : "ROW\_CONTENT", : ";", errorFieldName: "ERRORS", containsErrorsField: "CONTAINS\_ERRORS",

```
asIsFields: {
  COMPANY_ID: "COMPANY_ID",
  ROW_ID: "ROW_ID",
},
defaultFields: {
  ERRORS: "",
  CONTAINS_ERRORS: "N",
},

mappedFields: [
{
  fieldName: "ITEM_CODE",
  fieldType: "TEXT",
  fieldLength: 20,
  mandatory:   true,
},
```

| Attribute name   | Description                                                                                                             |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------- |
| srcDatasourceId  | datasource id used to access the first** **border table (can be null: default schema)                                   |
| srcTableName     | x\_csv first border table name (e.g. BT01\_ITEMS\_CSV)                                                                  |
| destDatasourceId | datasource id used to access the second** **border table (can be null: default schema)                                  |
| destTableName    | x\_data second border table name (e.g. BT02\_ITEMS\_DATTA)                                                              |
| clobFieldName    | optional; in case of a table where the the field name in the border table, having CLOB type and used to store a CSV raw |
| separator        | CSV separator: ; or .                                                                                                   |
| headers          | true if the CSV file contains a header list as the first raw                                                            |
| commitSize       | in case of a very large result set, commit is essential after a block of data written; e.g. 1000                        |
| clobFieldName    | the field name in the border table, having CLOB type and used to store a CSV raw                                        |
| datasourceId     | datasource id used to access the border table (can be null: default schema)                                             |
| tableName        |                                                                                                                         |



**3. validating data in terms of foreign keys, by using destination tables to check out values (FKs)**

```javascript
utils.validateCode(settings); // fill in FKs fields in the second border table, by validating CODEs in destination tables; this method must be called for each CODE field
```





**4. loading data from the border table to destination tables**

```javascript
utils.copyDataFromBorderTable(settings); // load a destination table from the seocnd border table; this method must be called for each destination table
```



