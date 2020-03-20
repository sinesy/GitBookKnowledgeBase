# validating a lookup

If a default value is configured in a lookup filter, this won’t be validated , you have to create an action that, once hooked with the befor rendering event, will validate the value.

This is part of the code to execute:

```javascript
controlsContainerXXX.getForm().findField("NOME_DEL_FILTRO").setValue(DEFAULT_VALUE);
controlsContainerXXX.getForm().findField("NOME_DEL_FILTRO").validateCode();
```

