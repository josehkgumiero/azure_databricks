# Tutorial: Query data with notebooks
- This tutorial walks you through using the Databricks Data Science & Engineering workspace to create a cluster and a notebook, create a table from a dataset, query the table, and display the query results.
- Step 1: create a cluster
    - A cluster is a collection of Azure Databricks computation resources. To create a cluster:
        - In the sidebar, click compute icon Compute.
        - On the Compute page, click Create Compute.
        - On the New Compute page, select 11.3 LTS ML (Scala 2.12, Spark 3.3.0) from the Databricks Runtime version dropdown.
        - Click Create Cluster.
- Step 2: Create a notebook
    - A notebook is a collection of cells that run computations on an Apache Spark cluster. To create a notebook in the workspace:

        - In the sidebar, click Workspace Icon Workspace.

        - In the Workspace folder, select Down Caret Create > Notebook.

        - Create notebook

        - On the Create Notebook dialog, enter a name and select SQL in the Language drop-down. This selection determines the default language of the notebook.

        - Click Create. The notebook opens with an empty cell at the top.

        - Attach the notebook to the cluster you created. Click the cluster selector in the notebook toolbar and select your cluster from the dropdown menu. If you don’t see your cluster, click More… and select the cluster from the dropdown menu in the dialog.
- Step 3: Create a table
    - Create a table using data from a sample CSV data file available in Sample datasets, a collection of datasets mounted to What is the Databricks File System (DBFS)?, a distributed file system installed on Azure Databricks clusters. You have two options for creating the table.
- Option 1: Create a Spark table from the CSV data
    - Use this option if you want to get going quickly, and you only need standard levels of performance. Copy and paste this code snippet into a notebook cell:
    - 
    ```
    DROP TABLE IF EXISTS diamonds;
    CREATE TABLE diamonds USING CSV OPTIONS (path "/databricks-datasets/Rdatasets/data-001/csv/ggplot2/diamonds.csv", header "true")
    ```
- Option 2: Write the CSV data to Delta Lake format and create a Delta table
    - Delta Lake offers a powerful transactional storage layer that enables fast reads and other benefits. Delta Lake format consists of Parquet files plus a transaction log. Use this option to get the best performance on future operations on the table.
        1. Read the CSV data into a DataFrame and write out in Delta Lake format. This command uses a Python language magic command, which allows you to interleave commands in languages other than the notebook default language (SQL). Copy and paste this code snippet into a notebook cell:
        ```
        %python

        diamonds = (
            spark.read.format("csv")
            .option("header","true")
            .option("inferSchema", "true")
            .load("/databricks-datasets/Rdatasets/data-001/csv/ggplot2/diamonds.csv")
        )

        diamonds.wite.format("delta").save("/mnt/delta/diamonds")
        ```
        2. Create a Delta table at the stored location. Copy and paste this code snippet into a notebook cell:
        - 
        ```
        DROP TABLE IF EXISTS diamonds;
        CREATE TABLE diamonds USING DELTA LOCATION 'mnt/delta/diamonds/'
        ```
- Step 4: Query the table
    - Run a SQL statement to query the table for the average diamond price by color.
    - 
    ```
    SELECT color, avg(price) AS price FROM diamonds BROUP BY color ORDER BY color
    ```
- Step 5: Display the data
    - Display a chart of the average diamond price by color.
        - Click the Bar chart icon Chart Button.
        - Click Plot Options.
        - Drag color into the Keys box.
        - Drag price into the Values box.
        - In the Aggregation drop-down, select AVG.
        - Click Apply to display the bar chart.
