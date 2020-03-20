# How to support multiple themes in a single application, accoding to a rule

The application theme is defined through CSS files and images stored within the application subfolder. It is possible to load a subset of these files, related to a specified theme, according to specific rules, for instance the value of the COMPANY\_ID. Since the login dialog is showed before the user authentication and hence before knowing the COMPANY\_ID value, the login dialog must have a unique theme. In order to define the login theme and the app theme, a javascript action can be define and linked to the "before app render" event, invoked both when creating the login dialog and when showing the app environment, next to the user authetication. This is an example of js code to include in such an action:

```javascript
if (window.companyId==null) {
    cssFiles = [];
}else if (window.companyId=='00000') {
    var tmp = [];
    for(var i=0;i&lt;cssFiles.length;i++){
        if(cssFiles[i].indexOf("/00000/")!=-1) tmp.push(cssFiles[i]);
    }
    cssFiles = tmp;
    topBarFileName = "../00000/bar/topbar.html";
}
```

Since the global variable companyId has not been defined in the login dialog, the code execution would enter the first branch and all css custom files \(cssFiles list\) are removed. You can apply another policy and select a specific theme for it. The second branch is entered when the app is showed: in that scenario, only files stored in appsubfolder/companyfolder/ are considered and loaded. Moreover, a topBarFileName \(and a statusBarFileName\) variable is available and preset with “topbar.html” value. You can redefine it, in order to load another topbar HTML file, such as the one stored within the appsubfolder/companyfolder/bar folder.

