# return value

In case of a scheduled process based on a server side JS action, it is possible to pass to the scheduled the outcome using the setReturnValue function. The JSON string to pass to that function must contain the following attributes:

```javascript
var response={};

var returnValue;
response.exitCode=1;
response.exitMessage="ERRORE....";
returnResponse =utils.getJSONObject(response);
utils.setReturnValue(returnResponse);
//exit;
```

