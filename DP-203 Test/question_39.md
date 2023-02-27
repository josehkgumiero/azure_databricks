As great as data lakes are at inexpensively storing our raw data, they also bring with them performance challenges:

• Too many small or very big files - more time opening & closing files rather than reading contents (worse with streaming).

• Partitioning also known as "poor man's indexing"- breaks down if you picked the wrong fields or when data has many dimensions, high cardinality columns.

• No caching - cloud storage throughput is low (cloud object storage is 20-50MB/s/core vs 300MB/s/core for local SSDs).

As a solution to the challenges with Data Lakes noted above, Delta Lake is a file format that can help you build a data lake comprised of one or many tables in Delta Lake format. Delta Lake integrates tightly with Apache Spark, and uses an open format that is based on Parquet.

Two of the core features of Delta Lake are performing UPSERTS and Time Travel operations.

What does the Time Travel operation do? (Select all that apply)

- a) Providing snapshot isolation for a set of queries for fast changing tables.
- b) Writing complex temporal queries;.
- c) Because delta lake is eversion controlled, you have the option to query past versions of data using a single file storage system.
- d) Re-creating analysis, reports, or outputs (for exaple, the output of a machine learning model). This coukd be useful for debugging or auditing, especialy in regulated industries.

Correct: A, B, C, D.