# Data: USGS earthquake catalogue (Kivu region)

**Source:** U.S. Geological Survey, Earthquake Catalog (ComCat), FDSN Event Web Service
https://earthquake.usgs.gov/fdsnws/event/1/

**Licence:** USGS-authored data are in the U.S. public domain (https://www.usgs.gov/faqs/how-should-i-cite-a-usgs-website-or-publication). The data contain no personal information.

## Study area and query

Circle of 300 km radius around Lake Kivu (latitude -2.0, longitude 29.2), magnitude 2.5 and above, from 1990, oldest first:

```
https://earthquake.usgs.gov/fdsnws/event/1/query?format=csv&starttime=1990-01-01&latitude=-2.0&longitude=29.2&maxradiuskm=300&minmagnitude=2.5&orderby=time-asc
```

Save the result as `usgs_kivu_events.csv` in this folder.

## Checking the size

The service has a `count` method that takes the same parameters:

```
https://earthquake.usgs.gov/fdsnws/event/1/count?starttime=1990-01-01&latitude=-2.0&longitude=29.2&maxradiuskm=300&minmagnitude=2.5
```

A single query returns at most 20,000 events. If the count is above that, split the query by year using `starttime` and `endtime`. If it is too low for the project, widen `maxradiuskm` or lower `starttime`.

## Sample record

| Field | Value |
|---|---|
| USGS event id | iscgem13213409 |
| Time (UTC) | 2008-02-03 07:34:12 |
| Latitude, longitude | -2.296, 28.900 |
| Depth | 10.0 km |
| Magnitude | 5.9 |
| Place | 20 km N of Cyangugu, Rwanda |

Source: USGS event page https://earthquake.usgs.gov/earthquakes/eventpage/iscgem13213409
