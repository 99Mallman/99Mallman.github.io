# Project 1: Forest Loss of Maryland
## Michael Allman

### Topic
I plan to document forest loss across Maryland counties between 2000 and 2018 using a coxcomb map. 

### Data
I will be using a shapefile provided by https://geodata.md.gov/imap/rest/services/PlanningCadastre/MD_LandUseLandCover/MapServer to plot the data. It consists of the counties of Maryland and has information on land cover that I probably won't use. In addition to the shapefile I will be extracting the appropriate data I need from the Hansen Global Forest Change Census, https://earthenginepartners.appspot.com/science-2013-global-forest/download_v1.6.html.

### Analysis and Data Transformation
The major obstacle is that there is no coxcomb option in QGIS that I'm aware of. Therefore, while I plan to do the plotting in QGIS, I must create a file in Rstudio of centroid points of the counties represented as coxcomb charts, then load it over a basemap of Maryland. The centroid points can be created easily in QGIS from the initial dataset. I will also need to create an additional field of the forest loss between 2002 and 2010. That can be done in QGIS as well.
  
### Outputs
The planned final output will be a coxcomb map. It will have a chloropleth map of the most recent forest cover percentage per county of Maryland laid on top of a basemap. Each county will have a respective coxcomb chart with 18 bars each, one for the amount of forest area lost during each year. Hopefully, the map will reveal whether forest loss is esculating or not on a regional scale. In the event that I am unable to figure out how to create a satisfying coxcomb output I will replace them with barcharts instead.

This project will require me to use both Rstudio and QGIS, meaning that I'll be able to portray what I've learned from both tools over the past few weeks. Some functions will be new, like the coxcomb part while others I've done before. I'll be demonstrating that I understand how to search for appropriate functions and data, edit metadata, write functional Markdown syntax, and create an understandable layer view map. To get the Hansen data I need I will be using Google Earth Engines. I have access to a script that will provide me with the output I desire prepared from a previous project. With a little adjustment I can export a CVS table into my Google drive, then join it to my shapefile. The link to the script is as follows: https://code.earthengine.google.com/f379c7c26bea69cdc86ffcbd6737ef7a.
