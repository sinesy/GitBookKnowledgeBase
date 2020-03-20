# How to invoke a generic SQL query defined through a business component

You can invoke a SQL statement defined in a business component starting from an action expressed in javascript: the js code will be sued to create an HTTP request that will invoke that SQL on the server side; it is possible to pass parameters to that SQL, as well. Suppose you have a SQL statement having variables:

```javascript
UPDATE TABLE SET FIELD1=VALUE WHERE FIELD2 = :VAR_NAME
```

In that case, you can pass to the HTTP request a corresponding parameter, expressed in "camel" form, for each variable included in the SQL statement; for the example reported above, you should pass a parameter having name varName. This is an example of a typical js scriptlet to write in order to call the SQL statement:

```javascript
var url = contextPath+"/executesql/executequery?appId=XXX&amp;applicationId=XXX&amp;compId=YYY";

vo[‘varName'] = ...;

var json = Ext.encode(vo);

var response = new SyncRequest().send(url,'POST',json,'application/json');

if (response!=null &amp;&amp; response!='') {

var risp = Ext.decode(response);

}
```

Notes:

* XXX is the code of your application
* YYY is the id of the business component
* vo is automatically injected in any js action linked to events of type beforeXXX or afterXXX \(see tables in previous paragraphs\).

If you link this action to other events, then you should declare a local variable "vo" before referring it:

```javascript
var vo = new Object();
```

