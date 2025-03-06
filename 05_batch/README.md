# Data engineering zoomcamp
## Module 5 Batch w/ Spark

In this homework we'll put what we learned about Spark in practice.

For this homework we will be using the Yellow 2024-10 data from the official website: 

```bash
wget https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2024-10.parquet
```


### Question 1: Install Spark and PySpark

- Install Spark
- Run PySpark
- Create a local spark session
- Execute spark.version.

What's the output?
**Answer: '3.5.4'**

> [!NOTE]
> To install PySpark follow this [guide](https://github.com/DataTalksClub/data-engineering-zoomcamp/blob/main/05-batch/setup/pyspark.md)


### Question 2: Yellow October 2024

Read the October 2024 Yellow into a Spark Dataframe.

Repartition the Dataframe to 4 partitions and save it to parquet.

What is the average size of the Parquet (ending with .parquet extension) Files that were created (in MB)? Select the answer which most closely matches.

1. 6MB
2. 25MB
3. 75MB
4. 100MB

**Answer : 25MB**

### Question 3: Count records 

How many taxi trips were there on the 15th of October?

Consider only trips that started on the 15th of October.

1. 85,567
2. 105,567
3. 125,567
4. 145,567

**Answer : 125,567**

### Question 4: Longest trip

What is the length of the longest trip in the dataset in hours?

1. 122
2. 142
3. 162
4. 182

**Answer : 162**

### Question 5: User Interface

Spark’s User Interface which shows the application's dashboard runs on which local port?

1. 80
2. 443
3. 4040
4. 8080

**Answer : 4040**

### Question 6: Least frequent pickup location zone

Load the zone lookup data into a temp view in Spark:

```bash
wget https://d37ci6vzurychx.cloudfront.net/misc/taxi_zone_lookup.csv
```

Using the zone lookup data and the Yellow October 2024 data, what is the name of the LEAST frequent pickup location Zone?

1. Governor's Island/Ellis Island/Liberty Island
2. Arden Heights
3. Rikers Island
4. Jamaica Bay

**Answer : Governor's Island/Ellis Island/Liberty Island**
