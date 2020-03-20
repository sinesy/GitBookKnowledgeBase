# Disabling a toolbar button

In order to disable a specific toolbar button, it is possible to use the setDisable\(true\|false\) function available in every of these buttons. Toolbar buttons can be accessed starting from the toolbar variable, using the following properties:

* insertButton
* copyButton
* cancelButton
* importButton
* reloadButton
* saveButton
* deleteButton

## Example

```javascript
gridxxx.insertButton.setDisabled(true);
```

