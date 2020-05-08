---
title: Query Azure Cosmos DB analytical store with Synapse Spark
description: How to query Cosmos DB analytical with Synapse Spark
services: synapse-analytics 
author: ArnoMicrosoft
ms.service: synapse-analytics 
ms.topic: quickstart
ms.subservice: 
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
---

# Query Azure Cosmos DB analytical store with Synapse Spark

This article gives some examples on how you can interact with the analytical store from Synapse gestures. Gestures are visible when you right-click on a container. With gestures, you can quickly generate code and tailor it to your needs. Gestures are also perfect for discovering data with a single click.

## Load to DataFrame

In this step, you'll read data from Azure Cosmos DB analytical store in a Spark DataFrame. It will display 10 rows from the DataFrame called ***df***. Once your data is into dataframe, you can perform additional analysis. 

This operation doesn't impact the transactional store.

```python
# To select a preferred list of regions in a multi-region Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

​df.show(10)
```

The equivalent code gesture in **Scala** would be the following code:
```java
// To select a preferred list of regions in a multi-region Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## Create Spark table

In this gesture, you'll create a Spark table pointing to the container you selected. That operation doesn't incur any data movement. If you decide to delete that table, the underlying container (and corresponding analytical store) won't be impacted. 

This scenario is convenient to reuse tables through third-party tools and provide accessibility to the data for the run-time.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## Write DataFrame to container
In this gesture, you'll write a dataframe into a container. This operation will impact the transactional performance and consume Request Units. Using Azure Cosmos DB transactional performance is ideal for write transactions. 

Make sure that you replace **YOURDATAFRAME** by the dataframe that you want to write back.

```python
# Write a Spark DataFrame into a Cosmos DB container
# To select a preferred list of regions in a multi-region Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

The equivalent code gesture in **Scala** would be the following code:
```java
// To select a preferred list of regions in a multi-region Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## Load streaming DataFrame from container
In this gesture, you'll use Spark Streaming capability to load data from a container into a dataframe. The data will be stored into the primary data lake account (and file system) that you connected to the workspace. 

If the folder */localReadCheckpointFolder* isn't created, it will be automatically created. This operation will impact the transactional performance of Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

The equivalent code gesture in **Scala** would be the following code:
```java
// To select a preferred list of regions in a multi-region Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## Write streaming DataFrame to container
In this gesture, you'll write a streaming dataframe into the Cosmos DB container you selected. If the folder */localReadCheckpointFolder* isn't created, it will be automatically created. This operation will impact the transactional performance of Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

The equivalent code gesture in **Scala** would be the following code:
```java
// To select a preferred list of regions in a multi-region Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## Next steps

* [Learn what is supported between Synapse and Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Connect to Synapse Link for Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)