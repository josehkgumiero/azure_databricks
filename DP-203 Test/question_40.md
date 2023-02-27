Which is the correct syntax for overwriting data in Azure Synapse Analytics from a Databricks notebook?

- a) df.write.format("com.databricks.spark.sqldw").ovewrite().option("...").option("...").save()
- b) df.write.format("com.databricks.spark.sqldw").update().option("...").option("...").save()
- c) df.write.format("com.databricks.spark.sqldw").mode("overwrite").option("...").option("...").save()
- d) df.write.mode("overwrite").option("...").option("...").save()

Correct: C