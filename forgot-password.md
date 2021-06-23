# Forgot password

The functionality, when enabled, shows a "Forgot Password" link in the Login Dialog.

When pressing on such a link, a window is prompted in order to ask the user to specify its email address. This address in compared with the one stored in the user credentials functionality: an email is then sent to that address. The email message contains a link to open the the web application and pre-fill the change password window in the login page with the already existing password. In this way, the user can change its password.

This is the workflow of the Forgot Password process:

* user clicks on Forgot Password 
* user types his email address 
* an email is sent to that address, containing a link to click on
* the link opens the Login Window again, with the Change Password window
* the user specifies a new password twice
* the user can access the web application.

In order to enable the Forgot password feature, these conditions must be fulfilled:

* forgotPwd has NOT been included among the "hidden fields" neither in the application nor in the global parameters \(HIDDEN\_ITEM\_IDS\)
* the "Email Host" application/global parameter \(MAIL\_SMTP\_HOST\) has been filled
* the "Email Administrator Email" application/global parameter \(ADMINISTRATOR\_EMAIL\) has been filled
* the "4WS.Platform base URL" application/global parameter \(PLATFORM\_BASE\_URL\) has been filled

