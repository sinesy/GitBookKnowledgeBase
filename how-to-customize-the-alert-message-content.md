# How to customize the alert message content

It is possible to customize the notification list on the top-bar.

In the following section we suppose the notification system has been already set up.

It is possible to customize a few settings about the notification list, through a series of CSS classes:

* **alertButton** - this class is linked to the whole notification container; it csan be used to set a max width, change border, etc.
* **chatnotificationlist** - this class is related to the title on the top of the notification list; it can be used to change its height or font size/style, etc.
* **chatnomessage** - this class is related to the message showed when there is not any message to show \(empty list\)
* **chatmarkasreadall** - this class is related to the command "Mark all messages as read"
* **chatmarkasreadchatmsg** - this class is related to the command "Mark chat message as read"
* **chatmarkasreademailmsg** - this class is related to the command "Mark email message as read"

Apart from the CSS classes, it is possible to customize the notification through a series of application parameters, located in the ALERT sub-section:

| Parameter name | Value |
| :--- | :--- |
| Show title in the alert message list \(def. N\) | Flag Y/N. If not specified, no title bar is showed on the top of the notification list. If set to true, the default text "Notification list" is viewed. This text can be customized through the **translations** functionality, by setting the entry: "**chat.notificationlist**" |
| Disable click on alert message \(def. N\) | Flag Y/N. If not specified, Platform will listen to the click event on a message reported in the notification list and automatically open a message detail window. If set to Y, the listener is removed and it will be up to the developer to optionally open or execute some other action on the click event. |
| Alert message template | This represents the **content of the message to show** in the notification list. This is a **template**, i.e. the real content to show is reckoned starting from this template + data in input, where each variable expressed as **{varname}** within the template is replaced by the corresponding value in the message in input. |

**Note**: if the Alert message template has not been specified, the default value is:

```text
<table><tr>
<td class="chatnote" >{typeIcon}</td>
    <td width="100px" class="x-menu-list-item-chat">  {from}  </td>
    <td width="200px" class="x-menu-list-item-chat" onClick=\'{openUrl}\'><b>{title}</b></td>
    <td width="150px" class="x-menu-list-item-chat">  {dt}  </td>
    <td align="right" class="x-menu-list-item-chat chat-icon-close" onClick=\'{markAsReadUrl}\'></td>
</tr></table>
```

The supported **variables** are:

* **typeIcon** - can be: mail or chat or alert
* **from** - username of the user sending the alert message
* **openUrl** - a javascript command to open the standard message detail window, i.e.  manageClickOnItem\(createDate, userId, note, destinations, pk.logType, pk.logId, conversationId\)
* **title** - the message text, i.e. CON60\_LOGS.NOTE field value
* **dt** - the formatted date+time of the message 
* **markAsReadUrl** - a javascript command to mark the message as read, i.e.  markMessageAsRead\(createDate, userId, note, destinations, pk.logType, pk.logId, conversationId\)



