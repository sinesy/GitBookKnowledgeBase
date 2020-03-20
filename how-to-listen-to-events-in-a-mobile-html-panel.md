# How to listen to events in a mobile HTML panel

When adding a Preview panel to a window in a mobile app, it is possible to render an HTML content, starting from a business component providing either an URL or directly the HTML content or an image or a PDF document.

In case of HTML content, it is possible to include links or buttons or any other HTML content and listen to events fired by such objects.

In order to listen to these events, it is needed to:

* add to the HTML content and include a javascript instruction named **Platform.htmlEventHandler** to an event on the HTML side
* add to the Preview panel an "click image" event, which will be automatically invoked by Platform when the event is fired on the HTML side

Example: suppose to have an HTML content containing a button. An HTML button can fire click events through "onClick" callback. It is possible to use it to call Platform.htmlEventHandler and pass forward any type of content, expressed as JSON string.

```javascript
<HTML>
...
<BUTTON text="Button A" onClick="Platform.htmlEventHandler("{ button: 'A' }") ;
<BUTTON text="Button B" onClick="Platform.htmlEventHandler("{ button: 'B' }") ;
...
</HTML>
```

In this way, the javascript action linked to the panel through event "click image" would receive the input passed forward:

```javascript
// vo contains the JSON value passed by the HTML button, so it could contain { button: 'A' } or { button: 'B' }
if (vo.button=='A') {
  // do something when pressing "Button A"
}
else if (vo.button=='B') {
// do something when pressing "Button B"
}
```

