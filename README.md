
# Code For Princeton: Open Data Workshop
Files and links necessary for the open data workshop

## Data sources

## OpenRefine data

Data from Jersey City Data Page: [Original link](http://www.cityofjerseycity.com/uploadedFiles/Data/JCPD%20Calls%20for%20Service%202014(1).xlsx) , [Mirror](https://github.com/BobString/open-data-workshop-CfP/blob/master/data/OriginalJCPDCallsforService2014.xlsx?raw=true)

Data in CSV format: [Link](https://raw.githubusercontent.com/BobString/open-data-workshop-CfP/master/data/OriginalJCPD2014.csv)


### Geocoding

1. Starting with a single address field `Edit Column > Add Column by Fetching URLs` Nominatim has a limit of 1 geocode per second so make sure to set the throttle delay to greater than 1000 milliseconds Fetch URL based on column (quotes needed):

`"http://nominatim.openstreetmap.org/search?format=json&email='EMAIL'&app=google-refine&q='" + escape(value, 'url')`

2. Extract lat/lon from newly created JSON column `Edit Column > Add Column based on This Column`. Parse the JSON and concatenate latitude, longitude:

`with(value.parseJson()[0], pair, pair.lat + ',' + pair.lon)`

Source: [Paul Bartsch GitHub](https://gist.github.com/pdbartsch/5987932)

### Date conversion

1. Transform Time Received and Time Dispached columns with `toDate(value,"MM/dd/yyyy H:m:s").toString("yyyy-dd-MM HH:mm:ss").replace(" ","T")` to adapt it to the date format in CartoDb

### Complete recepie

```json
[
  {
    "op": "core/column-removal",
    "description": "Remove column Column",
    "columnName": "Column"
  },
  {
    "op": "core/column-removal",
    "description": "Remove column Column2",
    "columnName": "Column2"
  },
  {
    "op": "core/column-split",
    "description": "Split column Call Code Description by separator",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "Call Code Description",
    "guessCellType": true,
    "removeOriginalColumn": true,
    "mode": "separator",
    "separator": ";",
    "regex": false,
    "maxColumns": 0
  },
  {
    "op": "core/row-removal",
    "description": "Remove rows",
    "engineConfig": {
      "facets": [
        {
          "expression": "value",
          "invert": true,
          "selectError": false,
          "omitError": false,
          "name": "Call Code Description 1",
          "selectBlank": false,
          "columnName": "Call Code Description 1",
          "omitBlank": false,
          "type": "list",
          "selection": [
            {
              "v": {
                "v": "HYDRANT PARKING VIOLATION",
                "l": "HYDRANT PARKING VIOLATION"
              }
            }
          ]
        }
      ],
      "mode": "row-based"
    }
  },
  {
    "op": "core/column-removal",
    "description": "Remove column Call Code Description 3",
    "columnName": "Call Code Description 3"
  },
  {
    "op": "core/column-removal",
    "description": "Remove column Call Code Description 2",
    "columnName": "Call Code Description 2"
  },
  {
    "op": "core/column-removal",
    "description": "Remove column Is Primary",
    "columnName": "Is Primary"
  },
  {
    "op": "core/mass-edit",
    "description": "Mass edit cells in column Address",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "Address",
    "expression": "value",
    "edits": [
      {
        "fromBlank": false,
        "fromError": false,
        "from": [
          "third (3RD ST) & COLES ST;  "
        ],
        "to": "3RD ST & COLES ST;  "
      }
    ]
  },
  {
    "op": "core/mass-edit",
    "description": "Mass edit cells in column Address",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "Address",
    "expression": "value",
    "edits": [
      {
        "fromBlank": false,
        "fromError": false,
        "from": [
          "glennwood ave (GLENWOOD AVE) & KENNEDY BLVD;  "
        ],
        "to": "GLENWOOD AVE & KENNEDY BLVD;  "
      }
    ]
  },
  {
    "op": "core/text-transform",
    "description": "Text transform on cells in column Address using expression value.trim()",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "Address",
    "expression": "value.trim()",
    "onError": "keep-original",
    "repeat": false,
    "repeatCount": 10
  },
  {
    "op": "core/text-transform",
    "description": "Text transform on cells in column Address using expression grel:value.replace(\";\",\"\")",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "Address",
    "expression": "grel:value.replace(\";\",\"\")",
    "onError": "keep-original",
    "repeat": false,
    "repeatCount": 10
  },
  {
    "op": "core/text-transform",
    "description": "Text transform on cells in column Address using expression value.toTitlecase()",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "Address",
    "expression": "value.toTitlecase()",
    "onError": "keep-original",
    "repeat": false,
    "repeatCount": 10
  },
  {
    "op": "core/text-transform",
    "description": "Text transform on cells in column Address using expression grel:value + \", Jersey City, New Jersey, USA\"",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "Address",
    "expression": "grel:value + \", Jersey City, New Jersey, USA\"",
    "onError": "keep-original",
    "repeat": false,
    "repeatCount": 10
  },
  {
    "op": "core/column-removal",
    "description": "Remove column CITY",
    "columnName": "CITY"
  },
  {
    "op": "core/column-addition-by-fetching-urls",
    "description": "Create column JSON Response at index 12 by fetching URLs based on column Address using expression grel:\"http://nominatim.openstreetmap.org/search?format=json&email='contact@robertmm.com'&app=google-refine&q='\" + escape(value, 'url')",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "newColumnName": "JSON Response",
    "columnInsertIndex": 12,
    "baseColumnName": "Address",
    "urlExpression": "grel:\"http://nominatim.openstreetmap.org/search?format=json&email='contact@robertmm.com'&app=google-refine&q='\" + escape(value, 'url')",
    "onError": "set-to-blank",
    "delay": 2000
  },
  {
    "op": "core/column-addition",
    "description": "Create column Lat & Lon at index 13 based on column JSON Response using expression grel:with(value.parseJson()[0], pair, pair.lat + ',' + pair.lon)",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "newColumnName": "Lat & Lon",
    "columnInsertIndex": 13,
    "baseColumnName": "JSON Response",
    "expression": "grel:with(value.parseJson()[0], pair, pair.lat + ',' + pair.lon)",
    "onError": "set-to-blank"
  },
  {
    "op": "core/column-removal",
    "description": "Remove column Time Arrived",
    "columnName": "Time Arrived"
  },
  {
    "op": "core/column-move",
    "description": "Move column Time Dispatched to position 3",
    "columnName": "Time Dispatched",
    "index": 3
  },
  {
    "op": "core/text-transform",
    "description": "Text transform on cells in column Time Received using expression grel:toDate(value,\"MM/dd/yyyy H:m:s\").toString(\"yyyy-dd-MM HH:mm:ss\").replace(\" \",\"T\")",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "Time Received",
    "expression": "grel:toDate(value,\"MM/dd/yyyy H:m:s\").toString(\"yyyy-dd-MM HH:mm:ss\").replace(\" \",\"T\")",
    "onError": "set-to-blank",
    "repeat": false,
    "repeatCount": 10
  },
  {
    "op": "core/text-transform",
    "description": "Text transform on cells in column Time Dispatched using expression grel:toDate(value,\"MM/dd/yyyy H:m:s\").toString(\"yyyy-dd-MM HH:mm:ss\").replace(\" \",\"T\")",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "Time Dispatched",
    "expression": "grel:toDate(value,\"MM/dd/yyyy H:m:s\").toString(\"yyyy-dd-MM HH:mm:ss\").replace(\" \",\"T\")",
    "onError": "keep-original",
    "repeat": false,
    "repeatCount": 10
  },
  {
    "op": "core/column-removal",
    "description": "Remove column JSON Response",
    "columnName": "JSON Response"
  }
]
``` 






