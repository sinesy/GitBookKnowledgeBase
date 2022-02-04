# How to send to the UI a notification to execute code automatically

Starting from a server-side javascript action, it is possible not only to send alert messages to the client side, but also a notification not visible but which can be used to automate the execution of javascript code (available from 5.3.2 version).

The server-side utility method available through the **utils** object has the following syntax:

**sendJavascriptMessage(fromUser, javascriptObjectToPassForward, destinationUsers, javascriptFuntionName)**

having the following arguments:

* **fromUser -** the username who sends this notification
* **javascriptObjectToPassForward** - a js object containing attributes to pass forward to the client-side js function to invoke
* **destinationUsers** - a string containing a list of usernames (separated by a comma) identifying the destination users who would receive this notification
* **javascriptFuntionName** - a string value containing the name of a client-side js function, which must be available

**Impotant note**: In order to use this automation, you have to define at global level (e.g. by injecting a function into the **window** global object) on the client-side the js function you have specified in the **javascriptFuntionName** argument.

Example:

```javascript
utils.sendJavascriptMessage(
  "ADMIN",
  { pkOfSomeTable: "...", ... }, 
  username,
  "openMyWindow"
);
```

This solution can be helpful in scenarios where there is a complex and long task to execute, starting from the button pression on the UI. the client-side js action linked to the button event could include something like:

* server-side action invocation: such action should return immediately to the client, by enqueuing something and returning to the client some kind of uuid, related to the enqueue task
* inject to the window object a function having a specified name and arguments which will be used to execute some automation

The enqueued action should:

* execute its business logic
* invoke the sendJavascriptMessage and notify the client about its termination, by invoking the js function injected by the client-side action
