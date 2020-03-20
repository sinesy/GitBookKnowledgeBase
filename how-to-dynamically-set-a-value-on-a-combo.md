# How to dynamically set a value on a combo

It can happen that you have to set a value on a combo-box dynamically, and maybe even if the combo has never been opened \(thus the store never loaded all the values\). To correctly set a value and make the combo show the relative description you have to load the store and use the callback function to set the value:

```javascript
myCombo.store.load({ callback:
    function(){
        myCombo.setValue(myValue);
    }
});
```

Watch out if you have to do the same process inside a loop: The store.load is an asynchronous method and the response might get back after many loop cycles! To correctly set a value inside a loop you have to create a scoped function like this:

```javascript
for(var i = 0; i &lt; 100; i++){

...

(function(i){
    // Same code as above but inside the scoped function
    myCombo.store.load({ callback:
        function(){
            myCombo.setValue(myValue[i]); //This i is
        }
    });
})(i); //This is the i of the cycle

...

}
```

This way the i will not change

