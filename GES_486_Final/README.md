# Forested Lake Buffers of Maryland
### GES 486, Final
This repository is structured to clearly document the process of this project. Any raw data and their sources used in the making of this project can be found in the data folder. The bin folder contains any tampered data sets after transformations have been made. Any files in it can be viewed as intermediate steps made between retrieving the raw data and the final output.

## Introduction
The goal of this project was to create several different forms of maps that explore how well Maryland lakes are buffered with vegetation. Maryland defines forested areas as [10% canopy](https://dnr.maryland.gov/forests/Pages/Forest-Tree-Data.aspx#:~:text=Resolution%3A%20The%20FIA%20considers%20a,at%20least%20120%20feet%20wide) and recommends at least [50 to 100 foot buffers](https://dnr.maryland.gov/forests/Pages/programapps/ripfbi.aspx#:~:text=Buffer%20widths%20of%2050%2D100,quality%20and%20fish%20habitat%20improvement). This project includes three separate maps. The first depicts the percent of of adequately buffered area by Maryland county in relation to to canopy cover. The second is a bivariate map comparing the same variable as before with the percent of the working population in agriculture. The last is an interactive webmap so views can explore individual Maryland lakes.  

## Data
Most of the data was provided by [Maryland.gov](https://data.imap.maryland.gov/). From there I downloaded and used three shapefiles and a raster data set. [Land cover](https://data.imap.maryland.gov/datasets/maryland-land-use-land-cover-county-land-use-land-cover-2010?geometry=-80.737%2C38.071%2C-73.799%2C39.568), [lakes (detailed)](https://data.imap.maryland.gov/datasets/71a539948a5b4751b3b676aec5368ed5_3?geometry=-76.950%2C39.430%2C-76.516%2C39.523), and [streams (detailed)](https://data.imap.maryland.gov/datasets/maryland-waterbodies-rivers-and-streams-detailed?geometry=-80.737%2C38.070%2C-73.799%2C39.568). The raster data was the [canopy cover](https://data.imap.maryland.gov/). I also used data provided by the American Census Bureau. American Community Survies (ACS) data is available for free download at [https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr](https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr). Every table has a different code which can be used to identify the specific data you desire. In Rstudio, with an API key, it is possible to extract tables by their codes to download to your computer. I used the codes  C24010_001, C24010_067, and C24010_031 to identify the percent of working population in agriculture in Rstudio for the ACS 2019. I also used two different basemaps in the making of my maps: ESRI imagery and Carto Light.

## Analysis and Transformations
I separate the processes and transformations of data into two sections. First I describe everything I did in Rstudio, then likewise in QGIS.

### Rstudio
The entire Rstudio process can also be found in the repository.

---
title: 'Maryland Lake Buffers'
author: "Michael Allman"
date: "5/19/2021"
output: html_document

---
#### Introduction
The goal of this project was to examine the extent to which Maryland lakes are adequately buffered by vegetation. In addition I wanted to compare the extent of buffering to the percent the population working in agriculture to see what patterns would arise. This script downloads data and performs several transformations before exporting the results into QGIS, where the final analysis will be conducted.
```{r setup, include=FALSE}
#setup
knitr::opts_knit$set(root.dir = "path/Final_Works_8")
library(tidyverse)
library(tidycensus)
library(ggplot2)
library(sf)
library(sp)
library(stringr)


# The following line is in regards to the api key.
census_api_key("YOURKEYHERE", overwrite = TRUE, install = TRUE)
readRenviron("~/.Renviron")
```

#### Downloading data
I have downloaded shapefiles of Maryland canopy, streams, and lakes separately. These all can be found at [Maryland.gov](https://data.imap.maryland.gov/). 

I used `tidycensus` to download the total working population (C24010_001) and those of which work in agriculture (C24010_019 and C24010_031) for all of Maryland by counties. The above codes also include foresters and fishermen but the detailed occupation table data, which exclusively provides agricultural occupations, did not appear to be complete so this data set is a compromise.
```{r download}
# Download total occupation and agricultural worker ACS 2019 Census data from https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr for Maryland.
MD_agri = get_acs(geography = "county",
      variables = c("total_occ" = "C24010_001",# Total working population above 16.
                    "agri_female" = "C24010_067", # Female agricultural workers
                     "agri_male" =  "C24010_031"), # Male agricultural workers
                          year = 2019,
survey = "acs5",
state = c(24),  # FIPS code for Maryland
geometry = TRUE,
output = "wide")

# Compute the percent of the working population in agriculture.
MD_agri$agri_percent = (((MD_agri$agri_femaleE +  MD_agri$agri_maleE) / MD_agri$total_occE)*100) 

# Create a sf object.
st_as_sf(MD_agri)
```
I'll now define the data I already downloaded and make sure they're all in the same projection before performing any analysis.
```{r read}
MD_lakes = st_read("path/Maryland_Waterbodies_Lakes_Detailed", "Maryland_Waterbodies_-_Lakes_(Detailed)")

MD_streams = st_read("path/Maryland_Waterbodies_Streams", "HYDR_RiversStreamsDetailed_SHA")
```

Now to reproject to an equal area projection because area will be important. In this case I use EPSG: 5070.
```{r transform}
MD_agri = st_transform(MD_agri, crs = 5070)

MD_lakes = st_transform(MD_lakes, crs = 5070)

MD_streams = st_transform(MD_streams, crs = 5070)
```

Several following steps of the project will be done in GQIS. Transformations will include creating buffers for the lakes and streams and then calculating the percent of that area which actually meets the standards set by Maryland legislation. According to [the Maryland Department of Natural Resources](https://dnr.maryland.gov/forests/Pages/programapps/ripfbi.aspx#:~:text=Buffer%20widths%20of%2050%2D100,quality%20and%20fish%20habitat%20improvement) the appropriate width of forested buffers is 50 to 100 feet. For the purposes of this project streams, being numerous and small, will be given the low end of 50 foot buffers while lakes will have 100 foot buffers. Due to time constraints, only the percent of adequately buffered lake area will be calculated.
```{r write, eval=FALSE}
st_write(MD_lakes, "path/MD_lakes.shp")
st_write(MD_streams, "path/MD_streams.shp")
```

#### Joining QGIS Findings and Bivariate Classes

Having done some analysis in QGIS, it's time to integrate the census data. I created a new layer of polygons that represent areas of less than 10% vegetation within Maryland lake buffer zones. I then used the field calculator to find out the total area of non-vegetated lake buffer perimeter and then the total buffer area for each Maryland county. Dividing the former by the latter gave me the percent area of lake perimeters that aren't adequately buffered per county. This section will be creating bivariate classes to express a comparison between agriculture and adequately buffered lakes.
```{r joining data}
# I wrote the new data down on a csv file. I'll have to read that file in and then use an appropriate join to merge it with the MD_agri file.

MD_per_buffered = read_csv("path/MD_Buffer_Percents.csv")

MD_agri_buffer = left_join(MD_agri, MD_per_buffered,
                              by = "NAME")
```

Now I'll create a new field for the bivariate classes and choose the method of classification based on what looks to best represent the data.
```{r bivariate}
library(biscale)

# Create classes
MD_agri_buffer = bi_class(MD_agri_buffer, x = agri_percent, y = PER_VEG, style = "quantile", dim = 3)
```

```{r define map, include=FALSE}
# Plot it to see how it looks.
MD_bivariate = ggplot() +
  geom_sf(data = MD_agri_buffer, mapping = aes(fill = bi_class), color = "white", size = 0.1, show.legend = FALSE) +
  bi_scale_fill(pal = "DkCyan", dim = 3) +
  labs(title = "Maryland Lake Buffers and Agriculture Occupations",
    subtitle = "Dark Cyan Palette") +
  theme(plot.title = element_text(size=12))
  bi_theme()

  # Create the legend
MD_legend = bi_legend(pal = "DkCyan",
                    dim = 3,
                    xlab = " Higher agriculture ",
                    ylab = " Higher forest ",
                    size = 8)

# Install cowplot if you haven't already 
library(cowplot)

Final_MD_map = ggdraw() +
  draw_plot(MD_bivariate, 0, 0, .8, 1) +
  draw_plot(MD_legend, .70, .15, 0.35, 0.35)

plot(Final_MD_map)
```

```{r plot}
plot(MD_bivariate)
```

I'll now export this as a shapefile so that I can use it to create a layout map and web map and QGIS. I'll also create a legend for the bivariate map.
```{r export, eval=FALSE}
st_write(MD_agri_buffer, "C:/Users/micha/Documents/GES_486/Final/Bin_Final/MD_agri_buffer.shp")
```
### QGIS
1.) The first change I did made after importing the shapefiles and raster file into QGIS was to use the `buffer` function on the lakes and streams. From the Maryland DNR source linked earlier, I decided to set the boundaries to 50 feet for the streams and 100 feet for the lakes. I saved the two outcomes and the buffered lake shapefile can be found in the bin folder. The buffered stream file was too large to upload.

2.) The next step was to use the `difference` function between the lakes and their buffers to remove the interior of the buffers because it was only the area of that 1oo foot wide perimeter around the lakes that acts as a buffer and can be vegetated. I could not do the same for the streams because it was a line file. 

3.) At this point I used the raster file, setting its symbology so that any cell with greater or equal to 10% to green so I knew approximately where there was canopy and where there wasn't. I then ensured that the buffered difference lake shapefile was in the same projection as the canopy cover raster file before turning its opacity down and setting it as the top layer. 

4.) The following step was to identify where the buffered difference lake shapefile overlapped with white cells, areas within the buffer range that weren't canopy, and mark their locations in some way. For lack of a better method, I did this by hand through creating a new layer of polygons. In this layer I documented which county each polygon belonged to.

5.) After that task was finished I reprojected the new layer into ESRI: 102003, an equal area projection. I then used the field calculator to find the areas of each polygon.

6.) I then used the select and statistical summary tools to find the sum total area of non-vegetated buffer space for each county. I documented this in Excel.

7.)  I did the same for the total perimeter area for each county. This was less simple because some lakes straddle county lines, meaning part of their buffer zone is in one county and another part in a different one. This required me to use the `clip` function and selection tool to only count the perimeter space within a county.

8.) I used Excel to divide the data from step six by the step seven data and multiplied by 100 to find the percent of lake buffer area in each county that wasn't forested.

9.) As shown in above, I saved this Excel sheet as a csv and used Rstudio to join it with the agricultural data I had previously downloaded.

10.) Once the bivariate class field was added and the shapefile was exported, I had what I needed to finish my first two maps. I laid out the layers as I wanted and used the printout feature to construct a fully realized map with a title, legend, and the likes.

11.) With the first map exported the next step was to make the bivariate map. I used this [website](https://slu-opengis.github.io/biscale/reference/bi_pal.html) to pick a color scheme for the bivariate classes and set the colors accordingly. It provides several tried and tested color schemes and lists the hex codes for each with their respective class. I wanted green to represent higher percent forests and avoided red to accompany it.

12.) I then duplicated the layer twice and assigned both three quantile classes. One depicted the percent of agricultural workers and the other the percent buffer area that is forested. The point of this was to find the thresholds of each bivariate class and construct a legend.

13.) I used the bivariate legend to capture an image of the legend I would use and then constructed my final bivariate product in a printout.

14.) The final map was a webmap. I used the qgis2web plugin for this part. There were some obstacles to overcome. The first was that the streams and buffered streams were far too large. They created long and often incomplete loading sessions. Since they were only there for the visuals I decided to remove them.

15.) The next issue was that after the webmap was exported the tiff raster file of the canopy cover was left blank. I visited [stackexchange.com](https://gis.stackexchange.com/questions/154651/qgis2web-plugin-openlayer3-for-qgis-not-exporting-raster-images) to assist me in finding a solution. I found there that saving the file as an image and decreasing its size solved this issue but the webmap proved too slow to handle.

16.) I finally decided to replace the canopy cover file with the ESRI imagery basemap. It depicts vegetation and offers a comparison between that raster file and itself. The last step was to export the webmap.




