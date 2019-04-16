# Cartiqo Technical Specs BETA release

Cartiqo is a Vector Tile product based on open source geodate of the Netherlands. It provides a full and detailed map of the Netherlands customizable to your own likes. 

## Introduction


See [cartiqo.nl](cartiqo.nl)


## Display a Cartiqo Map

### Endpoint

    https://ta.webmapper.nl/wm/styles/tile.json

OR 

    "tiles": [ 
        "https://ta.webmapper.nl/wm/cartiqo/{z}/{x}/{y}",
        "https://tb.webmapper.nl/wm/cartiqo/{z}/{x}/{y}",
        "https://tc.webmapper.nl/wm/cartiqo/{z}/{x}/{y}" 
    ]

### Example with [MapboxGL.js](https://docs.mapbox.com/mapbox-gl-js/overview/)


``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset='utf-8' />
    <title>Cartiqo Topo style map example</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.tiles.mapbox.com/mapbox-gl-js/v0.53.1/mapbox-gl.js'></script>
    <link href='https://api.tiles.mapbox.com/mapbox-gl-js/v0.53.1/mapbox-gl.css' rel='stylesheet' />
    <style>
        body { margin:0; padding:0; }
        #map { position:absolute; top:0; bottom:0; width:100%; }
    </style>
</head>
<body>
    <div id='map'></div>
    <script>
        var map = new mapboxgl.Map({
            container: 'map',
            style: 'https://ta.webmapper.nl/wm/styles/topography.json',
            zoom: 14.5,
            pitch: 60,
            bearing: 17.6,
            center: [5.60482, 51.76409]
        });
    </script>
</body>
</html>
```

## Styling a Cartiqo Map

### Available styles:

* https://ta.webmapper.nl/wm/styles/crafty.json
* https://ta.webmapper.nl/wm/styles/data_lines.json
* https://ta.webmapper.nl/wm/styles/topography.json

### custom styles

You can build your own custom style according to the [MapboxGL.js Styling specs](https://docs.mapbox.com/mapbox-gl-js/style-spec/)


## Vector Tile Contents

### Philosophy

The Cartiqo vector tiles are build with the aim to have as less as possible styling rules to create a clear map. 

In theory it will be possible to style a map by simply assigning one color per layer and you will be done. For example, blue for water, green for nature and grey for builtup areas. This would already give you a pretty good map. 

To add more detail to the map a distinction can be made by the `type` in the layer. The `type` is the first main division that can be given to a layer.

Even more detail can be found when using the `subtypes`. The second subdevision of the `type` of a layer.

Not every layer has as many subdevisions. Some even contain a `subsubtype` when needed. 

### Layers
A quick overview:

![img](./img/cartiqo_schema.png)

### Fields

All layers contain the following general fields, and will therefore be described here only once:

* `original_id`
* `name`

###### `originalid`

The `original_id` contains the feature id from the original dataset. This can be the Natural Earth dataset, Top10NL, BGT or even OSM ID's. No transformation is done on this number. When the `original_id` is missing it means a custom geometric transformation (other than simplification) on the feature has occurred, like merging features or transformation from polygon to line. These new features can therefore not be mapped back to its original source.

###### `name`

The Dutch name of the feature if provided by the source data. 


#### `water` (polygon)

**`water`** contains all water area polygons like oceans, sea, lakes and rivers. On lower zoom levels it contains all oceans from the Natural Earth dataset. At higher zoom levels it contains all water bodies from The Netherlands. Including the Wadden.  

##### Fields

* `original_id`
* `name`
* `type`
* `subtype`

###### `type`

The main type of the feature. 

One of:

* `sea`
* `tidal_flat`
* `lake`
* `water_way`

#### `natural` (polygon)

**`natural`** contains all nature polygons like nature areas, grass fields and forest areas. It describes the physical material of the land surface. 

Together with the layers **`agriculture`**, **`infrastructure`** and **`builtup`** it covers the total surface of the Netherlands from zoom level 13 and higher. 


##### Fields

* `original_id`
* `name`
* `type`
* `subtype`

###### `type`

The main type of the feature. 

One of:

* `high` is all natural vegetation like trees and forest areas. Including tree lines. 
* `low` covers all natural vegetation defined as grass, heather and shrubs. 

* `bare` are features describing natural areas without vegetation like sand areas: dunes and sand and rock plains. 


###### `subtype`

The subtype of the feature describes the type in even more detail and can be use to make more distinction in the map styling. So per type:

* `high`
    * `mixed`
    * `deciduous`
    * `coniferous`
    
* `low`
    * `heath`
    * `grass`
    * `shrubs`

* `bare`
    * `sand`
    * `rock`
    * `dune`

#### `builtup` (polygon)

The **`builtup`** layer contains all the urban areas and buildings. On lower zoom levels a city is represented as an urban area and on higher zoom levels the areas will be split up into building blocks and later by buildings and even more detail, entrances, walls and covers. 

##### Fields

* `original_id`
* `name`
* `type`
* `subtype`

###### `type`

The main type of the feature. 

One of:

* `area`
* `courtyard`
* `building`
* `wall`


#### `infrastructure` (polygon)

All physical areas human-made and not natural or vegetation covered. Mostly asphalt or stone coverages and found inside urban areas. 


##### Fields

* `original_id`
* `name`
* `type`
* `subtype`

###### `type`

The main type of the feature. 

One of:

* `parking`
* `road `
* `railway`
* `jetty`
* `tunnel`
* `bridge`
* `runway`
* `pavement` 


#### `agricultural` (polygon)


##### Fields

* `original_id`
* `name`
* `type`
* `subtype`

###### `type`

The main type of the feature. 

One of:
  
  * `agriculture`
  * `arboriculture` 
  * `pasture`
  * `greenhouse`
  * `fallow`

#### `waterline` (line)

 {3m,6m,12m,50m,125m,wide}

#### `railways` (line)
 {rail,tram,metro,industrial,touristic,light_rail} 
#### `roads` (line)
{highway,motorway,main,secondary,local,bike,path} 
#### `boundaries` (line)
 {country,province,municipality} 
#### `pois` (point)
 {food_drink,public_building,public_transport} 
#### `labels` (point)
 {place,admin,water,nature,industrial}  

### Zoom levels

### Source Data

