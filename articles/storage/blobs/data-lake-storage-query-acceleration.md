---
title: Azure Data Lake Storage query acceleration (preview)
description: Query acceleration (preview) is a new capability for Azure Data Lake Storage that enables applications and analytics frameworks to dramatically optimize data processing by retrieving only the data that is required for a processing operation.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 11/14/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
---

# Azure Data Lake Storage query acceleration (preview)

Query acceleration (Preview) is a new capability for Azure Data Lake Storage that enables applications and analytics frameworks to dramatically optimize data processing by retrieving only the data that they require to perform a given operation. This reduces the time and processing power that is required to gain critical insights into stored data.

> [!NOTE]
> The query acceleration feature is in public preview, and is available in the region1, region2, and region3 regions. To review limitations, see the [Known issues](data-lake-storage-known-issues.md) article. To enroll in the preview, see [this form](https://aka.ms/adls/qa-preview-signup). 

## Overview

Query acceleration accepts filtering *predicates* and *projections* which enable applications to filter rows and columns at the time that data is read from disk. Only the data that meets the conditions of a predicate are transferred over the network to the application. This reduces network latency and compute cost.  

You can use SQL to specify the row filter predicates and column projections in a query acceleration request. A request processes only one file. Therefore, advanced relational features of SQL, such as joins and group by aggregates, aren't supported. Query acceleration supports CSV and Json formatted data as input to each request.

The following diagram illustrates how a typical application uses query acceleration to process data.

![Query acceleration overview](./media/data-lake-storage-query-acceleration/query-acceleration.png)

The query acceleration feature isn't limited to Data Lake Storage (storage accounts that have the hierarchical namespace enabled on them). Query acceleration is completely compatible with the blobs in storage accounts that **don't** have a hierarchical namespace enabled on them. This means that you can achieve the same reduction in network latency and compute costs when you process data that you already have stored as blobs in storage accounts.

For an example of how to use query acceleration in a .NET application, see [Filter data by using Azure Data Lake Storage query acceleration](data-lake-storage-query-acceleration-how-to.md).

## Better performance at a lower cost

Query acceleration optimizes performance by reducing the amount of data that gets transferred and processed by your application.

To calculate an aggregated value, applications commonly retrieve **all** of the data from a file, and then process and filter the data locally. An analysis of the input/output patterns for analytics workloads reveal that applications typically require only 20% of the data that they read to perform any given calculation. This statistic is true even after applying techniques such as [partition pruning](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). This means that 80% of that data is needlessly transferred across the network, parsed, and filtered by applications. This pattern, essentially designed to remove unneeded data, incurs a significant compute cost.  

Even though Azure features an industry-leading network, in terms of both throughput and latency, needlessly transferring data across that network is still costly for application performance. By filtering out the unwanted data during the storage request, query acceleration eliminates this cost.

Additionally, the CPU load that is required to parse and filter unneeded data requires your application to provision a greater number and larger VMs in order to do it's work. By transferring this compute load to query acceleration, applications can realize significant cost savings.

## Pricing

Due to the increased compute load within the Azure Data Lake Storage service, the pricing model for using query acceleration differs from the normal Azure Data Lake Storage transaction model. Query acceleration charges a cost for the amount of data scanned as well as a cost for the amount of data returned to the caller.

Despite the change to the billing model, Query acceleration's pricing model is designed to lower the total cost of ownership for a workload, given the reduction in the much more expensive VM costs.

## Next steps

- [Query acceleration enrollment form](https://aka.ms/adls/qa-preview-signup)    
- [Filter data by using Azure Data Lake Storage query acceleration](data-lake-storage-query-acceleration-how-to.md)
- Query acceleration SQL language reference
- Query acceleration REST API reference



