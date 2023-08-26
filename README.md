# MBES_Code
### MBES
The Midlife Brain and Environment Study (MBES) is a longitudinal study (n = 151)  of people between the ages of 40 and 64 consisting of three timepoints, spaced two years apart. Each time point consists of two appointments, spaced 30 days apart. Data collected at each time point includes fMRI scans, both resting state and task based, blood testing, hair sampling, extensive cognitive testing, 30 days of actigraph accelerometry, and daily electronic momentary assesments. The goal of this study is to understand the relationship between enviornmental and socioeconimic factors and changes in network charactertersitics as the brain ages. The data processing in this repository was conducted after timepoint 1. This repository was created so that following timepoint 2, these methods can be used with updated, more detailed life history data. This study is conducted by the Wig Neuroimaging Lab https://www.wigneurolab.org/ at the Center for Vital Longevity, associated with the University of Texas at Dallas. 
### Life History Survey 
One of the measures completed by participants is the Life History Survey, derived from the Health and Retirement Study. https://hrs.isr.umich.edu/data-products/life-history
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
addSchoolLvlCol(dataframe, number of schools): adds three columns after each school, with binary data corresponding to if the school was an elementary, middle school, or high school. 

schoollevel(df, numschools): classifies the above generated columns using the following definitions: 
 Elem = [0,1,2,3,4,5]
  Mid = [6,7,8]
  High = [9,10,11,12]

nonminoritymajority(df):
Similair to the coding used in the Health and Retirement Study, a metric using answers to 'Most children in the school were...?' was constructed to aggregate the time during childhood a participant spent at a school that was not a minority-majority school. A minority-majority school is associated with increased racial segregation and is a measure of the systemic inequity that is associated with it. 

### School Name Data, as generated in School_History_Processing.ipynb: 

1. Original data was pivoted so that each row corresponded to a unique school.
   501 unique schools were found.
3. Using school names to manually webscrap addresses
 New dataframe was imported into excel, and names of schools manually webscraped using google, facebook, news articles, and other databases. Data recorded primarily included the street address of the school.  National Center for Education Statistics number was recorded for schools that had one. 
5. Geocoding addresses into (lat, long) coordinates ( See School_History_Analysis_and_Map_Making.ipynb)
After, I used google maps' python API to geocode these addresses into longitude and latitude, allowing plotting of participant movement during childhood  
7. Pulling Census Data 




## Residential History Processing
Participants provided a residential history that spans their whole life, including addresses. This data did not require web scraping, and was immediatly usable for geocoding.  Google maps' python api was used to geocde these addresses into longtitude and latitude. 
## Educationakl Histoy Map Making and Analysis 
The Folium library of python was used to generate maps from the coordinates of each school address. This is used to create a network graph on an interactive map vizualizing the geographical movement of each participant during childhood. The path length of each participants network representation was calculated in terms of both integer number of moves and geodesic distance moved. 
## Overview of Tools Built for Data Analysis and Vizualization 
