# MBES_Code
### MBES
The Midlife Brain and Environment Study (MBES) is a longitudinal study (n ~ 150)  of people between the ages of 40 and 64 consisting of three timepoints, spaced two years apart. Each time point consists of two appointments, spaced 30 days apart. Data collected at each time point includes fMRI scans, both resting state and task based, blood testing, hair sampling, extensive cognitive testing, 30 days of actigraph accelerometry, and daily electronic momentary assesments. The goal of this study is to understand the relationship between enviornmental and socioeconimic factors and changes in network charactertersitics as the brain ages. The data processing in this repository was conducted after timepoint 1. This study is conducted by the Wig Neuroimaging Lab https://www.wigneurolab.org/ at the Center for Vital Longevity 
### Life History Survey 
One of the measures completed by participants is the Life History Survey, derived from the Health and Retirement Study. 
## Educational History Processing
1. Using school names to manually webscrap addresses 
2. Geocoding addresses into (lat, long) coordinates
3. Pulling Census Data 

Participants provide a detailed pre-college educational history, with some providing up to 10. This data was analyzed by myself and colleagues from the Wig Lab, where we manually web scraped the address of each school. This involved using google, facebook, news articles, and the National Center for Education Statistics database.

After, I used google maps' python API to geocode these addresses into longitude and latitude, allowing plotting of participant movement during childhood 
## Residential History Processing
Participants provided a residential history that spans their whole life, including addresses. This data did not require web scraping, and was immediatly usable for geocoding.  Google maps' python api was used to geocde these addresses into longtitude and latitude. 
## Educationakl Histoy Map Making and Analysis 
The Folium library of python was used to generate maps from the coordinates of each school address. This is used to create a network graph on an interactice map vizualizing the geographical movement of each participant during childhood. The path length of each participants network representation was calculated in terms of both integer number of moves and geodesic distance moved. 
