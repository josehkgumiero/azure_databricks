Within the context of Azure Databricks, sharing data from one worker to another can be a costly operation.

Spark has optimized this operation by using a format called [?] which prevents the need for expensive serialization and de-serialization of objects in order to get data from one JVM to another.

- a) Stages
- b) Pipelining
- c) Lineage
- d) Shuffles
- e) Tungsten
- f) Stage Boundary

Correct: E