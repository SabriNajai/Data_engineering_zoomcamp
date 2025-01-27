# Data_engineering_zoomcamp
## Module 1 Docker & SQL

In this homework we'll prepare the environment and practice Docker and SQL

When submitting your homework, you will also need to include a link to your GitHub repository or other public code-hosting site.

This repository should contain the code for solving the homework.

When your solution has SQL or shell commands and not code (e.g. python files) file format, include them directly in the README file of your repository.

### Question 1 Understanding docker first run

docker run -it python:3.12.8 bash
pip --version

Answer: 24.3.1

### Question 2 Understanding Docker networking and docker-compose

Answer: db:5432

The `hostname` is the name of the database service in the `docker-compose.yaml` file, which is `db`. The `port` is the port that the database service is exposed on, which is `5432`.

### Question 3 Trip Segmentation Count

SELECT
    COUNT(*) FILTER (WHERE trip_distance <= 1) AS trips_up_to_1_mile,
    COUNT(*) FILTER (WHERE trip_distance > 1 AND trip_distance <= 3) AS trips_between_1_and_3_miles,
    COUNT(*) FILTER (WHERE trip_distance > 3 AND trip_distance <= 7) AS trips_between_3_and_7_miles,
    COUNT(*) FILTER (WHERE trip_distance > 7 AND trip_distance <= 10) AS trips_between_7_and_10_miles,
    COUNT(*) FILTER (WHERE trip_distance > 10) AS trips_over_10_miles
FROM green_taxi_data
WHERE lpep_pickup_datetime >= '2019-10-01' AND lpep_pickup_datetime < '2019-11-01';

Answer: 104,849; 199,040; 109,670; 27,693; 35,202

### Question 4 Longest trip for each day

SELECT  lpep_pickup_datetime::DATE AS date, 
        MAX(trip_distance) AS max_trip_distance
FROM green_taxi_trips
GROUP BY lpep_pickup_datetime::DATE
ORDER BY max_trip_distance DESC
LIMIT 1;

Answer: 2019-10-31

### Question 5 Three biggest pickup zones

SELECT z."Zone", SUM(total_amount) AS total_amount_sum
FROM green_taxi_trips gtt
JOIN zones z ON gtt."PULocationID" = z."LocationID"
WHERE gtt.lpep_pickup_datetime::DATE = '2019-10-18'
GROUP BY z."Zone"
HAVING SUM(total_amount) > 13000.0
ORDER BY total_amount DESC;

Answer: East Harlem North, East Harlem South, Morningside Heights

### Question 6 Largest tip

SELECT zdo."Zone" AS dropoff_zone, MAX(gtt.tip_amount) AS max_tip_amount
FROM green_taxi_trips gtt
JOIN zones zpu ON gtt."PULocationID" = zpu."LocationID"
JOIN zones zdo ON gtt."DOLocationID" = zdo."LocationID"
WHERE zpu."Zone" = 'East Harlem North'
GROUP BY zdo."Zone"
ORDER BY max_tip_amount DESC
LIMIT 1;

Answer: JFK Airport

### Question 7 Terraform Workflow

Answer: terraform init, terraform apply -auto-aprove, terraform destroy
