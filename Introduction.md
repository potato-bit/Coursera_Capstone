# Introduction / Business Problem
Using venue data, neighborhood demographics and crime data, which neighborhood in Chicago and New York City is the safest from property crimes to open a new venue?

Suppose, an entrepreneur wants to find out whether the location he/she is opening a new venue carries a high risk of property crime. We attempt to answer the question using k-Means Clustering and Linear Regression to find the best location to open a new venue safe from crime.

We will first organize the data from our datasets described in [Data.md](https://github.com/potato-bit/Coursera_Capstone/blob/master/Data.md#data), visualize the data and begin clustering analysis to explore the characteristics of both cities. Finally, we will perform a regression on crime rate against neighborhood characteristics to determine significant determinants of crime and another regression against venue variables to determine the risk of property crime

Further, the results of the analysis could provide useful insights for public planning officials for neighborhood-level development and crime prevention by focusing education and other poverty-reducing resources


Venue data is gathered using the Foursquare API, neighborhood demographics are gathered from various sources from the US Census Bureau, and crime data is taken from the relevant police authority databases. The data processing and sources are described in detail in [Data.md](https://github.com/potato-bit/Coursera_Capstone/blob/master/Data.md#data)
