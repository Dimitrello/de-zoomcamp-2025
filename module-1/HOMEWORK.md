Homework 1: Docker, SQL and Terraform for Data Engineering Zoomcamp 2025
https://github.com/DataTalksC…




# Questions

## Question 1. Understanding docker first run
Run docker with the python:3.12.8 image in an interactive mode, use the entrypoint bash.

```bash
docker run -it --entrypoint bash python:3.12.8
```

```bash
pip --version
```

**Answer: 24.3.1**



## Question 2. Understanding Docker networking and docker-compose
Given the following docker-compose.yaml, what is the hostname and port that pgadmin should use to connect to the postgres database?
If there are more than one answers, select only one of them.
```yaml
services:
  db:
    container_name: postgres
    image: postgres:17-alpine
    environment:
      POSTGRES_USER: 'postgres'
      POSTGRES_PASSWORD: 'postgres'
      POSTGRES_DB: 'ny_taxi'
    ports:
      - '5433:5432'
    volumes:
      - vol-pgdata:/var/lib/postgresql/data

  pgadmin:
    container_name: pgadmin
    image: dpage/pgadmin4:latest
    environment:
      PGADMIN_DEFAULT_EMAIL: "pgadmin@pgadmin.com"
      PGADMIN_DEFAULT_PASSWORD: "pgadmin"
    ports:
      - "8080:80"
    volumes:
      - vol-pgadmin_data:/var/lib/pgadmin  

volumes:
  vol-pgdata:
    name: vol-pgdata
  vol-pgadmin_data:
    name: vol-pgadmin_data
```

**Answer: db:5432 or postgres:5432**



> ### Prepare Postgres
> Run Postgres and load data as shown in the videos We'll use the green taxi trips from October 2019:
>
> ```bash
> wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-10.csv.gz
> ```
> You will also need the dataset with zones:
>
> ```bash
> wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv
>```
> Download this data and put it into Postgres.
>
> You can use the code from the course. It's up to you whether you want to use Jupyter or a python script.

## Question 3. Trip Segmentation Count
During the period of October 1st 2019 (inclusive) and November 1st 2019 (exclusive), how many trips, respectively, happened:

Up to 1 mile
In between 1 (exclusive) and 3 miles (inclusive),
In between 3 (exclusive) and 7 miles (inclusive),
In between 7 (exclusive) and 10 miles (inclusive),
Over 10 miles

```sql 
SELECT
    COUNT(*) FILTER (WHERE trip_distance <= 1) AS up_to_1_mile,
    COUNT(*) FILTER (WHERE trip_distance > 1 AND trip_distance <= 3) AS between_1_and_3_miles,
    COUNT(*) FILTER (WHERE trip_distance > 3 AND trip_distance <= 7) AS between_3_and_7_miles,
    COUNT(*) FILTER (WHERE trip_distance > 7 AND trip_distance <= 10) AS between_7_and_10_miles,
    COUNT(*) FILTER (WHERE trip_distance > 10) AS over_10_miles
FROM green_taxi_trips
WHERE
    lpep_pickup_datetime >= '2019-10-01'
    AND lpep_pickup_datetime < '2019-11-01';
```

**Answer:  104,802; 198,924; 109,603; 27,678; 35,189**


## Question 4. Longest trip for each day
Which was the pick up day with the longest trip distance? Use the pick up time for your calculations.

Tip: For every day, we only care about one single trip with the longest distance.

2019-10-11
2019-10-24
2019-10-26
2019-10-31

```sql
SELECT DATE(lpep_pickup_datetime), trip_distance as longest_distance FROM green_taxi_trips ORDER BY trip_distance DESC LIMIT 1;
```

**Answer: "2019-10-31" 515.89**


## Question 5. Three biggest pickup zones
Which were the top pickup locations with over 13,000 in total_amount (across all trips) for 2019-10-18?

Consider only lpep_pickup_datetime when filtering by date.

```sql 
SELECT
    t."PULocationID",
    z."Borough",
    z."Zone",
    z.service_zone,
    SUM(t.total_amount) AS total_amount
FROM green_taxi_trips AS t
JOIN taxi_zones AS z
    ON t."PULocationID" = z."LocationID"
WHERE
    DATE(t.lpep_pickup_datetime) = '2019-10-18'
GROUP BY t."PULocationID", z."Borough", z."Zone", z.service_zone
HAVING SUM(t.total_amount) > 13000
ORDER BY total_amount DESC;
```

**Answer: East Harlem North, East Harlem South, Morningside Heights**


## Question 6. Largest tip
For the passengers picked up in October 2019 in the zone named "East Harlem North" which was the drop off zone that had the largest tip?

Note: it's tip , not trip

We need the name of the zone, not the ID.

```sql 
SELECT
    z2."Zone" AS dropoff_zone,
    MAX(t.tip_amount) AS largest_tip
FROM green_taxi_trips AS t
JOIN taxi_zones AS z1
    ON t."PULocationID" = z1."LocationID"
JOIN taxi_zones AS z2
    ON t."DOLocationID" = z2."LocationID"
WHERE
    z1."Zone" = 'East Harlem North'
    AND t.lpep_pickup_datetime >= '2019-10-01'
    AND t.lpep_pickup_datetime < '2019-11-01'
GROUP BY z2."Zone"
ORDER BY largest_tip DESC
LIMIT 1;
```


**Answer: JFK Airport**

## Question 7. Terraform Workflow
Which of the following sequences, respectively, describes the workflow for:

Downloading the provider plugins and setting up backend,
Generating proposed changes and auto-executing the plan
Remove all resources managed by terraform`

**Answer: terraform init, terraform apply -auto-approve, terraform destroy** 


