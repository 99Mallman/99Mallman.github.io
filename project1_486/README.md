# Project 1: Forest Loss of Maryland
## Michael Allman

### Topic
I plan to document forest loss across Maryland counties between 1973, 2002, and 2010 using a coxcomb map. 

### Data
The data I will use was provided by https://geodata.md.gov/imap/rest/services/PlanningCadastre/MD_LandUseLandCover/MapServer. I will be using a shapefile that depicts land cover across Maryland.

### Analysis and Data Transformation
The major obstacle is that there is no coxcomb option in QGIS that I'm aware of. Therefore, while I plan to do the plotting in QGIS, I must create a file in Rstudio of centroid points of the counties represented as coxcomb charts, then load it over a basemap of Maryland. The centroid points can be created easily in QGIS from the initial dataset. I will also need to create an additional field of the forest loss between 2002 and 2010. That can be done in QGIS as well.
  
### Outputs
The final output will be a map. It will have a chloropleth map of the most recent forest cover percentage per county of Maryland laid on top of the basemap. Each county will have a respective coxcomb chart with two bars, forest lost between 1973 and 2002 and again between 2010 and 2002. Hopefully, the map will reveal whether forest loss is esculating or slowing down on a regional scale.
