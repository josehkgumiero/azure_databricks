Scenario: You are working in an Azure Databricks workspace and you want to filter based on the end of a column value using the Column Class. Specifically, you are looking at a column named verb and filtered by words ending with "ing".

Which command filters based on the end of a column value as required?


- a) df.filter("verb like '_ing'")
- b) df.filter().col("verb").like("%ing")
- c) df.filter("verb like '%ing'")
- d) df.filter(col("verb").endswith("ing"))

Correct: D