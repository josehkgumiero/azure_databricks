Within the context of Azure Databricks, sharing data from one worker to another can be a costly operation.

Sharing data from one worker to another can be a costly operation.

Spark has optimized this operation by using a format called Tungsten which prevents the need for expensive serialization and de-serialization of objects in order to get data from one JVM to another.

The data that is "shuffled" is in a format known as UnsafeRow, or more commonly, the Tungsten Binary Format.

When we shuffle data, it creates what is known as a stage boundary which represents a process bottleneck which Spark will break this one job into two stages.

In Stage #1, Spark will create a pipeline of transformations in which the data is read into RAM.

For Stage #2, Spark will again create a pipeline of transformations in which the shuffle data is read into RAM.

From the developer's perspective, we start with a read and conclude (in this case) with a write:

Step Transformation

1 Read

2 Select

3 Filter

4 GroupBy

5 Select

6 Filter

7 Write

However, Spark starts with the action (write(..) in this case).

What is the main benefit of working backward through your action's lineage?

- a) It serializes the work to make the work sequential, there by lowering cpu and ram cost.
- b) I t alows azure to distribute the oad to the required number of processor to optimize the load.
- c) It allows spark to work on various activities simultaneously using multiple nodes.
- d) It allows spark to determine if it is necessary to execute every transformation.

Correct: D