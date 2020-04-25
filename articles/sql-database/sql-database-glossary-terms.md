---
title: Glossary of terms 

description: A glossery of terms for working with 
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: 
ms.devlang: 
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: 
ms.date: 01/22/2020
---
# Azure SQL Database glossary of terms

## Azure SQL Database

|Context|Term|More information|
|:---|:---|:---|
|Azure service|Azure SQL Database or SQL Database|[Azure SQL Database](sql-database-technical-overview.md)|
|Purchasing model|DTU-based purchasing model|[DTU-based purchasing model](sql-database-service-tiers-dtu.md)|
||vCore-based purchasing model|[vCore-based purchasing model](sql-database-service-tiers-vcore.md)|
|Deployment option |Single database|[Single databases](sql-database-single-database.md)|
||Elastic pool|[Elastic pool](sql-database-elastic-pool.md)|
|Service tier|Basic, Standard, Premium, General Purpose, Hyperscale, Business Critical|For service tiers in the vCore model, see [SQL Database service tiers](sql-database-service-tiers-vcore.md#service-tiers). For service tiers in the DTU model, see [DTU model](sql-database-service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Compute tier|Serverless compute|[Serverless compute](sql-database-service-tiers-vcore.md#compute-tiers)
||Provisioned compute|[Provisioned compute](sql-database-service-tiers-vcore.md#compute-tiers)
|Compute generation|Gen5, M-series, Fsv2-series|[Hardware generations](sql-database-service-tiers-vcore.md#hardware-generations)
|Server entity| logical SQL server |[logical SQL servers in Azure](sql-database-servers.md)|
|Resource type|vCore|A CPU core provided to the compute resource for a single database, elastic pool. |
||Compute size and storage amount|Compute size is the maximum amount of CPU, memory and other non-storage related resources available for a single database, or elastic pool.  Storage size is the maximum amount of storage available for a single database, or elastic poole.  For sizing options in the vcore model, see [vCore single databases](sql-database-vcore-resource-limits-single-databases.md), and [vCore elastic pools](sql-database-vcore-resource-limits-elastic-pools.md).  (sql-database-managed-instance-resource-limits.md).  For sizing options in the DTU model, see [DTU single databases](sql-database-dtu-resource-limits-single-databases.md) and [DTU elastic pools](sql-database-dtu-resource-limits-elastic-pools.md).


## Azure Managed Instance

|Context|Term|More information|
|:---|:---|:---|
|Azure service|Azure SQL Managed Instance|[SQL Managed Instance](sql-database-managed-instance.md)|
|Purchasing model|vCore-based purchasing model|[vCore-based purchasing model](sql-database-service-tiers-vcore.md)|
|Deployment option |Single instance|[Single instance](sql-database-managed-instance.md)|
||Instance pool (preview)|[Instance pools](sql-database-instance-pools)|
|Service tier|General Purpose, Business Critical|[SQL Managed Instance service tiers](sql-database-managed-instance.md#service-tiers)..|
|Compute tier|Provisioned compute|[Provisioned compute](sql-database-service-tiers-vcore.md#compute-tiers)|
|Compute generation|Gen5|[Hardware generations](sql-database-service-tiers-vcore.md#hardware-generations)
|Server entity|Instance|[Database server](sql-database-servers.md)|
||SQL Database managed instance server, managed instance server, or instance server|[Managed instance](sql-database-managed-instance.md)|
|Resource type|vCore|A CPU core provided to the compute resource for a single database, elastic pool, or managed instance.|
||Compute size and storage amount|Compute size is the maximum amount of CPU, memory and other non-storage related resources available for a single database, elastic pool, or managed instance.  Storage size is the maximum amount of storage available for a single database, elastic pool, or managed instance.  For sizing options in the vcore model, see [vCore single databases](sql-database-vcore-resource-limits-single-databases.md), [vCore elastic pools](sql-database-vcore-resource-limits-elastic-pools.md) and [managed instances](sql-database-managed-instance-resource-limits.md).  For sizing options in the DTU model, see [DTU single databases](sql-database-dtu-resource-limits-single-databases.md) and [DTU elastic pools](sql-database-dtu-resource-limits-elastic-pools.md).

## SQL on Azure VM 