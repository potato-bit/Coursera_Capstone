# Data

## Neighborhood Data
First, we need to get location data for the three cities' neighborhoods, this is completed using the `BeautifulSoup4` HTML scraper to get the data from:
* Chicago: https://en.wikipedia.org/wiki/Community_areas_in_Chicago#List_of_community_areas
* Houston: https://en.wikipedia.org/wiki/List_of_Houston_neighborhoods

We then use `geocoder` to assign coordinates to each neighborhood and save to a dataframe

For New York however, we use an existing dataset that already contains all the geographical data as `geocoder` gives inconsistent results using the Bing API. The dataset is taken from https://geo.nyu.edu/catalog/nyu_2451_34572



## Venue Data
We then explore each neighborhood with the coordinates acquired using the Foursquare API and the `explore` endpoint, a free call request, we are only interested in the venue id, coordinates, and category response fields. Initially, I had tried to also use the popularity, tips, hours, and popular hours response fields from the `details` endpoint, a premium call request, however with only 500 call requests per day with a Sandbox account, it proved to be unfeasible with over 25,000 venues which would take ~50 days to complete.

We get a max of 100 of the most popular venues at the time the notebook is run for each neighborhood in a radius of 1km (500 meters in the case of New York, because its neighborhood borders are smaller). We also delete any duplicates arising from overlapping neighborhood radii.



## Arrest Data
We will use public arrest data published by the respective police authorities of Chicago and New York. The datasets include the following fields: unique id, arrest date, primary description, secondary description, address, and geographical location. Houston is not included in this section because the dataset did not include geographical location data and it was not possible to use `geocoder` to get location data from the address data for 20,000+ entries.

The datasets are taken from:
* Chicago: https://data.cityofchicago.org/Public-Safety/Crimes-Map/dfnk-7re6 
* New York: https://data.cityofnewyork.us/Public-Safety/NYPD-Arrests-Data-Historic-/8h9b-rp9u/data 

The datasets for 2019 onwards include over 200,000 entries each so to save on memory and processing, we will only use a random sample of 1/10th the original size using `random.sample`, this is still over 20,000 entries which is more than sufficient for clustering analysis.

Finally, to compare neighborhoods, we have to assign each crime and its location to a neighborhood in the city, this is completed using `geopy.distance.geodesic`. We loop through each crime and measure its geodesic distance (see [geodesic distance](https://en.wikipedia.org/wiki/Geodesics_on_an_ellipsoid)) to every neighborhood and assume that which ever neighborhood is the least distance away is the crime's corresponding neighborhood.
