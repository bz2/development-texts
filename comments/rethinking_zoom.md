# Rethinking zoom level

Meaning maps we produce use zoom as a core metaphor, a way to expand detail progressively and driven by the focus of a guided experience or user interest.

Principles:
* Show few enough things on the map to be at-a-glance comprehensible.
* Clearly indicate where to zoom to find the specific detail desired.

Currently we use an illustrator layer for a zoom range for the base map, for instance `~BASE 0-2` `~BASE 3` `~BASE 4` `~BASE 5-6` which gets turned into tiles `0-{x}-{y}.png` ... `6-{x}-{y}.png` in a single tiles/ folder, which the mapping library can load.

Problems:
* Zoom level displayed relates to resolution available to draw the map content in, not what the user should perceive.
* Content outside the tiles, such as markers, and now labels, needs to follow similar rules.
* Unclear for a given bounding box (such as a story) what zoom level will be displayed on a user device.
* Some zoom levels are passed over quickly.
* Some devices may not show the smallest zoom level at all.
* High resolution screens might end up scaling up low res tiles to make a blurry map.
* For lots of detail in one area we'd like zoom level 7, which is a lot of pixels.
* How overlays and the base map interact when details change across zoom is a source of confusion.
* Overlays tend to not support zoom levels even where it would make sense.

I haven't got a complete answer to all of these, but do wonder if it's worth severing the detail level from the resolution. In other words, producing for instance 'low' 'med' 'hi' detail tilesets, at all zoom levels, and handling the detail switching outside the mapping library, based on device characteristics and context.
