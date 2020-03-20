# Integrating Mailchimp lists

In this section is reported an example about the integration of a 4WS.Platform application and MailChimp.

For a more detail documentation, see the MailChimp APIs, accessible through the following link: MailChimp API

Two steps are required in order to integrate MailChimp:

* a client side js action is defined and used to communicate with another one defined on the server side, typically linked to an “after save” event in a grid or form.
* a server side js action where the real integration is carried out; the example reported below, a snipped is showed to add/update a contact in a MailChimp list.

Server side js action

```javascript
utils.log('vo.mailPersonale: '+vo.mailPersonale,'DEBUG');
utils.log('vo.nome: '+vo.nome,'DEBUG');
utils.log('vo.amministratore: '+vo.amministratore,'DEBUG');

var headers = new java.util.HashMap();

headers.put('Authorization','Basic 3e977ace7e94468143f9444dff91733b-us7');

json = utils.getJSONObject({
"email_address": vo.mailPersonale,
"status": "subscribed",
"merge_fields": {
"FNAME": vo.nome,
"LNAME": vo.amministratore
}
});

try {

    json=utils.getWebContentWithHeaders('http://us7.api.mailchimp.com/3.0/lists/295e2088ea/members/' //uri
    , false // replaceVariables
    , 'POST' //httpMethod
    , "application/json"  //contentType
    , json// requestBody
    , null //user
    , null// pwd
    , null// charSet
    , headers //headers
    );

    utils.log('response : '+json,'DEBUG');

}catch (e){

    var mail=new java.lang.String(vo.mailPersonale);
    var hash=org.apache.commons.codec.digest.DigestUtils.md5Hex(mail);

    utils.log('error: '+e.message,'ERROR');

    json=utils.getWebContentWithHeaders('http://us7.api.mailchimp.com/3.0/lists/295e2088ea/members/'+ hash //uri
        , false // replaceVariables
        , 'PUT' //httpMethod
        , "application/json"  //contentType
        , json// requestBody
        , null //user
        , null// pwd
        , null// charSet
        , headers //headers
        );

    utils.log('response : '+json,'DEBUG');
}
```

