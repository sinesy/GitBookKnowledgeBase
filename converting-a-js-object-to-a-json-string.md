# converting a JS object to a JSON string

In order to convert a JS object to a JSON string, there are several alternative methods available within a server side JS action: The easiest one:

```javascript
var JSONString = JSON.stringify(jsonObject);
```

or

```javascript
var objJSON = utils.getJSONObject(obj);
```

or

```javascript
var arrJSON = utils.getJSONList(jsObjectsList);
```

or

```javascript
var listResponseJSON = utils.getListResponse(arrObjs, arrObjs.length, false);
```

