# Baltimore City Comparison of Percent Change in Median Gross Rent and Percentage Below Poverty Level
### GES 486, lab 6
This directory is structured to effectively document the process of this project. Any raw data and their sources used in the making of this project can be found in the data folder. The bin folder contains any tampered data sets after transformations have been made. Any files in there can be viewed as intermediate steps made between retrieving the raw data and the final output.

## Introduction
The goal of this project was to create an easy-to-read bivariate map comparing the percent change of median gross rent with the percent change in population with incomes below the poverty level for Baltimore City tracts between the years of 2014 and 2019. Thus, correlations in the rise and fall of rents and poverty are illustrated for that time period.

## Data
The data was provided by the American Census Bureau. American Community Survies (ACS) data is available for free download at [https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr](https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr). Every table has a different code which can be used to identify the specific data you desire. In Rstudio, with an API key, it is possible to extract tables by their codes to download to your computer. I used the codes C17002 and B25064 to identify the ratio of income to poverty level and median gross income tables respectively in Rstudio for the ACS 2019 and ACS 2014. Then I saved the ACS 2019 data as a geojson file and the ACS 2014 data as a csv, which can both be found in the data folder.

The ratio of income to poverty level table has 8 fields. The first represents the total, and the remaining seven each contain a different range in ratios. Anything below 1 means that the income is below what is considered poverty level. This means I only extracted the fields with ranges below 1, along with the total. The median gross rent is simply one field.

## Transformations and Process
I separate the processes and transformations of data into two sections. First I describe everything I did in Rstudio, then likewise in QGIS.

### Rstudio
The entire, uneditted process for which Rstudio was used can be viewed both in the index and I've also uploaded the script to the repository. What follows is an explanation of the commands, post-downloading the data. 

1. The first step change I made to the data was done when I saved it. Saving one year as a geojson gave it a geography. I turned it into spatial data. Saving the second year as a csv would make for an easy join later on.
 
2. The second step was to create fields for the percentage of the population below the poverty level for both years. This was done by adding the two fields with ratios below 1 that I extracted earlier and then dividing that sum by the total. *(Balt_City_2019$ratio_halfE + Balt_City_2019$ratio_oneE) / Balt_City_2019$total_incomeE* and *(Balt_City_2014$ratio_halfE + Balt_City_2014$ratio_oneE) / Balt_City_2014$total_incomeE*
 
3. I next added a field for the percent above poverty. I didn't end up using this, but I wanted it available for a reference or in case I did end up wanting to use it. *1 - Balt_City_2019$below_poverty* and *1 - Balt_City_2014$below_poverty*
 
4. I then merged the two years into one table using a left join. They shared a common field 'GEOID' that identified the tracts, which is what I joined the two tables by. I also added suffixes to help identify which fields belonged to the right year. *left_join(Balt_City_2019, Balt_City_2014,
                                                                                                      by = "GEOID",
                                                                                                      suffix = c(".19", ".14"))* 
5. The following step was computing, or adding fields, for the difference between the two years. For the poverty data, since they were already in percentages, the command was simply *Balt_City_Poverty$below_poverty.19 - Balt_City_Poverty$below_poverty.14.* The median gross rent command was slightly more involved. To get the percent change in median gross rent I had to find the difference but then divide that by the original (2014) year. The command was *(Balt_City_Poverty$med_grs_rentE.19 - Balt_City_Poverty$med_grs_rentE.14)/Balt_City_Poverty$med_grs_rentE.14.*

6. The last step was to project the data into Web Mercator, *st_transform(Balt_City_Poverty, 3857),* and save it, which can now be found in the bin folder.


### QGIS
While Rstudio was used to retrieve the data and create the fields we would use, QGIS was used to display the information. A bivariate map requires two separate layers each of a different variable. For more information about the method visit [bnhr.xyz](https://bnhr.xyz/2019/09/15/bivariate-choropleths-in-qgis.html). Deciding how to classify the data was the most important part in this stage of the project. Since bivariate maps use two or more variables the number of classes are multiplied between the two map layers. Because of this it is helpful to only have a few classes for each map. For example, three classes for a map when combined become nine. 

I decided to use only three classes for simplicity. I also used natural breaks for the poverty percentage change because it did well to organize the data between tracts with negative change, near zero, and positive. The range of values for the percent change in median gross rent was much larger and so while the first class has the same range of negative values the second two are slightly more equally divided. I set the blending of the top layer to multiply which merged the colors together. Then I added the Stamen Toner Background basemap before lowering its opacity to 10%. A plugin is required to make the appropriate [bivariate legend](https://github.com/webgeodatavore/bivariate_legend/). I used an example from the website linked above and exported my legend as an image.

The next step was to create a print layout. I did so, adding the usual characteristics of a map like the legend, a scale and title, the date, data sources, a brief description of the map, and the creator. Finally, the last step was to export the product an image.

## Analysis

## Results
