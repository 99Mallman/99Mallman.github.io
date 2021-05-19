# Percent of Lake Buffer Area Adequately Forested in Maryland
### GES 486, Final
This repository is structured to clearly document the process of this project. Any raw data and their sources used in the making of this project can be found in the data folder. The bin folder contains any tampered data sets after transformations have been made. Any files in it can be viewed as intermediate steps made between retrieving the raw data and the final output.

## Introduction
The goal of this project was to create several different forms of maps that explore how well Maryland lakes are buffered with vegetation. Maryland defines forested areas as [10% canopy](https://dnr.maryland.gov/forests/Pages/Forest-Tree-Data.aspx#:~:text=Resolution%3A%20The%20FIA%20considers%20a,at%20least%20120%20feet%20wide) and recommends at least [50 to 100 foot buffers](https://dnr.maryland.gov/forests/Pages/programapps/ripfbi.aspx#:~:text=Buffer%20widths%20of%2050%2D100,quality%20and%20fish%20habitat%20improvement). This project includes three separate maps. The first depicts the percent of of adequately buffered area by Maryland county in relation to to canopy cover. The second is a bivariate map comparing the same variable as before with the percent of the working population in agriculture. The last is an interactive web map so views can explore individual Maryland lakes.  

## Data
Most of the data was provided by [Maryland.gov](https://data.imap.maryland.gov/). From there I downloaded and used four shapefiles. [Land cover](https://data.imap.maryland.gov/datasets/maryland-land-use-land-cover-county-land-use-land-cover-2010?geometry=-80.737%2C38.071%2C-73.799%2C39.568), [canopy cover](https://data.imap.maryland.gov/), [lakes (detailed)](https://data.imap.maryland.gov/datasets/71a539948a5b4751b3b676aec5368ed5_3?geometry=-76.950%2C39.430%2C-76.516%2C39.523), and [streams (detailed)](https://data.imap.maryland.gov/datasets/maryland-waterbodies-rivers-and-streams-detailed?geometry=-80.737%2C38.070%2C-73.799%2C39.568). I also used data provided by the American Census Bureau. American Community Survies (ACS) data is available for free download at [https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr](https://www.socialexplorer.com/data/ACS2019_5yr/metadata/?ds=ACS19_5yr). Every table has a different code which can be used to identify the specific data you desire. In Rstudio, with an API key, it is possible to extract tables by their codes to download to your computer. I used the codes  C24010_001, C24010_067, and C24010_031 to identify the percent of working population in agriculture in Rstudio for the ACS 2019.

## Transformations and Process
I separate the processes and transformations of data into two sections. First I describe everything I did in Rstudio, then likewise in QGIS.

### Rstudio
The entire Rstudio process can be found both in the repository and [here](file:///C:/Users/micha/Documents/GES_486/Final/Bin_Final/Allman_Final.html).

### QGIS
1.)
2.)
3.)
4.)
5.)




