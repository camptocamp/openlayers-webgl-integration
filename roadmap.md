OpenLayers / WebGL / 3D Roadmap
===============================

This document proposes a path for the evolution of OpenLayers to take advantage of rich new functionality and performance offered by embracing WebGL.  As WebGL becomes more widespread, initially on the desktop with mobile following, there will be a convergence between desktop applications like QGIS and web mapping libraries like OpenLayers.  The result will be rich geospatial web applications, combining the ubiquitous availability of web mapping with the power of their desktop counterparts.

As WebGL offers direct access to the graphics hardware on the client, it offers greater responsiveness that traditional web mapping libraries that must manipulate the browser's DOM to effect visual changes.   However, the WebGL model is fundamentally different to the traditional DOM/HTML/CSS web maps and as such OpenLayers API compatibility is probably best served by implementing a compatibility layer around a WebGL, 3D-compatible core, rather than attempting to adapt a core optimized for different goals.  Longer term, the WebGL language of graphics and coordinates is closer to the natural representation of geographical features than HTML elements and CSS styles, and as such provides a more natural base for building geospatial web applications.

This is a unique opportunity.  Four distinct milestones are proposed, each offering powerful new features and building on the solid foundations laid down by the previous milestone.



Milestone 1: 2D slippy maps with WebGL
--------------------------------------

### Client benefits

* Large vector layers

* Client-side re-projection of vector and raster layers

* Richer map interactions

* Richer per-layer visual controls

* Lower bandwidth costs when using multiple layers

* Eye candy

* "3D ready" API

### Core technologies demonstrated

* Use of WebGL

* Use of advanced accelerated graphics features, e.g. fragment and vertex shaders

* Integration with existing geospatial data sources

* Interaction with geographical features without using the browser's DOM

* Management of geospatial data on both the CPU and the GPU

* Management of both animated and low-power "on demand" rendering modes

### Suggested demonstration

* New interactions and eye candy with existing 2D data

### Description

WebGL, as well as being a rich and capable platform for 3D graphics, is equally powerful as a 2D graphics platform and offers possibilities and performance that would be difficult or even impossible to achieve with tradition DOM/HTML/CSS-based web maps.

Fast client-side vector operations allow:

* Richer map interactions, such as continuous rotation and smooth zooming.

* Efficient handling of millions of vector features on the client.

* Re-projection of both raster and vector layers on the client, allowing data to be mixed from multiple projections simultaneously, without over-heating the tile servers.  Such re-projections can be dynamic, allowing the user to switch projections instantaneously with the click of a mouse.

Fast client-side pixel operations allow:

* Much more control over layer blending than simple opacity, for example saturation, hue, and highlighting of color-coded features.

* Multiple tile layers can be packed into the red, green and blue color channels of a single tile, and then unpacked and displayed with the desired colors on the client.  This can massively increase speed and reduce bandwidth costs.

* Visual effects, such as dynamic relief shading based on the current sun position, building shadows, and animated water.

This milestone could be extended to allow limited tilt of the camera and the display of 3D building data in orthographic projections.

### Extensions

* Animated zoom

* Limited tilt close to the surface (all tiles at same zoom level, limited visible area)




Milestone 2: 3D terrain in Cartesian space
==========================================

### Client benefits

* Calibrated display of terrain in Swissgrid projection (e.g. DHM25 and swissALTI3D)

* Display of Swisstopo's 3D data (e.g. swissBUILDINGS3D)

* Free 3D camera

* Perspective and orthographic projections

* Cross-browser, without plugins

### Core technologies demonstrated

* True 3D terrain

* Streaming of terrain and building model data with bandwidth/detail control

* Core chunked level-of-detail algorithm

* Terrain collision detection

* 3D picking

* Server-side terrain data processing

* Server-side building data processing

### Suggested demonstration

* "Fly through" of the Valais with streaming DHM25 and SWISSIMAGE data

### Description

Building on the core technologies developed in milestone 1, milestone 2 adds true 3D data and a 3D view.  By using Cartesian space rather than a globe, the model is directly compatible with existing data in the Swissgrid projection, and as such is immediately useful.

Complete virtual globes require coordinate systems and projections that cover the entire world. A true 3D solution in Cartesian space offers the possibility to use local, more appropriate coordinate systems.

The core functionality added in this milestone addresses the algorithms and tools needed to handle 3D data efficiently, both optimizing existing data on the sever for streaming to the client, and displaying and interacting with it on the client.

This milestone also provides a baseline for adding new features and functions, such as:

* Fully-integrated terrain, building and vegetation tiles (like Nokia Maps 3D WebGL)

* Vegetation models

* Animated models

* Visual effects such as horizon glow, background star maps and fog

### Extensions

* 3D buildings with perspective projection (fisheye effect)

* Building shadows on flat plane

* Building shadows on 3D terrain (complex)



Milestone 3: The Earth as a sphere
==================================

### Client benefits

* 3D globe

### Core technologies demonstrated

* Navigation on a sphere

* Feature hit testing on a sphere

* Multiple raster and terrain layers

* Core chunked level-of-detail algorithm applied to a sphere

* Extension of server-side 3D data processing tools to a sphere

### Suggested demonstration

* True 3D globe with Swisstopo data displayed in Switzerland and global datasets (e.g. SRTM, Landsat) used elsewhere

### Description

This milestone adapts the technology built in milestone 2 to a true 3D globe.  The core algorithms remain unchanged, the primary developments are related to adapting the server-side processing code to a sphere, and more complex navigation and feature hit-testing on the client.

### Extensions

* Seamless zoom out from ground (Cartesian) to globe (Sphere) (complex)



Milestone 4: The Earth as an ellipsoid
======================================

### Client benefits

* True 3D globe with WGS84 ellipsoid

### Core technologies demonstrated

* Navigation on an ellipsoid

* Feature hit testing on an ellipsoid

* Extension of server-side 3D data processing tools to a sphere

### Description

This is an extension of milestone 3, but with more complicated mathematics required to handle navigation, data processing and picking.



Open questions
==============

* Is it possible to degrade gracefully on non WebGL-compatible browsers, for example to fallback to a DOM, SVG or Canvas renderer? (complex)



vim: filetype=markdown spell spelllang=en textwidth=0:
