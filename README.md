# Austin Bikeshare
SQL Queries exploring Austin Bikeshare Dataset on Google BigQuery Dataset


### 1. Number of active bikestands in Austin area
```sql
SELECT COUNT(*) 
FROM `bigquery-public-data.austin_bikeshare.bikeshare_stations` 
WHERE  status="active" 
LIMIT 1000
```
Query Result:

![image](https://user-images.githubusercontent.com/87647875/131242131-b555afae-4e1c-4aee-879b-94e26b3e65f1.png)

### 2. Districts with most number of bike stands

```sql
SELECT council_district,COUNT(council_district) 
FROM `bigquery-public-data.austin_bikeshare.bikeshare_stations` 
GROUP BY council_district  
LIMIT 1000
```
Query Result:

![image](https://user-images.githubusercontent.com/87647875/131242207-33e6be02-c560-437e-a029-a5f8d8b032e2.png)

### 3. Longest bike rides and their details

```sql
SELECT DISTINCT A.council_district,B.start_station_name,B.end_station_name,B.start_time,B.duration_minutes
FROM `bigquery-public-data.austin_bikeshare.bikeshare_stations` AS A
INNER JOIN `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS B ON B.start_station_id=A.station_id
WHERE A.status="active"
ORDER BY B.duration_minutes DESC 
LIMIT 1000
```
Query Result:

![image](https://user-images.githubusercontent.com/87647875/131242305-a53788d4-d00e-4d25-a1f3-ca7a592bc696.png)

### 4. Districts with most rides and longest durations, hence busiest districts/workplace districts

```sql
SELECT council_district,COUNT(council_district) AS number_of_trips
FROM 
(SELECT DISTINCT A.council_district,B.duration_minutes
FROM `bigquery-public-data.austin_bikeshare.bikeshare_stations` AS A
INNER JOIN `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS B ON B.start_station_id=A.station_id
WHERE A.status="active"
ORDER BY B.duration_minutes DESC 
LIMIT 1000)
GROUP BY council_district
ORDER BY COUNT(council_district) DESC
```
Query Result:

![image](https://user-images.githubusercontent.com/87647875/131242518-5b03afa1-b1fb-4912-b312-1ad82b0e69cd.png)

### 5. Districts and stations with higest short distance trips, hence these are districts with high profit & regular usage

```sql
SELECT district,start_station_id,end_station_id,count(*) AS number_of_rides
FROM 
(
    SELECT starting_district as district ,duration_minutes, start_station_id,end_station_id
FROM 
(SELECT A.council_district AS starting_district,B.trip_id,B.start_station_id,B.duration_minutes
FROM `bigquery-public-data.austin_bikeshare.bikeshare_stations` AS A
INNER JOIN `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS B ON B.start_station_id=A.station_id
WHERE A.status="active"
LIMIT 1000) AS C
INNER JOIN
(SELECT A.council_district AS ending_district,B.trip_id,B.end_station_id
FROM `bigquery-public-data.austin_bikeshare.bikeshare_stations` AS A
INNER JOIN `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS B ON SAFE_CAST(B.end_station_id AS INT64)=A.station_id
WHERE A.status="active" 
LIMIT 1000) AS D
ON
C.starting_district =D.ending_district 
WHERE 
start_station_id!=SAFE_CAST(end_station_id as int64)
ORDER BY
duration_minutes
)
GROUP BY start_station_id,end_station_id,district 
ORDER BY COUNT(*) DESC
```
Query Result:

![image](https://user-images.githubusercontent.com/87647875/131242564-627c92f4-fcbd-4ec2-99db-8a9cdb261abb.png)
