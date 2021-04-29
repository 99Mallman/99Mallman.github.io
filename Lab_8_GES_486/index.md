# Median Household Income and Population Percentage of Non-Hispanic Blacks for Howard County - 2019
View the web map [here](/Lab_8_GES_486/Bin/Howard_webmap/index).


### Data
[U.S. Census Bureau](https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr)

Carto light basemap

### Transformations
1. The first change I made to the data set was the addition of a column depicting percent population that was non-Hispanic Black. To do so I divided the population of non-Hispanic Blacks by the total population with the following equation: `Howard_income_race$Percent_Blck = (Howard_income_race$Blck_NonHE / Howard_income_race$total_popE)`.
 
2. I then reprojected the data using function `st_transform` into Web Mercator. 

3. I created another column using `Howard_income_race = bi_class(Howard_income_race, x = Percent_Blck, y = incomeE, style = "jenks", dim = 3)`. The data was classified based on natural jenks and Rstudio assigned appropriate bivariate classes to the data.

4. The data was then written out as a geojson file.
 
5. In QGIS, the data was exported as a web map using plugin, qgis2web.

6. The web map lacked a legend so in notepad I wrote in an image and assigned its position.   

### Description
This project was done for a lab in an Advanced GIS course. My assignment was to retrieve census data for two separate U.S. counties and construct two bivariate maps comparing median household income and the population percentage of a race by census tracts all within Rstudio. Following this, I was to save one as a geojson, and then create a web map through GQIS with a legend. The web map depicts a bivariate map of Maryland's Howard County along with a base map for reference. The data classes were organized by natural breaks or jenks. Every tract is interactive, displaying its title, median household income, and percentage of non-Hispanic Black members of the population in decimal form. A bivariate map spatially compares two variables by overlapping their color schemes. For example, a light tract has a median low income and a low percentage of Black people.   

### Additional Information
For a deeper analysis and a more extensive description of the project please visit my [repository](https://github.com/99Mallman/99Mallman.github.io/tree/master/Lab_8_GES_486).
