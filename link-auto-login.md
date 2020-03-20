# Link auto login

This explains how to immediately redirect a user into Platform after he’s signed in in a portal

varbackendUrl = "[http://testing.sinesy.it/platformpicktour/](http://testing.sinesy.it/platformpicktour/)" backendUrl += "/4ws/index\_app.jsp?autoLogin=true&applicationId=PICKTOUR&appTitle=PickTOUR&companyId=00000&siteId=100"; backendUrl += "&username=" + encodeURIComponent\(userId\); backendUrl += "&password=" + encodeURIComponent\(password\); backendUrl += "&languageId=en&theme4ws=false&loginVersion=1&isIE=true"; backendUrl += "&hiddenItemIds=companyId=00000,siteId=100";

You just have to replace the red characters with the user data.

NOTE: To allow the user change its password you can follow this guide.

For more info: PickTOUR – Luca Biasotto

