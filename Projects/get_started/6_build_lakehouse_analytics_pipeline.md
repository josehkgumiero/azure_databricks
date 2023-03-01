# Run your first ETL workload on Azure Databricks
- Learn how to use production-ready tools from Azure Databricks to develop and deploy your first extract, transform, and load (ETL) pipelines for data orchestration.
- By the end of this article, you will feel comfortable:
    1. Launching a Databricks all-purpose compute cluster.
    2. Creating a Databricks notebook.
    3. Configuring incremental data ingestion to Delta Lake with Auto Loader.
    4. Executing notebook cells to process, query, and preview data.
    5. Scheduling a notebook as a Databricks job.

# Requirenments
- You have permission to create a cluster

# Step 1: Create a cluster
- To do exploratory data analysis and data engineering, create a cluster to provide the compute resources needed to execute commands.
    - Click compute icon Compute in the sidebar.
    - On the Compute page, click Create Cluster. This opens the New Cluster page.
    - Specify a unique name for the cluster, leave the remaining values in their default state, and click Create Cluster.

# Step 2: Create a Databricks notebook
- To get started writing and executing interactive code on Azure Databricks, create a notebook.
    - Click New Icon New in the sidebar, then click Notebook.
    - On the Create Notebook page:
        - Specify a unique name for your notebook.
        - Make sure the default language is set to Python or Scala.
        - Select the cluster you created in step 1 from the Cluster dropdown.
        - Click Create.
- A notebook opens with an empty cell at the top.

# Step 3: Configure Auto Loader to ingest data to Delta Lake
- Databricks recommends using Auto Loader for incremental data ingestion. Auto Loader automatically detects and processes new files as they arrive in cloud object storage.
- Databricks recommends storing data with Delta Lake. Delta Lake is an open source storage layer that provides ACID transactions and enables the data lakehouse. Delta Lake is the default format for tables created in Databricks.
- To configure Auto Loader to ingest data to a Delta Lake table, copy and paste the following code into the empty cell in your notebook:
```
# Import functions
from pyspark.sql.functions import input_file_name, current_timestamp

# Define variables used in code below
file_path = "/databricks-datasets/structured-streaming/events"
username = spark.sql("SELECT regexp_replace(current_user(), '[^a-zA-Z0-9]', '_')").first()[0]
table_name = f"{username}_etl_quickstart"
checkpoint_path = f"/tmp/{username}/_checkpoint/etl_quickstart"

# Clear out data from previous demo execution
spark.sql(f"DROP TABLE IF EXISTS {table_name}")
dbutils.fs.rm(checkpoint_path, True)

# Configure Auto Loader to ingest JSON data to a Delta table
(spark.readStream
  .format("cloudFiles")
  .option("cloudFiles.format", "json")
  .option("cloudFiles.schemaLocation", checkpoint_path)
  .load(file_path)
  .select("*", input_file_name().alias("source_file"), current_timestamp().alias("processing_time"))
  .writeStream
  .option("checkpointLocation", checkpoint_path)
  .trigger(availableNow=True)
  .toTable(table_name))
```

# Step 4: Process and interact with data
- Notebooks execute logic cell-by-cell. To execute the logic in your cell:
    - To run the cell you completed in the previous step, select the cell and press SHIFT+ENTER.
    - To query the table you’ve just created, copy and paste the following code into an empty cell, then press SHIFT+ENTER to run the cell.
    ```
    df = spark.read.table(table_name)
    ```
    ```
    display(df)
    ```

# Step 5: Schedule a job
- You can run Databricks notebooks as production scripts by adding them as a task in a Databricks job. In this step, you will create a new job that you can trigger manually.
- To schedule your notebook as a task:
    - Click Schedule on the right side of the header bar.
    - Enter a unique name for the Job name.
    - Click Manual.
    - In the Cluster drop-down, select the cluster you created in step 1.
    - Click Create.
    - In the window that appears, click Run now.
    - To see the job run results, click the External Link icon next to the Last run timestamp.
