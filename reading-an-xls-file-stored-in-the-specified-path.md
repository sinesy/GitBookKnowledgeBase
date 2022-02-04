# Reading an xls file stored in the specified path

Read up to 10000 rows x 1000 columns from the xls file stored in the specified path and get back the content of a specific folder

Both xls (Excel 97 format) and xlsx are supported.

Example:

```javascript
var dirId = ... // directory id where the xls file is located
var fileName = "abc.xls"; // xls file to read
var sheetIndex = 0; // which sheet within the xls spreadsheet to read: sheets are identified starting from 0, 1, 2, ..
var fromRow = 0; // initial line within the sheet containing the first row to read
var attributeNames = ["attrName1", "attrName2",... ]; 
// data is read from column A upward, one line a time;
// for each line, a javascript object will be created and 
// for each column in the line, a corresponding attribute will be set for the object containing the cell value

var list = utils.getXlsContentWithEncoding(
 dirId,
 fileName,
 sheetIndex,
 fromRow,
 attributeNames
);
// at the end, list is a javascript Array containing javascript objects, one for each row read from the xls file
```

**Important note:** values stored in each javascript object can be accessed as: object.get("attributeName")

**Important note:** do not try to read a large amount of rows from a spreadsheet; xls files are not a good format to save data, since it consumes a lot of memory; it would ALWAYS be better to read a csv file instead.
