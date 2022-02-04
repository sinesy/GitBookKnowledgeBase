# How to manage encrypted fields

Platform can manage text type fields as **encrypted fields.** In order to set them, you have to change the default field type for a data model field, from its original value (text) to the new one: Encrypted Text.

When saving a record containing an encrypted field, these fields are encrypted along with the rest of the record, just before saving them.

Writing operations are **automatically performed by Platform, when using the standard insert/update operations** in a **grid** or **form**.

When the writing operation is carried out manually, without using the standard web services, i.e.within a server-side javascript action, it is up to the programmer to use the **utils.encodePassword** method.

**Important note**: when designing the database table hosting the encrypted value, bear in mind that the encryption algorithm can convert the original text in a new text having a longer length. Consequently, **do not let the end user to fill in the whole field length**, since it could be not possibile to save all of it, when encrypted (e.g. if a field is 255 chars length, it would be better to let the user to fill in up to 200 chars).

**Important note**: if you need to encrypted non text values, like dates or numbers, please note that they are always converted to a text, since the encryption algorithm always save data as a text. Consequently, **you have to design your database with text type fields for dates/numbers**, if you want to save them as encrypted data.

Reading operations of encrypted data is **automatically managed by Platform, when using the standard business components for grid or form with SQL**. It means that Platform would read encrypted data form the table, decrypt it and pass plain text to the UI.

If you do not use the standard Platform components, it is up to you to decrypt data, in a javascript business component of server-side javascript action, using the **utils.decodePassword** method.

## Filtering encrypted codes

Once data has been encrypted, you cannot filter part of it, since the whole text has been converted in something completely different.

Consequently, the **only filtering operator you can still apply is the equal**, since you can only search for **exact (case-sensitive) matching** between the search pattern you wrote and te whole value saved encrypted in the database table.

For example, if you have a CLIENTS table containing a CLIENT\_CODE encrypted field, you have to:

* change the operator from "Like" to "Equals" for the encrypted CLIENT\_CODE field in the filter panel
* activate the "Uppercase" flag for the encrypted CLIENT\_CODE field in the filter panel
* save CLIENT\_CODE in grid/form always in upper case (i.e. activate the "Uppercase" flag for the encrypted CLIENT\_CODE field in the gird/form panel where it is allowed to save data)

In this way, the filter field would allow to specify the whole code, always expressed in upper case and Platform behind the scenes would encrypt that value when searching data and can successfully compare the value with the ones saved in the table.

A wrong setting would be to apply a "like" operator or case-insensitive settings: it would not work, since it is impossible to search for part of a text, if it has been encrypted.

## Filtering encrypted data

Once data has been encrypted, you cannot filter part of it, since the whole text has been converted in something completely different.

Consequently, the **only filtering operator you can still apply is the equal**, since you can only search for **exact (case-sensitive) matching** between the search pattern you wrote and te whole value saved encrypted in the database table.

A more complex scenario is when the encrypted text to search for is not a code, but something more complex like a corporate name, when not all text is specified (no exact matching) and the search condition is case-insensitive.

Such a scenario, as reported above, is not possible, since the original text has been replaced by an encrypted one which cannot be compared only in part, but only as it is: the whole case-sensitive text.

A workaround which can be applied in such a scenario is **adding a second text plain field, which contains the first part of the encrypted text in uppercase**. For example, if you have a CLIENTS table containing a CORPORATE\_NAME encrypted field, you have to:

* add another text field PLAIN\_CORPORATE\_NAME in the database table, hosting for example the first two letters (in uppercase) of the original corporate name, saved in plain text (no encrypted)
* use "before saving on insert/update" event in the grid/form used to save data and link a client-side javascript action, whose purpose is to automatically fill in the PLAIN\_CORPORATE\_NAME hidden field, starting from the CORPORATE\_NAME value: only the first two letters are saved and in uppercase; in this way, PLAIN\_CORPORATE\_NAME and CORPORATE\_NAME are always aligned with each other
* show a filter field for PLAIN\_CORPORATE\_NAME and let the "Like" operator for it
* activate the "Uppercase" flag for the PLAIN\_CORPORATE\_NAME field in the filter panel
* add a "before search" event where replacing the value provided by the end user and cut it to up to two characters.

In this way, the filter field would allow to specify the whole text, part of it, two characters only or less than that: the "before search action"  always cut that pattern to up to 2 characters expressed in upper case and Platform behind the scenes would search data in the plain text field instead of the original one.

Example of "before saving on insert/update" action content for a grid:

```
var sels = grid9.getSelectionModel().getSelections(); // get all changed rows
if (sels!=null) {
    for(var i=0;i<sels.length;i++) { 
        // for each row, fill in the "plainCorporateName" field
        // starting from the "corporateName" value provided by the end user
        var sel = sels[i];
        var descr = sel.get('plainCorporate');
        var len = descr.length;
        if (len>2)
          len = 2;
        sel.set("plainCorporateName",descr.substring(0,len).toUpperCase());        
    }
}
```

Example of "before saving on insert/update" action content:

```
var value = getFilterValue("plainCorporateName");
if (value!=null) {
  if (value.length>2)
    value = value.substring(0,2);
  changeFilter("plainCorporateName","like",true,value);
}
```

Here two utility methods are used:&#x20;

* **getFilterValue(filterName)** - it returns the value of the filter field identified by "filterName"; it returns null if not found/not filled
* **changeFilter(filterName, filterOp, filterCaseSensitive, filterValue)** - it replaces the filtering conditions for "filterName" (if found and not empty), by replacing them with the new values provided as argument: filterOp, filterCaseSensitive, filterValue

