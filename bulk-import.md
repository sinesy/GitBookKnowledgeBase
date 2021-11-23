# Bulk import

A "bulk import" is a process involving a large amount of data to write in one or more connected tables, usually starting from a file.

Writing data means inserting records in table or updating them.

Typical problems to deal with when importing data are:

* how to speed up the import process (e.g. loading 1M rows from a file in a few minutes and not in days or weeks)
* updating already existing records, where a specific record can be identified by a set of fields which are not the PK but they can be used to identity a single record (e.g. COMPANY\_ID + CODEs instead of a PK based on a single numeric field like PROG\_ID)
* validating input data in terms of:&#x20;
  * **NOT NULL** constraints
  * **type** validation (e.g. an integer, e decimal number, a date expressed as yyyy-MM-dd, a date+time expressed as yyyy-MM-dd HH:mm)
  * **length** validation (e.g. a text no more larger than x characters, an integer having no more than N digits, a decimal number having no more than N/M int/dec digits)
  * **allowed values **(compared with an enumeration of allowed values, like "Y" or "N")
  * **unique keys**, i.e. a set of fields defined in this border table with represent a unique record (e.g. FIRST\_NAME + LAST\_NAME or EMAIL for a customer border table).
* validating input data with respect to other tables, i.e. through a **foreign key **(e.g. a CODE passed in input to validate using another table where CODE is one of its fields to check out and then fetch its PROD\_ID primary key)

The best way to manage all these issues is by splitting them into a sequence of steps:

1. **loading a CSV file to a border table **"**x\_**_**csv**"._ This table must have a simple structure: a CLOB field hosting the whole CSV raw, a numeric PK (e.g. ROWID), other fields to partition data per tenant (e.g. COMPANY\_ID). The aim of this table is to forget the file and work only on a database level, which is way faster than working with files. **A grid can be defined to show and edit the CLOB content, splitted in many columns, one for each CSV column. ** Editing is helpful to fix input data through the grid, in case of validation errors. Additional fields can be added:&#x20;
   * ERRORS - text field to store a list of errors (e.g a JSON list of errors)
   * CONTAINS\_ERRORS - char field used to store an error condition (Y/N)
2. **validating  data in terms of mandatory, type, length, enumeration and unique key, by loading a second border table**; a second table is now needed in order to host each field; consequently a mapping is needed between the CLOB field in the first border table to N fields in the second border table. During this step, the validation is performed. In case of errors, these are stored in the first border table: fixes can be applied in the first border table and then the validation can be re-executed.&#x20;
3. **validating data in terms of foreign keys, by using destination tables to check out values (FKs)** - destination tables are needed to checkout a CODE and get the PK (e.g. PROG\_ID); the second border table must contain not only CODE fields but also the FK (PROG\_ID). In order to speed up this process, a limited amount of UPDATES must be carried out.
4. **loading data from the border table to destination tables** - the second border table contains all data required to fill in any destination table: only INSERT or UPDATE operation are now needed, except for the calculation of the PK for a destination table, in case this is based on an UUID or a counter (reckoned through a counter table). In order to speed up the calculation of counters, a unique UPDATE must be carried out: in this way, a lock is performed only once and one only update operation is performed.

![](<.gitbook/assets/Schermata 2021-11-22 alle 10.52.46.png>)

The steps reported above can be executed using a few utility methods:

**1.loading a CSV file to a border table **"**x\_**_**csv**"_

```javascript
utils.readCsvAndLoadTable(settings); 
```

where settings is a javascript object containing the following attributes

| Attribute name   | Description                                                                                                                                                                            |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| srcDirId         | directory id where the CSV file is located                                                                                                                                             |
| srcFileName      | CSV file name to read                                                                                                                                                                  |
| charSet          | char set used to save the CSV file (if not specified, it is assumed it is "UTF-8")                                                                                                     |
| idAsNum          | optional field name (having numeric type) which must be created in the border table x\_csv and used to host the numeric PK, generated automatically by Platform when importing the CSV |
| idAsUuid         | optional field name (having string type) which must be created in the border table x\_csv and used to host a text UUID PK, generated automatically by Platform when importing the CSV  |
| additionalFields | javascript object containing additional fields included in the border table, which must be filled in and not coming from the CSV file (e.g. COMPANY\_ID)                               |
| headers          | flag true/false indicating whether the CSV file has a first row with the headers (read data starts from the second row)                                                                |
| commitSize       | in case of a very large result set, commit is essential after a block of data written; e.g. 1000                                                                                       |
| clobFieldName    | a CLOB type field name included in  the border table x\_csv which is used to host the whole row                                                                                        |
| datasourceId     | optional datasource id (can be set to null to access the default schema) used to refer the border table                                                                                |
| tableName        | border table name                                                                                                                                                                      |

Example of x\_csv border table and javascript:

```
TABLE:

create table BT01_ITEMS_CSV( 
 COMPANY_ID CHAR(5) NOT NULL, 
 ROW_ID NUMERIC(12) NOT NULL, 
 ROW_CONTENT MEDIUMTEXT, 
 ERRORS VARCHAR(4000), 
 CONTAINS_ERRORS CHAR(1), 
 PRIMARY KEY(COMPANY_ID,ROW_ID) 
);

JAVASCRIPT:

var settings = {
    srcDirId: 9,
    srcFileName: "art.csv",
    charSet:"UTF-8",
    idAsNum:"ROW_ID",
    idAsUuid: null,
    additionalFields: {
        COMPANY_ID: "00000",
        ERRORS: "",
        CONTAINS_ERRORS: "N"
    },
    headers: false,
    commitSize: 10000,
    clobFieldName: "ROW_CONTENT",
    datasourceId: null,
    tableName: "BT01_ITEMS_CSV"
};   
utils.readCsvAndLoadTable(settings);
// Time spent to load 1M raws from a CSV file: 93474ms

```

****

**2. validating  data in terms of mandatory, type and length, by loading a second border table**

```javascript
utils.mapClobFieldToTable(settings);
```

where settings is a javascript object containing the following attributes

| Attribute name      | Description                                                                                                                                                                                                                                                |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| srcDatasourceId     | datasource id used to access the first** **border table (can be null: default schema)                                                                                                                                                                      |
| srcTableName        | x\_csv first border table name (e.g. BT01\_ITEMS\_CSV)                                                                                                                                                                                                     |
| destDatasourceId    | datasource id used to access the second** **border table (can be null: default schema)                                                                                                                                                                     |
| destTableName       | x\_data second border table name (e.g. BT02\_ITEMS\_DATTA)                                                                                                                                                                                                 |
| clobFieldName       | optional; in case of a table where the the field name in the border table, having CLOB type and used to store a CSV raw                                                                                                                                    |
| separator           | CSV separator: ; or .                                                                                                                                                                                                                                      |
| errorFieldName      | field name in the FIRST border table where saving validation errors fired when loading the second border table                                                                                                                                             |
| commitSize          | in case of a very large result set, commit is essential after a block of data written; e.g. 1000                                                                                                                                                           |
| containsErrorsField | field name in the FIRST border table used as flag Y/N, set to Y in case of validation errors fired when loading the second border table                                                                                                                    |
| asIsFields          | javascript object containing the mapping between fields in the first border table and the second one                                                                                                                                                       |
| defaultFields       | javascript object containing fields+values to fill in the second border table                                                                                                                                                                              |
| mappingFields       | javascript Array containing javascript objects, each related to a mapping from the CLOB field and fields in the second border table                                                                                                                        |
| uniqueKeys          | optional: if specified, it is a String representing the list of fields defined in this border table which represent a unique key (e.g. "FIRST\_NAME,LAST\_NAME or "EMAIL"). Fields must be separated by a comma (this property is NOT an javascript array) |
| srcRowId            | optional; required if uniqueKeys has been specified: it represent the field name in the first border table, to identify the record to update in case of duplicate key errors                                                                               |
| destFilter          | SQL condition to add to the checking query for duplicates                                                                                                                                                                                                  |
| deleteFilter        | optional SQL filter, used to DELETE data in the second border table before starting the writing process; if set to null, no DELETE is performed                                                                                                            |
| commitSize          | block of records to save in the second border table after that a commit is performed                                                                                                                                                                       |

**Important note:** in order to make it fast this validation process, **it is essential to create an index for the "unique key" **passed as argument to this method. DO NOT create a unique key!

Example of the second border table and javascript:

```
TABLE:

create table BT02_ITEMS_DATA(
 COMPANY_ID CHAR(5) NOT NULL,
 ROW_ID NUMERIC(12) NOT NULL,
 ITEM_CODE VARCHAR(20),
 DESCRIPTION VARCHAR(255),
 BARCODE VARCHAR(255),
 VAT_CODE VARCHAR(20),
 FK_ART01 NUMERIC(12),
 FK_ART02 NUMERIC(12),
 FK_ART03 NUMERIC(12),
 PRICE DECIMAL(18,5),
 SELL_NR NUMERIC(18),
 CREATE_DATE DATE,
 LAST_UPDATE DATETIME,
 ERRORS VARCHAR(4000),
 CONTAINS_ERRORS CHAR(1),
 PRIMARY KEY(COMPANY_ID,ROW_ID)
);

JAVASCRIPT:

var settings = {
    srcDatasourceId: null,
    srcTableName: "BT01_ITEMS_CSV",
    destDatasourceId: null,
    destTableName: "BT02_ITEMS_DATA",
    clobFieldName: "ROW_CONTENT",
    separator: ";",
    errorFieldName: "ERRORS",
    containsErrorsField: "CONTAINS_ERRORS",

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
      mandatory:   true
    },
    {
      fieldName: "DESCRIPTION",
      fieldType: "TEXT",
      fieldLength: 255,
      mandatory:   true
    },
    {
      fieldName: "BARCODE",
      fieldType: "TEXT",
      fieldLength: 255,
      mandatory:   true
    },
    {
      fieldName: "VAT_CODE",
      fieldType: "TEXT",
      fieldLength: 20,
      mandatory: Boolean.FALSE
    },
    {
      fieldName: "PRICE",
      fieldType: "DEC",
      fieldInt: 8,
      fieldDec: 2,
      decSeparator: ".",
      mandatory:   true
    },
    {
      fieldName: "SELL_NR",
      fieldType: "INT",
      fieldInt: 8,
      mandatory: Boolean.FALSE
    },
    {
      fieldName: "CREATE_DATE",
      fieldType: "DATE",
      dateFormat: "yyyy-MM-dd",
      mandatory: Boolean.FALSE
    },
    {
      fieldName: "LAST_UPDATE",
      fieldType: "DATETIME",
      dateFormat: "yyyy-MM-dd HH:mm",
      mandatory: Boolean.FALSE
    },
    {
      fieldName: "FLAG",
      fieldType: "TEXT",
      fieldLength: 1,
      enumeration: "Y,N",
      mandatory: Boolean.FALSE
    }
    ],
    commitSize: 10000
};

utils.mapClobFieldToTable(settings);
// Time spent to map 1M raws of a CSV content stored in a CLOB field of a table to another table: 112075ms

```



**3. validating data in terms of foreign keys, by using destination tables to check out values (FKs)**

```javascript
utils.validateCode(settings); // fill in FKs fields in the second border table, by validating CODEs in destination tables; this method must be called for each CODE field
```

where settings is a javascript object containing the following attributes

| Attribute name      | Description                                                                                                                                  |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| srcDatasourceId     | datasource id used to access the second** **border table (can be null: default schema)                                                       |
| fromTable           | x\_data second border table name (e.g. BT02\_ITEMS\_DATA)                                                                                    |
| fromTableFilter     | SQL filter (to add to WHERE clause) to filter input data (e.g. COMPANY='00000')                                                              |
| fromRowId           | field name in the border table containing the numeric ID and used to update a single record in the border table in case of validation errors |
| fromFieldCode       | field name in the border table containing the code to validate in the application table                                                      |
| fromFK              | field name where hosting the PK coming form the application table just validated by code (e.g. FK\_ART02)                                    |
| destDatasourceId    | datasource id used to access the ** **application table containing data to check out (can be null: default schema)                           |
| toTable             | application table name (e.g. ART02\_VATS)                                                                                                    |
| toTableFilter       | SQL filter (to add to WHERE clause) to filter the application table data (e.g. COMPANY='00000')                                              |
| toFieldCode         | field name in the application table, containing the code to check out, in order to validate  the one in the border table                     |
| toFK                | field name in the application table representing the PK to copy to the corresponding field in the border table (fromFK)                      |
| errorFieldName      | field name in the FIRST border table where saving validation errors fired when loading the second border table                               |
| commitSize          | in case of a very large result set, commit is essential after a block of data written; e.g. 1000                                             |
| containsErrorsField | field name in the FIRST border table used as flag Y/N, set to Y in case of validation errors fired when loading the second border table      |

```
TABLES:

create table ART02_BARCODES(
 PROG_ID NUMERIC(12) NOT NULL,
 COMPANY_ID CHAR(5) NOT NULL,
 BARCODE VARCHAR(255),
 PRIMARY KEY(PROG_ID)
) ;

create table ART03_VATS(
 PROG_ID NUMERIC(12) NOT NULL,
 COMPANY_ID CHAR(5) NOT NULL,
 VAT_CODE VARCHAR(20),
 VAT  NUMERIC(5),
 PRIMARY KEY(PROG_ID)
) ;

JAVASCRIPT:

var settings = {
    srcDatasourceId: null,
    fromTable: "BT02_ITEMS_data",
    fromTableFilter: "company_id='00000'",
    fromRowId: "ROW_ID",
    destDatasourceId: null,
    toTable: "ART03_VATS",
    toTableFilter: "company_id='00000'",
    errorFieldName: "ERRORS",
    containsErrorsField: "CONTAINS_ERRORS",
    fromFieldCode: "VAT_CODE",
    toFieldCode: "VAT_CODE",
    fromFK: "fk_art03",
    toFK: "prog_id",
    commitSize: 10000
};    
utils.validateCode(settings); // 2112ms + 20709ms
// Time spent to decode all records for field VAT_CODE in table BT02_ITEMS_data: 21586ms

var settings = {   
    srcDatasourceId: null,
    fromTable: "BT02_ITEMS_data",
    fromTableFilter: "company_id='00000'",
    fromRowId: "ROW_ID",
    destDatasourceId: null,
    toTable: "ART02_BARCODES",
    toTableFilter: "company_id='00000'",
    errorFieldName: "ERRORS",
    containsErrorsField: "CONTAINS_ERRORS",
    fromFieldCode: "BARCODE",
    toFieldCode: "BARCODE",
    fromFK: "fk_art02",
    toFK: "prog_id",
    commitSize: 10000
};
utils.validateCode(settings
// Time spent to decode all records for field BARCODE in table BT02_ITEMS_data: 31563ms
```



**4. loading data from the border table to destination tables**

```javascript
utils.copyDataFromBorderTable(settings); // load a destination table from the seocnd border table; this method must be called for each destination table
```

where settings is a javascript object containing the following attributes

| Attribute name      | Description                                                                                                                                                                                                                                                                                                                   |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| srcDataSourceId     | datasource id used to access the second** **border table (can be null: default schema)                                                                                                                                                                                                                                        |
| fromTable           |  second border table                                                                                                                                                                                                                                                                                                          |
| fromTableFilter     | SQL filter applied when reading records from the border table (e.g. COMPANY\_ID = '00000')                                                                                                                                                                                                                                    |
| operation           | <p>can be INSERT or UPDATE. </p><p>In case of INSERT, it is mandatory to fill in other attributes, like idAsxxx progIdxxx. </p><p>In case of UPDATE, the FK field in the border table representing the PK in the destination table must be already be retrieved. If it is not, use the validateCode method to fetch them.</p> |
| destDatasourceId    | datasource id used to access the destination table (can be null: default schema)                                                                                                                                                                                                                                              |
| toTable             | destination table name                                                                                                                                                                                                                                                                                                        |
| idAsNum             | optional field name (having numeric type) which must be created in the destination table and in the border table x\_data and used to host the numeric PK, generated automatically by Platform before copying data to the destination table, starting from an application table used for counters.                             |
| progIdValueField    | field in the application table for counters, used to maintain the current counter value (e.g. CURRENT\_VALUE)                                                                                                                                                                                                                 |
| progIdIncField      | field in the application table for counters, used to maintain the value to use to increment the counter (e.g. INCREMENT\_VALUE)                                                                                                                                                                                               |
| progIdTable         | application table used for counters                                                                                                                                                                                                                                                                                           |
| progIdWhere         | SQL filter to apply for updating/select a single record in the application table for counters                                                                                                                                                                                                                                 |
| idAsUuid            | optional field name (having string type) which must be created in the border table/destination tableand used to host a text UUID PK, generated automatically by Platform when copying data to the destination table                                                                                                           |
| defaultFields       | javascript object containing additional fields to fill in the destination table (e.g. CREATE\_DATE)                                                                                                                                                                                                                           |
| defaultInLineFields | javascript object used ONLY for UPDATEs, containing additional fields to fill in the destination table (e.g. _ROW\__VERSION_+1 for the field ROW\__VERSION)                                                                                                                                                                   |
| mappingFields       | javascript object containing field names in the border table to copy to the destination table. Field types must be identical                                                                                                                                                                                                  |
| commitSize          | in case of a very large result set, commit is essential after a block of data written; e.g. 1000                                                                                                                                                                                                                              |

```
TABLES:

create table INI22_COUNTERS(
 TABLE_NAME VARCHAR(20) NOT NULL,
 COLUMN_NAME VARCHAR(20) NOT NULL,
 CURRENT_VALUE NUMERIC(12),
 INCREMENT_VALUE  NUMERIC(12),
 PRIMARY KEY(TABLE_NAME,COLUMN_NAME)
);
insert into INI22_COUNTERS(TABLE_NAME,COLUMN_NAME,CURRENT_VALUE,INCREMENT_VALUE) VALUES('ART01_ITEMS','PROG_ID',1,1);

create table ART01_ITEMS(
 PROG_ID NUMERIC(12) NOT NULL,
 COMPANY_ID CHAR(5) NOT NULL,
 ITEM_CODE VARCHAR(20),
 DESCRIPTION VARCHAR(255),
 FK_ART02 NUMERIC(12),
 FK_ART03 NUMERIC(12),
 CREATE_DATE DATE,
 LAST_UPDATE DATETIME,
 PRIMARY KEY(PROG_ID)
);

JAVASCRIPT:

var settings = {  
    srcDatasourceId: null,
    fromTable: "BT02_ITEMS_data",
    fromTableFilter: "company_id='00000'",
    //fromFK: "FK_ART01"
    //errorFieldName: "ERRORS"
    //containsErrorsField: "CONTAINS_ERRORS"
    operation: "INSERT",
    destDatasourceId: null,
    toTable: "ART01_ITEMS",
    //toTableFilter: "PROG_ID=?"
    idAsNum: "PROG_ID",
    progIdValueField: "CURRENT_VALUE",
    progIdTable: "INI22_COUNTERS",
    progIdIncField: "INCREMENT_VALUE",
    progIdWhere: "TABLE_NAME='ART01_ITEMS' AND COLUMN_NAME='PROG_ID'",
    commitSize: 10000,
    defaultFields: {},
    defaultInLineFields: {},
    mappingFields: {
      COMPANY_ID: "COMPANY_ID",
      ITEM_CODE: "ITEM_CODE",
      DESCRIPTION: "DESCRIPTION",
      CREATE_DATE: "CREATE_DATE",
      LAST_UPDATE: "LAST_UPDATE",
      FK_ART02: "FK_ART02",
      FK_ART03: "FK_ART03"
    }
};
utils.copyDataFromBorderTable(settings);
// Time spent to copy data from table BT02_ITEMS_data to table ART01_ITEMS: 107694ms

```

Example of javascript to use to update data from the second border table to the destination table: PROG\_ID must be first reckoned and then used to update data.

```
var settings = { 
     fromTable: "BT02_ITEMS_data",
     fromTableFilter: "company_id='00000'",
     fromRowId: "ROW_ID",
     toTable: "ART01_ITEMS",
     toTableFilter: "company_id='00000'",
    //: errorFieldName: "ERRORS");
    //: containsErrorsField: "CONTAINS_ERRORS");
     fromFieldCode: "ITEM_CODE",
     toFieldCode: "ITEM_CODE",
     fromFK: "fk_art01",
     toFK: "prog_id",
     commitSize: 10000
};
utils.validateCode(settings); 
// Time spent to decode all records for field ITEM_CODE in table BT02_ITEMS_data: 18810ms


var settings = {
    fromTable: "BT02_ITEMS_data",
    fromTableFilter: "company_id='00000'",
    fromFK: "FK_ART01",
    //errorFieldName: "ERRORS");
    //containsErrorsField: "CONTAINS_ERRORS");
    operation: "UPDATE",
    toTable: "ART01_ITEMS",
    toTableFilter: "PROG_ID=?",
    commitSize: 10000,
    
    defaultFields: {},
    defaultInLineFields: {},
    mappingFields: {
        COMPANY_ID: "COMPANY_ID",
        ITEM_CODE: "ITEM_CODE",
        DESCRIPTION: "DESCRIPTION",
        CREATE_DATE: "CREATE_DATE",
        LAST_UPDATE: "LAST_UPDATE",
        FK_ART02: "FK_ART02",
        FK_ART03: "FK_ART03"
    }
};
utils.copyDataFromBorderTable(settings); 
// Time spent to copy data from table BT02_ITEMS_data to table ART01_ITEMS: 130631ms

```
