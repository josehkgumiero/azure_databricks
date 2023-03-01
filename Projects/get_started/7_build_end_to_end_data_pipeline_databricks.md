# Build an end-to-end data pipeline in Databricks
- This article provides an example of creating and deploying an end-to-end data processing pipeline, including ingesting raw data, transforming the data, and running analyses on the processed data.


# What is a data pipeline?
- A data pipeline implements the steps required to move data from source systems, transform that data based on requirements, and store the data in a target system. A data pipeline includes all the processes necessary to turn raw data into prepared data that users can consume. For example, a data pipeline might prepare data so data analysts and data scientists can extract value from the data through analysis and reporting.
- An extract, transform, and load (ETL) workflow is a common example of a data pipeline. In ETL processing, data is ingested from source systems and written to a staging area, transformed based on requirements (ensuring data quality, deduplicating records, and so forth), and then written to a target system such as a data warehouse or data lake.

# Data pipeline steps

- To help you get started building data pipelines on Azure Databricks, the example included in this article walks through creating a data processing workflow:

    - Use Azure Databricks features to explore a raw dataset.
    - Create a Databricks notebook to ingest raw source data and write the raw data to a target table.
    - Create a Databricks notebook to transform the raw source data and write the transformed data to a target table.
    - Create a Databricks notebook to query the transformed data.
    - Automate the data pipeline with a Azure Databricks job.

- Databricks also provides Delta Live Tables to facilitate the implementation of data processing pipelines. Delta Live Tables is a framework that provides a declarative interface for implementing data processing pipelines. This article demonstrates how you can create a complete data pipeline using Databricks notebooks and a Azure Databricks job to orchestrate a workflow, but Databricks recommends using Delta Live Tables to build reliable, maintainable, and testable data processing pipelines.

# Requirements

- You’re logged into Azure Databricks and in the Data Science & Engineering workspace.
- You have permission to create a cluster

# Example: Million Song dataset

The dataset used in this example is a subset of the Million Song Dataset, a collection of features and metadata for contemporary music tracks. This dataset is available in the sample datasets included in your Azure Databricks workspace.

# Step 1: Create a cluster

- To perform the data processing and analysis in this example, create a cluster to provide the compute resources needed to run commands.

    - Click compute icon Compute in the sidebar.
    - On the Compute page, click Create Cluster. The New Cluster page appears.
    - Specify a unique name for the cluster, leave the remaining values in their default state, and click Create Cluster.

- To learn more about Databricks clusters, see Clusters.


# Step 2: Explore the source data
1. In the sidebar, click New Icon New and select Notebook from the menu. The Create Notebook dialog appears.

2. Enter a name for the notebook, for example, Explore songs data. In Default Language, select Python. In Cluster, select the cluster you created or an existing cluster.

3. Click Create.

4. To view the contents of the directory containing the dataset, enter the following in the first cell of the notebook, click Run Menu, and select Run Cell.
```
display(dbutils.fs.ls("/databricks-datasets/songs/"))
```
5. The README file has information about the dataset, including a description of the data schema. The schema information is used in the next step when ingesting the data. To view the contents of the README, click Down Caret in the cell actions menu, select Add Cell Below, enter the following in the new cell, click Run Menu, and select Run Cel
```
with open("/dbfs/databricks-datasets/songs/README.md") as f:
  x = ''.join(f.readlines())

print(x)
```
6. The records used in this example are in the /databricks-datasets/songs/data-001/ directory. To view the contents of this directory, click Down Caret in the cell actions menu, select Add Cell Below, enter the following in the new cell, click Run Menu, and select Run Cell.
```
display(dbutils.fs.ls("/databricks-datasets/songs/data-001"))
```
7. To view a sample of the records, click Down Caret in the cell actions menu, select Add Cell Below, enter the following in the new cell, click Run Menu, and select Run Cell.
```
with open("/dbfs/databricks-datasets/songs/data-001/part-00000") as f:
  x = ''.join(f.readlines())

print(x)
```

# Step 3: Ingest raw data to Delta Lake

- Databricks recommends using Auto Loader for data ingestion. Auto Loader automatically detects and processes new files as they arrive in cloud object storage.
- Databricks recommends storing data with Delta Lake. Delta Lake is an open source storage layer that provides ACID transactions and enables the data lakehouse. Delta Lake is the default format for tables created in Databricks.
- You can configure Auto Loader to automatically detect the schema of loaded data, allowing you to initialize tables without explicitly declaring the data schema and evolve the table schema as new columns are introduced. This eliminates the need to manually track and apply schema changes over time. Databricks recommends schema inference when using Auto Loader. However, as seen in the data exploration step, the songs data does not contain header information. Because the header is not stored with the data, you’ll need to explicitly define the schema, as shown in the next example.
    1. In the sidebar, click New Icon New and select Notebook from the menu. The Create Notebook dialog appears.
    2. Enter a name for the notebook, for example, Ingest songs data. In Default Language, select Python. In Cluster, select the cluster you created or an existing cluster.
    3. Click Create.
    4. Enter the following in the first cell of the notebook:
    ```
    from pyspark.sql.types import DoubleType, IntegerType, StringType, StructType, StructField

    # Define variables used in code below
    file_path = "/databricks-datasets/songs/data-001/"
    table_name = "<table-name>"
    checkpoint_path = "<checkpoint-path>"

    # For purposes of this example, clear out data from previous runs. Because Auto Loader
    # is intended for incremental loading, in production applications you normally won't drop
    # target tables and checkpoints between runs.
    spark.sql(f"DROP TABLE IF EXISTS {table_name}")
    dbutils.fs.rm(checkpoint_path, True)

    schema = StructType(
      [
        StructField("artist_id", StringType(), True),
        StructField("artist_lat", DoubleType(), True),
        StructField("artist_long", DoubleType(), True),
        StructField("artist_location", StringType(), True),
        StructField("artist_name", StringType(), True),
        StructField("duration", DoubleType(), True),
        StructField("end_of_fade_in", DoubleType(), True),
        StructField("key", IntegerType(), True),
        StructField("key_confidence", DoubleType(), True),
        StructField("loudness", DoubleType(), True),
        StructField("release", StringType(), True),
        StructField("song_hotnes", DoubleType(), True),
        StructField("song_id", StringType(), True),
        StructField("start_of_fade_out", DoubleType(), True),
        StructField("tempo", DoubleType(), True),
        StructField("time_signature", DoubleType(), True),
        StructField("time_signature_confidence", DoubleType(), True),
        StructField("title", StringType(), True),
        StructField("year", IntegerType(), True),
        StructField("partial_sequence", IntegerType(), True)
      ]
    )

    (spark.readStream
      .format("cloudFiles")
      .schema(schema)
      .option("cloudFiles.format", "csv")
      .option("sep","\t")
      .load(file_path)
      .writeStream
      .option("checkpointLocation", checkpoint_path)
      .trigger(availableNow=True)
      .toTable(table_name))
  ```
    - Replace:
        - <table-name> with the name of the Delta table to contain the ingested records, for example, pipeline_get_started_raw_song_data.
        - <checkpoint-path> with a path to a directory in DBFS to maintain checkpoint files, for example, /tmp/pipeline_get_started/_checkpoint/song_data.

    5. Click Run Menu, and select Run Cell. This example defines the data schema using the information from the README, ingests the songs data from all of the files contained in file_path, and writes the data to the Delta table specified by table_name.

# Step 4: Prepare raw data and write to Delta Lake

- In this step, you transform the raw songs data by filtering out unneeded columns and adding a new field containing a timestamp for the creation of the new record.

    1. In the sidebar, click New Icon New and select Notebook from the menu. The Create Notebook dialog appears.

    2. Enter a name for the notebook, for example, Prepare songs data. In Default Language, select SQL. In Cluster, select the cluster you created or an existing cluster.

    3. Click Create.

    4. Enter the following in the first cell of the notebook:
    ```
    CREATE OR REPLACE TABLE
  <table-name> (
    artist_id STRING,
    artist_name STRING,
    duration DOUBLE,
    release STRING,
    tempo DOUBLE,
    time_signature DOUBLE,
    title STRING,
    year DOUBLE,
    processed_time TIMESTAMP
  );

    INSERT INTO
      <table_name>
    SELECT
      artist_id,
      artist_name,
      duration,
      release,
      tempo,
      time_signature,
      title,
      year,
      current_timestamp()
    FROM
      <raw-songs-table-name>
    ```
    - Replace
        - <table-name> with the name of the Delta table to contain the filtered and transformed records, for example, pipeline_get_started_prepared_song_data.
        - <raw-songs-table-name> with the name of the Delta table containing the raw songs records ingested in the previous step, for example, pipeline_get_started_raw_song_data.
    5. Click Run Menu, and select Run Cell.

# Step 5: Query the transformed data

- In this step, you extend the processing pipeline by adding queries to analyze the songs data. These queries use the prepared records created in the previous step.

    1. In the sidebar, click New Icon New and select Notebook from the menu. The Create Notebook dialog appears.

    2. Enter a name for the notebook, for example, Analyze songs data. In Default Language, select SQL. In Cluster, select the cluster you created or an existing cluster.

    3. Click Create.

    4. Enter the following in the first cell of the notebook:
    ```
    CREATE OR REPLACE VIEW
      artists_by_year
    AS SELECT
      artist_name,
      year
    FROM
      <prepared-songs-table-name>
    -- Remove records where the year field isn't populated
    WHERE
      year > 0;

    -- Which artists released the most songs in each year?
    SELECT
      artist_name,
      count(artist_name)
    AS
      num_songs,
      year
    FROM
      artists_by_year
    GROUP BY
      artist_name,
      year
    ORDER BY
      num_songs DESC,
      year DESC
    ```
    - Replace
        - <prepared-songs-table-name> with the name of the prepared table created in the previous step, for example pipeline_get_started_prepared_song_data.
    5. Click Down Caret in the cell actions menu, select Add Cell Below and enter the following in the new cell:
    ```
    -- Find songs for your DJ list
    CREATE OR REPLACE VIEW
       danceable_songs
    AS SELECT
       artist_name,
       title,
       tempo
    FROM
       <prepared-songs-table-name>
    WHERE
       time_signature = 4
       AND
       tempo between 100 and 140;

    SELECT * FROM danceable_songs limit 100
    ```
    - Replace

        - <prepared-songs-table-name> with the name of the prepared table created in the previous step, for example pipeline_get_started_prepared_song_data.

    6. To run the queries and view the output, click Run all.


# Step 6: Create a Azure Databricks job to run the pipeline

- You can create a workflow to automate running the data ingestion, processing, and analysis steps using an Azure Databricks job.

    - In your Data Science & Engineering workspace, do one of the following:
        - Click Jobs Icon Workflows in the sidebar and click Create Job Button.
        - In the sidebar, click New Icon New and select Job.
    - In the task dialog box that appears on the Tasks tab, replace Add a name for your job… with your job name, for example, “Songs workflow”.
    - In Task name, enter a name for the first task, for example, Ingest_songs_data.
    - In Type, select the Notebook task type.
    - In Source, select Workspace.
    - Use the file browser to find the data ingestion notebook, click the notebook name, and click Confirm.
    - In Cluster, select Shared_job_cluster or the cluster you created in the Create a cluster step.
    - Click Save task.
    - Click Add Task Button below the task you just created.
    - In Task name, enter a name for the task, for example, Prepare_songs_data.
    - In Type, select the Notebook task type.
    - In Source, select Workspace.
    - Use the file browser to find the data preparation notebook, click the notebook name, and click Confirm.
    - In Cluster, select Shared_job_cluster or the cluster you created in the Create a cluster step.
    - Click Save task.
    - Click Add Task Button below the task you just created.
    - In Task name, enter a name for the task, for example, Analyze_songs_data.
    - In Type, select the Notebook task type.
    - In Source, select Workspace.
    - Use the file browser to find the data analysis notebook, click the notebook name, and click Confirm.
    - In Cluster, select Shared_job_cluster or the cluster you created in the Create a cluster step.
    - Click Save task.
    - To run the workflow, Click Run Now Button. To view details for the run, click the link in the Start time column for the run in the job runs view. Click each task to view details for the task run.
    - To view the results when the workflow completes, click the final data analysis task. The Output page appears and displays the query results.


# Step 7: Schedule the data pipeline job
- A common requirement is to run a data pipeline on a scheduled basis. To define a schedule for the job that runs the pipeline:
    - Click Jobs Icon Workflows in the sidebar.
    - In the Name column, click the job name. The side panel displays the Job details.
    - Click Edit schedule in the Job details panel and set the Schedule Type to Scheduled.
    - Specify the period, starting time, and time zone. Optionally select the Show Cron Syntax checkbox to display and edit the schedule in Quartz Cron Syntax.
    - Click Save.

