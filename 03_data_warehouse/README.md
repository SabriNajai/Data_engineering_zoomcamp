# Data engineering zoomcamp
## Module 3 Data warehouse w/ BigQuery

**Important Note:** For this homework we will be using the Yellow Taxi Trip Records for **January 2024 - June 2024 NOT the entire year of data**
Parquet Files from the New York
City Taxi Data found here: https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page
If you are using orchestration such as Kestra, Mage, Airflow or Prefect etc. do not load the data into Big Query using the orchestrator.
Stop with loading the files into a bucket.

**Load Script:** You can manually download the parquet files and upload them to your GCS Bucket or you can use the linked script [here](./load_yellow_taxi_data.py):
You will simply need to generate a Service Account with GCS Admin Priveleges or be authenticated with the Google SDK and update the bucket name in the script to the name of your bucket<br>
Nothing is fool proof so make sure that all 6 files show in your GCS Bucket before begining.

**NOTE:** You will need to use the PARQUET option files when creating an External Table

**BIG QUERY SETUP:**
Create an external table using the Yellow Taxi Trip Records.
Create a (regular/materialized) table in BQ using the Yellow Taxi Trip Records (do not partition or cluster this table).

### Question 1 
What is count of records for the 2024 Yellow Taxi Data?

1. 65,623
2. 840,402
3. 20,332,093
4. 85,431,289

**Answer: 20,332,093**

```sql
SELECT COUNT(*) AS total_records
FROM `de_zoomcamp_449512.ny_taxi.native_yellow_tripdata_2024`;
```

### Question 2 
Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables.</br>
What is the **estimated amount** of data that will be read when this query is executed on the External Table and the Table?

1. 18.82 MB for the External Table and 47.60 MB for the Materialized Table
2. 0 MB for the External Table and 155.12 MB for the Materialized Table
3. 2.14 GB for the External Table and 0MB for the Materialized Table
4. 0 MB for the External Table and 0MB for the Materialized Table

**Answer : 0 MB for the External Table and 155.12 MB for the Materialized Table**

```sql
SELECT COUNT(DISTINCT PULocationID)
FROM `de_zoomcamp_449512.ny_taxi.external_yellow_tripdata_2024`;

SELECT COUNT(DISTINCT PULocationID)
FROM `de_zoomcamp_449512.ny_taxi.native_yellow_tripdata_2024`;
```

### Question 3

Write a query to retrieve the PULocationID from the table (not the external table) in BigQuery. Now write a query to retrieve the PULocationID and DOLocationID on the same table. Why are the estimated number of Bytes different?

1. BigQuery is a columnar database, and it only scans the specific columns requested in the query. Querying two columns (PULocationID, DOLocationID) requires
  reading more data than querying one column (PULocationID), leading to a higher estimated number of bytes processed.
2. BigQuery duplicates data across multiple storage partitions, so selecting two columns instead of one requires scanning the table twice,
  doubling the estimated bytes processed.
3. BigQuery automatically caches the first queried column, so adding a second column increases processing time but does not affect the estimated bytes scanned.
4. When selecting multiple columns, BigQuery performs an implicit join operation between them, increasing the estimated bytes processed

**Answer : 1.**

```sql
SELECT PULocationID
FROM `de_zoomcamp_449512.ny_taxi.native_yellow_tripdata_2024`;

SELECT PULocationID, DOLocationID
FROM `de_zoomcamp_449512.ny_taxi.native_yellow_tripdata_2024`;
```

### Question 4

How many records have a fare_amount of 0?

1. 128,210
2. 546,578
3. 20,188,016
4. 8,333

**Answer : 8,333**

```sql
SELECT COUNT(1)
FROM `de_zoomcamp_449512.ny_taxi.regular_yellow_taxi`
WHERE fare_amount = 0;
```

### Question 5 

What is the best strategy to make an optimized table in Big Query if your query will always filter based on tpep_dropoff_datetime and order the results by VendorID (Create a new table with this strategy)
1. Partition by tpep_dropoff_datetime and Cluster on VendorID
2. Cluster on by tpep_dropoff_datetime and Cluster on VendorID
3. Cluster on tpep_dropoff_datetime Partition by VendorID
4. Partition by tpep_dropoff_datetime and Partition by VendorID

**Answer : Partition by tpep_dropoff_datetime and Cluster on VendorID**

```sql
CREATE OR REPLACE TABLE `de_zoomcamp_449512.ny_taxi.yellow_tripdata_2024_partitioned`
PARTITION BY DATE(tpep_dropoff_datetime)
CLUSTER BY VendorID AS
SELECT * FROM `de_zoomcamp_449512.ny_taxi.external_yellow_tripdata_2024`;
```

### Question 6

Write a query to retrieve the distinct VendorIDs between tpep_dropoff_datetime 03/01/2024 and 03/15/2024 (inclusive)

Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table you created for question 4 and note the estimated bytes processed. What are these values? </br>

Choose the answer which most closely matches.

1. 12.47 MB for non-partitioned table and 326.42 MB for the partitioned table
2. 310.24 MB for non-partitioned table and 26.84 MB for the partitioned table
3. 5.87 MB for non-partitioned table and 0 MB for the partitioned table
4. 310.31 MB for non-partitioned table and 285.64 MB for the partitioned table

**Answer : 310.24 MB for non-partitioned table and 26.84 MB for the partitioned table**

```sql
SELECT DISTINCT VendorID
FROM `de_zoomcamp_449512.ny_taxi.native_yellow_tripdata_2024`
WHERE tpep_dropoff_datetime >= '2024-03-01'
AND tpep_dropoff_datetime <=  '2024-03-15';

SELECT DISTINCT VendorID
FROM `de_zoomcamp_449512.ny_taxi.yellow_tripdata_2024_partitioned`
WHERE tpep_dropoff_datetime >= '2024-03-01'
AND tpep_dropoff_datetime <=  '2024-03-15';
```

### Question 7

Where is the data stored in the External Table you created?

1. Big Query
2. Container Registry
3. GCP Bucket
4. Big Table

**Answer : GCP Bucket**

### Question 8

It is best practice in Big Query to always cluster your data:

1. True
2. False

**Answer : False**

### (Bonus) Question 9:

Write a `SELECT count(*)` query FROM the materialized table you created. How many bytes does it estimate will be read? Why?

**Answer : BigQuery reads 0 MB for materialized tables because it retrieves the total row count from metadata instead of scanning the entire table.**

```sql
SELECT COUNT(*)
FROM `de_zoomcamp_449512.ny_taxi.native_yellow_tripdata_2024`;
```
