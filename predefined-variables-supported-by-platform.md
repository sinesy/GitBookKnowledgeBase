# Predefined variables supported by Platform

JAVASCRIPT

There is a series of predefined variables automatically set by Platform after the logon of the user and that can be accessed by any client side javascript action:

* username
* languageId
* applicationId
* siteId
* companyId

SQL BUSINESS COMPONENTS

Similar variables are supported on the server-side, within SQL business components and they can be referred using the : symbol before them and they are always expressed as upper case variables:

* USERNAME – username of the logged user
* :LANGUAGE\_ID – the current language, binded to the logged user
* :APPLICATION\_ID – the current application identifier
* :TODAY – a date without time
* :NOW – a date+time
* :COMPANY\_ID
* :COMPANY\_ID\_AS\_NUM \(to use only in case the COMPANY\_ID is a number, it is automatically converted in a number\)
* :SITE\_ID
* :YEAR
* :MONTH
* :DAY – day of the month
* :DOW – day of week

