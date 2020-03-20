# How to read a text or csv file and save data on the database

Platform provides 2 distinct utility methods in a server-side javascript action to read a text file:

* read a CSV file
* read a generic text file, whose content is not clearly delimited by a special symbol \(like for a CSV file\)

In both cases, the best approach is reading the file content line by line: in this way, there will not be a memory consumption due to the file size, since only one line is read a time.

In order to do it, a callback function must be defined: it will be automatically invoked by the utility method for each line and the whole line will be passed forward to such a callback function.

Within this callback function it is possible to save data in the database. Again, there are different ways to do it, in order to manage whether the writing operation must be an INSERT or an UPDATE.

One way to do it, is by executing first a SELECT, in order to figure out if the record already exists; after that, an INSERT or UPDATE operation can be carried out.

## Reading a CSV file

Example: suppose you have a comma \(,\) separated CSV file having 3 fields: persone code, first name, last name.

```javascript
var myCallbackFunction = function(obj) { 
  // obj is a javascript object containing a line read from the CSV file, having the structure:
  // { personCode: "..", firstName: "...", lastName: "..." }

  var json = utils.executeQuery(
    "SELECT PERSON_CODE FROM PEOPLE WHERE PERSON_CODE=? ",
    null,
    false,
    true,
    [obj.personCode]
  );
  var list = JSON.parse(json); // here list is a zero length list in case the specified personCode does not exist in the db

  if (list.length==0) {
    // record does not exist yet: insert it
    utils.executeSql(
      "INSERT INTO PEOPLE(PERSON_CODE,FIRSTNAME,LASTNAME) VALUES(?,?,?) ",
      null, 
      false, 
      true,
      [ obj.personCode, obj.firstName, obj.lastName ]
    );
  }
  else {
    // update record
    utils.executeSql(
      "update PEOPLE set FIRSTNAME=?,LASTNAME=? WHERE PERSON_CODE=? ",
      null, 
      false, 
      true,
      [ obj.firstName, obj.lastName, obj.personCode ]
    );
  }
}

var attributeNames = ["personCode","firstName","lastName"]; 
var directoryId = ... // id for a directory defined in the AppDesigner
var fileName = "..."; // file name for the CSV file saved in the path specified by directory id

utils.readCSVFile(
  fileName,
  directoryId,
  ",", // fields separator: , or ;
  false, // flag used to skip the first line in the CSV file
  "myCallbackFunction",
  attributeNames
);
```

## Reading a text file

Example: suppose you have a text file having 3 fixed length fields: 10 characters for a code, 20 characters for the first name, 20 characters for the last name.

```javascript
var myCallbackFunction = function(line) { 
  // line is a javascript String containing the whole line just read
  var personCode = line.substring(0,10);
  var firstName = line.substring(10,30);
  var lastName = line.substring(30);

  var json = utils.executeQuery(
    "SELECT PERSON_CODE FROM PEOPLE WHERE PERSON_CODE=? ",
    null, // data source id
    false, // separatedTransaction
    true, // interruptExecution
    [personCode]
  );
  var list = JSON.parse(json); // here list is a zero length list in case the specified personCode does not exist in the db

  if (list.length==0) {
    // record does not exist yet: insert it
    utils.executeSql(
      "INSERT INTO PEOPLE(PERSON_CODE,FIRSTNAME,LASTNAME) VALUES(?,?,?) ",
      null, // data source id
      false, // separatedTransaction
      true, // interruptExecution
      [ personCode, firstName, lastName ]
    );
  }
  else {
    // update record
    utils.executeSql(
      "update PEOPLE set FIRSTNAME=?,LASTNAME=? WHERE PERSON_CODE=? ",
     null, // data source id
      false, // separatedTransaction
      true, // interruptExecution  
      [ firstName, lastName, personCode ]
    );
  }
}

var directoryId = ... // id for a directory defined in the AppDesigner
var fileName = "..."; // file name for the CSV file saved in the path specified by directory id

utils.readTextFile(String fileName,Long directoryId,String callbackFunName,String charset)
  fileName,
  directoryId,
  "myCallbackFunction",
  "UTF-8" // char-set for the text file to read
);
```

## How to write data in a database

Both examples above report a way to write data in a database: using SQL statements through the utils.executeSql utility method.

An alternative way to do it is by defining a Data Model in the App Designer for the table where writing and then use the utils.insertObject or utils.updateObject methods to automate the SQL statement generation.

Example: suppose there is a table named PEOPLE, having 5 fields: PERSON\_CODE, FIRST\_NAME, LAST\_NAME, ENABLED and CREATE\_DATE \(having datetime field type\).

The callback function for the first example \(CSV file\) would be:

```javascript
var myCallbackFunction = function(obj) { 

  var json = utils.executeQuery(
    "SELECT PERSON_CODE FROM PEOPLE WHERE PERSON_CODE=? ",
    null, // data source id
    false, // separatedTransaction
    true, // interruptExecution
    [obj.personCode]
  );
  var list = JSON.parse(json); // here list is a zero length list in case the specified personCode does not exist in the db

  obj.enabled: "Y",
  obj.createDate: utils.getCurrentDateAndTime();

  var dataModelId = ... // data model id related to the table PEOPLE

  if (list.length==0) {
    // record does not exist yet: insert it
    utils.insertObject(
      obj,
      "PEOPLE",
      null, // data source id
      false, // separatedTransaction
      true // interruptExecution
    );
  }
  else {
    // update record
    utils.updateObject(
      obj,
      "PEOPLE",
      null, // data source id
      false, // separatedTransaction
      true // interruptExecution
    );
  }
}
```

The callback function for the second example \(text file\) would be:

```javascript
var myCallbackFunction = function(line) { 
  // line is a javascript String containing the whole line just read
  var personCode = line.substring(0,10);
  var firstName = line.substring(10,30);
  var lastName = line.substring(30);

  var json = utils.executeQuery(
    "SELECT PERSON_CODE FROM PEOPLE WHERE PERSON_CODE=? ",
    null, // data source id
    false, // separatedTransaction
    true, // interruptExecution
    [personCode]
  );
  var list = JSON.parse(json); // here list is a zero length list in case the specified personCode does not exist in the db

  var obj = {
    personCode: personCode,
    firstName: firstName,
    lastName: lastName,
    enabled: "Y",
    createDate: utils.getCurrentDateAndTime()
  };

  var dataModelId = ... // data model id related to the table PEOPLE

  if (list.length==0) {
    // record does not exist yet: insert it
    utils.insertObject(
      obj,
      "PEOPLE",
      null, // data source id
      false, // separatedTransaction
      true // interruptExecution
    );
  }
  else {
    // update record
    utils.updateObject(
      obj,
      "PEOPLE",
      null, // data source id
      false, // separatedTransaction
      true // interruptExecution
    );
  }
}
```

In a more complex scenario, input data can be so large that an approach like the one reported above would be fairly heavy for the database, since a writing operation is always performed, for each input file, even when there is no line to update.

Let's take the scenario where there is 1 million lines in input with 1% of INSERTs \(10.000 new records in the database\) and only 10% of input lines to update \(100.000 records\), since all the others have not really changed. There would be 890.000 useless UPDATE operations carried out!

A way to avoid such a large useless updates is by adding in the table an additional text field \(e.g. 255 length\) containing an MD5 signature representing the whole line/record content. It would be possible to check it with the corresponding input line and avoid the update operation when the MD5 for the input line is the same as the MD5 for the already saved record in the table.

Example: suppose you have an MD5 text field in the table and a CSV input file with the same 3 fields as above:

```javascript
var myCallbackFunction = function(obj) { 
  // obj is a javascript object containing a line read from the CSV file, having the structure:
  // { personCode: "..", firstName: "...", lastName: "..." }

  var inputMd5 = utils.md5(obj.personCode+"_"+obj.firstName+"_"+obj.lastName);

  var json = utils.executeQuery(
    "SELECT MD5 FROM PEOPLE WHERE PERSON_CODE=? ",
    null,
    false,
    true,
    [obj.personCode]
  );
  var list = JSON.parse(json); // here list is a zero length list in case the specified personCode does not exist in the db

  if (list.length==0) {
    // record does not exist yet: insert it
    utils.executeSql(
      "INSERT INTO PEOPLE(PERSON_CODE,FIRSTNAME,LASTNAME,MD5) VALUES(?,?,?,?) ",
      null, 
      false, 
      true,
      [ obj.personCode, obj.firstName, obj.lastName, inputMD5 ]
    );
  }
  else if (list[0].md5==null || list[0].md5!=inputMD5) {
    // update record
    utils.executeSql(
      "update PEOPLE set FIRSTNAME=?,LASTNAME=?,MD5=? WHERE PERSON_CODE=? ",
      null, 
      false, 
      true,
      [ obj.firstName, obj.lastName, inputMD5, obj.personCode ]
    );
  }
  else {
    // do nothing, since the MD5 is the same: no data changed!
  }
}

var attributeNames = ["personCode","firstName","lastName"]; 
var directoryId = ... // id for a directory defined in the AppDesigner
var fileName = "..."; // file name for the CSV file saved in the path specified by directory id

utils.readCSVFile(
  fileName,
  directoryId,
  ",", // fields separator: , or ;
  false, // flag used to skip the first line in the CSV file
  "myCallbackFunction",
  attributeNames
);
```

## Optimizing csv reading and data loading on a single table

The best approach to read a csv file and save its content in a single table, is using the ad hoc method provided by Platform.

Example:

```javascript
 var fileName = "..."; // file name
 var dirId = ...; // directory id where the file is located
 var tableName = "..."; // table where saving the csv content
 var defaultFieldNames = {
    CREATE_USER: userInfo.username,
    CREATE_DATE: utils.getCurrentDateAndTime()
 };
 var csvFields = [
   { fieldName: "personCode" },
   { fieldName: "firstName" },
   { fieldName: "lastName" },
   { fieldName: "birthDate", convertToSqlDate: "yyyy-MM-dd" }, 
   { fieldName: "lastPurchaseTime", convertToSqlTimestamp: "yyyy-MM-dd HH:mm:ss"} 
 ];

 var processedRows = utils.readCSVFileAndWriteToTable( 
  fileName,
  directoryId,
  ";", // separator: , or ;
  false, // skip first row in the csv file
  null, // datastore Id
  tableName,
  defaultFieldNames,
  csvFields
 );
```

