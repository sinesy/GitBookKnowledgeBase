# accessing the authorizations set for a specific grid

If there is the need to figure out the authorizations set to a specific grid \(according to the roles assigned to the current user\), a global javascript variable is available: buttonsAuth

```javascript
buttonsAuth[gridPanel.functionId];
```

The returned value is an array containing 3 boolean values, related to the flags: insert, update and delete.

