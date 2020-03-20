# Accessing to translations form a server

It is possible to access to the custom translations from within a server-side js action using the following function:

```javascript
utils.getResourceByLanguageId(item, languageId)
```

where item is the property name inside the bundle related to the specified languageId, which represents the language and can contain values such as IT, EN, FR.

A scheduled process defined as a server-side js action, it is possible to pass forward a specific language to use, through the userInfo.languageId, where userInfo is related to the user specified for the scheduled process, which typically is set with ADMIN.

