---
layout: post
title:  "Risky Users in Tawharanui and Waitakere Ranges Regional Parks"
date:   2021-01-06 12:00:00 +1300
categories: assignment
image: '/images/kauridieback.jpg'
---
<link rel="stylesheet" href="https://unpkg.com/leaflet@0.7.7/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@0.7.7/dist/leaflet.js"></script>

## What is Kauri Dieback?

[![An aerial photo of a grey, dead kauri tree in the middle of a forest.](/images/kauridieback.jpg)](https://www.kauridieback.co.nz/what-is-kauri-dieback/)

Kauri dieback is a disease afflicting kauri trees (*Agathis australis*) that is caused by a fungal infection of the tree's roots. Its spores live in the ground, moving around by swimming through soil moisture. While the fungus spores can move through space to infect more trees by themselves, other agents can spread the spores much farther than their natural range.

Key agents in the spread of kauri dieback are animals and people. Through the movement of soil stuck to feet, hooves, or shoes, for example, kauri dieback spores can be spread far away from their original location. This effect is multiplied by humans' increased mobility due to vehicles and their desire to travel to forested areas for recreation or leisure.

## Mapping Problem User Groups

In order to better understand the spread of kauri dieback, DoC (Department of Conservation) Rangers were interviewed and asked to identify five key problematic user groups and their most common locations.

First, the rangers were asked to identify user groups of their respective forest areas, then rate their risk factor with regards to spreading kauri dieback.

<img src="/images/waitakeregrouplist.jpg" alt="A messy handwritten list of user groups and their riskiness ratings.">

This generated a large list of risky user groups, which the rangers were then asked to narrow down to just five of the most risky kauri dieback spreaders.

The locations that each of these problematic user groups frequent were indicated on large printed maps using dye markers. The rangers put a dot in any grid square they felt that a user group frequented.

<img src="/images/tawharanuimapping.jpg" alt ="A photograph of multiple people's hands holding dye markers placing dots on a large printed map.">

<img src="/images/waitakereblobs.jpg" alt="A photograph of a topographic map that has been annotated with dots.">

This data is rather chaotic and it is difficult to see any patterns in this form. Therefore, the data needed to be digitised into separate layers for patterns to emerge.

In short, the grid squares were turned into polygons, and each user group was assigned a column in the polygons' attribute table. If a blob was present in that square, a value of 1 was assigned to that column for the grid square. If a cross was also present, indicating high risk, a value of 2 was assigned to that column.

## Data Digitisation

The process used to digitise the data is as follows:

1. Georeference the map images that were used for annotation in QGIS using the [Georeferencer](https://docs.qgis.org/3.16/en/docs/user_manual/working_with_raster/georeferencer.html) tool
    1. Download shapefiles for the New Zealand coastline and New Zealand road centrelines from [LINZ](https://data.linz.govt.nz/)
    1. Use the intersections of roads as Ground Control Points, selecting them on the map image and the already geographically referenced road centreline shapefile -- do so for **at least four points**
        * Use five or more points if it is difficult to find an even spread of road intersections in places with few roads such as Hunua
    1. Run the georeferencer tool using the selected GCPs with the transformation type set to Thin Plate Spline
        * Thin Plate Spline was chosen following [this tutorial](https://www.qgistutorials.com/en/docs/georeferencing_basics.html)

1. Create a grid of polygons over top of the georeferenced map image to match the grid squares within the image
    1. Determine the extent of the map images by creating a rectangular polygon along the outermost gridlines
        * This removes the edge of the map images that has the 6-digit grid reference information, thereby making grid creation easier
    1. Use the [Create Grid](https://docs.qgis.org/3.16/en/docs/user_manual/processing_algs/qgis/vectorcreation.html#create-grid) tool to create a grid of cell size 400 metres (the same as the grid in the map image) that uses the polygon just created as the extent

1. Open the Attribute Table of this grid layer and add fields for each of the five top risk factors identified by the DoC Rangers -- the data type should support integers

1. Enter the presence of identified risks in to the Attribute Table using the following format:

    <table>
        <tr>
            <th>Value</th>
            <th>Description</th>
        </tr>
        <tr>
            <td>0</td>
            <td>Risk factor not present</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Risk factor present</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Risk factor present in identified high levels ("highly sensitive or risky")</td>
        </tr>
    </table>

1. Optional: summarise total risk to an area
    1. Add a 'Riskiness' field in the Attribute Table
    1. Use the [Field Calculator](https://docs.qgis.org/3.16/en/docs/user_manual/working_with_vector/attribute_table.html#using-the-field-calculator) to fill out this field with the sum total of the five individual risk factors
        * My expression looked like this for Tawharanui: `Researcher + Livestock + DogWalker + Staff + HiRiskUser`

1. Visualise the risk factors by setting the grid to be symbolised by the value of one of the fields in its Attribute Table in `Properties > Symbology`

<p></p>

The output of the data digitisation can be seen in the map below. It is much more readable than the original data on the printed maps, and computer analysis can be performed since it is digital data.

<p></p>

<div id="map1" style="width: 75vw; height: 75vh; margin: 0 auto;"></div>

<p></p>

<div id="map2" style="width: 75vw; height: 75vh; margin: 0 auto;"></div>

<script type="text/javascript">
var tawharanuiMap = L.map('map1').setView([-36.3685, 174.8395], 13);

L.tileLayer('http://tiles-a.data-cdn.linz.govt.nz/services;key=9f3abe891f4e4e34938959eef5608b45/tiles/v4/layer=50767/EPSG:3857/{z}/{x}/{y}.png', {
	attribution: 'Tiles &copy; LINZ - Land Information New Zealand'
}).addTo(tawharanuiMap);

var waitakereMap = L.map('map2').setView([-36.9455, 174.5274], 12);

L.tileLayer('http://tiles-a.data-cdn.linz.govt.nz/services;key=9f3abe891f4e4e34938959eef5608b45/tiles/v4/layer=50767/EPSG:3857/{z}/{x}/{y}.png', {
	attribution: 'Tiles &copy; LINZ - Land Information New Zealand'
}).addTo(waitakereMap);

</script>

<p></p>

## What This Data Shows

Due to the different scales of Tawharanui and Waitakere, it can be easier or more difficult to identify patterns by eye. Nevertheless, both datasets show patterns.

The patterns shown at Tawharanui include:
* Both Researchers and Staff were present everywhere in the park (and outside the fence). Staff were considered high-risk users in two areas: outside the predator-proof fence, and on the North Coast Track.
* Livestock were present across the park except for the end of the peninsula near Takatu Point, with clusters of high risk areas.
* High-Risk Users from other areas were seen as a risk everywhere, especially along the North Coast Track and at Anchor Bay. This is also where Dog Walkers were seen as most risky.

The patterns shown at Waitakere include:
* Social Media Users showed risk only in specific places. These places coincided with features such as Lion Rock at Piha and the dam at the Upper Nihotupu River.
* Locals and Dog Walkers tended to appear near settlement or on roads and large tracks.
* Mountain Bikers very clearly followed tracks, but stayed away from roads.
* Hunter entry points were located along major roads.
