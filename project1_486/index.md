# The Plan

### Topic
I plan to document forest loss across Maryland counties between 2000 and 2018 using a coxcomb map. 

### Data
I will be using a shapefile provided by https://geodata.md.gov/imap/rest/services/PlanningCadastre/MD_LandUseLandCover/MapServer to plot the data. It consists of the counties of Maryland and has information on land cover that I probably won't use. In addition to the shapefile I will be extracting the appropriate data I need from the Hansen Global Forest Change Census, https://earthenginepartners.appspot.com/science-2013-global-forest/download_v1.6.html.

### Analysis and Data Transformation
The major obstacle is that there is no coxcomb option in QGIS that I'm aware of. Therefore, while I plan to do the plotting in QGIS, I must create a file in Rstudio of centroid points of the counties represented as coxcomb charts, then load it over a basemap of Maryland. The centroid points can be created easily in QGIS from the initial dataset.
  
### Outputs
The planned final output will be a coxcomb map. It will have a chloropleth map of the most recent forest cover percentage per county of Maryland laid on top of a basemap. Each county will have a respective coxcomb chart with 18 bars each, one for the amount of forest area lost during each year. Hopefully, the map will reveal whether forest loss is esculating or not on a regional scale. In the event that I am unable to figure out how to create a satisfying coxcomb output I will replace them with barcharts instead or make a gif of the compiled years.

This project will require me to use both Rstudio and QGIS, meaning that I'll be able to portray what I've learned from both tools over the past few weeks. Some functions will be new, like the coxcomb part while others I've done before. I'll be demonstrating that I understand how to search for appropriate functions and data, edit metadata, write functional Markdown syntax, and create an understandable layer view map. To get the Hansen data I need I will be using Google Earth Engines. I have access to a script that will provide me with the output I desire prepared from a previous project. With a little adjustment I can export a CVS table into my Google drive, then join it to my shapefile. The link to the script is as follows: https://code.earthengine.google.com/f379c7c26bea69cdc86ffcbd6737ef7a.

# Description
The final product is in some parts different than what was initially planned. Most notably the coxcomb charts have been replaced with histograms. For the purposes of this project, they would both display the same information, being the general trend of forest loss through the years. Coxcombs would simply provide a tidier look. This decision was based on necessity. Even after searching the internet I was unable to find the appropriate tools for what I wanted. I had also planned to have a chloropleth map laid beneath the graphs portraying the most recent forest cover percentage of the counties of Maryland. I instead based it on the summation of forest loss of the 18 years. I felt this way provided the viewer the means to understand the scope of the loss and simultaneously give weight to the bar graphs.

What this map is primarily about is the rate of forest loss by Maryland county. A single bar, which represents a year, that towers over the rest shows that an unprecidented amount of clearing occured in a single year. Looking at a county like Worcester, the viewer can see that they experienced massive clearing in 2001, followed by several years of lower but still high loss. In the early 2010s the rate of forest loss in Worcester fell but has since picked up once again. A county with a series of even bars such as Carroll or Anne Arundel has had a constant rate of forest loss for the past two decades. It appears that the general trend of the state losely follows Worcester's example. Most seem to have a negative slope starting from a high in the early 2000s and hitting a low around 2010 then seeing an uptick in recent years.   

The underlying chloropleth map depicts counties with the lowest total forest loss as the darkest green. Ironically, this pust Balitmore City as the "greenest" county. This alone should highlight that this map is restricted in several important ways. The coloring represents the sum alone, the hectares of forest loss proportionate to the county's size is not considered. 54 hectares is more significant in the city than it is in a county like Frederick. Secondly clearing prior to 2001 is not represented. Balitmore City looks green because it's already highly developed. Even so, we know which counties have been experiencing the largest amount of forest loss, and possibly the most development. We also know which counties have been able to maintain their forests.

[MD Forest Loss][<img src="images/logo.png?raw=true"/>](/images/index)


More on [Maryland forests](https://dnr.maryland.gov/forests/pages/mdfacts.aspx#:~:text=Each%20year%20more%20than%206000,of%20forest%20and%20marsh%20land).



