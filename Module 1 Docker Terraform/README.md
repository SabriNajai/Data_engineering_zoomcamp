# Data engineering zoomcamp
## Module 1 Docker & SQL

In this homework we'll prepare the environment and practice Docker and SQL

When submitting your homework, you will also need to include a link to your GitHub repository or other public code-hosting site.

This repository should contain the code for solving the homework.

When your solution has SQL or shell commands and not code (e.g. python files) file format, include them directly in the README file of your repository.

### Question 1 Understanding docker first run
Run docker with the python:3.12.8 image in an interactive mode, use the entrypoint bash.
What's the version of pip in the image?

docker run -it python:3.12.8 bash

pip --version

**Answer: 24.3.1**

### Question 2 Understanding Docker networking and docker-compose

**Answer: db:5432**

The `hostname` is the name of the database service in the `docker-compose.yaml` file, which is `db`. The `port` is the port that the database service is exposed on, which is `5432`.

### Prepare Postgres
Run Postgres and load data as shown in the videos We'll use the green taxi trips from October 2019:

wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-10.csv.gz
You will also need the dataset with zones:

wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv
Download this data and put it into Postgres.

You can use the code from the course. It's up to you whether you want to use Jupyter or a python script.

### Question 3 Trip Segmentation Count
During the period of October 1st 2019 (inclusive) and November 1st 2019 (exclusive), how many trips, respectively, happened:

1. Up to 1 mile
2. In between 1 (exclusive) and 3 miles (inclusive),
3. In between 3 (exclusive) and 7 miles (inclusive),
4. In between 7 (exclusive) and 10 miles (inclusive),
5. Over 10 miles

```sql
SELECT
    COUNT(*) FILTER (WHERE trip_distance <= 1) AS trips_up_to_1_mile,
    COUNT(*) FILTER (WHERE trip_distance > 1 AND trip_distance <= 3) AS trips_between_1_and_3_miles,
    COUNT(*) FILTER (WHERE trip_distance > 3 AND trip_distance <= 7) AS trips_between_3_and_7_miles,
    COUNT(*) FILTER (WHERE trip_distance > 7 AND trip_distance <= 10) AS trips_between_7_and_10_miles,
    COUNT(*) FILTER (WHERE trip_distance > 10) AS trips_over_10_miles
FROM green_taxi_data
WHERE lpep_pickup_datetime >= '2019-10-01' AND lpep_pickup_datetime < '2019-11-01';
```

**Answer: 104,849; 199,040; 109,670; 27,693; 35,202**

### Question 4 Longest trip for each day
Which was the pick up day with the longest trip distance? Use the pick up time for your calculations.
Tip: For every day, we only care about one single trip with the longest distance.

```sql
SELECT  lpep_pickup_datetime::DATE AS date, 
        MAX(trip_distance) AS max_trip_distance
FROM green_taxi_trips
GROUP BY lpep_pickup_datetime::DATE
ORDER BY max_trip_distance DESC
LIMIT 1;
```

**Answer: 2019-10-31**

### Question 5 Three biggest pickup zones
Which were the top pickup locations with over 13,000 in total_amount (across all trips) for 2019-10-18?
Consider only lpep_pickup_datetime when filtering by date.

```sql
SELECT z."Zone", SUM(total_amount) AS total_amount_sum
FROM green_taxi_trips gtt
JOIN zones z ON gtt."PULocationID" = z."LocationID"
WHERE gtt.lpep_pickup_datetime::DATE = '2019-10-18'
GROUP BY z."Zone"
HAVING SUM(total_amount) > 13000.0
ORDER BY total_amount DESC;
```

**Answer: East Harlem North, East Harlem South, Morningside Heights**

### Question 6 Largest tip
For the passengers picked up in October 2019 in the zone named "East Harlem North" which was the drop off zone that had the largest tip?
Note: it's tip , not trip
We need the name of the zone, not the ID.


```sql
SELECT zdo."Zone" AS dropoff_zone, MAX(gtt.tip_amount) AS max_tip_amount
FROM green_taxi_trips gtt
JOIN zones zpu ON gtt."PULocationID" = zpu."LocationID"
JOIN zones zdo ON gtt."DOLocationID" = zdo."LocationID"
WHERE zpu."Zone" = 'East Harlem North'
GROUP BY zdo."Zone"
ORDER BY max_tip_amount DESC
LIMIT 1;
```

Answer: JFK Airport

## Terraform
In this section homework we'll prepare the environment by creating resources in GCP with Terraform.
In your VM on GCP/Laptop/GitHub Codespace install Terraform. Copy the files from the course repo here to your VM/Laptop/GitHub Codespace.
Modify the files as necessary to create a GCP Bucket and Big Query Dataset.

### Question 7 Terraform Workflow
Which of the following sequences, respectively, describes the workflow for:

Downloading the provider plugins and setting up backend,
Generating proposed changes and auto-executing the plan
Remove all resources managed by terraform`

**Answer: terraform init, terraform apply -auto-aprove, terraform destroy**
