# Executing SQL statements from within an action

Inside a server-side javascript action it is possible to execute SQL statements \(SELECT, UPDATE, etc…\). In order to to that, the executeSQL instruction must be used.

In case of a SQL SELECT query \(for instance to feed a grid report\), the executeQuery method must be used instead:

```javascript
var vo_par = new Object();

/* Note that the parameters here are expressed in camel mode (COMPANY_ID -&gt; companyId) */
vo_par['par1']=vo['par1'];
vo_par['par2']=vo['par2'];


/* Encode the parameters array into a JSON string */
json = Ext.encode(vo_par);


var url = contextPath + '/executesql/executequery?appId=' + applicationId + '&amp;applicationId=' + applicationId + '&amp;compId=XXX';

var response = new SyncRequest().send(url, 'POST', json, 'application/json');

if (response != null &amp;&amp; response != '') {
 risp = Ext.decode(response);

 if (risp['success'] == false) {
 showMessageDialog("dialog.title.error", risp['message'], function() {}, false);
 return false;
 }else{
 /* here it is possible to scroll along the value objects list (which is an array) */
 var obj=risp.valueObjectList[0].NOME_TABELLA;
 }
}
```

In case of a SQL statement like INSERT, UPDATE, DELETE, the right method is executeSQL:

