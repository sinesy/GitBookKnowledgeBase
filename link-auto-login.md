# Link auto login

This explains how to  redirect a user into Platform and by-pass the login page,  starting from user credentials, i.e. company id, site id, username and password \(and language id\).

```text
var applicationId = "...";
var applicationTitle = "...";
var companyId = "...";
var siteId = "...";
var userId = "...";
var password = "...";
var languageId = "..."; // e.g. "it"

var backendUrl = "https://<yourhostandport>/<platformcontextpath>/";
backendUrl += 
  "/4ws/index_app.jsp?autoLogin=true&"+
  "applicationId="+applicationId+"&"+
  "appTitle="+applicationTitle+"&"+
  "companyId="+companyId+"&"+
  "siteId="+siteId; 

backendUrl += "&username=" + encodeURIComponent(userId); 
backendUrl += "&password=" + encodeURIComponent(password); 
backendUrl += "&languageId="+languageId+"&theme4ws=false&loginVersion=1&isIE=true"; 
//backendUrl += "&hiddenItemIds=companyId=00000,siteId=100";

```

It is up to you figure out how to fill in the variables described above.

For example, you can define and URL as the "backendUrl" variable defined above and be sure to dynamically include all required values.

In case you want to generate an URL to auto login, starting from a web portal, a safer solution is to use the  SSO token. This is described in the section about the SSO server settings. This feature is available starting from 5.x version of Platform.

