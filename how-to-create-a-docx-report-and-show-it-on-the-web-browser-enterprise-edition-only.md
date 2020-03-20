# How to create a docx report and show it on the web browser Enterprise Edition only

Once a docx template has been defined through the App Designer definition window, it is possible to create docx reports starting from that template. A docx can be generated and then stored on the file system, sent via email as an attachment or showed in the web browser as for any other kind of report. In the latter case, a javascript action must be defined and linked to some event, such as the click event of an ad hoc button. The following code shows an example of how to create the report on the server side and then show it on the browser:

```javascript
var json = "{....}";
var conn = new Ext.data.Connection({timeout: 300000});
conn.request({
 url: contextPath+"/generateDocxDocument?applicationId=XXX&amp;reportId=YYY&amp;langId="+languageId,
 method: "POST",
 jsonData: json,
 success: function(resp,opt) {
 var obj = Ext.decode(resp.responseText);
 var url = contextPath + "/showDocument?docId='+obj.docId; url += '&amp;applicationId=TEST&amp;title=Prova&amp;exportFormat="+"application/vnd.openxmlformats-officedocument.wordprocessingml.document";
 openPage(
 url,
 "ReportTitle",
 "width=800," +
 "height=600," +
 "toolbar=no," +
 "menubar=yes," +
 "left=0," +
 "top=0," +
 "location=no," +
 "resizable=yes"
 );
 },
 failure: function(resp,opt) {
 }
});
```

Basically, the report id must be specified, as well as a json string containing &lt;attribute,value&gt; entries; these values can be needed by the queries executed when generating the report as input values that filter the query results.

