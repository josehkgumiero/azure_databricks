Scenario: You are working at an online retailer and have been tasked with finding average of sales transactions by storefront.

Which of the following aggregates would you use?


- a) df.select(col("storefront")).avg("completedTransactions").groupBy(col("storefront))
- b) df.groupBy(col("storefront")).avg("completedTransactions")
- c) df.select(col("storefront")).avg("completedTransactions")
- d) df.groupBy(col("storefront")).avg(col("completedTransactions"))

Correct: B