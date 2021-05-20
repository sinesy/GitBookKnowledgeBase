---
description: (6.0.2 version)
---

# Retrieve and send the log of a mobile app

## Example of the final result

![example of notification made by an app user](.gitbook/assets/image%20%283%29.png)

![example of automatic report on error](.gitbook/assets/image%20%284%29.png)

## Retrieve and send the log 

The following code allows you to: 

* retrieve the log of your application 
* retrieve the local database 
* upload everything to a bucket on GCP
* send a message to a chat room with links to download everything

### Client side action

This action can be invoked for example when the user presses a "send report" button.

Replace `XXX_SERVER_ACTION_ID` with your server action and `logDir` whit the bucket folder id.

in this action there are 4 important functions that you can adapt to your purpose:

* saveUnsentCrash 
* saveDb
* saveScreenshot
* saveLog
* sendChatNotification

```javascript
/**
 * Send a notification to Chat
 */
function sendChatNotification() {

    var notifParam = {
        fileLogPath: destFileNameLog,
        fileDbPath: destFileNameDb,
        fileCrashPath: destFileNameCrash,
        fileScreenPath: destFileNameScreen
    };
    
    var notifNote = "";
    
    if (!notifParam.fileLogPath || notifParam.fileLogPath == "") {
        notifNote += "Nessun LOG inviato\n";
    }
    if (!notifParam.fileDbPath || notifParam.fileDbPath == "") {
        notifNote += "Nessun DB inviato\n";
    }
    if (!notifParam.fileScreenPath || notifParam.fileScreenPath == "") {
        notifNote += "Nessuno SCREENSHOT inviato\n";
    }
    if (!notifParam.fileCrashPath || notifParam.fileCrashPath == "") {
        notifNote += "Nessun CRASH inviato\n";
    }
    
    if (notifNote && notifNote != "") {
        notifNote += "\n";
    }
    
    if (note && note != "") {
        notifNote += note;
    }
    
    notifParam.note = notifNote;
    
    executeServerAction(XXX_SERVER_ACTION_ID, notifParam);
}


/**
 * Upload log error
 */
function showError(){
    //invio le notifiche, anche se parziali
    asyncFunction("sendChatNotification", []);
    
    //....update your UI
}

/**
* Callback after file send
**/
function sendCallback(responseFromWebService) {
    
    if(!responseFromWebService){
        showError();
    }else{
        var respObj = JSON.parse(responseFromWebService);
        if(respObj.success){
            asyncFunction("sendChatNotification", []);
            
            //....update your UI
        }else{
            showError();    
        }
    }
    
}

/**
 * Save on bucket crashes not sent to crashlytics
 */
function saveUnsentCrash(connectionData){
    log("Connection data: " + connectionData);
    try{
        //recupero il file di log
        var crashPath = getCrashlyticsUnsentReports();
        if(crashPath){
                
            //invio il file al server che lo carica nel bucket
            var logDir = 49;
            //platform in autmatico mette già COMPANY_ID/SITE_ID
            //il file avrà un percorso tipo 210_1001/210_1001_20191126_123000.zip
            destFileNameCrash = destFilePrefix + "_CRASH.zip";
            var url = getBaseURL() + "/uploadfiles/uploadfilegrid?appId=MOBILE_SHOP&applicationId=MOBILE_SHOP&dirId=" + logDir + "&unzip=false&restfulToken="+getToken();
            sendFile(url, crashPath, destFileNameCrash, "saveDb");
        }else{
             log("[3629] saveUnsentCrash SKIPPED NO PATH");
            saveDb();
        }
        
        try{
            //invio i report mancanti a crashlytics
            sendCrashlyticsUnsentReports();
        }catch(ec){
            log("[3629] sendCrashlyticsUnsentReports error: " + ec);
        }
    
    }catch(e){
        log("[3629] saveUnsentCrash ERROR: " + e);
        saveDb();
    }
}

/**
 * Save on bucket the app db
 */
function saveDb() {
    
    try {
        var dbPath = getDbPath("DBNOSYNC"); //DBNOSYNC | DBCON | DBREADONLY
        
        if (dbPath) {
            var zipPathDb = zipFile(dbPath,"dbnosync.zip");
            //invio il file al server che lo carica nel bucket
            var logDirDb = 49;
            var filePathDb = zipPathDb;
            //platform in autmatico mette già COMPANY_ID/SITE_ID
            //il file avrà un percorso tipo 210_1001/210_1001_20191126_123000.zip
            destFileNameDb = destFilePrefix + "_DB.zip";
            var urlDb = getBaseURL() + "/uploadfiles/uploadfilegrid?appId=MOBILE_SHOP&applicationId=MOBILE_SHOP&dirId=" + logDirDb + "&unzip=false&restfulToken="+getToken();
            sendFile(urlDb, filePathDb, destFileNameDb, "saveScreenshot");
        }else {
            log("[3629] saveDb SKIPPED NO PATH");
            saveScreenshot();
        }
    }catch(e) {
        log("[3629] saveDb ERROR: " + e);
        saveScreenshot();
    }
}

/**
 * Save on bucket an eventualy user screenshots passed in the window params
 */
function saveScreenshot() {
    log("[3629] SAVE SCREEN");
    try {
        //uso il path fisso!!
        var screenPath = getWindowParameter('screenshotPath');
        log("[3629] screenPath: " + screenPath);
        if (screenPath) {
            var zipPathScreen = zipFile(screenPath,"screenshot.zip");
            //invio il file al server che lo carica nel bucket
            var logDirDb = 49;
            var filePathScreen = zipPathScreen;
            //platform in autmatico mette già COMPANY_ID/SITE_ID
            //il file avrà un percorso tipo 210_1001/210_1001_20191126_123000.zip
            destFileNameScreen = destFilePrefix + "_SCREEN.zip";
            var urlDb = getBaseURL() + "/uploadfiles/uploadfilegrid?appId=MOBILE_SHOP&applicationId=MOBILE_SHOP&dirId=" + logDirDb + "&unzip=false&restfulToken="+getToken();
            sendFile(urlDb, filePathScreen, destFileNameScreen, "saveLog");
        }else {
            log("[3629] saveScreenshot SKIPPED NO PATH");
            saveLog();
        }
    }catch(e) {
        log("[3629] saveScreenshot ERROR: " + e);
        saveLog();
    }
}

/**
 * Save on bucket the log file
 */
function saveLog(){
    log("[3629] NOTE SEGNALAZIONE: " + note);
    //recupero il file di log
    var logPath = getLogFilePath();
    if(logPath){
        
        //faccio uno zip del log
        var zipPath = zipFile(logPath, "log.zip");
        if(zipPath){
            
            //invio il file al server che lo carica nel bucket
            var logDir = 49;
            var filePath = zipPath;
            //platform in autmatico mette già COMPANY_ID/SITE_ID
            //il file avrà un percorso tipo 210_1001/210_1001_20191126_123000.zip
            destFileNameLog = destFilePrefix + "_LOG.zip";
            var url = getBaseURL() + "/uploadfiles/uploadfilegrid?appId=MOBILE_SHOP&applicationId=MOBILE_SHOP&dirId=" + logDir + "&unzip=false&restfulToken="+getToken();
            sendFile(url, zipPath, destFileNameLog, "sendCallback");
     
            //condivide lo zip con le app presenti nel device
            //shareDocument(zipPath,log.zip);
        }else{
            log("[3629] no zipPath");
            showError();
        }
    }else{
        log("[3629] no logPath");
        showError();
    }
}

function inviaSegnalazione() {
    //invia crahs, log e db dell'app
    saveUnsentCrash("");
}


var lastSyncDate = getVariable("LAST_SYNC_DATE");

var destFilePrefix = userInfo.username + "/" + userInfo.username + "_" + userInfo.deviceId + "_" + stringifyDate(new Date().getTime(), "yyyyMMdd_HHmmss", true);
var destFileNameLog = "";
var destFileNameDb = "";
var destFileNameCrash = "";
var destFileNameScreen = "";
var note = ""//TODO get user not from your UI

asyncFunction("inviaSegnalazione", [],400);


```

### Server side action

Replace `WEB_HOOK_URL` with the id of your web hook.

```javascript
function appendWidget(topLabel,content,contentMultiline,iconUrl) {
    var tmpWdgt = {
        "keyValue": {
            "topLabel": topLabel,
            "content": content,
            "contentMultiline":contentMultiline,
            "iconUrl":iconUrl
        }
    };
    widgets.push(tmpWdgt);
}

//Invia la notifica a google Chat se viene caricata una segnalazione nel bucket

//WEBHOOK DI GOOGLE CHAT
var googleChatURL = "WEB_HOOK&threadKey="+utils.getUUID();
utils.log("[8479] vo: " + JSON.stringify(vo,jsonReplacer),"INFO");

var response = {succcess:true};
var BUCKET_DIR_ID = 49;//bucket segnalazioni

try{
    var bucketName = utils.getDirectoryPath(BUCKET_DIR_ID);
    var expiration = new Date().getTime() + (60 * 60 * 1000); //il link ha durata di 60 minuti
    var filePathBase = userInfo.companyId + "/" + userInfo.siteId + "/";
    
    var buttonsArr = [];
    //LOG DELL'APP
    if (vo.fileLogPath) {
        var fileLogPath = filePathBase + vo.fileLogPath;
        var bucketLogUrl = utils.getGoogleCloudStorageSignedURL("GET", expiration, bucketName, fileLogPath, null) + "";
        //aggiungo il bottone per scaricare il db dell'app
        var logButton = {
            "textButton": {
                "text": "CONTROLLA SEGNALAZIONE (60min Link)",
                "onClick": {
                    "openLink": {
                        "url": bucketLogUrl
                    }
                }
            }
        };
        buttonsArr.push(logButton);
    }
    //DB DELL'APP
    if (vo.fileDbPath) {
        var fileDbPath = filePathBase + vo.fileDbPath;
        var bucketDbUrl = utils.getGoogleCloudStorageSignedURL("GET", expiration, bucketName, fileDbPath, null) + "";
        //aggiungo il bottone per scaricare il db dell'app
        var dbButton = {
            "textButton": {
                "text": "SCARICA DATI APPLICATIVI (60min Link)",
                "onClick": {
                    "openLink": {
                        "url": bucketDbUrl
                    }
                }
            }
        };
        buttonsArr.push(dbButton);
    }
    //CRASH DELL'APP
    if (vo.fileCrashPath) {
        var fileCrashPath = filePathBase + vo.fileCrashPath;
        var bucketCrashUrl = utils.getGoogleCloudStorageSignedURL("GET", expiration, bucketName, fileCrashPath, null) + "";
        //aggiungo il bottone per scaricare il db dell'app
        var crashButton = {
            "textButton": {
                "text": "SCARICA CRASH NON INVIATI (60min Link)",
                "onClick": {
                    "openLink": {
                        "url": bucketCrashUrl
                    }
                }
            }
        };
        buttonsArr.push(crashButton);
    }
    //SCREENSHOT DELL'APP
    if (vo.fileScreenPath) {
        var fileScreenPath = filePathBase + vo.fileScreenPath;
        var bucketScreenUrl = utils.getGoogleCloudStorageSignedURL("GET", expiration, bucketName, fileScreenPath, null) + "";
        //aggiungo il bottone per scaricare il db dell'app
        var screenButton = {
            "textButton": {
                "text": "SCARICA SCREENSHOT (60min Link)",
                "onClick": {
                    "openLink": {
                        "url": bucketScreenUrl
                    }
                }
            }
        };
        buttonsArr.push(screenButton);
    }
    
    var widgets = [];
    //Widget Company - Site
    appendWidget(
        "Company - Site",
        userInfo.companyId + " - " + userInfo.siteId,
        "true",
        "https://github.com/google/material-design-icons/blob/master/png/social/group/materialicons/24dp/1x/baseline_group_black_24dp.png?raw=true"
    );
    //Widget Username
    appendWidget(
        "Utente",
        userInfo.username,
        "true",
        "https://github.com/google/material-design-icons/blob/master/png/social/person/materialicons/24dp/1x/baseline_person_black_24dp.png?raw=true"
    );
    //Widget Data e Ora segnalazione
    appendWidget(
        "Data e Ora Segnalazione",
        utils.convertDateToString(utils.getCurrentDateAndTime(), "yyyy-MM-dd HH:mm:ss"),
        "true",
        "https://github.com/google/material-design-icons/blob/master/png/social/person/materialicons/24dp/1x/baseline_person_black_24dp.png?raw=true"
    );
    //widget Nome del bucket
    appendWidget(
        "Nome del bucket",
        bucketName,
        "true",
        "https://github.com/google/material-design-icons/blob/master/png/file/folder/materialicons/24dp/1x/baseline_folder_black_24dp.png?raw=true"
    );
    
    //Widget note
    if (vo.note && vo.note !== "") {
        appendWidget(
            "Note",
            vo.note,
            "true",
            "https://github.com/google/material-design-icons/blob/master/png/notification/sms/materialicons/24dp/1x/baseline_sms_black_24dp.png?raw=true"
        );  
    }
    
    //aggiungo i pulsanti
    var buttons = {
        "buttons": buttonsArr
    };
    
    widgets.push(buttons);
    
    var requestBody = {
                "cards": [
                    {
                        "header": {
                            "title": "<b>Nuova segnalazione ricevuta</b>",
                            "imageUrl": "https://w7.pngwing.com/pngs/169/167/png-transparent-package-delivery-mail-box-parcel-box-thumbnail.png",
                            "imageStyle": "AVATAR"
                        },
                        "sections": [
                            {
                                "widgets": widgets
                            }
                        ]
                    }
                ]
            };
            
    utils.getWebContent(googleChatURL, false, "POST", "application/json", JSON.stringify(requestBody,jsonReplacer), null, null);
    
}catch(e) {
    utils.log("[8479] Invio notifica fallito: " + e.toString(),"INFO");
}
utils.setReturnValue(JSON.stringify(response,jsonReplacer));
```

### 

### Define an incoming webhook <a id="define_an_incoming_webhook"></a>

Official documentation: https://developers.google.com/hangouts/chat/how-tos/webhooks

From the chat room menu:

1. Select _Configure Webhooks_

   ![](https://developers.google.com/hangouts/chat/images/webhook-menu-option.png)

   A dialog appears that lists any incoming webhooks already defined for the room.

   ![](https://developers.google.com/hangouts/chat/images/webhook-new.png)

2. Click on _ADD WEBHOOK_.
3. Fill in the name field and optionally the avatar URL field.
4. Click _SAVE_.
5. Copy and save the webhook URL

{% hint style="success" %}
Once you're strong enough, save the world.
{% endhint %}

