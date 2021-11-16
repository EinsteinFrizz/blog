---
layout: post
title:  "Using Blender as a GIS Visualisation Tool"
date:   2021-02-11 12:00:00 +1300
categories: assignment
image: '/images/hunuafull1.jpg'
---

Maps have many purposes, from navigation to planning to data visualisation, however many people overlook the aesthetic quality of maps. Maps can be works of art.

When thinking of a map that one could hang on a wall as art, images such as the [London Underground Map](http://content.tfl.gov.uk/standard-tube-map.pdf) ('the Tube map') maybe come to mind (they certainly do for me, as we had one on our wall for many years).

However, map nerds will likely agree that more 'conventional' maps can also be beautiful. A particularly striking example (in my opinion) is [this Soviet population map from 1929](https://tile.loc.gov/image-services/iiif/service:gmd:gmd7:g7001:g7001e:mf000013/full/pct:12.5/0/default.jpg).

What more can be added to these already aesthetically pleasing maps? The answer lies within the rapidly growing digital art community. Sean Conway, known on Instagram and Twitter as [`@geo_spatialist`](https://www.instagram.com/geo_spatialist/), is a notable presence in the digital map art scene. Conway adds terrain data to largely historic maps, turning them into three-dimensional models that can be rendered into digital art.

<img src="https://pbs.twimg.com/media/Em60jQVXMAAgvFA?format=jpg&name=4096x4096" alt="A map of Washington state, USA, with three-dimensional terrain added in.">
Art by Sean Conway, with terrain data overlaid by an 1883 map of the Washington state area.

## From Animation Software to GIS Tool

[Blender](https://www.blender.org/) is a free, open-source 3D rendering software with versatile functions including animation and video rendering. Its target audience is artists, especially those who make 3D animations, however [add-ons](https://docs.blender.org/manual/en/latest/editors/preferences/addons.html) such as [BlenderGIS](https://github.com/domlysz/BlenderGIS) extend the software's functionality far beyond its initial scope.

As with any specialist software, there is a steep learning curve associated with learning to use Blender. Despite having used its video editing functionality for a previous university project, the level of understanding of 3D modelling and rendering required for this project was beyond any previous experience.

## How It's Made

The [tutorial](https://wesleybarrgis.wordpress.com/2020/05/19/how-to-make-a-3d-map-in-blender/) by [Wesley Barr](https://wesleybarrgis.wordpress.com/) from 2020 was a key starting point in the creation of these 3D maps, as was the [tutorial](https://somethingaboutmaps.wordpress.com/blender-relief-tutorial-getting-set-up/) by [Daniel Huffman](https://somethingaboutmaps.wordpress.com/).

*Note: the exact locations of some of these menu options have changed with Blender updates, but they are all generally in the same place.*

In general, the process for making relief-displaced historic maps is as follows:

<img src="/images/FlowchartWhite.svg" alt="A flow chart of the process of making these historic relief maps.">

1. Download historic maps from a source such as the University of Auckland's [GeoDataHub](https://geodatahub.library.auckland.ac.nz/geonetwork/srv/eng/catalog.search#/home) in a georeferenced format (if possible, otherwise the images will have to be georeferenced manually) and a Digital Elevation Model (DEM) from [LINZ](https://data.linz.govt.nz/).

1. Use QGIS to [crop](https://docs.qgis.org/3.16/en/docs/user_manual/processing_algs/gdal/rasterextraction.html#clip-raster-by-mask-layer) the margins out of the historic maps (if applicable) and [merge](https://docs.qgis.org/3.16/en/docs/user_manual/processing_algs/gdal/rastermiscellaneous.html#merge) them into one image.

1. Use QGIS to [crop](https://docs.qgis.org/3.16/en/docs/user_manual/processing_algs/gdal/rasterextraction.html#clip-raster-by-mask-layer) the historic map and DEM to the same extent, preferably choosing an aspect ratio consistent with the desired final output, i.e. 210:297 for A4/A3 print, or 16:9 for desktop backgrounds. Note: export the DEM as a rendered image, rather than the raw data &mdash; the raw data TIFF has caused issues in the past from personal experience.

1. Optional: use [GIMP](https://www.gimp.org/) to save the DEM in a 16-bit unsigned integer format (this was recommended in Huffman's tutorial), and recolour the historic maps for aesthetic improvements. Resizing the images to a lower resolution may also be necessary depending on the hardware being used to render images.

1. In Blender's preferences, under `Edit > Preferences > Add-ons` search `Image` and enable `Import-Export: Import Images as Planes`. Also, change the rendering engine from `Eevee` to `Cycles` under the `Render Properties` tab. Enable the `Experimental` feature set. This allows easier importing of the historic maps, and more accurate displacement of the plane when we add the DEM.

1. Import the historic map into Blender using `File > Import > Images as Planes`. It may be necessary to adjust the rotation of the plane to get it to lie horizontally.

1. Switch to the `Shader Editor` (shortcut: `Shift + F3`) and add an image texture and a displacement vector via `Add > Texture > Image Texture` and `Add > Vector > Displacement`. In the Image Texture box just added, open the DEM. Drag from the `Color` node on the DEM box to the `Height` node on the displacement box. Change the `Midlevel` to 0. It will likely be necessary to adjust the `Scale` to a small value like 0.1. Drag the `Displacement` node from the displacement box to the `Displacement` node on the `Material Output` box.

1. Back in the `3D Viewport` (shortcut: `Shift + F5`), add two light sources using `Add > Light > Sun`. Position one to the top left of the plane, and one directly above the centre of the plane. The top left sun will act as the actual sun, and should have a strength value of around 2-3. The other sun above the plane acts as a wash of light, and should have a strength value of less than 1.

1. Set the camera to an orthographic view, and align it directly above the centre of the plane. Change the scene resolution under `Output Properties` to the desired output image size in pixels. Adjusting the `Orthographic Scale` value will align the camera so its viewshed is exactly the extent of the plane.

1. Currently, the rendered image will be 'faking' the displacement of the plane using the DEM to create a hillshade effect. To actually displace the plane, it needs to be [subdivided](https://docs.blender.org/manual/en/latest/modeling/modifiers/generate/subdivision_surface.html) using [`Adaptive` subdivision](https://docs.blender.org/manual/en/latest/render/cycles/object_settings/adaptive_subdiv.html), and the [displacement method](https://docs.blender.org/manual/en/latest/render/materials/components/displacement.html) needs to be changed from `Bump Only` to `Displacement Only`.

Play around with the hue of the lighting (you can use a blackbody node under the `Shader Editor` via `Add > Converter > Blackbody` to specify a colour temperature) and other parameters to get the aesthetic you like best.

Post-processing in image editing software such as GIMP may help if colour saturation and white balance become difficult to control in Blender.

<p></p>

## The Output

Using the [NZMS 117(A) series of maps](https://geodatahub.library.auckland.ac.nz/public/web/docs/NZMS_series.pdf), and combining sheets N43 and S12, the following image of the Hunua area was created:

<img src="/images/hunuafull1.jpg" alt="A terrain-rendered cadastral map of the Hunua region.">

## Harder Than It Looks

There are several notable difficulties with creating these terrain-rendered historic maps. These include computing time and resource demand, and difficulties associated with using old map data.

##### Computing Time and Resource Demand

The image above was rendered on a computer with an Intel i3 four-core 3.0 GHz processor, 8 GB RAM, and an integrated Intel 4600 graphics card. These specs are not atypical of all-in-one computers, however for comparison it is less powerful than the [lowest-spec 2019 iMac](https://www.techradar.com/nz/reviews/apple-imac-2019-27-inch). While this is proof that these images can be made by anyone with a modern enough computer, the practicality of creating this output on such a computer poses some difficulty.

Generating the above image on the all-in-one computer took many hours. Some of this time was spent on performing the steps outlined in the method, however most of it was waiting for the computer to process the necessary operations. It should be noted that the original size of the combined NZMS map sheets was approximately 49000 &times; 66000 pixels in size. This was scaled down to approximately 2600 &times; 3500 pixels. Even the scaled-down version of the map, less than 0.3% of the original size, took hours to process.

A key barrier, therefore, to making this form of art, is the computer which one has access to. Trying to run the operations on the full-size images borderline crashed the all-in-one computer, leaving programs stuck on 'Not Responding' all night. Thus, the hardware on which the images are processed creates an upper bound on the image quality one can use. Additionally, computing time decreases as computing power increases, meaning the power of the hardware being used determines the time taken to process each operation, prolonging the process on lower-spec hardware. This leads to the unsurprising conclusion that using a more powerful computer streamlines computing processes significantly.

##### Old Map Data Difficulties

Using old maps comes with a unique set of complications. One of the most notable in this project was coordinate reference systems. Current maps of New Zealand use the [New Zealand Transverse Mercator 2000](https://epsg.io/2193) coordinate reference system. As the name suggests, this uses data from 2000 and is therefore only used in maps made in the 21st century. Prior to this, the [New Zealand Map Grid](https://epsg.io/27291) was used.

The elevation data from LINZ is modern data, generally less than 10 years old. This means it uses the New Zealand Transverse Mercator coordinate reference system. Therefore, there needs to be standardisation between the historic map and elevation data so they line up properly.

Thankfully, QGIS allows for easy on-the-fly conversion between these coordinate systems, including older systems like the New Zealand Map Grid that use less standard measurements (the British yard) and datums (NZ Geodetic Datum 1949).

The transformation between the Map Grid and Transverse Mercator involves both translation and rotation. Translating images does not generally impact their quality, however rotating images can. Vertical straight lines that are rotated through angles that are not multiples of 90&deg; become blurred due to the square pixel shape used in images. Therefore, since either the historic map or the DEM has to be rotated to match up with the other image, it would be more ideal to transform the DEM to maintain the clarity of the historic map.

However, the transformation between the two coordinate reference systems (for these files, at least) is not perfect. When converting the DEM to the Map Grid system, it ends up in a location thousands of kilometres away from the historic map. Similarly, any polygons drawn over the historic map in the Map Grid system that were used to clip by a mask layer produced images that were tens of kilometres away from their actual location. After many troubleshooting attempts, it was decided that the best course of action was to sacrifice some image quality and convert the historic map to the New Zealand Transverse Mercator system.

With everything in the New Zealand Transverse Mercator coordinate system, the operations went smoothly. However, the output was tilted due to the rotation caused by transforming the map to another reference system, as seen below.

<img src="/images/hunuafull1raw.jpg" alt="A version of the map of Hunua above with less saturated colours and a noticeable slant.">

Therefore, as part of the post-processing done in GIMP, the image had to be rotated back to its original position. This, again, sacrifices image quality somewhat.

To make matters worse, some of the historic map images (namely, for the Waitakere area) did not match up correctly with the DEM data even with the coordinate system transformations. This resulted in noticeable discrepancies, especially at coastlines.

## Digital Art: More Than Clicking Buttons

One might expect that producing these digital artworks from already computer-readable data is trivial, simply involving a few button presses. Indeed, this process could theoretically be entirely scripted, given QGIS, Blender, and GIMP's support of scripting in languages such as Python. However, this ignores the intricacies of working with imperfect historic data as well as the inherent subjective aesthetics that go into producing these images.

With enough time and love (and maybe a better computer), creating works of art using geospatial data is achievable by anyone.

<img src="https://pbs.twimg.com/media/EV7T9dAXgAAB1EL?format=jpg&name=4096x4096" alt="A terrain three-dimensional map of California, USA with land use zones coloured and occasional clouds scattered above the map.">
Sean Conway's render of California, USA.
