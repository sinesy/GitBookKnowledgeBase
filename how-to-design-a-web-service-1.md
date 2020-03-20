# How to design a web service

Platform allows you to create web services which can be invoked externally. A web service is implemented through a server-side js action. In order to access it, an authentication task is needed: only when the authentication step passessuccessfully, the server-side js action is called. The default behavior of a Platform web service is to load also parameters and authorizations connected to the logged user. That means that an overhead is required to fetch additional information and several seconds are spent for that activity. You can by-pass this additional task through the “onlyLogin” parameter \(see How to remotelly invoke an action\) which reducesthat overhead to the minimum. That means the invoked web service will act as a stateless web service. Every additional data required by the web service must be passed as an input parameter or fetched internally to the service. The perk of this solution is that the web service will be very fast. The drawback is that additional data needed by the web service to work must be included within the web service and this will reduce its performance. A good web service design passes through the use of stateless web services, combined with the data retrieval of additional information, stored in a cache, so that the additional data is retrieved only once and then stored in such a cache, whichcan be accessed any number of times in the future, to speed up the data retrieval. Platform provides a global mem-cache, composed of a setof key-value couples. You can store up to 1000 different entries. That limit is needed to ensure thatthe cache will not consume too much memory. The problem with a cache is the memory consumption: a better performance is achieved through a higher consumption of memory and this usage must be carefully managed. Platform mem-cache must be managed programmatically, through a series of js methods available within the server-side js action:

* utils.addValueInCache\(String varName,Object value\) –Add a value to the shared web cache, with no expiration time for that variable
* utils.addValueInCache\(String varName,Object value,Long expirationTime\) –Add a value to the shared web cache. Optionally, specify an expiration time for that variable, expressed in minutes. In this way, the content of the cache will reduce automatically

  utils.removeValueInCache\(String varName\) – Remove an entry from the cache 

var value = utils.getValueInCache\(String varName\)– Read a valore from the cache. If the variable is stored in cache, it returns the corresponding value, otherwise an exception will be fired var value = utils.getValueInCache\(String varName,String alternativeFunctionName\) –Fetch a value from the shared web cache. In case the specified variable name is not found in cache, then execute the js function and get back the returning value, which is also stored in cache. The alternativeFunctionName must be a string value, not directly the function \(see the example below\) var value = utils.getValueInCache\(String varName,String alternativeFunctionName,Long expirationTime\) –Fetch a value from the shared web cache. In case the specified variable name is not found in cache, then execute the js function and get back the returning value, which is also stored in cache. The alternativeFunctionName must be a string value, not directly the function \(see the example below\). An expiration time is also specified

Important note: it is up to you ensuring the correct contentof the cache. For instance, if an application parameter is changed externally, the cache is not able to figure it out and it could contain not up-to-date content. This is not a wrong behavior of the cache: you have to take into account this chance and apply the correct management for such event, such as setting an expiration time for every data fetched and stored in the cache.

This is an example of how to use the cache:

```javascript
var getParam1 = function() {
  return utils.getParameter("PARAM1");
}

var param1 = utils.getValueInCache("P1","getParam1",60);
/* a param P1 is stored in cache, initially set with the value of the PARAM1 parameter, fetched as a parameter defined externally (global or application level parameter). An expiration time is set to one hour */

// now I can use param1...
```

