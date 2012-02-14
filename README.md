OpenLayers and WebGL integration
================================


Objective
---------

This document attempts to technically analyse options for integrating WebGL and OpenLayers.

There are currently three proposals:

1. Add a drawing framework abstraction layer to encapsulate differences between DOM rendering, 2D canvas, and WebGL (3D canvas).

2. Create a WebGL-specific core and add an OpenLayers-compatible API wrapper.

3. Create a familiar, very OpenLayers-like, API around a WebGL core, with tweaks where required to fit better with a WebGL rather than a DOM/CSS core.

In this document the phrase "manually" refers to operations that must be managed by the programmer in Javascript.  For example, the graphics card memory must be manually managed by the programmer: there is no garbage collection.


OpenLayers and the DOM
----------------------

An OpenLayers map creates a "map viewport" div and adds it to the user-provided div. It also creates a "layer container" div, and adds it to the map viewport div.

When a layer is created a div is created. When a layer is added to the map its div is added to the layer container div.

To move its layers the map moves the layer container div by acting on the DOM. We change the layer container's top/left style properties today, but we could also rely on CSS3 transform for capable browsers.

The tile-related code (Layer.Grid, and Tile.Image) is tightly coupled with the DOM. Tiles are img tags that change visibility and opacity for CSS fade-in animations. Grid layers create additional divs for zoom animation, and can scale their divs for continuous zooming.

Vector layers are divs including SVG, VML or Canvas elements. The drawing is handled by Renderers. We have three renderers today: SVG, VML, and Canvas.

So the displaying of data layers heavily relies on the DOM today, with DOM-related code all other the place across the lib. Creating a single extension point, on which a drawing abstraction layer would be built, would prove very difficult I think.

Another key concept of OpenLayers is "Controls". Controls basically listen to DOM events occurring on the map viewport div and acts on the map object.

For example the DragPan control listens to mousedown, mousemove, and mouseup events, and calls map.pan() to actually move the map. This particular control works with pixels only, and doesn't need to know about geographic coordinates - map.pan received pixel deltas. So it's possible that such a control can be used with a  canvas-based map.


Technical considerations: not specific to 3D
--------------------------------------------

WebGL requires a per-map rendering context.  This context is required for all drawing operations.  Multiple maps on the same web page need separate rendering contexts.

The rendering context can be lost at any time.  When this happens its entire state must be manually reconstructed.

Many objects, for example tile images and coordinates, must be copied to the graphics card memory and this memory must be manually managed and freed - there is no garbage collection.

Example: `Tile.Image` and `Feature.Vector` need to manage graphics card memory.

There is no DOM beyond a single WebGL canvas element. Events (e.g. feature interactions) must be routed and handled manually.

Example: code calling `document.createElement`, `Util.createImage`, `Util.createDiv`, `Util.createAlphaImageDiv`, or `Util.modifyDOMElement` may need re-writing.

WebGL coordinates are in view space (floating point coordinates from -1 to 1 in both the X and Y directions).  Conversion between view space and screen space (pixels) must be handled manually, including handling resizes and maintaining the correct aspect ratio.  Similarly, the projection from world space (map projection) to view space must be handled manually, and all these transformations must be possible in both directions.

WebGL uses 32-bit floating point coordinates (and sometimes 20-bit floating point on mobile devices) which only provide sufficient precision for limited zoom levels.  Extra transformations are required to maintain precision at moderate to high zoom levels.

Seamless rendering of tiles requires a two-pass rendering process: first the tiles are written to a hidden image (framebuffer) and then this framebuffer is resampled to produce the final rendered image. It is not possible to render tiles in a single step without visual artefacts.

The graphics card requires expensive per-layer configuration (state changes) to render different layers.  These state changes should be minimised where possible to avoid performance penalties.  This means that layers may need to be rendered to their intermediate framebuffers in a different order to their z-indices.

Example: it is no longer possible to rely on attaching DIVs to the DOM correctly to manage the order of layers.  A core rendering function/compositor must be written, which has no direct equivalent in OpenLayers.

There is no CSS.  All feature visibility, layer ordering, animations, effects must be programmed manually.

Example: any code that sets a style on a DOM element may need to be re-written.

The browser will not know when to redraw the scene, for example when an image tile loads or while an animation effect is in progress.  All handling of dirty state must be done manually.

Continually redrawing the WebGL context every frame leads to significant power consumption and is a non-started for battery-powered devices.   At least "static" and "animating" modes must be supported (c.f. requestAnimationFrame).

Example: any code that relies on setting CSS styles may need rewriting.

There are no high level 2D APIs for drawing arbitrary polygons or styled lines.  Polygons must be manually dissected into triangles before being rendered.  Lines must be drawn manually.

Example: any code that relies on SVG, VML or 2D canvas functions may need rewriting.


Technical considerations: specific to 3D
----------------------------------------

If the API is to be eventually extended to support 3D, fundamental objects must be extended or completely replaced.  For example, 2D coordinates must have an extra field added (z or elevation), and simple bounding boxes must be replaced with perspective projection-aware view frustums.  It is no longer possible to iterate over visible tiles with a nested for loop, instead cameras and streaming level-of-detail techniques must be employed.


Useful links
------------

* http://www.html5rocks.com/en/tutorials/webgl/webgl_fundamentals/



vim: set filetype=markdown spell spelllang=en:
