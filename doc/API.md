
# Torque API


## L.TorqueLayer(options)

One of two core classes for the Torque library - it is used to create an animated torque layer with custom settings.

### Usage example

```js
  // initialize a torque layer that uses the CartoDB account details and SQL API to pull in data
  var torqueLayer = new L.TorqueLayer({
    user       : 'viz2',
    table      : 'ow',
    cartocss:  CARTOCSS
  });
```

### Options

##### Provider options

| Option    | type       | Default   | Description                            | Options |
|-----------|:-----------|:----------|:---------------------------------------|---------|
| provider  | string     | ```sql_api```   | Where is the data coming from    | `sql_api`, `url_template`, or `windshaft` |

##### CartoDB data options (SQL API provider)

| Option    | type       | Default   | Description                            |
|-----------|:-----------|:----------|:---------------------------------------|
| user_name      | string     | ```null```      | CartoDB account name. Found from: http://accountname.cartodb.com|
| table_name     | string     | ```null```      | CartoDB table name where data is found  |
| query       | string     | ```null```      | SQL query to be performed to fetch the data. You must use this param or table, not at the same time |
| cartocss  | string     | ```null```      | CartoCSS style for this map |
| loop      | boolean    | ```true```      | If ```false```, the animation is paused when it reaches the last frame |


### Time methods

| Method    | options    | returns   | Description                            |
|-----------|:-----------|:----------|:---------------------------------------|
| ```setStep(step)``` | ```time numeric```    | ```this```   | sets the animation to the step indicated by ```step```, must be between 0 and ```steps```|
| ```play()```| | ```this```| starts the animation
| ```stop()```| | ```this```| stops the animation and set time to step 0
| ```pause()```| | ```this```| stops the animation but keep the current time (play enables the animation again)
| ```toggle()```| | ```this```| toggles (pause/play) the animation 
| ```getStep()``` | | current animation step (integer)   | gets the current animation step
| ```getTime()``` | | current animation time (Date) | gets the real animation time
| ```isRunning()``` |  | `true`/`false` | describes whether the Torque layer is playing or is stopped 

### Layer control methods

| Method    | options    | returns   | Description                            |
|-----------|:-----------|:----------|:---------------------------------------|
| ```hide()``` | none    |```this```  | hides the Torque layer |
| ```show()```| none| ```this``` | shows the Torque layer |

### Style methods 

| Method    | options    | returns   | Description                            |
|-----------|:-----------|:----------|:---------------------------------------|
| ```setCartoCSS(cartocss)``` | ```cartocss string```    | ```this```   | style the map rendering using client-side cartocss (not available with named maps) | 

The full CartoCSS spec is not supported by Torque but instead only a limited subset with some additions related to torque rendering. To see the full list of supported parameters, read the [Torque CartoCSS documentation](CartoCSS.md). ``value`` and ``zoom`` variables can be used. ``value`` is the value of aggregation (see ``countby`` constructor option). ``zoom`` is the current zoom being rendered

TorqueLayer currently expects ```marker``` styling.

##### CartoCSS Example

This should be ```string``` encoded in Javascript.

```css
#layer {
  marker-fill: #662506;
  marker-width: 20;
  [value > 1] { marker-fill: #FEE391; }
  [value > 2] { marker-fill: #FEC44F; }
  [value > 3] { marker-fill: #FE9929; }
  [value > 4] { marker-fill: #EC7014; }
  [value > 5] { marker-fill: #CC4C02; }
  [value > 6] { marker-fill: #993404; }
  [value > 7] { marker-fill: #662506; }
  [frame-offset = 1] {  marker-width: 20; marker-fill-opacity: 0.05;}' // renders the previous frame
  [frame-offset = 2] {  marker-fill: red; marker-width: 30; marker-fill-opacity: 0.02;}' // renders two frames ago from the current being rendered
}
```

### Data methods
| Method    | options    | returns   | Description                            |
|-----------|:-----------|:----------|:---------------------------------------|
| ```setSQL(sql statement)``` | ```SQL string```    | ```this```   | Change the SQL on the data table (not available with named maps) | 
| ```error(callback)``` | ```callback function with a list of errors as argument```    | ```this```   | specifies a callback function to run if there are query errors | 

##### SQL Example

Limit the data used in the Torque map.
```js
torqueLayer.setSQL("SELECT * FROM table LIMIT 100");
```

### Interaction methods (only available for Leaflet)
| Method    | options    | returns   | Description                            |
|-----------|:-----------|:----------|:---------------------------------------|
| ```getValueForPos(x, y[, step])```| | an object like { bbox:[], value: VALUE } if there is value for the pos, null otherwise | allows to get the value for the coordinate (in map reference system) for a concrete step. If step is not specified the animation one is used. This method is expensive in terms of CPU so be careful. It returns the value from the raster data not the rendered data |
| ```getValueForBBox(xstart, ystart, xend, yend)```| | a number | returns an accumulated numerical value from all the torque areas within the specified bounds |
| ```getActivePointsBBox(step)```|  | list of bbox | returns the list of bounding boxes active for ``step``
| ```getValues(step)```|  | list of values| returns the list of values for the pixels active in ``step``
| ```invalidate()```|  | | forces a reload of the layer data.

### Interaction methods (only available for Leaflet)
| Method    | options    | returns   | Description                            |
|-----------|:-----------|:----------|:---------------------------------------|
| ```getActivePointsBBox(step)```|  | list of bbox | returns the list of bounding boxes active for ``step``
| ```invalidate()```|  | | forces a reload of the layer data.


### Events
Events in Torque follow the format:
```js
torqueLayer.on('event-type', function([callback_obj]) {
    // do something
});
```

| Events    | callback object   | Description                            |
|-----------|:----------|:---------------------------------------|
| ```change:steps```| current step | When a map changes steps, this event is triggered |
| ```change:time```| current time, step number | When a map changes time, this event is triggered |
| ```play```|   none      | Triggered when the Torque layer is played  |
| ```pause```|     none      | Triggered when the Torque layer is paused |
| ```stop```|   none     | Triggered when the Torque layer is stopped |
| ```load```| none        | Triggered when the Torque layer is loaded |

##### Event Example

Print the current step to the console log.
```js
torqueLayer.on('change:steps', function(step) {
    // do something with step
    console.log("Current step is " + step);
});
```


# Google Maps Layers

## GMapsTorqueLayer(options) 
This class does exactly the same as ``L.TorqueLayer`` but using Google Maps instead. The main difference is that this class
is not a layer but is an overlay, so in order to add it to the a map use, ``layer.setMap`` instead of ``overlayMapTypes``. See the [Overlay View](https://developers.google.com/maps/documentation/javascript/reference#OverlayView) reference in Google Maps API doc.

### Options

##### options
| Option    | type       | Default   | Description                            |
|-----------|:-----------|:----------|:---------------------------------------|
| map | google.maps.Map |    | google.maps.Map instance |

see ``L.TorqueLayer`` for the rest of the options.


