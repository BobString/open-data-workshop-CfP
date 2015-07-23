
# Code For Princeton: Open Data Workshop
Files and links necessary for the open data workshop

## Data sources

## OpenRefine data

Data from Jersey City Data Page: [Original link](http://www.cityofjerseycity.com/uploadedFiles/Data/JCPD%20Calls%20for%20Service%202014(1).xlsx) [Mirror](./data/OriginalJCPDCallsforService2014.xlsx)

Data in CSV format: [Link](./data/OriginalJCPD2014.csv)


### Geocoding

1. Starting with a single address field `Edit Column > Add Column by Fetching URLs` Nominatim has a limit of 1 geocode per second so make sure to set the throttle delay to greater than 1000 milliseconds Fetch URL based on column (quotes needed):

`"http://nominatim.openstreetmap.org/search?format=json&email='EMAIL'&app=google-refine&q='" + escape(value, 'url')`

2. Extract lat/lon from newly created JSON column `Edit Column > Add Column based on This Column`. Parse the JSON and concatenate latitude, longitude:

`with(value.parseJson()[0], pair, pair.lat + ',' + pair.lon)`

Source: [Paul Bartsch GitHub](https://gist.github.com/pdbartsch/5987932)
