# How to invoke a generic SQL statement defined through a SQL action

You can invoke a SQL statement defined through a SQL action, starting from an action expressed in javascript: the js code will be sued to create an HTTP request that will invoke that SQL on the server side; it is possible to pass parameters to that SQL, as well. Suppose you have a SQL statement having variables:

```javascript
UPDATE TABLE SET FIELD1=VALUE WHERE FIELD2 = :VAR_NAME
```

In that case, you can pass to the HTTP request a corresponding parameter, expressed in "camel" form, for each variable included in the SQL statement; for the example reported above, you should pass a parameter having name varName. This is an example of a typical js scriptlet to write in order to call the SQL statement:

```javascript
vo[‘varName'] = ...;



var json = Ext.encode(vo);

var url = contextPath+"/executesql?applicationId=XXX&amp;actionId=YYY";

var jsonResponse = new SyncRequest().send(url,'POST',json,'application/json');

var res = JSON.parse(jsonResponse);

if (!res.success) {

    showMessageDialog("dialog.title.error",res.message,function() {},false);

    return;

}
```

Notes: XXX is the code of your application YYY is the id of the SQL action vo is automatically injected in any JS action linked to events of type beforeXXX or afterXXX \(see tables in previous paragraphs\). If you link this action to other events, then you should declare a local variable "vo" before referring it:

```javascript
var vo = new Object();
```

Note: in case of an invocation of an Oracle Stored Procedure, you can define an action of type SQL and specify a statement like this one:

```javascript
CALL PROCEDURE_NAME(arg1,arg2…)
```

This can be invoked from a javascript action in the way described above. You cannot invoke Oracle Stored Functions in the same way, since the Oracle syntax required to invoke functions is not based on the CALL statement; in this case, you have to write a statement similar to this one:

```javascript
SELECT FUNCTION_NAME(arg1,arg2,...) FROM DUAL
```

In order to get on the client side the value returned by the function, it does not make sense to invoke it using "executesql" web service, since it does not return values; you have to use "/executesql/executequery", which returns a value back, having a JSON format similar to:

```javascript
{

    "valueObjectList": [

        { "FUNCTION_NAME(...)": }
    ],

    "resultSetLength": 1

}
```

