# Extracting Elevation Along a Cross Section Using Google Earth Engine

## Overview
This script extracts elevation values along a defined cross-section using Google Earth Engine (GEE). A line segment is drawn, and sample points are placed along it at equal intervals. The script then retrieves elevation values at each of these locations using the CGIAR SRTM 90m Digital Elevation Model (DEM). The results are displayed in a chart for visualization.

---
## Code Explanation
### **Step 1: Define Cross-Section and Sampling Parameters**
A line segment (CrossSection1) is used as the cross-section for analysis. A user-defined variable (numbPoints) specifies the spacing between sample points.
```javascript
var CrossSection1 = geometry; // Define a straight line segment
var numbPoints = 5000; // Distance between sample points in lat/lon
```
### **Step 2: Generate Equally Spaced Points Along the Line**
Extract the starting (point1) and ending (point2) coordinates of the line:
```javascript
var point1 = ee.List(CrossSection1.coordinates().get(0));
var point2 = ee.List(CrossSection1.coordinates().get(1));
```
Generate a sequence of equally spaced coordinates along the line:
```javascript
var XmapList = ee.List.sequence(point1.get(0), point2.get(0), null, numbPoints);
var YmapList = ee.List.sequence(point1.get(1), point2.get(1), null, numbPoints);
```
### **Step 3: Construct a Feature Collection of Sample Points**
Using the generated coordinate lists, construct a set of equally spaced points along the line:
```javascript
var points = ee.FeatureCollection(XmapList.map(function(Xcoord){
  var Ycoord = YmapList.get(XmapList.indexOf(Xcoord));
  return ee.Feature(ee.Geometry.Point([Xcoord, Ycoord]));
}));
```
Each Feature in the collection represents a sample location along the cross-section.
### **Step 4: Extract Elevation Data from the DEM**
Load the CGIAR SRTM 90m DEM image:
```javascript
var DEMIMAGE = ee.Image("CGIAR/SRTM90_V4");
```
Retrieve elevation values at each sampled location using reduceRegions():
```javascript
var elevation = DEMIMAGE.reduceRegions(points, ee.Reducer.first(), 10);
```
reduceRegions() extracts elevation values at multiple points.
The first reducer ensures that only the first intersecting elevation value is selected per point.
A scale of 10 meters is used to refine the resolution of sampling.

### **Step 5: Visualize Elevation Profile**

The extracted elevation values are plotted using the ui.Chart.feature.byFeature() function:
```javascript
print(ui.Chart.feature.byFeature(elevation));
```
This generates a chart displaying the elevation variation along the cross-section.

## Key Features

✔ Efficient Sampling: Generates points at fixed intervals along a straight-line cross-section.

✔ High-Resolution Elevation Data: Uses 90m SRTM DEM to obtain elevation values.

✔ Automated Charting: Displays elevation profile directly in the GEE interface.

## Potential Applications

📍 Topographic Analysis: Study elevation changes along rivers, roads, or mountain slopes.

📍 Hydrological Studies: Evaluate terrain profiles for flood modeling and watershed delineation.

📍 Infrastructure Planning: Assess slope variations for transportation and civil engineering projects
