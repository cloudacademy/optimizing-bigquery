## Optimizing BigQuery Performance, Cost, and Security
This file contains text you can copy and paste for the examples in Cloud Academy's _Optimizing BigQuery Performance, Cost, and Security_ course.

### Reducing the Amount of Data Processed
#### Stock exchange data files

| Table Name    | Location                                                           |
| ------------- | ------------------------------------------------------------------ |
| gbpusd_201401 | gs://solutions-public-assets/time-series-master/GBPUSD_2014_01.csv |
| gbpusd_201402 | gs://solutions-public-assets/time-series-master/GBPUSD_2014_02.csv |

The schema for the above tables is:  
venue:STRING,currencies:STRING,time:TIMESTAMP,bid:FLOAT,ask:FLOAT

#### BETWEEN operator
```sql
SELECT time, bid
FROM examples.gbpusd_201401
WHERE time
  BETWEEN TIMESTAMP("2014-01-01 00:00:00")
  AND TIMESTAMP("2014-01-01 00:30:00")
ORDER BY time ASC
```

#### Wildcard in table reference
```
SELECT MIN(time) AS mintime, MAX(time) AS maxtime
FROM `examples.gbpusd_20140*`
```
