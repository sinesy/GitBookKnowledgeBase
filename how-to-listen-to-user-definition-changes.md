# How to listen to user definition changes

Platform provides built-in functionalities to manage authentication and authorization, available both within the App Designer and the interpreted application:

* Administration -&gt; Permissions -&gt; **Roles** - used to define a role, i.e. a list of functionalities enabled for that role
* Administration -&gt; Permissions -&gt; **Users** - used to define a user and his roles, i.e. the user definition \(in terms of username, password, date expiration, etc.\) and the roles enabled for that user

Moreover, Platform provides also the chance to listen to events related to users management and execute custom logic when they are fired. Such events can be intercepted through a few application parameters:

* Action id \(JSServer\) to use after change password
* Action id \(JSServer\) to use after application user logout
* Action Id \(server-side js\) to be performed **after** insert/update/delete a user
* Action Id \(server-side js\) to take **before** you insert/update/delete a user

 The last two events are very similar in the way they are managed by Platform. The invoked server-side js action receives in input the following content:

* reqParams 
  * **operationType** - can be filled with either "insert" or "update" or "delete", according to the current writing operation
* vo - the JSON content received from the UI, expressed as a Map \(js object\)

That is to say:

```text
var reqParams = {
    operationType: "update" // or insert or delete...
};

var vo = {
    dateExpirationPassword: // a js date
    decriptedPassword: "..",
    languagePrm08: { ... } // record of PRM08 for such a user
    rowVersion: xyz,
    userIdUpdate: "...",
    siteIdSub02: xyz,
    description: "...",
    isAdmin: "Y", // or "N"
    userIdCreate: "...",
    connected: "N",
    encriptedPassword: "N", // or "Y"
    password: "...",
    lastUpdate: // a js date
    subjectIdSub02: xyz,
    pk: { companyId: "...", siteId: xyz, userCodeId: "..." },  
    locked: "N",
    erasable: "Y",
    createDate: // a js date
    status: "E"
};

//...
```

Finally, the "before" action allows to interrupt the writing operation at all, by returning back the following JSON content:

```text
utils.setReturnValue(JSON.stringify({
    success: false,
    message: "thereasonwhy..."
}));
```

