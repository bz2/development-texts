# Showing edges

Connections between elements of the map in the current view can be taken from data in the model and shown graphically as joining lines.

## Defining relationships

### In illustration

Separate to the data presented in the model, the tiles used can flexibly indicate relationships, by proximity, grouping, or linking visuals. As labels for items will be removed and drawn by the platform, boxes which are themselves arrows, or other designs that would appear to be interactive components rather than static elements should be avoided.

### Direct predicate

A simple tree hierarchy can be derived from the illustrator export, currently used to create a `vm:broader` triple linking child as subject to parent as object. More specific relationships, for instance additional categorisation, can be added via external data and a transform.

### Linking item

For relationships that themselves have properties, RDF requires there be an item to hold the properties rather than a direct link (though see also RDF\*). These relating items require identity, incoming and outgoing links, and the additional properties. At the moment `vm:ActivityInvolvement` has specially handling to make this all (kind of) work.


## Using gloss

Here a gloss is an indirection that is assigned from semantics, and defines the display properties of the link.

### Choosing

Based on the local properties of the graph, a simple string is derived that selects the display.

For example, any relationship where the predicate is 'vm:HE/stronglycauses' is currently assigned the gloss 'causes+'.

This is global to all maps at present, as in the general case we should make design decisions that map the semantics of the map we've chosen, but this could get overridden on a map-by-map basis later.

In first implementation, the class of the subject and the predicate are the only properties used to decide, but the whole local graph shape can be considered.

### Drawing

Each link with the same gloss is drawn using the same set of rules.

Abstractly the gloss is defining if a link is directional, as well as the exact styling properties used (from within the limits of the canvas api).

There are glosses that draw straight lines, that draw midpoint arrow to indicate direction, and draw with different widths, colours, and solid or dashed. Variations are easy to add.

Connections are drawn resolution-independently at present - the width of the lines is a factor of the display, not the zoom level. This is a choice that can be revisited.

Exact layout is not controlled, the only inputs are the origin and destination points, everything must be derived from that and the gloss.


## Experience

Currently edges are shown from the selected item to directly related items only. Later we may wish edges in some contexts to appear regardless of selection, or to show multiple connected steps.

For items with many connections this may result in a large number of lines being drawn on selection, even if those connections are a not the most important aspect of the item.

Edges cannot be selected. Those with identity could be, but would need a clear hit target represented on the map.
