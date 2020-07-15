# Data

## Neighborhood Data
First, we need to get location data for the two cities' neighborhoods, this is completed using the `BeautifulSoup4` HTML scraper to get the data from:
* Chicago: https://en.wikipedia.org/wiki/Community_areas_in_Chicago#List_of_community_areas

We then use `geocoder` to assign coordinates to each neighborhood and save to a dataframe

For New York however, we use an existing dataset that already contains all the geographical data as `geocoder` gives inconsistent results using the Bing API. The dataset is taken from https://geo.nyu.edu/catalog/nyu_2451_34572

The resulting dataframes should look like this:

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_c.png 'Chicago')

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_ny.png 'New York City')



## Venue Data
We then explore each neighborhood with the coordinates acquired using the Foursquare API and the `explore` endpoint, a free call request, we are only interested in the venue id, coordinates, and category response fields:

`https://api.foursquare.com/v2/venues/explore?&client_id=CLIENT_ID&client_secret=CLIENT_SECRET&v=VERSION&ll=LAT,LNG&radius=1000&limit=500` (CAPS are placeholders)

Initially, I had tried to also use the popularity, tips, hours, and popular hours response fields from the `details` endpoint, a premium call request, however with only 500 call requests per day with a Sandbox account, it proved to be unfeasible with over 25,000 venues which would take ~50 days to complete.

We get a max of 100 of the most popular venues at the time the notebook is run for each neighborhood in a radius of 1km (500 meters in the case of New York, because its neighborhood borders are smaller). We also delete any duplicates arising from overlapping neighborhood radii.

A sample response looks like [this](https://foursquare.com/developers/explore#req=venues%2Fexplore%3Fnear%3DNYC). We use the `json` package to read the JSON file and extract the relevant fields, namely:
* `request["response"]['groups'][0]['items']['venue']['name']` 
* `request["response"]['groups'][0]['items']['venue']['location']['lat']` 
* `request["response"]['groups'][0]['items']['venue']['location']['lng']`  
* `request["response"]['groups'][0]['items']['venue']['categories'][0]['name']`
* `request["response"]['groups'][0]['items']['venue']['id']`

We iterate through every neighborhood to get a maximum of 100 venues per neighborhood and assign it to the dataframe `c_venues` for Chicago, or `ny_venues` for NYC. See examples below.

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/c_venues.png 'Chicago')

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/ny_venues.png 'Chicago')



## Crime Data
We will use public arrest data published by the respective police authorities of Chicago and New York. The datasets include the following fields: unique id, arrest date, primary description, secondary description, address, and geographical location. 

The datasets are taken from:
* Chicago: https://data.cityofchicago.org/Public-Safety/Crimes-Map/dfnk-7re6 
* New York: https://data.cityofnewyork.us/Public-Safety/NYPD-Arrests-Data-Historic-/8h9b-rp9u/data 

We import the `.csv` files as dataframes:

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_crime_c_before.png 'Chicago')

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_crime_n_before.png 'New York City')

And we drop any unnecessary columns and rename the columns for consistency:

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_crime_c_after.png 'Chicago')

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_crime_n_after.png 'New York City')

And finally since we aren't interested in all crimes, only property crimes, we drop all rows that don't include **THEFT, CRIMINAL DAMAGE, BURGLARY, ROBBERY, CRIMINAL TRESPASS, ARSON,** and **INTIMIDATION** for the Chicago dataset. And all rows without **PETIT LARCENY, CRIMINAL MISCHIEF AND RELATED OF, GRAND LARCENY, ROBBERY, FORGERY, BURGLARY, OTHER OFFENSES RELATED TO THEF, CRIMINAL TRESPASS, POSSESSION OF STOLEN PROPERTY, OFFENSES INVOLVING FRAUD, FRAUDS, BURGLAR'S TOOLS, THEFT OF SERVICES, THEFT-FRAUD,** and **ARSON.** for the NYC dataset

This reduces the dataset to between 60,000-100,000 entries, we reduce it further and take a `random.sample` of about 20,000 entries for each dataset.

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/drop.png 'Taking a Sample')

Finally, to compare neighborhoods, we have to assign each crime and its location to a neighborhood in the city, this is completed using `geopy.distance.geodesic`. We loop through each crime and measure its geodesic distance (see [geodesic distance](https://en.wikipedia.org/wiki/Geodesics_on_an_ellipsoid)) to every neighborhood and assume that which ever neighborhood is the least distance away is the crime's corresponding neighborhood.

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/get_neighborhood.png 'getting Neighborhoods')

The resulting dataset will look like this:

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_crime_c_final.png 'Chicago')

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_crime_n_final.png 'New York City')


## Demographic Data
Demographic data for Chicago and NYC were collected from the US Census Bureau and compiled from various sources.
* Chicago: [Population and Ethnicity](http://3ia2qo49aq3t30ap983kxt9x-wpengine.netdna-ssl.com/wp-content/uploads/2019/03/Census-Data-by-Chicago-Community-Area-2018.xlsx), [Unemployment and Poverty Rate](https://data.cityofchicago.org/Health-Human-Services/Poverty-Indicators-by-COmmunity-Area/c44j-fgcy), and [PCI and Population aged 25 without High School Diploma](https://data.cityofchicago.org/Health-Human-Services/Per-Capita-Income/r6ad-wvtk)
* New York: [NYU Furman Center CoreData.nyc](app.coredata.nyc/visit)

The data has been compiled into CSV files: `chicago_demographics.csv` and `nyc_demographics.csv` which are included in the Github repository which include the 5-year averages (2013-2018) for each variable.

We import the `.csv` files to make the following dataframes:

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_demo_c.png 'Chicago')

![alt-text](https://github.com/potato-bit/Coursera_Capstone/blob/master/demo/df_demo_n.png 'New York City')


_Note that, for NYC, we are looking at sub-borough areas now instead of neighborhoods (the only data that was available), for clustering, we will continue using the neighborhoods, but for the regression we will use sub-borough areas instead. And we have median income instead of PCI, for individual city clustering, this will not be an issue, however when we perform the regression analysis, we will have to drop the income columns_








