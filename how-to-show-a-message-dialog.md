# How to show a message dialog

Javacript language provides the function alert() to show a message dialog: this function should never be used, since it does not inherit the theme used for the whole application.

4WS.Platform provides 3 message dialogs, having the following signature:

* showMessageDialog(title, message, okFunc, modal, closable, opts)
* showConfirmDialog(title, message, yesFunc, noFun, opts)
* showInputDialog(title, message, okFunc, modal)  deprecated
* showInputDialog3(title, message, okFunc, modal, cancelFun, inputValue, width, height, closable)
* showListDialog(title, width, height, codes, descriptions, callback, multipleSelection, closable)
* showMoreInputDialog(title, labels, attributes, okFun, modal, cancelFun, defaultAttributesValue, attrProperties)

The first function shows a message dialog with a title, a message and a "ok" button. A callback function is invoked when pressing the "ok" button; the modal boolean flag is used to make the dialog modal or not.

## Example

```javascript
showMessageDialog( "window1.title", "window1.message", function() {}, true, false, 
    {
        "formCls" : "cls-form-alert",
        "windowCls": "",
        "windowIconCls": "",
        "yesButtonCls": "",
        "yesButtonIcon": "",
        "cancelButtonCls": "",
        "cancelButtonIcon": "",
        "switchButton": "Y", //Y=cancel-ok; N or null=ok-cancel
        "buttonAlign": "center" //"left" or "right"
    }
);
```

The second function shows a message input dialog with a title, a message and two buttons: one used to confirm and the other to refuse the choice. A callback function is invoked when pressing the confirmation button; a second callback function is invoked when pressing the refusal button.

## Example

```javascript
showConfirmDialog( "window1.title", "window1.message", 
    function() {
      // do something if user has confirmed the choice
    }, 
    function() {}, 
    {
        "formCls" : "cls-form-alert",
        "windowCls": "",
        "windowIconCls": "",
        "yesButtonCls": "",
        "yesButtonIcon": "",
        "cancelButtonCls": "",
        "cancelButtonIcon": "",
        "switchButton": "Y", //Y=cancel-ok; N or null=ok-cancel
        "buttonAlign": "center" //"left" or "right"
    });
```

The third function shows a message dialog with a title, a message and a "ok" button. A callback function providing the inputed value is invoked when pressing the "ok" button; the modal boolean flag is used to make the dialog modal or not.

## Example

```javascript
showInputDialog3( "window1.title", "window1.message", function(value) {
  // do something with the inputed "value"
}, true, function(){
  //cancel callback
}, "defaultValue", 500, 300, true);
```

With the forth one, you can open a dialog with a list inside it, where the user can choose one (or more) entries and use a callback function to handle the selected ones.

```javascript
showListDialog(
"My Dialog Title",
500, //Width
300, //Height
[1, 2, 3, 4, 5], //My codes (example: IDs)
["First Code", "Second Code", "Third Code", ...], //My descriptions
function(entry){
console.log(entry[0]); //Logs the selected CODE
},
false, //Only allow one item to be selected
false //Do not let the user close the modal without selecting an entry
)
```

You can directly specify title and message in your own language if your application supports one language only, otherwise, you should specify "entries" for title and message whose real translations can be defined through the "Translation" functionality: this feature allows you to add new translations, via "add" button; in this way you can create translations for each language that will be used by these message dialogs. For each message dialog, you should add two rows in the "Translations" list: one for the title and the other for the message entry. For each row, you can leave empty the first cell (Topic), fill in "Id" with the entry (title/message) and fill in all the other dynamic columns with a translation for each language.

If you want more input valued from users you can use:

```javascript
showMoreInputDialog(
  "window1.title", 
  [
    "prefix.custom label one",
    "prefix.custom label two"
  ], //labels
  [
    "fieldOne",
    "fieldTwo"
  ], //attributes
  function(value) {
    // do something with the inputed "value"
  }, //okFun 
  true, //modal
  function(){
    //cancel callback
  }, //cancelFun
  [
    "",
    "field two value"
  ], //defaultAttributesValue
  , 
  [ {type: 'NUM'}, {type: 'CHECK', positiveValue: 'Y', negativeValue: 'N'}] //attrProperties
);

```
