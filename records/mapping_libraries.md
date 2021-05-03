VMs mapping requirements are currently very simple. Literally, in the
projection sense.

* Tile rendering
* Common interactions
* Compositing of additional elements

While the metaphor is a map, this doesn't mean a javascript GIS
library has to be used. This is a quick go over options.

## Leaflet

https://leafletjs.com/

What is currently used, and have most experience of in the team. Very
widely used, lots of plugins, familiar to users, and stable. Also a
little dead, core people went off to do mapbox, and most active
development is in plugins rather than the core tool.

Normal operation uses svg elements in the dom and css transformations,
which is nice and introspectable and extensible. Has a few gotchas
with z-order, and performance implications for large numbers of items,
which require aggregating before insertion. Being an old library,
expects to control its own page content and state directly, and
interfaces through dom events. This makes working in the react model a
little awkward, but reasonably managed by react-leaflet library, or
similar approaches.


## Mapbox

https://www.mapbox.com/

Vector tile centered mapping, using webgl, though has raster tile
support. Doesn't support multiple coordinate systems, can't find
examples of people using it for non-geographic maps. Overall
experience on examples is good, however though the documentation is
extensive it's not very clear how to actually implement common
functionality. Basic operation requires an api key, where the freemium
wall lies is also not clear. Possible barriers to offline deployment?

They have released quite a bit of code, but in recent years it is not
well maintained, priorities perhaps switched to internal/commercial
concerns.

## OpenLayers

https://openlayers.org/

Active development, though documentation is spotty feeling, has dead
links, overall confusing. Code quality looks broadly okay though. Core
component composites to canvas, so a little more opaque than leaflet,
but api level is broadly similar. There's some integration with
mapbox's vector approach and style specification.

The updating of tiles on zoom *feels* wrong to me. This may be
psychovisual as it lacks a crossfade, it may actually be something
slower in animation/network fetch/execution.


## OpenSeadragon

https://openseadragon.github.io/

Not a mapping library, but does tiles and deepzoom. Can be used to
make very smooth feeling interactions, though the defaults are
actually somewhat awkward. Supports overlays and user interactions,
and plugins for much more (particularly annotations). There's not much
in the ecosystem that's related to orientation/navigation or
editability, generally assumed from position in image, and optional
minimap.


## ThreeJS

https://threejs.org/

Thin wrapper around WebGL. Mentioned mostly as the far extreme.
Possible to implement smooth tile transitions with custom shaders, ray
casting hit detection, all kinds of fancy dynamic effects.
Considerable work to do so however.


Issues with seams:
https://github.com/Leaflet/Leaflet/issues/3575
https://github.com/openseadragon/openseadragon/issues/1683

Leaflet future:
https://github.com/Leaflet/Leaflet/issues/6882

WebGL questions:
https://github.com/openseadragon/openseadragon/issues/1482

Cool (genome browsing) leaflet usage:
https://github.com/Leaflet/Leaflet/issues/5883
https://github.com/linnarsson-lab/loom-viewer

Leaflet use as non-map:
https://leafletjs.com/examples/crs-simple/crs-simple.html

Featuring another edition of terrible Venn Diagrams:
https://bachasoftware.com/comparing-mapbox-openlayers-and-leaflet/

Moe misc links:
https://taylor.callsen.me/using-reactflux-with-openlayers-3-and-other-third-party-libraries/
https://gis.stackexchange.com/questions/354906/non-geographical-maps-in-openlayers
https://www.geoapify.com/map-libraries-comparison-leaflet-vs-mapbox-gl-vs-openlayers-trends-and-statistics/
https://rawgit.com/allenhwkim/react-openlayers/master/app/index.html#/
