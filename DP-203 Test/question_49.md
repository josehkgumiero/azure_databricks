As great as data lakes are at inexpensively storing our raw data, they also bring with them performance challenges:

• Too many small or very big files - more time opening & closing files rather than reading contents (worse with streaming).

• Partitioning also known as "poor man's indexing"- breaks down if you picked the wrong fields or when data has many dimensions, high cardinality columns.

• No caching - cloud storage throughput is low (cloud object storage is 20-50MB/s/core vs 300MB/s/core for local SSDs).

As a solution to the challenges with Data Lakes noted above, Delta Lake is a file format that can help you build a data lake comprised of one or many tables in Delta Lake format. Delta Lake integrates tightly with Apache Spark, and uses an open format that is based on Parquet.

Two of the core features of Delta Lake are performing UPSERTs and Time Travel operations.

What does the UPSERT command do?

- a) The command will insert q row and if the row already exsists, append a new row in the table with an update notation.
- b) The command will insert a column and if the column already exists, add ad ne column in the table with an update notation.
- c) The command will inet a column and if the column already exists, update the column.
- d) the command will insert a row and if the row already eists, update the row.
- e) THe command will insert a table already exsists, update the table.

Correct: D