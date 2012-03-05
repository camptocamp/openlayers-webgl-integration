Objective
---------

The goal of this document is to be a starting point for a discussion for the design of an evolved API for OpenLayers suitable for use with alternative renderers and eventually 3D functionality.

Key objectives are:

* Build on experience and expertise in OpenLayers API, while taking the opportunity to make improvements.

* Support both DOM and WebGL renderers.  It is anticipated that the DOM renderer will have much less functionality than the WebGL renderer, but will provide a limited fallback for when WebGL is not available.

* Be ready to integrate 3D data.


Top-down view
-------------

There are five key components:

* The map

* Views

* Renderers

* Layers

* Controls

The map is the master object.  It contains a single renderer object, which can either be a DOM renderer or a WebGL renderer.  Maps maintain the map projection, a view, an ordered list of layers, and a list of controls.  Maps are responsible for managing dirty and animating states. They listen to events fired by layers and controls.  View objects represent views of the layer data.  In 2D this will typically be defined by a bounding box and a zoom level, in 3D it may be a free camera.

Layers are pure data providers. They provide raster, vector and eventually other types of content, but do not know how to render them.  Layers can have their own projections, which can be different from the map's projection in which they are used.  Individual layer objects can be shared between multiple maps.  When their content changes they fire "changed" events.

Controls can manipulate both the map view and individual layers.  They should work in map projection coordinates.

Due to differences between the renderers, only two types of coordinates are used: coordinates in the map's projection, and pixel coordinates relative to the DIV containing the map.  Map objects provide functions for converting between the two, using the View object and the map projection.  Layer objects can have their own projections, and all interactions with layers is through coordinates in their own local projection.


Layer types
-----------

Each of these layer types will require a specialized renderer.  As a result, they can be considered as the fundamental layer types.

### Tiles (2D)

Raster tiles can be displayed directly in the DOM renderer, but need to be treated as textures by the WebGL renderer.  In 2.5D they need to be draped over the terrain.

### Vectors (2D)

Vector layers can be rendered with SVG, VML or Canvas in the DOM renderer, but will need custom rendering functions in the WebGL render.

### Labels (2D or 3D)

Labels are effectively (position, label text) pairs. They need to be treated specially as they should always be drawn horizontally even if the base layer is rotated.

### Terrain elevation (2.5D)

Terrain elevation, possibly encoded in either JSON or raw VBO data, is not used in 2D.  The structure and metadata of such data is closely tied to the rendering algorithm and projection used.

### Buildings (3D)

In 3D, building data typically consists of a 3D model and a texture draped over it.  In 2D it might simply be an outline with a height (for shadows) or a more complex 2.5D object (c.f. Google MapsGL).

### Vegetation (3D)

Vegetation in 3D is typically rendered as the repetition of a set of very simple models.

### Point clouds (3D)

Point clouds are pure 3D objects, and are not used in 2D.


Miscellaneous
-------------

Maps need to maintain a global clock for animations.

Tile queues should be per-host to avoid being blocked by a single slow host.  Renderers should get tile URLs from tile layers, then request the actual image via the tile queue.  The tile queue can be reprioritized by the map when the view changes.

Colors need to be RGBA-tuples of floating point values, with appropriate conversion.

The Closure Compiler and Library provides many advantages for development and distribution.


Open questions
--------------

How to efficiently convert map coordinates into tile coordinates?  The visible tiles are not defined by a simple rectangle if the map and layer projections are different.

Do we need a "LayerView" which is a Layer (data) plus map-specific metadata, e.g. opacity, hue, saturation, terrain exaggeration, etc.?

Do we need an MVC framework like Backbone.js?  Layers are models, maps are views, controls are controllers.  It might be easier to bake in a framework from the start, although it might be conceptually cleaner not to.

Do we need some sort of capability query to determine available functionality?  For example, a DOM renderer might provide per-layer opacity control but not hue/saturation or rotation.



vim: set filetype=markdown spell spelllang=en:
