# checking for "undefined" values

What is the most appropriate way to test if a variable is undefined in JavaScript? There are several possible ways.

Since “undefined” is not a truthy value, it can be inserted in a IF statement like this

```javascript
if (window.myVariable){/* Do something when the variable is defined */}
```

Or you can test if the variable is not null \(or undefined\). Remember that

```javascript
null == undefined;    //true
null === undefined;  //false
```

so

```javascript
if(window.myVariable != null){ /* Do something when the variable is defined */ }
```

But probably the best and most secure way to test a variable is this:

```javascript
if (typeof(myVariable) !== typeof undefined){/* Do something when the variable is defined */}
```

This will test if the type of the variable is the same of the type of undefined \(both are “undefined” if the variable is not defined\). The typeof function also doesn’t raise any error if the variable isn’t defined so there’s no need to wrap everything into a try/catch block.

