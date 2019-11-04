---
title: Best practices for Synapse SQL Analytics | Microsoft Docs
description: Recommendations and best practices you should know as you work with SQL Analytics. 
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 10/25/2019
ms.author: martinle
ms.reviewer: igorstan
---

# Best practices for SQL Analytics
This article is a collection of best practices to help you achieve optimal Azure SQL Analytics performance. Below you'll find basic guidance and important areas to focus on as you build your solution. Each section introduces you to a concept and then points you to  more detailed articles that cover the concept in more depth.

## SQL pool
For SQL Analytics pool loading guidance, see [Guidance for loading data](../../sql-data-warehouse/guidance-for-loading-data.md).

### Reduce cost with pause and scale
For more information about reducing costs through pausing and scaling, see the [Manage compute](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md). 


### Maintain statistics
Unlike SQL Server, which automatically detects and creates or updates statistics on columns, SQL Analytics pool requires manual maintenance of statistics. You will want to maintain your statistics to ensure that the SQL Analytics pool plans are optimized.  The plans created by the optimizer are only as good as the available statistics.  **Creating sampled statistics on every column is an easy way to get started with statistics.**  

It's equally important to update statistics as significant changes happen to your data.  A conservative approach may be to update your statistics daily or after each load.  There are always trade-offs between performance and the cost to create and update statistics. 

If you find it is taking too long to maintain all of your statistics, be more selective about which columns have statistics or which columns need frequent updating.  For example, you might want to update date columns, where new values may be added on a daily basis. **You will gain the most benefit by having statistics on columns involved in joins, columns used in the WHERE clause, and columns found in GROUP BY.**

See also [Manage table statistics][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS].

### Group INSERT statements into batches
A one-time load to a small table with an INSERT statement such as `INSERT INTO MyLookup VALUES (1, 'Type 1')`may be the best approach depending on your needs.  However, if you need to load thousands or millions of rows throughout the day, it's likely that singleton INSERTS aren't optimal. Instead, develop your processes so that they write to a file, and then another process periodically comes along and loads this file.

See also [INSERT][INSERT]

### Use PolyBase to load and export data quickly
SQL Analytics pool supports loading and exporting data through several tools including Azure Data Factory, PolyBase, and BCP.  For small amounts of data where performance isn't critical, any tool may be sufficient for your needs.  

However, when you are loading or exporting large volumes of data, or fast performance is needed, PolyBase is the best choice.  PolyBase is designed to leverage the MPP (Massively Parallel Processing) architecture of SQL Analytics pool and will load and export data magnitudes faster than any other tool.  

PolyBase loads can be run using CTAS or INSERT INTO.  **Using CTAS will minimize transaction logging and the fastest way to load your data.**  Azure Data Factory also supports PolyBase loads and can achieve similar performance as CTAS. PolyBase supports a variety of file formats including Gzip files.  **To maximize throughput when using gzip text files, break up files into 60 or more files to maximize parallelism of your load.**  For faster total throughput, consider loading data concurrently.

See also [Load data][Load data], [Guide for using PolyBase][Guide for using PolyBase], [Azure SQL Analytics pool loading patterns and strategies][Azure SQL Analytics pool loading patterns and strategies], [Load Data with Azure Data Factory][Load Data with Azure Data Factory], [Move data with Azure Data Factory][Move data with Azure Data Factory], [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT], [Create table as select (CTAS)][Create table as select (CTAS)]

### Load then query external tables
While Polybase, also known as external tables, can be the fastest way to load data, it is not optimal for queries. SQL Analytics pool Polybase tables currently only support Azure blob files and Azure Data Lake storage. These files do not have any compute resources backing them.  As a result, SQL Analytics pool cannot offload this work and must read the entire file by loading it to tempdb in order to read the data.  Therefore,if you have several queries that will be querying this data, it is better to load this data once and have queries use the local table.

See also [Guide for using PolyBase][Guide for using PolyBase]

### Hash distribute large tables
By default, tables are Round Robin distributed.  This makes it easy for users to start creating tables without having to decide how their tables should be distributed.  Round Robin tables may perform sufficiently for some workloads. But, in most cases, selecting a distribution column will perform much better.  

The most common example of when a table distributed by a column will far outperform a Round Robin table is when two large fact tables are joined.  For example, if you have an orders table, which is distributed by order_id, and a transactions table, which is also distributed by order_id,when you join your orders table to your transactions table on order_id, this query becomes a pass-through query. This means we eliminate data movement operations. Fewer steps mean a faster query. Less data movement also makes for faster queries.

When loading a distributed table, be sure that your incoming data is not sorted on the distribution key as this will slow down your loads.  See the links below for additional details on how selecting a distribution column can improve performance as well as how to define a distributed table in the WITH clause of your CREATE TABLE statement.

See also [Table overview][Table overview], [Table distribution][Table distribution], [Selecting table distribution][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

### Do not over-partition
While partitioning data can be effective for maintaining your data through partition switching or optimizing scans by with partition elimination, having too many partitions can slow down your queries.  Often a high granularity partitioning strategy that may work well on SQL Server may not work well on SQL Analytics pool.  

Having too many partitions can also reduce the effectiveness of clustered columnstore indexes if each partition has fewer than 1 million rows. SQL Analytics pool partitions your data for you into 60 databases. So, if you create a table with 100 partitions, the result witll be 6000 partitions.  Each workload is different so the best advice is to experiment with partitioning to see what works best for your workload.  

One option to consider is using a granularity that is lower than what may have worked for you in SQL Server.  For example, consider using weekly or monthly partitions rather than daily partitions.

See also [Table partitioning][Table partitioning]

### Minimize transaction sizes
INSERT, UPDATE, and DELETE statements run in a transaction and when they fail they must be rolled back.  To minimize the potential for a long rollback, minimize transaction sizes whenever possible.  This can be done by dividing INSERT, UPDATE, and DELETE statements into parts.  For example, if you have an INSERT that you expect to take 1 hour, you can break up the INSERT into four parts, thereby shortening each run to 15 minutes.  

Leverage special Minimal Logging cases, like CTAS, TRUNCATE, DROP TABLE or INSERT to empty tables to reduce rollback risk.  Another way to eliminate rollbacks is to use Metadata Only operations like partition switching for data management.  For example, rather than execute a DELETE statement to delete all rows in a table where the order_date was in October of 2001, you could partition your data monthly and then switch out the partition with data for an empty partition from another table (see ALTER TABLE examples).  

For unpartitioned tables, consider using a CTAS to write the data you want to keep in a table rather than using DELETE.  If a CTAS takes the same amount of time, it is a much safer operation to run as it has minimal transaction logging and can be canceled quickly if needed.

See also [Understanding transactions][Understanding transactions], [Optimizing transactions][Optimizing transactions], [Table partitioning][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)]

### Reduce query result sizes  
This helps you avoid client-side issues caused by large query result.  You can edit your query to reduce the number of rows returned. Some query generation tools allow you to add “top N” syntax to each query.  You can also CETAS the query result to a temporary table and then use PolyBase export for the downlevel processing.

### Use the smallest possible column size
When defining your DDL, using the smallest data type that will support your data will improve query performance.  This is especially important for CHAR and VARCHAR columns.  If the longest value in a column is 25 characters, then define your column as VARCHAR(25).  Avoid defining all character columns to a large default length.  In addition, define columns as VARCHAR when that is all that is needed rather than using NVARCHAR.

See also [Table overview][Table overview], [Table data types][Table data types], [CREATE TABLE][CREATE TABLE]

### Use temporary heap tables for transient data
When you are temporarily landing data on SQL Analytics pool, you may find that using a heap table will make the overall process faster.  If you are loading data only to stage it before running more transformations, loading the table to a heap table will be much faster than loading the data to a clustered columnstore table.  

In addition, loading data to a temp table will also load much faster than loading a table to permanent storage.  Temporary tables start with a "#" and are only accessible by the session that created it, so they may only work in limited scenarios.   Heap tables are defined in the WITH clause of a CREATE TABLE.  If you do use a temporary table, remember to create statistics on that temporary table too.

See also [Temporary tables][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

### Optimize clustered columnstore tables
Clustered columnstore indexes are one of the most efficient ways you can store your data in SQL Analytics pool.  By default, tables in SQL Analytics pool are created as Clustered ColumnStore.  To get the best performance for queries on columnstore tables, having good segment quality is important.  When rows are written to columnstore tables under memory pressure, columnstore segment quality may suffer.  

Segment quality can be measured by the number of rows in a compressed Row Group.  See the [Causes of poor columnstore index quality][Causes of poor columnstore index quality] in the [Table indexes][Table indexes] article for step by step instructions on detecting and improving segment quality for clustered columnstore tables.  Because high-quality columnstore segments are important, it's a good idea to use users IDs that are in the medium or large resource class for loading data. Using lower [data warehouse units](resource-consumption-models.md) means you want to assign a larger resource class to your loading user.

Since columnstore tables generally won't push data into a compressed columnstore segment until there are more than 1 million rows per table, and each SQL Analytics pool table is partitioned into 60 tables, columnstore tables won't benefit a query unless the table has more than 60 million rows.  For tables with less than 60 million rows, having a columstore index may not be the optimal solution.  

Furthermore, if you partition your data, then you will want to consider that each partition will need to have 1 million rows to benefit from a clustered columnstore index.  If a table has 100 partitions, then it will need to have at least 6 billion rows to benefit from a clustered columns store (60 distributions * 100 partitions * 1 million rows).  

If your table does not have 6 billion rows, either reduce the number of partitions or consider using a heap table instead.  It also may be worth experimenting to see if better performance can be gained by using a heap table with secondary indexes rather than a columnstore table.

When querying a columnstore table, queries will run faster if you select only the columns you need.  

See also [Table indexes][Table indexes], [Columnstore indexes guide][Columnstore indexes guide], [Rebuilding columnstore indexes][Rebuilding columnstore indexes]

### Use larger resource class to improve query performance
SQL Analytics pool uses resource groups as a way to allocate memory to queries.  Out of the box, all users are assigned to the small resource class that grants 100 MB of memory per distribution.  Since there are always 60 distributions and each distribution is given a minimum of 100 MB, the total system-wide memory allocation is 6,000 MB, or just under 6 GB.  

Certain queries, like large joins or loads to clustered columnstore tables, will benefit from larger memory allocations.  Some queries, such as pure scans, will see no benefit.  Alternatively, utilizing larger resource classes impacts concurrency, so you will want to take this into consideration before moving all of your users to a large resource class.

See also [Resource classes for workload management](workload-management-resource-classes.md)

### Use Smaller Resource Class to Increase Concurrency
If you notice that user queries have a long delay, it could be that your users are running in larger resource classes and are consuming numerous concurrency slots which is causing other queries to queue up.  To determine if users queries are queued, run `SELECT * FROM sys.dm_pdw_waits` to see if any rows are returned.

See also [Resource classes for workload management](workload-management-resource-classes.md), [sys.dm_pdw_waits][sys.dm_pdw_waits]

### Use DMVs to monitor and optimize your queries
SQL Analytics pool has several DMVs that can be used to monitor query execution.  The monitoring article below walks you through step-by-step instructions on how to view details of an executing query.  To quickly find queries in these DMVs, using the LABEL option with your queries can help.

See also [Monitor your workload using DMVs][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

### Other resources

Also see our [Troubleshooting][Troubleshooting] article for common issues and solutions.

If you didn't find what you were looking for in this article, try using the "Search for docs" on the left side of this page to search all of the Azure SQL Analytics pool documents.  The [Azure SQL Analytics pool Forum][Azure SQL Analytics pool MSDN Forum] is a place for you to pose questions to other users and to the SQL Analytics pool Product Group.  

We actively monitor this forum to ensure that your questions are answered either by another user or one of us.  If you prefer to ask your questions on Stack Overflow, we also have an [Azure SQL Analytics pool Stack Overflow Forum][Azure SQL Analytics pool Stack Overflow Forum].

Use the [Azure SQL Analytics pool Feedback][Azure SQL Analytics pool Feedback] page to make feature requests.  Adding your requests or up-voting other requests helps us to prioritize features.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ../../sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ../../sql-data-warehouse/sql-data-warehouse-develop-ctas.md
[Table overview]: development-tables-overview.md
[Table data types]: development-tables-data-types.md
[Table distribution]: ../../sql-data-warehouse/sql-data-warehouse-tables-distribute.md
[Table indexes]: ../../sql-data-warehouse/sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ../../sql-data-warehouse/sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ../../sql-data-warehouse/sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ../../sql-data-warehouse/sql-data-warehouse-tables-partition.md
[Manage table statistics]: development-tables-statistics.md
[Temporary tables]: development-tables-temporary.md
[Guide for using PolyBase]: ../../sql-data-warehouse/guidance-for-loading-data.md
[Load data]: ../../sql-data-warehouse/design-elt-data-loading.md
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ../../sql-data-warehouse/load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ../../sql-data-warehouse/sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]:../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ../../sql-data-warehouse/sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ../../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ../../sql-data-warehouse/sql-data-warehouse-troubleshoot.md
[LABEL]: development-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Analytics pool Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Analytics pool MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Analytics pool Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Analytics pool loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/



## SQL on-demand

### General considerations

SQL Analytics on-demand allows you to query files in your Azure storage accounts. It doesn't have local storage or ingestion capabilities, meaning that all files that the query targets are external to SQL Analytics on-demand. Therefore, everything related to reading files from storage might have an impact on query performance.

### Colocate Azure Storage account and SQL Analytics on-demand

To minimize latency, colocate your Azure storage account and your SQL Analytics on-demand endpoint. Storage accounts and endpoints provisioned during workspace creation are located in the same region. 

For optimal performance, if you access other storage accounts with SQL Analytics on-demand, make sure they are in the same region. Otherwise, there will be increased latency for the data's network transfer from the remote region to the endpoint's region.

### Azure Storage throttling

Multiple applications and services may access your storage account. When the combined IOPS or throughput generated by applications, services, and SQL Analytics on-demand workload exceed the limits of the storage account,storage throttling occurs. When storage throttling occurs, there is a substantial negative effect on query performance. 

Once throttling is detected, SQL Analytics on-demand has built-in handling of this scenario. SQL Analytics on-demand will make requests to storage at a slower pace until throttling is resolved. However, for optimal query execution, it is advised that you not stress the storage account with other workloads during query execution.

### Prepare files for querying

If possible, you can prepare files for better performance:

- Convert CSV to Parquet – Parquet is columnar format. Since it is compressed, it has smaller file sizes than CSV files with the same data, and SQL Analytics on-demand will need less time and storage requests to read it.
- If a query targets a single large file, you will benefit from splitting it into multiple smaller files.
- Try keeping your CSV file size below 10 GB.
- It is preferred to have equally sized files for a single OPENROWSET path or an external table LOCATION.
- Partition your data by storing partitions to different folders or file names - check [use filename and filepath functions to target specific partitions](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### Use fileinfo and filepath functions to target specific partitions

Data is often organized in partitions. You can instruct SQL Analytics on-demand to query particular folders and files. This will reduce the number of files and amount of data the query needs to read and process. Consequently,  you will achieve better performance. For more information, check [filename](development-storage-files-overview.md#filename-function) and [filepath](development-storage-files-overview.md#filepath-function) functions and examples on how to [query specific files](query-specific-files.md).

If your data in storage is not partitioned, consider partitioning it so you can use these functions to optimize queries targeting those files.

When [querying partitioned Spark tables](development-storage-files-spark-tables.md) from SQL Analytics on-demand, the query will automatically target only the files needed.

### Use CETAS to enhance query performance and joins

[CETAS](development-tables-cetas.md) is one of the most important features available in SQL Analytics on-demand. CETAS is a parallel operation that creates external table metadata and exports the result of the SELECT query to a set of files in your storage account.

You can use CETAS to store often used part of queries, like joined reference tables, to a  new set of files. Later on, you can join to this single external table instead of repeating common joins in multiple queries. As CETAS generates Parquet files, statistics will be automatically created when the first query targets this external table and you will gain improved performance.

