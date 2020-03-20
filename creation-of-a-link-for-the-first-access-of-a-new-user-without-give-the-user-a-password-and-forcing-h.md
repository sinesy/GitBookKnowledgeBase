# Creation of a link for the first access of a new user  without give the user a password and forcing

This post could be useful if you want to give to a user a link for access your portal, without the need of a password, and then force him to create a password at his fist login. You can, for example, send an email after the sign up with a link that allows the user change its password.

Steps:

* Create your user
* Define the parameter:
* PLATFORM\_BASE\_URL \(you can find it in 4WS.Platform\), in this case: [http://testing.sinesy.it/platformpicktour/](http://testing.sinesy.it/platformpicktour/) Note: this parameter is also needed for the forgotten password function.
* Create you link in this way:

```javascript
var rtk = utils.getEncodedRtk("applicationId=" + APPLICATION_ID + "&amp;companyId=" + COMPANY_ID + "&amp;siteId=" + SITE_ID + "&amp;username=" + USER_ID + "&amp;password=" + PASSWORD + "&amp;forgotpwd=Y" + "&amp;appTitle=" + APP_TITLE);



var firstLoginUrl = utils.getParameter("PLATFORM_BASE_URL");
firstLoginUrl += '/4ws/index_app.jsp?rtk=';
firstLoginUrl += rtk;
```

Notes:

* The following parameters specified in the function must be the new user ones
* APPLICATION\_ID
* COMPANY\_ID,
* SITE\_ID
* USER\_ID
* PASSWORD
* APP\_TITLE
* In this case a temporary password for the user was manually generated and sent by email.

