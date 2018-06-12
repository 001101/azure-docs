---
title: Azure SQL Database DTU-based resource limits - elastic pools| Microsoft Docs
description: This page describes some common DTU-based resource limits for elastic pools in Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: carlrab

---
# Azure SQL Database DTU-based purchasing model limits for elastic pools

> [!IMPORTANT]
> For DTU-based purchasing model resource limits for single databases, see [DTU-based resource limits - single databases](sql-database-vcore-resource-limits-elastic-pools.md).For vCore-based resource limits, see [vCore-based resource limits - single databases](sql-database-vcore-resource-limits-single-databases.md) and [vCore-based resource limits - elastic pools](sql-database-vcore-resource-limits-elastic-pools.md).

## Elastic pool: Storage sizes and performance levels

For SQL Database elastic pools, the following tables show the resources available at each service tier and performance level. You can set the service tier, performance level, and storage amount using the [Azure portal](sql-database-elastic-pool-manage.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool-manage.md#manage-elastic-pools-and-databases-using-powershell), the [Azure CLI](sql-database-elastic-pool-manage.md#manage-elastic-pools-and-databases-using-the-azure-cli), or the [REST API](sql-database-elastic-pool-manage.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> The resource limits of individual databases in elastic pools are generally the same as for single databases outside of pools based on DTUs and the service tier. For example, the max concurrent workers for an S2 database is 120 workers. So, the max concurrent workers for a database in a Standard pool is also 120 workers if the max DTU per database in the pool is 50 DTUs (which is equivalent to S2).

### Basic elastic pool limits

| eDTUs per pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Included storage per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max storage choices per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max In-Memory OLTP storage per pool (GB) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| Max number DBs per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Max concurrent workers (requests) per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Min eDTUs choices per database | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Max eDTUs choices per database | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Max storage per database (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### Standard elastic pool limits

| eDTUs per pool | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Included storage per pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Max storage choices per pool (GB)* | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Max In-Memory OLTP storage per pool (GB) | N/A | N/A | N/A | N/A | N/A | N/A | 
| Max number DBs per pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Max concurrent workers (requests) per pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Min eDTUs choices per database | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Max eDTUs choices per database | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Max storage per database (GB)* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### Standard elastic pool limits (continued) 

| eDTUs per pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Included storage per pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Max storage choices per pool (GB)* | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Max In-Memory OLTP storage per pool (GB) | N/A | N/A | N/A | N/A | N/A | 
| Max number DBs per pool | 500 | 500 | 500 | 500 | 500 | 
| Max concurrent workers (requests) per pool | 2400 | 3200 | 4000 | 5000 | 6000 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Min eDTUs choices per database | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max eDTUs choices per database | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Max storage choices per database (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### Premium elastic pool limits

| eDTUs per pool | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Included storage per pool (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Max storage choices per pool (GB)* | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Max In-Memory OLTP storage per pool (GB) | 1 | 2 | 4 | 10 | 12 | 
| Max number DBs per pool | 50 | 100 | 100 | 100 | 100 | 
| Max concurrent workers per pool (requests) | 200 | 400 | 800 | 1600 | 2400 | 
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Min eDTUs per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Max eDTUs per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Max storage per database (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### Premium elastic pool limits (continued) 

| eDTUs per pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Included storage per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Max storage choices per pool (GB)* | 2048 | 2560 | 3072 | 3548 | 4096|
| Max In-Memory OLTP storage per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Max number DBs per pool | 100 | 100 | 100 | 100 | 100 | 
| Max concurrent workers (requests) per pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Min eDTUs choices per database | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Max eDTUs choices per database | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Max storage per database (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> - Storage sizes greater than the amount of included storage are in preview and extra costs apply. For details, see the [SQL Database pricing page](https://azure.microsoft.com/pricing/details/sql-database/). Storage sizes greater than the amount of included storage are in preview and extra costs apply. For details, see the [SQL Database pricing page](https://azure.microsoft.com/pricing/details/sql-database/).
>
> - More than 1 TB of storage in the Premium tier is available in all regions except the following: UK North, West Central US, UK South2, China East, USDoDCentral, Germany Central, USDoDEast, US Gov Southwest, US Gov South Central, Germany Northeast,  China North, US Gov East. More widespread availability is planned. In other regions, the storage max in the Premium tier is limited to 1 TB. See [P11-P15 Current Limitations](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
>

If all DTUs of an elastic pool are used, then each database in the pool receives an equal amount of resources to process queries. The SQL Database service provides resource sharing fairness between databases by ensuring equal slices of compute time. Elastic pool resource sharing fairness is in addition to any amount of resource otherwise guaranteed to each database when the DTU min per database is set to a non-zero value.

### Database properties for pooled databases

The following table describes the properties for pooled databases.

| Property | Description |
|:--- |:--- |
| Max eDTUs per database |The maximum number of eDTUs that any database in the pool may use, if available based on utilization by other databases in the pool. Max eDTU per database is not a resource guarantee for a database. This setting is a global setting that applies to all databases in the pool. Set max eDTUs per database high enough to handle peaks in database utilization. Some degree of overcommitting is expected since the pool generally assumes hot and cold usage patterns for databases where all databases are not simultaneously peaking. For example, suppose the peak utilization per database is 20 eDTUs and only 20% of the 100 databases in the pool are peak at the same time. If the eDTU max per database is set to 20 eDTUs, then it is reasonable to overcommit the pool by 5 times, and set the eDTUs per pool to 400. |
| Min eDTUs per database |The minimum number of eDTUs that any database in the pool is guaranteed. This setting is a global setting that applies to all databases in the pool. The min eDTU per database may be set to 0, and is also the default value. This property is set to anywhere between 0 and the average eDTU utilization per database. The product of the number of databases in the pool and the min eDTUs per database cannot exceed the eDTUs per pool. For example, if a pool has 20 databases and the eDTU min per database set to 10 eDTUs, then the eDTUs per pool must be at least as large as 200 eDTUs. |
| Max storage per database |The maximum database size set by the user for a database in a pool. However, pooled databases share allocated pool storage. Even if the total max storage **per database* is set to be greater than the total available storage **space of the pool*, the total space actually used by all of the databases will not be able to exceed the available pool limit. Max database size refers to the maximum size of the data files and does not include the space used by log files. |
|||
 

## What is the maximum number of servers and databases?

| Maximum | Value |
| :--- | :--- |
| Databases per server | 5000 |
| Number of servers per subscription per region | 20 |
|||

> [!IMPORTANT]
> As the number of databases approaches the limit per server, the following can occur:
> <br> •	Increasing latency in running queries against the master database.  This includes views of resource utilization statistics such as sys.resource_stats.
> <br> •	Increasing latency in management operations and rendering portal viewpoints that involve enumerating databases in the server.

## What happens when elastic pool resource limits are reached?

### Compute (DTUs and eDTUs)

When database compute utilization (measured by DTUs and eDTUs) becomes high, query latency increases and can even time out. Under these conditions, queries may be queued by the service and are provided resources for execution as resource become free.
When encountering high compute utilization, mitigation options include:

- Increasing the performance level of the elastic pool to provide the elastic pool with more eDTUs. See [Elastic pool: change eDTUs](#elastic-pool-change-edtus).
- Optimizing queries to reduce the resource utilization of each query. For more information, see [Query Tuning/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### Storage

When database space used reaches the max size limit, database inserts and updates that increase the data size fail and clients receive an [error message](sql-database-develop-error-messages.md). Database SELECTS and DELETES continue to succeed.

When encountering high space utilization, mitigation options include:

- Increasing the max size of the elastic pool, or change the performance level to obtain more included storage. See [SQL Database resource limits](sql-database-dtu-resource-limits-elastic-pools.md).
- Move the database outside of the elastic pool so that its storage space is not shared with other databases.

### Sessions and workers (requests) 

The maximum number of sessions and workers are determined by the service tier and performance level (eDTUs). New requests are rejected when session or worker limits are reached, and clients receive an error message. While the number of connections available can be controlled by the application, the number of concurrent workers is often harder to estimate and control. This is especially true during peak load periods when database resource limits are reached and workers pile up due to longer running queries. 

When encountering high session or worker utilization, mitigation options include:
- Increasing the service tier or performance level of the elastic pool. See [Elastic pool: change storage size](#elastic-pool-change-storage-size), and [Elastic pool: change eDTUs](#elastic-pool-change-edtus).
- Optimizing queries to reduce the resource utilization of each query if the cause of increased worker utilization is due to contention for compute resources. For more information, see [Query Tuning/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## Next steps

- See [SQL Database FAQ](sql-database-faq.md) for answers to frequently asked questions.
- For information about general Azure limits, see [Azure subscription and service limits, quotas, and constraints](../azure-subscription-service-limits.md).
- For information about DTUs and eDTUs, see [DTUs and eDTUs](sql-database-what-is-a-dtu.md).
- For information about tempdb size limits, see https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
