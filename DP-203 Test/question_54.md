Within the context of Azure Databricks, sharing data from one worker to another can be a costly operation.

Spark has optimized this operation by using a format called [?] which prevents the need for expensive serialization and de-serialization of objects in order to get data from one JVM to another.

- a) shuffles
- b) stge boundary
- c) pipelining
- d) .tugsten
- e) stages
- f) lineage

Correct: D