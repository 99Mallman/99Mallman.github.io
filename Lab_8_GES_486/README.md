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

---
title: "Lab 8: Bivariate Mapping in R"
author: "Michael Allman"
date: "4/29/21"
output: html_document
---

```{r, setup, include=FALSE}
#setup
knitr::opts_knit$set(root.dir = "PATH")
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

## Downloading data

**1. Use `tidycensus` to download 1. race/ethnicity (B03002) and 2. median household income for Baltimore City. Store this data in a new object. Choose which race/ethnicity you'd like to relate to income (Non-Hispanic Black and Non-Hispanic White work best). Which census tract has the highest _percentage_ of your target race/ethnicity (and what is the percent) and which has the highest median household income (and how much is it?)? (5 points)** Reminder: Since we will be mapping our data, make sure you include use `geometry = TRUE` in `get_acs()` 

```{r download} 
# Download total population, population of Blacks, and median household income ACS 2019 Census data from https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr for Maine tracts.
Balt_income_race = get_acs(geography = "tract",
      variables = c("total_pop" = "B03002_001",# Total population
                    "Blck_NonH" = "B03002_004", # Non-Hispanic Black population
                    "income" = "B19013_001"), # Median household income
      year = 2019,
survey = "acs5",
state = c(24),  # FIPS code for Maryland
county = c(510), # FIPS code for Baltimore City
geometry = TRUE,
output = "wide")
# Compute the percent of the population that is Black.
Balt_income_race$Percent_Blck = (Balt_income_race$Blck_NonHE / Balt_income_race$total_popE)
# Create a sf object.
st_as_sf(Balt_income_race)
```

**2. Which census tract has the highest percentage of Black people? (and what is the percent) and which has the highest median household income (and how much is it?)? (5 points)**
```{r max}
Balt_income_race %>%
  filter(Percent_Blck == max(Percent_Blck, na.rm = TRUE))
Balt_income_race %>%
  filter(incomeE == max(incomeE, na.rm = TRUE))
# 1.) The tract with the largest percentage of Black people, which equals 99.32%, is number 2007.02. The tract with the highest median house hold income is number 2711.02 and equals $195,156.
```

**2. Please reproject this data to Web Mercator. (1 points)**
```{r transform}
Balt_income_race = st_transform(Balt_income_race, crs = 3857)
st_crs(Balt_income_race)
```

**3. Create two plots. In the first plot highlight the tract with the highest concentration of your selected race/eth. In the second plot highlight the tract with the highest median household income? (5 points)**

```{r plot concentration}
library(dplyr)
# Define a variable equivalent to the tract with the highest Black percentage.
Max_percent = Balt_income_race %>%
  filter(Percent_Blck == max(Percent_Blck, na.rm = TRUE)) 
ggplot(Balt_income_race) + 
  geom_sf(aes(fill = cut_number(Percent_Blck, 4))) +
  geom_sf(data = Max_percent, color = "yellow", fill = "yellow") +
  scale_fill_brewer(type = "seq", palette = 1,
                    name = "Percent of Pop that is Black") +
  labs(title = "Percent of Baltimore City's Non-Hispanic Black Population") +
  xlab("Longitude") +
  ylab("Latitude") +
  theme_bw()
```
```{r plot income}
# Define a variable equivalent to the tract with the highest median income.
Max_income = Balt_income_race %>%
  filter(incomeE == max(incomeE, na.rm = TRUE)) 
ggplot(Balt_income_race) + 
  geom_sf(aes(fill = cut_number(incomeE, 4))) +
  geom_sf(data = Max_income, color = "yellow", fill = "yellow") +
  scale_fill_brewer(type = "seq", palette = 1,
                    name = "Median Household Income") +
  labs(title = "Median Household Income in Baltimore City") +
  xlab("Longitude") +
  ylab("Latitude") +
  theme_bw()
```

**4. Create a third column using the bi_class function from the tutorial. (2 points)**
```{r bivariate class}
library(biscale)
# Create classes
Balt_income_race = bi_class(Balt_income_race, x = Percent_Blck, y = incomeE, style = "jenks", dim = 3)
```

**5. Create a bivariate map with your data. (3 points)**  
```{r bivariate map, results = 'hide'}
# create map
bivariate = ggplot() +
  geom_sf(data = Balt_income_race, mapping = aes(fill = bi_class), color = "white", size = 0.1, show.legend = FALSE) +
  bi_scale_fill(pal = "DkViolet", dim = 3) +
  labs(title = "Income and Percent Black People in Baltimore City",
    subtitle = "Dark Violet Palette") +
  theme(plot.title = element_text(size=12))
  bi_theme()
```

```{r plot bivariate map (no legend)}
plot(bivariate)
```

**6. Use the cowplot package and ggdraw, like in the tutorial to add a legend (2 points)**.
```{r adding legend}
# Create the legend
legend = bi_legend(pal = "DkViolet",
                    dim = 3,
                    xlab = " Higher % NonHis Black ",
                    ylab = " Higher Income ",
                    size = 8)
# Install cowplot if you haven't already 
library(cowplot)
Final_map = ggdraw() +
  draw_plot(bivariate, 0, 0, .8, 1) +
  draw_plot(legend, .70, .15, 0.35, 0.35)
plot(Final_map)
```

**7. Rinse and repeat for another county of your choosing, using a _different_ color scheme. Be sure to use Psuedo-Mercator (3857). (5 points)**
```{r Howard County download}
Howard_income_race = get_acs(geography = "tract",
      variables = c("total_pop" = "B03002_001",# Total population
                    "Blck_NonH" = "B03002_004", # Non-Hispanic Black population
                    "income" = "B19013_001"), # Median household income
      year = 2019,
survey = "acs5",
state = c(24),  # FIPS code for Maryland
county = c(027), # FIPS code for Baltimore City
geometry = TRUE,
output = "wide")
# Compute the percent of the population that is Black.
Howard_income_race$Percent_Blck = (Howard_income_race$Blck_NonHE / Howard_income_race$total_popE)
# Create a sf object.
st_as_sf(Howard_income_race)
```

```{r Howard maxes}
Howard_income_race %>%
  filter(Percent_Blck == max(Percent_Blck, na.rm = TRUE))
Howard_income_race %>%
  filter(incomeE == max(incomeE, na.rm = TRUE))
# 7.1) The tract with the largest percentage of non-Hispanic Black people, which equals 48.6%, is number 6069.07. The tract with the highest median house hold income is number 6501.04 and equals $220,583.
```

```{r Howard transform}
Howard_income_race = st_transform(Howard_income_race, crs = 3857)
st_crs(Howard_income_race)
```


```{r plot concentration Howard}
# Define a variable equivalent to the tract with the highest percentage of non-Hispanic Blacks for Howard County.
Max_percent = Howard_income_race %>%
  filter(Percent_Blck == max(Percent_Blck, na.rm = TRUE)) 
ggplot(Howard_income_race) + 
  geom_sf(aes(fill = cut_number(Percent_Blck, 4))) +
  geom_sf(data = Max_percent, color = "yellow", fill = "yellow") +
  scale_fill_brewer(type = "seq", palette = 1,
                    name = "Percent of Pop That is non-Hispanic Black") +
  labs(title = "Percent of Howard County's non-Hispanic Black Population") +
  xlab("Longitude") +
  ylab("Latitude") +
  theme(plot.title = element_text(size=8)) +
  theme_bw()
```

```{r plot income Howard}
# Define a variable equivalent to the tract with the highest median income for Howard County.
Max_income = Howard_income_race %>%
  filter(incomeE == max(incomeE, na.rm = TRUE)) 
ggplot(Howard_income_race) + 
  geom_sf(aes(fill = cut_number(incomeE, 4))) +
  geom_sf(data = Max_income, color = "yellow", fill = "yellow") +
  scale_fill_brewer(type = "seq", palette = 1,
                    name = "Median Household Income") +
  labs(title = "Median Household Income by Howard County Census Tracts") +
  xlab("Longitude") +
  ylab("Latitude") +
  theme_bw()
```

```{r bivariate class and map Howard, results = 'hide'}
# Create classes
Howard_income_race = bi_class(Howard_income_race, x = Percent_Blck, y = incomeE, style = "jenks", dim = 3)
# Create map
Howard_bivariate = ggplot() +
  geom_sf(data = Howard_income_race, mapping = aes(fill = bi_class), color = "white", size = 0.1, show.legend = FALSE) +
  bi_scale_fill(pal = "DkViolet", dim = 3) +
  labs(title = "Income and Percent Non-Hispanic Black in Howard County",
    subtitle = "Dark Blue (DkBlue) Palette") +
  theme(plot.title = element_text(size=12))
  bi_theme()
```

```{r bivariate plot Howard}
# Create the legend
Howard_legend = bi_legend(pal = "DkViolet",
                    dim = 3,
                    xlab = " Higher % NonHis Black ",
                    ylab = " Higher Income ",
                    size = 8)
Howard_map = ggdraw() +
  draw_plot(Howard_bivariate, 0, 0, .8, .8) +
  draw_plot(Howard_legend, .70, .30, 0.35, 0.35)
plot(Howard_map)
```

**8. Write the bi_class output to a geojson file. (1 points)**
```{r write, eval = FALSE}
st_write(Howard_income_race, "Howard_income_race.geojson")
```
### QGIS and Notepad
With the geojson written out it was QGIS' job to tidy it up. Because I had already created a column assigning bivariate classes I didn't need to duplicate two layers and blend them together. Instead I needed only to categorize the symbols by their appropriate colors, found [here](https://slu-opengis.github.io/biscale/reference/bi_pal.html). While the `bi_class` function can easily create classes classified by various methods I didn't actually yet know what the ranges of those classes were. To find out, I simply duplicated an extra layer that I would later delete and set its symbology to graduated, value to income, mode to jenks, and classes to 3 to give me the equivalent ranges. I then did the same for the second variable. I used these two layers to create a legend from the bivariate legend plugin, which I screenshotted, and then added the Carto light basemap and lowered its opacity to 80%.

The next step was to prepare the map's attribute data for the web map. I set unwanted fields to hidden and labeled others I wanted to pop up in the web map. Then, using the qgis2web plugin, I exported the web map as a leaflet. The last step was to add the legend to the web map through notepad. This required the addition of two script chunks, the first to define the image, and the second to define its position.

## Analysis
The web map 

A general look at the city shows a majority of blue and dark grey tracts. This means that most of the city, by space not population, has experienced a rise in rent or relatively small to no change in the five year period. Seeing more blue than red means poverty has largely fallen, in some cases to great extent. But the locations of the red tracts is of significance. Historically marginalized areas appear to be rising in poverty, even where rents are lowered. 

## Results
The final output can be found in the bin folder. It reflects the goal of this project and the lessons introduced by this class. The map I created is a bivariate, with red and blue color schemes. Viewers are able to compare the two variables mentioned above quickly and easily for the area. The data sources are listed and a brief description adjacent to the map aims to clarify any confusion a viewer might have.  

I used both Rstudio and QGIS in the production of the map. Through the commands and resources I've learned I was able to extract the data I desired and then use it to compute new data to use. I was able to add geometry to the data and save it for farther use. I practiced using joins, tidycensus, and changing projections. In QGIS I applied what I've learned in class to display a professional looking map with multiple blended layers. The color schemes compliment each other and the classification of the data was chosen based on natural breaks due to the nature of the data. This project required knowledge about printing layouts, installing and using plugins, and deciding the appropriate symbology of the layers. I've also taken great care to document my process in a clear way to provide viewers the means to reconstruct the very same map. Through GitHub I'm able to display this project for people to see. If anything, this project represents the power of open source software. The production of professional, informative maps can be done for free if you have only the knowledge to look. 
