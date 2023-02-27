Which correct syntax to specify the location of a checkpoint directory when defining a Delta Lake streaming query?

- a) .writeStream.format("delta").option("checkpointLocation", checkpointPath)
- b) .writeStream.format("delta.paquet").option("checkpointLocation", checkpointPath)
- c) .writeStream.format("parquet").option("checkpointLocation", checkpointPath)
- d) .writeStream.format("delta").checkpoint("location", checpointPath)

Correct: A