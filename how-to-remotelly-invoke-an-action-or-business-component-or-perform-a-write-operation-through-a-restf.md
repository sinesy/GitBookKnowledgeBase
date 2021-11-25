# How to remotelly invoke an action or business component or perform a write operation through a Restf

It is possible to invoke Platform web services in three alternative ways:

* by invoking directly a specific web service; in that case credentials must be provided directly in the request.This is the simplest case, when you do not need to execute multiple requests; in this case, remember also to include as a parameter removeSession=true, in order to invalidate the session just after its usage
* by using an authentication token, previously provided and generated internally to Platform; this token must be included in any request through the parameter authToken;remember also to execute a logoutapp request too, at the end of the whole processing sequence, in order to invalidate the session when it is no longer needed
* by logging on to Platform, through the login web service (using either GET or POST methods), which gets back an authentication token, valid only for that session and to include in any request, through the parameter restfulToken;remember also to execute a logoutapp request too, at the end of the whole processing sequence, in order to invalidate the session when it is no longer needed

In the following sections, all these cases are reported.

The simplest way to invoke a single request is by calling it directly, and passing the credentials along with the other parameters required by the specific web service to invoke.

## Example

```javascript
http://localhost:8080/platform/getlist?applicationId=...&amp;functionId=utenti_abilitati.gridReportPanel889&amp;compId=809&amp;panelId=889&amp;companyId=...&amp;siteId=...&amp;=...&amp;username=...password=...&amp;languageId=....&amp;removeSession=true
```

The last parameter force the removal of any session content involved with this single request. Pay attention that this solution is not the best in case you have to execute the same request multiple times: it would be better in that case, to perform the login first, get an authentication token and use it for the next requests. This solution is described below. Another optional parameter is onlyLogin=true which by-pass the loading of any additional data after the authentication step: that will speed up the response of the web service, which will be a stateless web service. On the other hand, no additional data will be loaded and consequently it is up to the js code within the action to fetch any data needed for the correct execution of the web service.

Platform allows to create tokens dynamically; a token can then be included in a Restful request to access Platform functionalities. Tokens generation can be performed from within a server-side js action (see that section). This can be helpful for example in case of single requests coming from an email message or from a button pressed in another application: a request should be generated, using a token previously generated within platform. Once got a token, it is possible to use it starting from an external application, when a plain URL can be invoked.

## Example

```javascript
http://localhost:8080/platform/getlist?applicationId=...&amp;functionId=utenti_abilitati.gridReportPanel889&amp;compId=809&amp;panelId=889&amp;authToken=...&amp;username=...&amp;languageId=...&amp;companyId=...&amp;siteId=...
```

A better way to send such a request is by using HTTPS instead of HTTP: in this case the request headers and body are encripted. Moreover, it is a good idea to send the credentials not as request parameters (like in the example above), but by passing them in the request header, so that they will be encrypted when using the HTTPS protocol. In this case, theURI must be something like: [http://localhost:8280/wag/login/loginPostHeader?appId=\&#8230;\&amp;applicationId=\&#8230;\&amp;needTempToken=1\&amp;username=\&#8230](http://localhost:8280/wag/login/loginPostHeader?appId=\&#8230;\&amp;applicationId=\&#8230;\&amp;needTempToken=1\&amp;username=\&#8230); whereas on the request headers you have to include the following parameters:

* companyId
* siteId
* username
* password
* languageId

Another way to get a token is to perform the authentication before invoking the service. An example of Restful authentication is:

```javascript
http://localhost:8080/platform/login?username=...&amp;password=...&amp;companyId=...&amp;siteId=100&amp;applicationId=...&amp;appId=...&amp;needTempToken=1
```

Again, a better way to send such a request is by using HTTPS instead of HTTP: in this case the request headers and body are encripted. Moreover, it is a good idea to send the credentials not as request parameters (like in the example above), but by passing them in the request header, so that they will be encrypted when using the HTTPS protocol.

An example of an action invocation to perform after a successful authentication is:

```javascript
http://localhost:8080/platform/executeJs?applicationId=...&amp;actionId=...&amp;restfulToken=...&amp;companyId=...&amp;siteId=...&amp;username=...
```

If the authentication task completes successfully, the response expressed in JSON format would contain the token to include in any Restful request sent from this time, as for the one reported above. Successful response is like the one reported as follow:

```javascript
{"message":"","siteId":XXX,"companyId":"YYYY","success":true,"token":"ZZZZZZ"}
```

where the value of the attribute “token” is the one to pass in any request.

In case of errors when trying to authenticate, a JSON error response is sent back. This is the list of possible JSON responses:

* { “success”: false, “message”: “Reached maximum number of concurrent users for this licence”, “errorCode”: “MAX\_NR\_OF\_USERS” }
* { “success”: false, “message”: “Invalid credentials”, “errorCode”: “INVALID\_CREDENTIALS” }
* { “success”: false, “message”: “Password expired”, “errorCode”: “PASSWORD\_EXPIRED” }
* { “success”: false, “message”: “Account locked”, “errorCode”: “ACCOUNT\_LOCKED” }
* { “success”: false, “message”: “Max number of requests reached”, “errorCode”: “MAX\_NR\_OF\_REQUESTS\_REACHED” }
* { "success": false, "message": "Exceeded maximum number of requests for user \<XXX> for \<ALIAS>", "errorCode":"EXCEEDED\_REQUEST\_USER\_MINUTE" }
* { “success”: false, “message”: “Token expired”, “errorCode”: “TOKEN\_EXPIRED” }
* { “success”: false, “message”: “Internal error”, “errorCode”: “INTERNAL\_ERROR” }

The session is maintained active typically for 10 minutes, without receiving other requests (which can be configured on the web.xml file of the web application). Additional requests will extend the timeout. If needed, you can create on your external client a thread to maintain the session active, through the “keepAlive” command. The following request is provided for that usage:

```javascript
http://localhost:8080/platform/keepAlive?applicationId=...&amp;restfulToken=...&amp;companyId=...&amp;siteId=...&amp;username=...
```

In any case, you’d better also execute a second request, used to inform Platform that the session is concluded and the force the log out from the application: in this way, any session content is removed from the server. Platform automatically removes this sessions after a timeout, but if you forget to perform the log out request and execute the same request a large number of times, there is the risk that the sessions will be removed after many minutes and before that time, the server’s memory could overflow the maximum value, is it is always a good idea to call the log out request too:

```javascript
http://localhost:8080/platform/logoutapp?applicationId=...&amp;restfulToken=...
```
