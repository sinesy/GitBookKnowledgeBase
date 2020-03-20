# Deploying an application

In order to move a specific version of the application from one environment \(e.g. development\) to the next one \(e.g. test or production\), Platform provides two distinct features: metadata and files export from the first environment and then metadata + files import in the next environment.

Notes:

* It’s always good to export roles and users from the test environment
* Instead, it’s always better NOT to export parameters, but declaring them in the new installation
* Also if the app has static files \(images, icons, fonts, css, …\) they should be exported from the relative window too

Once the metadata has been exported from the first environment \(a .zip file\), the next step is to log on into the next environment and then import the same file.

NOTE: Always pay attention to which parameters to export: they will be imported on the the new installation and override the existing ones. This might not be wanted, so make sure to double check the parameters before exporting the app.

