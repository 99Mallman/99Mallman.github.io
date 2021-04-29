# Median Household Income and Population Percentage of Non-Hispanic Blacks for Howard County - 2019
### GES 486, lab 8
This repository is structured to clearly document the process of this project. Any raw data and their sources used in the making of this project can be found in the data folder. The bin folder contains any tampered data sets after transformations have been made. Any files in it can be viewed as intermediate steps made between retrieving the raw data and the final output.

## Introduction
The goal of this project was to create an interactive web map displaying an easy-to-read bivariate map comparing median household income with the population percentage of non-Hispanic Black people for Howard County tracts. This project will hopefully reveal correlations between wealth and race distribution.

## Data
The data was provided by the American Census Bureau. American Community Survies (ACS) data is available for free download at [https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr](https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr). Every table has a different code which can be used to identify the specific data you desire. In Rstudio, with an API key, it is possible to extract tables by their codes to download to your computer. I used the codes BO3002 and B19013 to identify the percent of population and median household income tables respectively in Rstudio for the ACS 2019. The lab asked for the extraction of data for two different counties. I first save the ACS 2019 data for Baltimore City as a geojson file and then did the same for Howard County data, both can be found in the data folder.

## Transformations and Process
I separate the processes and transformations of data into two sections. First I describe everything I did in Rstudio, then likewise in QGIS.

### Rstudio
What follows is the entire uneditted process for which Rstudio was used.



(move these three main transformations to index)
1. The first change I made to both data sets was add a column of percent population that was non-Hispanic Black. To do so I divided the population of non-Hispanic Blacks by the total population with the following equation: *Howard_income_race$Black_percent = (Howard_income_race$Blck_NonHE / Howard_income_race$total_popE)*.
 
2. I then reprojected the data using function *st_transform* into Web Mercator. 
 
3. Bi_class.

### QGIS
While Rstudio was used to retrieve the data and create the fields we would use, QGIS was used to display the information. A bivariate map requires two separate layers each of a different variable. For more information about the method visit [bnhr.xyz](https://bnhr.xyz/2019/09/15/bivariate-choropleths-in-qgis.html). Deciding how to classify the data was the most important part in this stage of the project. Since bivariate maps use two or more variables the number of classes are multiplied between the two map layers. For example, three classes for a map when combined become nine. Because of this it is helpful to only have a few classes for each map.

I decided to use only three classes for simplicity. I also used natural breaks for the poverty percentage change because it did well to organize the data between tracts with negative change, near zero, and positive. The range of values for the percent change in median gross rent was much larger and so I used natural breaks as I guideline but manually changed the ranges of some classes for more equally divided classes. I set the blending of the top layer to multiply which merged the colors together. Then I added the Stamen Toner Background basemap before lowering its opacity to 10%. A plugin is required to make the appropriate [bivariate legend](https://github.com/webgeodatavore/bivariate_legend/). I used an example from the website linked above and exported my legend as an image.

The next step was to create a print layout. I did so, adding the usual characteristics of a map like the legend, a scale and title, the date, data sources, a brief description of the map, and the creator. Finally, the last step was to export the product an image.

## Analysis
I ended up using just two fields of the data sets I saved and imported into QGIS. These were the last two. The one of percent difference in poverty level and the percent change of median gross income. The goal of overlapping them and blending the two layers together was to display which tracts of Baltimore City have increased in poverty and in rent, or vice versa. Grey tracts meant that there was a decrease in rent and poverty between 2014 and 2019. Blue tracts represent those that experienced high rent growth but a loss in poverty. Red tracts are the opposite. Purple tracts are those with an increase in poverty and rent. There are 12 of these in total. For the most part, they are well distributed. Three of them, along with many red tracts, are clustered together in West Baltimore, a section of the city with historic redlining. A second cluster of red tracts runs along the east and southeast sections of the city. Like the first cluster, these tracts too are predominately African American. [This article](https://www.brookings.edu/blog/the-avenue/2015/05/11/good-fortune-dire-poverty-and-inequality-in-baltimore-an-american-story/) has relevant maps on the issue. 

A general look at the city shows a majority of blue and dark grey tracts. This means that most of the city, by space not population, has experienced a rise in rent or relatively small to no change in the five year period. Seeing more blue than red means poverty has largely fallen, in some cases to great extent. But the locations of the red tracts is of significance. Historically marginalized areas appear to be rising in poverty, even where rents are lowered. 

## Results
The final output can be found in the bin folder. It reflects the goal of this project and the lessons introduced by this class. The map I created is a bivariate, with red and blue color schemes. Viewers are able to compare the two variables mentioned above quickly and easily for the area. The data sources are listed and a brief description adjacent to the map aims to clarify any confusion a viewer might have.  

I used both Rstudio and QGIS in the production of the map. Through the commands and resources I've learned I was able to extract the data I desired and then use it to compute new data to use. I was able to add geometry to the data and save it for farther use. I practiced using joins, tidycensus, and changing projections. In QGIS I applied what I've learned in class to display a professional looking map with multiple blended layers. The color schemes compliment each other and the classification of the data was chosen based on natural breaks due to the nature of the data. This project required knowledge about printing layouts, installing and using plugins, and deciding the appropriate symbology of the layers. I've also taken great care to document my process in a clear way to provide viewers the means to reconstruct the very same map. Through GitHub I'm able to display this project for people to see. If anything, this project represents the power of open source software. The production of professional, informative maps can be done for free if you have only the knowledge to look. 
