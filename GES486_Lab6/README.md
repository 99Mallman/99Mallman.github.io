# Baltimore City Median Gross Rent and Percentage Below Poverty Line Comparison
### GES 486, lab 6
This directory is structured to effectively document the process of this project. Any raw data and their sources used in the making of this project can be found in the data folder. The bin folder contains any tampered data sets after transformations have been made. Any files in there can be viewed as intermediate steps made between retrieving the raw data and the final output.

## Introduction
The goal of this project was to create an easy-to-read bivariate map comparing the percentage of households with incomes below the poverty level and the median gross rent by Baltimore City tract. 

## Data


## Transformations and Process

### Rstudio
The entire, uneditted process for which Rstudio was used can be viewed both in the index and I've also uploaded the script to the repository. What follows is an explanation of the commands, post donwloading the data, in paragraph form.

### QGIS
While Rstudio was used to retrieve the data and create the fields we would use, QGIS was used to display the information. A bivariate map requires two separate layers each of a different variable. For more information about the method visit [bnhr.xyz](https://bnhr.xyz/2019/09/15/bivariate-choropleths-in-qgis.html). How to classify the data was the most important decision in this stage of the project. Since bivariate maps use two or more variables the number of classes are multiplied between the two map layers. Because of this it is helpful to only have three to five classes for each map, which in the final product becomes nine or twenty five. 

I decided to use only three classes for simplicity. I also used natural breaks for the poverty percentage change because it did well to organize the data between tracts with negative change, near zero, and positive. The range of values for the percent change in median gross rent was much larger and so while the first class has the same range of negative values the second two are slightly more equally divided. I set the blending of the top layer to multiply to merge the colors together and then added a Stamen Toner basemap before lowering its opacity to 30%. A plugin is required to make the appropriate [bivariate legend](https://github.com/webgeodatavore/bivariate_legend/). I used an example from the website linked above and exported my legend as an image.

The next step was to create a print layout. I did so, adding the usual characteristics of a map like the legend, a scale and title, the date, data sources, a brief description of the map, and the creator. Finally, the last step was to export the product an image.


## Analysis

## Results
