# Reduce map loading times

Page load can take more than ten seconds for maps that have a large amount of model data. Per [Website Response Times] we should have _something_ rendered in under a second, and a usable page in under ten seconds.

This pitch will focus on what can be done to minimise the overall work done, to get to a usable page faster.


## Recommendations

In increasing level of trickiness to implement, options for improving page load time based on observations below:

* Flag to skip reshape (~3.5s improvement)
* Put settings into base page template (0.1s improvement)
* Put tiles for lens details in base page template (0.4s+ improvement, final visual only)
* Pre-prepare model server-side in form to be served (~2s improvement)
* Change model format away from JSON and with better compression (0.5s+ improvement)


## Results

### Measurement

Done using [Firefox Profiler] from Developer Tools.

Sadly the profiler does not [support source maps] so to get useful function names out it's important to run against non-minified javascript. So, best to run multiple profiles, as the overall timings are better indications against the staging server, but need locally served javascript to get a good idea of where in the code time is being spent.

There are also profilers for other browsers that could be used to compare results.

Quality of network connection matters for the HTTP timings, and device matters for the browser and javascript timings.


### Timings

As an indicative timeline, the stages below form a linear sequence. Other things, such as fetching more JS files, CSS, and so on, happen in parallel off the critical path. Stitched together from remote and locally run profiles to get useful http and js time breakdowns.

* 240ms HTTP GET html page (~1KB)
* 140ms HTTP GET core js (12KB)
* 95ms HTTP GET settings (~1KB)
* 2400ms HTTP GET model (~2100KB gzip)
  * 1950ms request and wait
  * 435ms response
* 230ms BROWSER `nsStreamLoader::OnStopRequest` (unicode and json parsing, gc)
* ~3645ms JS `modelFromData`
  * ~3615ms `reshapeModel`
    * ~1700ms `addStakeholderOrder`
    * ~820ms `_getSubjCache` (spread in various callsites)
    * ~310ms `newView`
    * ~290ms `labelLayersToLens`
    * ~180ms `ontologyForCategoricalProperties`
* ~800ms JS mostly react
  * ~360ms `useFilteredItems`
  * ~210ms (various map components and hooks)
  * ~70ms `annotationDetails`
  * ~45ms `annotationTriples`
* 420ms HTTP GET blobstore tiles (~64KB in parallel)
* ~70ms JS `hasRAGData`

Time to get tiles from the blobstore can vary wildly - have seen up to 2000ms to complete - but they do load incrementally into the canvas under labels and the page is responsive by then.


## Proposals in detail

### No reshape

A majority of the time spent due to having a larger map model is not getting the model to the browser, but making changes to it once it had loaded. The reshape code has been designed so that the frontend code can grown new expectations about the shape and required ontology for maps, without imposing the cost of revisiting all older maps to update for all migrations. For large and frequently updated maps, we should handle these ongoing expectation changes at the project level instead.

Skipping the reshape step entirely can be a flag that exists on the model (or in local storage for testing), and will be a better option than optimising the existing reshape steps which perform fine for the small historical maps they were designed for.

There are several updates required for any model wishing to skip the current reshape steps:

* The introduction view needs to be in the model rather than coming from the 'welcome-splash' map settings.
* All categories need to be derived from `gist:Caterory` and `gist:isCategorizedBy` predicate in the ontology.
* An annotation category needs to exist in the ontology.
* The map must use lenses not layers.
* New form geo predicates must be used.

There is also some convenience linking added to lenses which may need to be replaced with a cache instead.


### API without settings.json

The page load currently relies on fetching an additional api file before loading the map model, which contains information that could instead be included in the html page template and avoid an additional network roundtrip.


### Base template with more details from model

Tiles cannot start loading until nearly all other work has been done at present, because the paths for the tiles are in the model and openlayers will only start loading after all the dependent providers and components are ready. If some values, such as what the lenses are, and what tilesets they use, are extracted from the model and included in the base template, it would be possible to serve the dual purpose of having a base page that has some useful content where the browser does not support the full range of features needed to interact with the map, and an opportunity to start loading tiles into cache in parallel with model loading.


### Server-side pre-baked model

Loading the model has a large amount of wait time currently, as the server has to do work to load the json from the database and gzip compress it before sending it onto the wire. If the model is stored in a compressed and ready to send form, the first load time will be closer to the time it takes for the client to load the file over the network.


### New model format

This will require some additional research and pitching, but using a different format for the model has the potential to both be less data transmitted over the network, and less work required to load and get the model ready for querying in the browser.

Some indicative sizes:

```sh
$ du -bh nh_20231116*
2.6M	nh_20231116.hdt
858K	nh_20231116.hdt.gz
36M	nh_20231116.json
2.1M	nh_20231116.json.gz
11M	nh_20231116.ttl
```



[Website Response Times]: https://www.nngroup.com/articles/website-response-times/
[Firefox Profiler]: https://profiler.firefox.com/
[support source maps]: https://github.com/firefox-devtools/profiler/issues/605
