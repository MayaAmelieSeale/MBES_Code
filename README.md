# MBES_Code

### Project Summary 

The goal of this ongoing project is to generate meaningful insights that would help us better understand the determinants of an individual's brain health. We focused on childhood socio-economic status and social mobility. This dataset contained both unstructured and structured data about our middle-aged participant's life. Specifically, there is a section on school and residential history. With a response rate of 70%, I first had to fill the gaps. After pivoting the data to allow for data entry, I used manual web scraping and deduction, and later cold-calling participants to do this. After my colleague and I validated the dataset, I built a library of functions for current and future lab use to create data analysis pipelines to clean, transform, and visualize the data. This involved geocoding addresses into coordinates, organizing participant history into graphs, and visualizing residential history as a geographic network using Folium. Next, using the US Census API, I pulled the median income for each residence's census tract and created a network map where each edge is color-coded based on a gradient color map representing the change in median income between two points. As well, I calculated the total geodesic distance traveled, binary path length, and total change in median income for each participant. I then created functions that allow for basic statistical analysis between participant subgroups. Since our participants consist of balance groups by ‘middle income’ and ‘low income’ status, I calculated differences in median values for each group based on distance traveled, binary path length, and median income change. Here, I found that people in the middle-income bracket had a higher median lifetime geodesic distance traveled than low. 

### About the Midlife Brain and Environment Study (MBES)
The Midlife Brain and Environment Study (MBES) is a longitudinal study (n = 151)  of people between the ages of 40 and 64 consisting of three timepoints, spaced two years apart. Each time point consists of two appointments, spaced 30 days apart. Data collected at each time point includes fMRI scans, both resting state and task based, blood testing, hair sampling, extensive cognitive testing, 30 days of actigraph accelerometry, and daily electronic momentary assesments. The goal of this study is to understand the relationship between enviornmental and socioeconimic factors and changes in network charactertersitics as the brain ages. The data processing in this repository was conducted after timepoint 1. This repository was created so that following timepoint 2, these methods can be used with updated, more detailed life history data. This study is conducted by the Wig Neuroimaging Lab https://www.wigneurolab.org/ at the Center for Vital Longevity, associated with the University of Texas at Dallas. 

# Life History Survey 
One of the measures completed by participants is the Life History Survey, derived from the Health and Retirement Study. https://hrs.isr.umich.edu/data-products/life-history
In this survey, participants report detailed residential and educational information. This is the data processed in this document. 

# Education Data

## Educational History Processing
Participants provide a detailed pre-college educational history, with some providing up to 10. 
### Original Data, as exported from Redcap: 
Data with each row representing a unique participants complete school history, stored in columns
Columns: ['Study ID', 'Write the start grade of your FIRST school.',
       'Write the end grade of your FIRST school.',
       'Write the name of your FIRST school.',
       'Write the city/town of your FIRST school.',
       'Write the state (or country) of your FIRST school.',
       'Write the start age of attending your FIRST school.',
       'Write the end age of attending your FIRST school.',
       'Was this a public or private/religious school? ',
       'Most children in the school were...?',
       'Write the start grade of your SECOND school.',] repeated to 10 schools. Participants with less than 10 schools were left blank. 
A total of 128 participants submitted school history data. 

Functions using the above dataframe were constructed: 

**addSchoolLvlCol(dataframe, number of schools)**: adds three columns after each school, with binary data corresponding to if the school was an elementary, middle school, or high school. 

**schoollevel(df, numschools)**: classifies the above generated columns using the following definitions: 
 Elem = [0,1,2,3,4,5]
  Mid = [6,7,8]
  High = [9,10,11,12]
Due to the nonuniform school classification, some schools are classified as two or more categories 

**nonminoritymajority(df)**:
Similair to the coding used in the Health and Retirement Study, a metric using answers to 'Most children in the school were...?' was constructed to aggregate the time during childhood a participant spent at a school that was not a minority-majority school. A minority-majority school is associated with increased racial segregation and is a measure of the systemic inequity that is associated with it. 

### School Name Data generated, for use in data entry and School_History_Analysis_and_Map_Making.ipynb: 

1. Original data was pivoted so that each row corresponded to a unique school. This was done using MakeSchoolInfoDataEntry() function. 
   501 unique schools were found.
2. Using school names to manually webscrap addresses
 New dataframe was imported into excel, and names of schools manually webscraped using google, facebook, news articles, and other databases. Data recorded primarily included the street address of the school.  National Center for Education Statistics number was recorded for schools that had one. 

**MakeSchoolInfoDataEntry(df)**: Function that takes the original, redcap exported dataframe as an argument. This data is pivoted so that each row represents an individual school, rather than individual participant. Returns pivoted dataframe 


## Educational History Map Making and Analysis 
Data: Data pivoted to row represenation of individual schools, and webscraped to include addresses when available. 

filepath: variable called pathe is left blank, assign to path of 'MBES' folder where data for this project is stored. 

Use: The Folium library of python was used to generate maps from the coordinates of each school address. This is used to create a network graph on an interactive map vizualizing the geographical movement of each participant during childhood. The path length of each participants network representation was calculated in terms of both integer number of moves and geodesic distance moved. 
### Geocoding and Preprocessing

**1.** Data was cleaned to remove '\n' and to drop schools with blank addresses. After this, 
   Total number of schools: 421
   Total number of participants with atleast 1 address: 120

**2.** Geocoding addresses into (lat, long) coordinates: 
 Google Maps' python API was used to geocode addresses returned from manual webscraping into longitude and latitude.

**latlong(df)**: Function that extracts latitude and longitude from googlemaps API returned JSON file

After Geocoding and Dropping Blanks...
Total number of schools are: 420
Total number of participants with atleast 1 address: 119

**3.** Using US Census API to map coordinates to census tract.  We store this data in a different dataframe, **dftract** since not all geocodable addresses have a census tract.

**tractinfo(df)**: Function used to extract 2020 census tract from Census API returned JSON file

   This only applies to US addresses. Since some participants listed schools in other countries, some coordinates cannot be mapped.
   The number of addresses without Census Tracts are: 24
   These addresses were dropped.
   
**4.** Using Census tract to pull data on median income for each school
   
**5.** Made a function to calculate the change in income between each school and the previous school attended. This is 0 if it is a participants first or only school

### Map Making and Analysis

data: as processed above 

Folium library of python is used to construct the following functions: 

**mapmapker(df)**: This function plots each geocoded address on a global, interactive map

**graphmap(df)**: This function creates a network with each node representing a geocodable address, and each edge representing consecutive addresses for a participant. This network is on top of an interactive, global folium map.  This vizualizes the movement of participants during chilhood. 

**searchgraphmap(df, subset)**: This function takes an argument subset of datatype list that contains a list of study IDs. This is used to generate a subset of df with only the ID's in the given subset. This subset is then graphed in the same way as graphmap. 

**geocodedratio(df, subset)**: gives the ratio of the number of IDs with geocodable addresses to the number of IDs in a subset. For example, the subset of IDs in the middle income group may not all be geocodable. This ratio may differ based on subsets, and could confound analysis done in the next steps.

**compareGroups(df, subset1, subset2, title1, title2)**: 
This function takes two list type arguments (subset1 & subset2) of study IDs. Titles are string arguments used to make titles for the graph map. This function then makes a network graph of df, with each subgraph colorcoded based on its participation in either subset1 or subset2. *warning* subsets MUST be disjoint. 

This document uses two examples, one comparing 'middle' vs 'low' income participant subsets, and another comparing 'yes' vs 'no' response subsets on a question from the Childhood SES questionaire also administered during the MBES study. This question asks if participants remember moving due to financial hardship before the age of 16. 

#### Computing and analysing geodesic and binary path length
In this section, a new dataframe is created that stores metrics about each participant. This dataframe is refered here as **dis**, and is intended to store key metrics for each participant for quick data analysis. 

**geographicdistance(df)**: this function does three things. First, it calculates the total distance traveled by each participant by adding up the geodesic distance between each succesive school attended. Second, it calculates the total number of moves. Third, it stores these values in a new dataframe and returns this dataframe. This dataframe is called **dis** and is used an input in the following functions.

**subsetdis( dis, filepath)**: This function takes the dis dataframe amd a filepath to a redcap report returning a target subset of study ID's. This function returns a subset of dis with only the values from the redcap report. 

**compareGroups(dis, nameofCol, file1, value1, file2, value2)**: This function uses the above function to reduce the code needed to add a binary column to 'dis' classifying each ID based on a redcap report generated subset to juse one line. File1 and file2 are filepaths to a redcap report corresponding to two disjoint subsets. nameofCol is the name of the column storing this binary variable. value1 is the value to assign to nameofCol for IDs belonging to the 1st subset, and value2 is the value to assign to nameofCol for IDs belonging to the 2nd subset

#### Comparing Economic Mobility with Geographic Mobility
Next, I calculated economic changes for each participant. I put the result into the same dataframe dis. 

**econchange(df, dis)**: calculates the change in median income between the last and the first reported school, stores it as a new column in dis, and returns dis. 

Seaborn libary of python is used to make a scatterplot of Economic Mobility vs Geographic Mobility

#### Maps Integrated with Census Data

**incomemap(df)**: This function creates a network graph similair to the above graphmap, except the color of each line is a vizualization of economic changes between the originating address and the next address. The more green a line, the higher the increase in median income associated with the move. The more red a line, the higher the decrease in median income associated with the move. 

# Residential Data

## Residential History Processing
Participants provided a residential history that spans their whole life, including addresses. This data did not require web scraping, and was immediatly usable for geocoding.  Google maps' python api was used to geocode these addresses into longtitude and latitude. 

**MakeResInfoDataEntry(df)**: Function similair to MakeSchoolInfoDataEntry() that pivots the redcap exported dataframe to one where each row represents an individual residence instead of an individual participant. Returns pivoted dataframe. 

## Residential History Map Making and Analysis 
Data: Data pivoted to row represenation of individual residences, and webscraped to include addresses when available. 

filepath: variable called pathe is left blank, assign to path of 'MBES' folder where data for this project is stored. 

Use: The Folium library of python was used to generate maps from the coordinates of each residential address. This is used to create a network graph on an interactive map vizualizing the geographical movement of each participant during their life. The path length of each participants network representation was calculated in terms of both integer number of moves and geodesic distance moved. 
### Geocoding and Preprocessing 

**1.** Data was cleaned to remove '\n' and to drop residences with no address or zipcode. After this, 
   Total number of residences: 546
   Total number of participants with atleast 1 residence: 121
   
**2.** Geocoding addresses into (lat, long) coordinates: 
 Google Maps' python API was used to geocode addresses into longitude and latitude.

**latlong(df)**: Function that extracts latitude and longitude from googlemaps API returned JSON file

After Geocoding and Dropping Blanks...
Total number of schools are: 420
Total number of participants with atleast 1 address: 119

**3.** Using US Census API to map coordinates to census tract. We store this data in a different dataframe, **dftract** since not all geocodable addresses have a census tract.

**tractinfo(df)**: Function used to extract 2020 census tract from Census API returned JSON file

   This only applies to US addresses. Since some participants listed schools in other countries, some coordinates cannot be mapped.
   These addresses were dropped.
   
**4.** Using Census tract to pull data on median income for each residence, with the Census API 

4a Dropping addresses with a median income of 0 or less 

**5.** Calculating the change in income for each residence, relative to previous residence 
**incomechange(df)** : Function to calculate change in income between each residence and the previous residence. This is 0 if it is the only or first resiedence

### Map Making and Analysis

data: as processed above 

Folium library of python is used to construct the following functions: 

**mapmapker(df)**: This function plots each geocoded address on a global, interactive map

**graphmap(df)**: This function creates a network with each node representing a geocodable address, and each edge representing consecutive addresses for a participant. This network is on top of an interactive, global folium map.  This vizualizes the movement of participants during life.

**searchgraphmap(df, subset)**: This function takes an argument subset of datatype list that contains a list of study IDs. This is used to generate a subset of df with only the ID's in the given subset. This subset is then graphed in the same way as graphmap. 

**geocodedratio(df, subset)**: gives the ratio of the number of IDs with geocodable addresses to the number of IDs in a subset. For example, the subset of IDs in the middle income group may not all be geocodable. This ratio may differ based on subsets, and could confound analysis done in the next steps.

**compareGroups(df, subset1, subset2, title1, title2)**: 
This function takes two list type arguments (subset1 & subset2) of study IDs. Titles are string arguments used to make titles for the graph map. This function then makes a network graph of df, with each subgraph colorcoded based on its participation in either subset1 or subset2. *warning* subsets MUST be disjoint. 

This document uses two examples, one comparing 'middle' vs 'low' income participant subsets, and another comparing 'yes' vs 'no' response subsets on a question from the Childhood SES questionaire also administered during the MBES study. This question asks if participants remember moving due to financial hardship before the age of 16. 

#### Computing and analysing geodesic and binary path length
In this section, a new dataframe is created that stores metrics about each participant. This dataframe is refered here as **dis**, and is intended to store key metrics for each participant for quick data analysis. 

**geographicdistance(df)**: this function does three things. First, it calculates the total distance traveled by each participant by adding up the geodesic distance between each succesive residence. Second, it calculates the total number of moves. Third, it stores these values in a new dataframe and returns this dataframe. This dataframe is called **dis** and is used an input in the following functions.

**subsetdis( dis, filepath)**: This function takes the dis dataframe amd a filepath to a redcap report returning a target subset of study ID's. This function returns a subset of dis with only the values from the redcap report. 

**compareGroups(dis, nameofCol, file1, value1, file2, value2)**: This function uses the above function to reduce the code needed to add a binary column to 'dis' classifying each ID based on a redcap report generated subset to juse one line. File1 and file2 are filepaths to a redcap report corresponding to two disjoint subsets. nameofCol is the name of the column storing this binary variable. value1 is the value to assign to nameofCol for IDs belonging to the 1st subset, and value2 is the value to assign to nameofCol for IDs belonging to the 2nd subset

#### Comparing Economic Mobility with Geographic Mobility
Next, I calculated economic changes for each participant. I put the result into the same dataframe dis. 

**econchange(df, dis)**: calculates the change in median income between the last and the first reported school, stores it as a new column in dis, and returns dis. 

Seaborn libary of python is used to make a scatterplot of Economic Mobility vs Geographic Mobility

#### Maps Integrated with Census Data

**incomemap(df)**: This function creates a network graph similair to the above graphmap, except the color of each line is a vizualization of economic changes between the originating address and the next address. The more green a line, the higher the increase in median income associated with the move. The more red a line, the higher the decrease in median income associated with the move. 

