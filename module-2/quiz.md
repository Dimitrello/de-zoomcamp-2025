## Questions

#### 1. Within the execution for Yellow Taxi data for the year 2020 and month 12: what is the uncompressed file size (i.e. the output file yellow_tripdata_2020-12.csv of the extract task)?

128.3 MB

#### 2. What is the rendered value of the variable file when the inputs taxi is set to green, year is set to 2020, and month is set to 04 during execution?

green_tripdata_2020-04.csv

#### 3. How many rows are there for the Yellow Taxi data for all CSV files in the year 2020?

```sql
SELECT COUNT(*) cnt
FROM public.yellow_tripdata
WHERE filename LIKE 'yellow_tripdata_2020-%.csv';
```

24,648,499

#### 4. How many rows are there for the Green Taxi data for all CSV files in the year 2020?

```sql
SELECT COUNT(*) cnt
FROM public.green_tripdata
WHERE filename LIKE 'green_tripdata_2020-%.csv';
```

1,734,051 

#### 5. How many rows are there for the Yellow Taxi data for the March 2021 CSV file?

```sql
SELECT COUNT(*) cnt
FROM public.yellow_tripdata
WHERE filename = 'yellow_tripdata_2021-03.csv';
```

1,925,152

#### 6. How would you configure the timezone to New York in a Schedule trigger?

Add a timezone property set to America/New_York in the Schedule trigger configuration

```yaml
triggers:
  - id: green_schedule
    type: io.kestra.plugin.core.trigger.Schedule
    cron: "0 10 1 * *"
    timezone: America/New_York 
    inputs:
      taxi: green

  - id: yellow_schedule
    type: io.kestra.plugin.core.trigger.Schedule
    cron: "0 12 1 * *"
    timezone: America/New_York 
    inputs:
      taxi: yellow
```