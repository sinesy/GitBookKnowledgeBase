# How to set content to a Google Map linked to a grid or form

A Google Map panel is automatically filled starting from the business component linked to it:

* if it is connected to a list business component, it will show a list of pins, one for each record read
* if it is connected to a detail business component, it will show one pin only, related to the record read

In any case, the record must provide some fields:

* latitude
* longitude
* label linked to the pin

It is possible to manage the map content dynamically, for instance by reloading the pin in the map each time a row in another grid is selected. The same is possible if latitude and longitude coordinates are provided dynamically from a detail form, which contains these two fields too. In these scenarios, the business component linked to the map is not useful: it is used just to define the fields definition the map is supposed to read. In case of a map connected to a grid, a javascript action must be defined and connected to a row click event. This is an example of the javascript action:

```javascript
try {

/* the markers must be defined "globally" */

if (googleMap181.mmarkers==null)

googleMap181.mmarkers = [];



/* used to remove any previous pin */

for (var i = 0; i &lt; googleMap181.mmarkers.length; i++) {

googleMap181.mmarkers[i].setMap(null);

}



var rec = grid161.getSelectionModel().getSelected();

var myLatLng = new google.maps.LatLng(

rec.data['latitudine'],

rec.data['longitudine']

);



var marker = new google.maps.Marker({

position: myLatLng,

map: googleMap181.getMap()

});



googleMap181.mmarkers.push(marker);



var mapBounds = new google.maps.LatLngBounds();

mapBounds.extend(myLatLng);



googleMap181.getMap().fitBounds(mapBounds);

googleMap92.getMap().setZoom(16);



} catch(e) {

}
```

The value "googleMap181" reported in the example above must be replaced with your own value: 181 represents the id of the map panel.The variable "vo" reported above is the value object related to the grid’s row currently selected. Moreover, a second javascript action must be defined an linked to the map’s event "map rendered": this event ensures that the map’s pin is set only when the map has been correctly created and rendered. This is an example of such a kind of action:

```javascript
try {


/* the markers must be defined "globally" */

if (googleMap181.mmarkers==null)

googleMap181.mmarkers = [];


/* used to remove any previous pin */

for (var i = 0; i &lt; googleMap181.mmarkers.length; i++) {

googleMap181.mmarkers[i].setMap(null);

}


googleMap181.cache.marker = [];



var rec = grid161.getSelectionModel().getSelected();

var myLatLng = new google.maps.LatLng(rec.data['latitudine'], rec.data['longitudine']);



var marker = {

lat: rec.data['latitudine'],

lng: rec.data['longitudine'],

marker: {title: rec.data['note']},

listeners: {click: function() {}}

};


googleMap181.mmarkers.push(marker);


var mapBounds = new google.maps.LatLngBounds();

mapBounds.extend(myLatLng);



googleMap181.cache.marker = [];

googleMap181.clearMarkers();

googleMap181.addMarkers(markers);



googleMap181.getMap().fitBounds(mapBounds);

googleMap181.getMap().setZoom(16);

} catch(e) {}
```

The value "grid161" reported in the example above must be replaced with your own value: 161 represents the id of the grid panel. In case of a detail panel who manages the map content, a similar javascript action should be defined, where the starting record is retrived in the classical way:

```javascript
formPanelXXX.getForm().record
```

Typically, this action is then linked to a "after form loading" event.

