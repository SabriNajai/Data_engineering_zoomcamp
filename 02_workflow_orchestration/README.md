# Data engineering zoomcamp
## Module 2 Workflow orchestration w/ Kestra

**Assignment**
So far in the course, we processed data for the year 2019 and 2020. Your task is to extend the existing flows to include data for the year 2021

### Question 1 
Within the execution for `Yellow` Taxi data for the year `2020` and month `12`: what is the uncompressed file size (i.e. the output file `yellow_tripdata_2020-12.csv` of the `extract` task)?

1. 128.3 MB
2. 134.5 MB
3. 364.7 MB
4. 692.6 MB

**Answer: 128.3 MB**

### Question 2 
What is the rendered value of the variable `file` when the inputs `taxi` is set to `green`, `year` is set to `2020`, and `month` is set to `04` during execution?

1. `{{inputs.taxi}}_tripdata_{{inputs.year}}-{{inputs.month}}.csv` 
2. `green_tripdata_2020-04.csv`
3.`green_tripdata_04_2020.csv`
4. `green_tripdata_2020.csv`

**Answer: `green_tripdata_2020-04.csv`**

### Question 3 
How many rows are there for the `Yellow` Taxi data for all CSV files in the year 2020?

1. 13,537.299
2. 24,648,499
3. 18,324,219
4. 29,430,127

**Answer: 24,648,499**

```sql
SELECT COUNT(*)
FROM public.yellow_tripdata
WHERE filename LIKE 'yellow_tripdata_2020-__.csv';
```

### Question 4
How many rows are there for the `Green` Taxi data for all CSV files in the year 2020?

- 5,327,301
- 936,199
- 1,734,051
- 1,342,034

**Answer: 1,734,051**

```sql
SELECT COUNT(*)
FROM public.green_tripdata
WHERE filename LIKE 'green_tripdata_2020-__.csv';
```

### Question 5
How many rows are there for the `Yellow` Taxi data for the March 2021 CSV file?

- 1,428,092
- 706,911
- 1,925,152
- 2,561,031

**Answer: 1,925,152**

```sql
SELECT COUNT(*)
FROM public.yellow_tripdata
WHERE filename LIKE 'yellow_tripdata_2021-03.csv';
```

### Question 6
How would you configure the timezone to New York in a Schedule trigger?

- Add a `timezone` property set to `EST` in the `Schedule` trigger configuration  
- Add a `timezone` property set to `America/New_York` in the `Schedule` trigger configuration
- Add a `timezone` property set to `UTC-5` in the `Schedule` trigger configuration
- Add a `location` property set to `New_York` in the `Schedule` trigger configuration  

**Answer: Add a timezone property set to America/New_York in the Schedule trigger configuration**

```yaml
triggers:
  - id: green_schedule
    type: io.kestra.plugin.core.trigger.Schedule
    cron: "0 9 1 * *"
    timezone: America/New_York # New York time zone
    inputs:
      taxi: green

  - id: yellow_schedule
    type: io.kestra.plugin.core.trigger.Schedule
    cron: "0 10 1 * *"
    timezone: America/New_York # New York time zone
    inputs:
      taxi: yellow
``` 
