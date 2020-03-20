# How to send an email

In order to send email messages, you have first to set up the SMTP connection; some parameters are needed to do that and must be defined as global parameters. To set parameters go to Applications &gt; Parameters from the main menu. Parameters:

* SMTP Host
* \(optional\) SMTP port \(e.g. 25\)
* \(optional\) SMTP protocol \(e.g. smtp\)
* \(optional\) SMTP Username
* \(optional\) SMTP Password

After you have defined these settings, it is possible to send emails starting from a javascript action:

```javascript
var url = contextPath+"/sendEmail?appId=XXX&amp;applicationId=XXX";

var vo = new Object();

vo['from'] = 'x@y';

vo['to'] = 'z@t,';

vo['cc'] = '...';

vo['bcc'] = ...;

vo['message'] = 'abc';

vo['mimeType'] = 'text/html'; // "text/plain"

vo['returnReceipt'] = 'true';

var json = Ext.encode(vo);

var response = new SyncRequest().send(url,'POST',json,'application/json');

if (response!=null &amp;&amp; response!='') {

risp = Ext.decode(response);

}
```

Note:

* The XXX parameter in the URL is the code of your application

