# How to write text or csv files

Platform provides 2 server-side javascript methods to write a text file, with data coming from a database: one can be applied for CSV formatted files and the other more generic, related to a text file.

In both cases, the most important thing is avoiding the reading of a long result set coming from the execution of a SQL query in a database: data must be read one line a time and processed as it is read.

In order to do it, a special method to read a long result set must be used: utils.executeQueryWithCallback

This method requires the definition of a javascript callback function, which will be automatically invoked by Platform for each record read from the result set: this callback must be used to save each line in the text file. It goes without saying that the text file must be opened before executing the SQL query and manually closed when the result set has been read completely.

To sum up, this must be the right sequence to follow:

* opening the text file
* executing the SQL query
* for each callback invocation: save a line
* the result set has been read completely: close the text file

In the following sections, two examples are reported: one for writing a CSV file, the other is more generic.

## Writing a CSV file

```javascript
// open the CSV file, in order to write into it
var fileId = utils.openCSVFile(
    "ab.csv",true,9, ",",true,
    ["userCodeId","password","rowVersion","dateExpirationPassword"],
    [null,null,null,null]
);

// declare a callback function, invoked by a SQL query, used to write a single line into the CSV file
var processRow = function(jsonRow) {
    utils.writeToCSVFile(fileId,jsonRow);
}

// execute the SQL query, whose result set will be read row by row, by invoking each time the specified callback
utils.executeQueryWithCallback(
    "processRow", // callback function, invoked for each record coming from the query, whose argument will receive a JSON object representing the record
    "SELECT U.USER_CODE_ID,U.DESCRIPTION,U.PASSWORD,U.DATE_EXPIRATION_PASSWORD,U.ROW_VERSION FROM PRM01_USERS U",
    null,
    false,
    true,
    []
);

// close the CSV file, after reading all records from the query
utils.closeCSVFile(fileId);
```

See [https://4wsplatform.gitbooks.io/api/content/File.html](https://4wsplatform.gitbooks.io/api/content/File.html) for more details about the meaning of each argument.

## Writing a text file

```javascript
// open the text file, in order to write into it
var fileId = utils.openTextFile(
    "abc.txt",true,9, true
);

// declare a callback function, invoked by a SQL query, used to write a single line into the text file
var processRow = function(jsonRow) {
    var row = jsonRow.userCodeId+","+jsonRow.description+"\r\n"; // or any other custom logic to use to produce a text content
    utils.writeToTextFile(fileId,row);
}

// execute the SQL query, whose result set will be read row by row, by invoking each time the specified callback
utils.executeQueryWithCallback(
    "processRow", // callback function, invoked for each record coming from the query, whose argument will receive a JSON object representing the record
    "SELECT U.USER_CODE_ID,U.DESCRIPTION,U.PASSWORD,U.DATE_EXPIRATION_PASSWORD,U.ROW_VERSION FROM PRM01_USERS U",
    null,
    false,
    true,
    []
);
```

See [https://4wsplatform.gitbooks.io/api/content/File.html](https://4wsplatform.gitbooks.io/api/content/File.html) for more details about the meaning of each argument.

## Optimizing writing a csv file, starting from a SQL query

```javascript
var fileName = "..."; // file name for the csv file to create will be saved
var overwrite = true; // overwrite a previous version of the csv file having the same name
var directoryId = ...; // directory id where saving the csv file
var languageId = ".."; // e.g. "IT"; used to format dates and numbers according to the language
var printTitles = true; // add a first row containing labels for each column, whose name is like the SELECT's fields
var fileAppend = false; // define whether the csv file must append lines to an already existing file
var formatColumns = null; // if specified, it must be a javascript Array containing formatters for the data read from the SQL query
// formatters depend on the data type: for a number can be "0.00" whereas for a date can be "yyyyMMdd"

var errorMsg = utils.createCSVFileFromSQLQuery(
   fileName, 
   overwrite, 
   directoryId, 
   ";", // fields separator: , or ;
   languageId, 
   printTitles, 
   fileAppend, 
   formatColumns,
   null, // datasource Id
   "SELECT FIELD1,FIELD2,... FROM ... WHERE...", // SQL query to execute
   [...] // parameters for the bind variables
);
```

