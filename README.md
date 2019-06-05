# Cartiqo Technical Specs BETA release

Cartiqo is a Vector Tile product based on open source geodate of the Netherlands. It provides a full and detailed map of the Netherlands customizable to your own likes. 

## Introduction

Have a look at [cartiqo.nl](cartiqo.nl). 

## How to display a Cartiqo Map

### Endpoint

There is a tile JSON available pointing to the tile sources, having the right attribution and describing the available features. *according to the [TileJSON spec](https://github.com/mapbox/tilejson-spec)*
 
    https://ta.webmapper.nl/wm/styles/tile.json

Or you can directly get the pbf tiles at: 

    "https://ta.webmapper.nl/wm/cartiqo/{z}/{x}/{y}"
    "https://tb.webmapper.nl/wm/cartiqo/{z}/{x}/{y}"
    "https://tc.webmapper.nl/wm/cartiqo/{z}/{x}/{y}" 


Please reference to Cartiqo in the attribution:

    "attribution": "Tiles &copy; <a href='https://cartiqo.nl' target='_blank' title'Powered by Cartiqo'>Cartiqo</a>. Map data: &copy; <a href='https://cbs.nl' target='_blank'>CBS</a>  &copy; <a href='https://kadaster.nl' target='_blank'>Kadaster</a> &copy; <a href='https://osm.org/copyright'>OpenStreetMap</a>.<br/>Cartography: &copy; <a href='https://webmapper.net' target='_blank'>Webmapper</a>."


### Example with [MapboxGL.js](https://docs.mapbox.com/mapbox-gl-js/overview/)

Cartiqo can easily be used in a Mapbox GL js map. Without the use of a Mapbox access token! 

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

### Example with [Leaflet.js Mapbox Gl Plugin](https://github.com/mapbox/mapbox-gl-leaflet)

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset='utf-8' />
    <title>Cartiqo Topo style map example</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.0.3/leaflet.js"></script>
    <script src="https://cdn.maptiler.com/mapbox-gl-js/v0.53.0/mapbox-gl.js"></script>
    <script src="https://cdn.maptiler.com/mapbox-gl-leaflet/latest/leaflet-mapbox-gl.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.0.3/leaflet.css" />
    <link rel="stylesheet" href="https://cdn.maptiler.com/mapbox-gl-js/v0.53.0/mapbox-gl.css" />
    <style>
        body { margin:0; padding:0; }
        #map { position:absolute; top:0; bottom:0; width:100%; }
    </style>
  </head>
  <body>
    <div id="map"></div>
    <script>
      var map = L.map('map').setView([ 52.02624, 5.17197], 11);
      var gl = L.mapboxGL({
        accessToken: 'not-needed',
        style: 'https://ta.webmapper.nl/wm/styles/topography.json'
      }).addTo(map);
    </script>
  </body>
</html>
```

## Styling a Cartiqo Map

### Available styles:

* https://ta.webmapper.nl/wm/styles/topography.json
* https://ta.webmapper.nl/wm/styles/crafty.json
* https://ta.webmapper.nl/wm/styles/data_lines.json

### custom styles

You can build your own custom style according to the [MapboxGL.js Styling specs](https://docs.mapbox.com/mapbox-gl-js/style-spec/)

Example :

```js
{
    "version": 8,
    "name": "Example",
    "glyphs": "https://ta.webmapper.nl/wm/glyphs/{fontstack}/{range}.pbf",
    "sprite": "https://ta.webmapper.nl/wm/sprite",
    "sources": {
        "cartiqo": {
            "type": "vector",
            "url": "https://ta.webmapper.nl/wm/styles/tile.json"
        }
    },
    "layers": [
        {
            "id": "water",
            "source":"cartiqo",
            "source-layer":"water",
            "type": "fill",
            "paint": {
                "fill-color": "#6d87a0"
            }
        }
    ]
}
```

### Glyphs

For Cartiqo we have some Fonts available at `https://ta.webmapper.nl/wm/glyphs/{fontstack}/{range}.pbf`

* `ArbutusSlab`
* `Comfortaa`
* `DosisLight`
* `Giraffey`
* `Lato`
* `LatoSemi`
* `Open Sans Regular`
* `RalewayBold`


## Vector Tile Contents

1. [Philosophy](#Philosophy)
2. [Layer overview](#Layers)
3. [Fields explanation](#Fields)
4. [Source data](#Source-Data )
5. [In depth description of data layers](#Data-Layers)
6. [In depth description per zoom level](#Data-per-Zoom-Level)


### Philosophy

The Cartiqo vector tiles are build with the aim to have as less as possible styling rules to create a clear map. Data rearrangement desisions are always made with a cartographic goal in mind. 

In theory it will be possible to style a map by simply assigning one color per layer and you will be done. For example, blue for water, green for nature and grey for built-up areas. This would already give you a pretty good map. 

To add more cartographic detail to the map a distinction can be made by the `type` in the layer. The `type` is the first main division that can be given to a layer which is interesting for cartographic reasons. Like natural is divided in high vegetation and low vegetation, a main distinction for the use of dark green and lighter shades of green.

Even more detail can be found when using the `subtypes`. This is a subdevision of the `type`.

Not every layer has the same amount of subdevisions. All layers contain `type`, most also contain a `subtype` and some even contain a `subsubtype` when needed. 

### Layers
A quick overview:

![img](./img/cartiqo_schema.png)

### Fields

All layers contain the following general fields:

1. [ `originalid`](#originalid)
2. [`name`](#name)
3. [`type`](#type)

And possibly contain the following fields:

4. [`subtype`](#subtype)
5. [`subsubtype`](#subsubtype)

*Not every layer has the same amount of subdevisions. All layers contain `type`, most also contain a `subtype` and some even contain a `subsubtype` when needed.* 

###### `originalid` 
The `originalid` contains the feature id from the original dataset. This can be the Natural Earth dataset, Top10NL, BGT or even OSM ID's. No transformation is done on this number. When the `originalid` is missing it means a custom geometric transformation (other than simplification) on the feature has occurred, like merging features or transformation from polygon to line. These new features can therefore not be mapped back to its original source.

###### `name`
The Dutch name of the feature if provided by the source data. 

##### `type`
The main type of the feature. First main sub-division of the data for cartographic purposes.

##### `subtype`
This is a subdevision of the `type`. So more detail can be found when using the `subtypes`.

### Source Data 

Cartiqo is a new data model build from a combination of several datasets. With easy recognizable layer names and types. The model is designed to quickly style the data in a comprehensive way over all zoom levels from all source data. No knowledge from the source data is needed to understand the Cartiqo data model. 
We choose the best combination of the data on each zoom level to provide a good and complete map of the Netherlands. Choices are a tradeoff between detailed data and fast simplified features to keep the tiles small and fast. 

The following sources are used: 

* Basis Registratie Grootschalige Topografie (BGT)
* Basis Registratie Topografie (BRT)
    * Top10NL
    * Top50NL
    * Top100NL
    * Top250NL
    * Top500NL
    * Top1000NL
* Basis Registratie Adressen en Gebouwen (BAG)
* CBS 
* Natural Earth data
* Open Street Map (OSM)

The BGT, BRT and BAG datasets are imported with [NLExtract](https://nlextract.nl/).

The OSM data is imported with the help of the [OpenMaptiles](https://github.com/openmaptiles/openmaptiles/) stack and schema. So the OSM data is already preprocessed and can be handled more easy and quicker.  

Natural Earth data is downloaded form [the website](https://www.naturalearthdata.com/). All large, medium and small scale data. 

CBS data form the [cbs website](https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data)

In the following 2 chapters we will explain the data per layer and per zoom level. Also mentioning which source data is included. 


### Data Layers


* [ `water`](#water)
* [ `natural`](#natural)
* [ `builtup`](#builtup)
* [ `infrastructure`](#infrastructure)
* [ `agricultural`](#agricultural)
* [ `waterline`](#waterline)
* [ `railways`](#railways)
* [ `roads`](#roads)
* [ `boundaries`](#boundaries)
* [ `pois`](#pois)
* [ `labels`](#labels)

#### `water` (polygon)

**`water`** contains all water area polygons like oceans, sea, lakes and rivers. On lower zoom levels it contains all oceans from the Natural Earth dataset. At higher zoom levels it contains all water bodies from The Netherlands. Including the Wadden.  

##### Fields

* `originalid`
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

* `originalid`
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

* `originalid`
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

* `originalid`
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

* `originalid`
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

### Data per Zoom Level

