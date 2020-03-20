# sending email from a template

You can send an email from a template using the following function:

```javascript
sendAlertEmailFromTemplate(String from, Number templateId, String destinations, String priority, boolean isHtmlContent, NativeObject jsObj,  filesToAttach)
```

With this function, the fields FROM and TO must be USER\_CODE\_ID \(Usernames, not email addresses\). To send the email from a real address instead that from a USER\_CODE\_ID you must set the value FROM inside the email template.

