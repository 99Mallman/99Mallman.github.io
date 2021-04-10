#  Percent Change of Median Gross Rent and Populaiton Under the Poverty Level for Baltimore City Tracts - 2014 to 2019

## Collecting the Data:
  
### Baltimore City Poverty and Rent
Census Differences GES 486, lab 6 

Michael Allman (mallman1@umbc.edu)

April 6th, 2021

### Setup
First, load the packages needed. To gain access to census data request for a api key, which you can find [here](https://api.census.gov/data/key_signup.html). 

```
{r setup}
#Loading the following packages. Install them if you haven't already.
library(tidyverse)
library(tidycensus)
library(sf)
library(ggplot2)

#Settings for tidycensus
options(tigris_class = "sf") #Allows me to request data from the census. 
options(tigris_use_cache = TRUE) # Telling tigris to cache data

#The following line is in regards to the api key.
census_api_key("your key here", install = TRUE)
```

### Downloading the data
The next step is retrieving the data we need for our project. The command 'get_acs' will be used to specify which variables we want to extract. Here's the [Data Dictionary](https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr).

```
{r download census}
#Download data on the ratios of income compared to the poverty level along with median gross rent for 2019.
Balt_City_2019 = get_acs(geography = "tract",
      variables = c("total_income" = "C17002_001", #all ratios
                         "ratio_half" = "C17002_002", #population with incomes less than half of the poverty line.
                         "ratio_one" = "C17002_003", #population with incomes less than that of the poverty line but above half.
                    "med_grs_rent" = "B25064_001"), #median gross rent
year = 2019,
survey = "acs5",
state = c(24), #Maryland
county = c(510), #Baltimore City
geometry = TRUE, #download the shapefile with the data
output = "wide") 
#This gets the same data but for 2014.
Balt_City_2014 = get_acs(geography = "tract",
      variables = c("total_income" = "C17002_001",
                         "ratio_half" = "C17002_002", 
                         "ratio_one" = "C17002_003",
                    "med_grs_rent" = "B25064_001"),
year = 2014,
survey = "acs5",
state = c(24), 
county = c(510, 5), 
geometry = FALSE, #The shapefile from the 2019 data will give use the geometry already. We don't need it twice.
output = "wide") 
#We now have raw data we should save. 

#First we have to transform it to 3857 because we want to save it as a geojson.
st_write(st_transform(Balt_City_2019, 3857), "Balt_City_2019.geojson")
st_write(Balt_City_2014, "Balt_City_2014.csv") # Geometry is false.
```
### Computing the percent under poverty.
This section is really doing everything twice, once for each year. We add the portion of the population that makes less than half of the poverty level with the portion that makes between half and one. We then divide the sum by the total to get the percentage of the population that live under the poverty level. We use the data that ends with 'E' instead of 'M' because it stands for 'estimate.'

```
{r percent}
#Compute the percentage under the poverty line for 2019
Balt_City_2019$below_poverty = (Balt_City_2019$ratio_halfE + Balt_City_2019$ratio_oneE) / Balt_City_2019$total_incomeE

#Compute the percentage under the poverty line for 2014
Balt_City_2014$below_poverty = (Balt_City_2014$ratio_halfE + Balt_City_2014$ratio_oneE) / Balt_City_2014$total_incomeE

#While here, let's also compute the percentage of the population that is above the poverty line. Because this is simply the inverse of what we just computed above, we can subtract the variables defined above from 1.

#Compute the percentage of population above poverty line for 2019
Balt_City_2019$above_poverty = 1 - Balt_City_2019$below_poverty

#Compute the percentage of population above poverty line for 2014
Balt_City_2014$above_poverty = 1 - Balt_City_2014$below_poverty
```
### Merge the two years.
```
{r merge}
#Join the two years using the GEOID field. The suffixes are for clarity.
Balt_City_Poverty = left_join(Balt_City_2019, Balt_City_2014,
                              by = "GEOID",
                              suffix = c(".19", ".14"))
                              
#Compute differences in percentage of population between 2019 and 2014.
Balt_City_Poverty$poverty_diff = Balt_City_Poverty$below_poverty.19 - Balt_City_Poverty$below_poverty.14

#Compute difference in median household income
Balt_City_Poverty$median_diff = Balt_City_Poverty$med_grs_rentE.19 - Balt_City_Poverty$med_grs_rentE.14

Balt_City_Poverty = st_transform(Balt_City_Poverty, 3857) # reproject into web Mercator.
```
### Write to file.
```
{r write}

st_write(Balt_City_Poverty, "Balt_City_Poverty_diff.geojson")
```

## About the Project
This project was done for an Advanced GIS class. The goal was to create a bivariate map comparing the percent change of median gross rent with the percent change in population with incomes below the poverty level for Baltimore City tracts between the years of 2014 and 2019. A detailed description of the process and analysis can be found in the [README.md](https://github.com/99Mallman/99Mallman.github.io/tree/master/GES486_Lab6) file at the backend of my repository. The project was created with census data extracted using Rstudio and then displayed using QGIS. 

A bivariate map compares two variables. Each variable is given a separate color scheme, in this case grey to red and grey to blue. The colors are blended together making a color chart that illustrates every combined option for the classes. Light colors depict tracts of negative change, or loss, in both variables. Dark colors depict tracts that experienced growth in rent and poverty. The middle color of the chart, which is a dark grey, represents small or no change in either variable in the 5 year period. Although it is not shown in the map, the maximum values of rent growth was 130% and the same value for poverty growth was 23%. Prove of this can be found in the bin folder of the repository.

What is notable of this map is where clusters of red or blue tracts can be found. Baltimore City has a history of redlining and segregation. Much of the red tracts align with these areas. For more information about Baltimore City's historical segregation, visit [this article from *Brookings*](https://www.brookings.edu/blog/the-avenue/2015/05/11/good-fortune-dire-poverty-and-inequality-in-baltimore-an-american-story/).

## The final output:

![Balt_Poverty_and_Rent](https://user-images.githubusercontent.com/78063176/114249178-df2ece80-9967-11eb-8137-944947cffb47.png)


