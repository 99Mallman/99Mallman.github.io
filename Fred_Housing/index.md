# Frederick County Housing Units
Much like the Maryland developed land gif this map was part of a larger project. The purpose was to familiarize myself with census data in Rstudio. Inspired by my first gif, I decided to look into change in housing units of Frederick county over time. The data was retrieved from [social explorer](https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr). It was also made using GIMP. 

### Process

---
Lab 5: Frederick County Housing

Michael Allman

March 6th, 2021

---

#### Setup
First, load the packages needed. For this script I needed to sign up for my own census api key.
```{r setup}
library(tidyverse)
library(tidycensus)
library(sf)
library(ggplot2)

settings for tidycensus
options(tigris_class = "sf")
options(tigris_use_cache = TRUE)
census_api_key("YOURKEYHERE", overwrite = TRUE, install = TRUE)
readRenviron("~/.Renviron")
```
##### Get Census Data
Use the get_arcs command to get data from the Census. Here's the [Data Dictionary from Social Explorer](https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr)

```{r download census}
# This gets housing units and population data for Frederick county between 2015 and 2019.
Fred_hu_19 = get_acs(geography = "tract", 
                  variables = c("pop" = "B03002_001",
                                "hu_total" = "B25001_001"),
                  year = 2019,
                  survey = "acs5",
                  state = c(24),
                  county = c(021),
                  geometry = TRUE,
                  output = "wide")

# Does the same as above but for 2010 to 2014.
Fred_hu_14 = get_acs(geography = "tract", 
                  variables = c("pop" = "B03002_001",
                                "hu_total" = "B25001_001"),
                  year = 2014,
                  survey = "acs5",
                  state = c(24),
                  county = c(021),
                  geometry = TRUE,
                  output = "wide")
# 2005 to 2009.
Fred_hu_09 = get_acs(geography = "tract", 
                  variables = c("pop" = "B03002_001",
                                "hu_total" = "B25001_001"),
                  year = 2009,
                  survey = "acs5",
                  state = c(24),
                  county = c(021),
                  geometry = TRUE,
                  output = "wide")
```
##### Reproject Data
```{r test output}
# This reprojects the 2019 data.
Fred_hu_19 = st_transform(Fred_hu_19, 3857)

# Now for the 2014 data.
Fred_hu_14 = st_transform(Fred_hu_14, 3857) 

# 2009 data
Fred_hu_09 = st_transform(Fred_hu_09, 3857)
```

##### Export data so that it can be imported into QGIS.

```{r Export Data}
st_write(Fred_hu_19, "Fred_hu_19.geojson")
st_write(Fred_hu_14, "Fred_hu_14.geojson")
st_write(Fred_hu_09, "Fred_hu_09.geojson")
```

I didn't use the 2009 data because the census tracts changed between that year and the next. 

![Allman_Frederick_Housing](https://user-images.githubusercontent.com/78063176/119061845-4e560480-b9a3-11eb-8fc0-54457055274b.gif)


