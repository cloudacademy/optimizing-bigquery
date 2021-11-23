## Optimizing Google BigQuery
This file contains text you can copy and paste for the examples in Cloud Academy's _Optimizing Google BigQuery_ course.

### Reducing the Amount of Data Processed
#### Stock exchange data files
GBPUSD_2014_01.csv and GBPUSD_2014_02.csv are no longer available from Google, but you can download smaller versions of them from this repository.  

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

### Partitioned Tables
#### Create partitioned table
```
bq query --use_legacy_sql=false --replace --destination_table 'examples.gbpusd_201401p' --time_partitioning_field time "SELECT * from examples.gbpusd_201401"
```

### Denormalized Data Structures
#### MusicBrainz data files
**Note: The files below are no longer available. We are working on uploading smaller versions of them to this repository.**

| Table         | Data                          | Schema                                                 |
| ------------------ | ----------------------------- | ------------------------------------------------------ |
| artist             | gs://solutions-public-assets/bqetl/artist.json | https://storage.googleapis.com/solutions-public-assets/bqetl/artist_schema.json |
| artist_credit_name | gs://solutions-public-assets/bqetl/artist_credit_name.json | https://storage.googleapis.com/solutions-public-assets/bqetl/artist_credit_name_schema.json|
| recording            | gs://solutions-public-assets/bqetl/recording.json | https://storage.googleapis.com/solutions-public-assets/bqetl/recording_schema.json |

#### Create denormalized table
```
SELECT
  artist.id, artist.gid AS artist_gid, artist.name AS artist_name, artist.area,
  recording.name AS recording_name, recording.length, recording.gid, recording.video
FROM
  `examples.artist` AS artist
INNER JOIN
  `examples.artist_credit_name` AS artist_credit_name
ON
  artist.id = artist_credit_name.artist
INNER JOIN
  `examples.recording` AS recording
ON
  artist_credit_name.artist_credit = recording.artist_credit
```

#### Query denormalized table
```
SELECT artist_name, recording_name
FROM `cloud-academy-content-team.examples.recording_by_artist`
WHERE artist_name LIKE '%Elvis%'
```

### Nested Repeated Fields
#### Example data
| Table         | Data                          | Schema                                                 |
| ------------------ | ----------------------------- | ------------------------------------------------------ |
| persons_data       | https://raw.githubusercontent.com/cloudacademy/optimizing-bigquery/master/personsData.json | https://raw.githubusercontent.com/cloudacademy/optimizing-bigquery/master/personsDataSchema.json |

#### Query a nested field
```
SELECT fullName, phoneNumber.number
FROM `cloud-academy-content-team.examples.persons_data`
```

#### Unnest a field
```
SELECT fullName, place
FROM `cloud-academy-content-team.examples.persons_data`,
UNNEST(citiesLived)
WHERE place = "Austin"
```

#### Query a view
```
SELECT fullName, place
FROM `cloud-academy-content-team.examples.cities_by_person`
WHERE place = 'Stockholm'
```

### Security
#### Query for authorized view
```
SELECT fullName, age, gender
FROM `cloud-academy-content-team.examples.persons_data`
```
