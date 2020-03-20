# How to import java classes in server

If you have developed your own java classes and want to refer them from a server-side javascript action or if you have a third party jar library you want to refer from javascript, you can do it:

* first, import the .jar file containing your classes \(or the third party library\) in WEB-INF/lib of your Platform web application
* alternatively, prepare a .zip file containing the same structure: WEB-INF/lib/yourlibrary.jar and that .zip filein the sub-context using Import/Export Application
* restart the service related to the web application, in order to make it available to Platform
* within your server-side js action, write this scriptlet:

```javascript
importClass(Packages.yournamespace.YourClassName);

// e.g. importClass(Packages.it.sinesy.test.MyTest);
```

* finally, you can instance such a class, using its constructor, as long as you refer the class with the whole namespace:

```javascript
var myInstance = new yournamespace.YourClassName();
```

