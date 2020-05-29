---
title: Configure replication to Azure SQL Edge (Preview) 
description: Learn about configuring replication to Azure SQL Edge (Preview).
keywords: 
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
---

# Configure replication to Azure SQL Edge (Preview) 

You can configure an instance of Azure SQL Edge as the push subscriber for one-way transactional replication or snapshot replication. This instance can't act as the publisher or the distributor for a transactional replication configuration. Note that Azure SQL Edge doesn't support merge replication, peer-to-peer replication, or Oracle publishing.

## Supported configurations
  
- Azure SQL Edge instance must be a push subscriber for a publisher.
- The publisher and the distributor can be either
   - An instance of SQL Server running on-premises or an instance of SQL Server running in an Azure virtual machine. For more information, see [SQL Server on Azure Virtual Machines overview](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). SQL Server instances must be using a version greater than SQL Server 2016.
   - An instance of Azure SQL Managed Instance. Managed Instance can host publisher, distributor, and subscriber databases. For more information, see [Replication with SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- The instance of Azure SQL Edge must be a push subscriber for a publisher.
- The publisher and the distributor can be either:
   - An instance of SQL Server running on-premises, or an instance of SQL Server running in an Azure virtual machine. For more information, see [SQL Server on Azure Virtual Machines overview](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). SQL Server instances must be using a version later than SQL Server 2016.
   - An instance of Azure SQL Database Managed Instance. This instance can host publisher, distributor, and subscriber databases. For more information, see [Replication with SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- The distribution database and the replication agents can't be placed on an instance of Azure SQL Edge.  

> [!NOTE]
> If you attempt to configure replication by using an unsupported version, you might receive the following two errors: MSSQL_REPL20084 ("The process could not connect to Subscriber.") and MSSQL_REPL40532 ("Cannot open server \<name> requested by the login. The login failed.").  

## Remarks

The following requirements and best practices are important to understand as you configure replication:

- You can configure replication by using [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). You can also do so by running Transact-SQL statements on the publisher, by using either SQL Server Management Studio or [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio).
- To replicate to an instance of Azure SQL Edge, you must use SQL Server authentication to sign in.
- Replicated tables must have a primary key.
- A single publication on SQL Server can support both Azure SQL Edge and SQL Server (on-premises and SQL Server in an Azure virtual machine) subscribers.  
- Replication management, monitoring, and troubleshooting must be performed from the SQL Server instance.  
- Only push subscriptions to Azure SQL Edge are supported.  
- Only `@subscriber_type = 0` is supported in **sp_addsubscription** for Azure SQL Edge.  
- Azure SQL Edge does not support bi-directional, immediate, updatable, or peer to peer replication.
- Azure SQL Edge only supports a subset of features available in SQL Server or SQL Managed Instance, as such an attempt to replicate a database (or objects within the database) which contain one or more unsupported features will result in a failure. For example, attempting to replicate a database which contains objects with spatial data types will result in an error. For more information on features supported by Azure SQL Edge, see [Supported features of Azure SQL Edge](features.md).

## Scenarios  

### Initialize reference data on an instance of Azure SQL Edge

A common scenario where replication can be useful is when there is a need to initialize the edge instance with reference data which changes over time. For example, updating ML models on the Edge instance after they have been trained on a SQL Server instance.

1. Create a transactional replication publication on a SQL Server database.  
2. On the SQL Server instance, use the **New Subscription Wizard** or Transact-SQL statements to create a push to subscription to Azure SQL Edge.  
3. The replicated database on Azure SQL Edge can be initialized by either using a snapshot generated by the snapshot agent and distributed and delivered by the distribution agent or using a backup of the database from the publisher. Once again, if the database backup contains objects/features that are not supported by Azure SQL Edge, the restore operation will fail.

## Limitations

The following options aren't supported for Azure SQL Edge subscriptions:

- Copy file groups association  
- Copy table partitioning schemes  
- Copy index partitioning schemes  
- Copy user defined statistics  
- Copy default bindings  
- Copy rule bindings  
- Copy fulltext indexes  
- Copy XML XSD  
- Copy XML indexes  
- Copy permissions  
- Copy spatial indexes  
- Copy filtered indexes  
- Copy data compression attribute  
- Copy sparse column attribute  
- Copy filestream, `hierarchyid`, or spatial data types
- Convert `hierarchyid` to MAX data types  
- Convert spatial to MAX data types  
- Copy extended properties  
- Copy permissions  

## Examples

Create a publication and a push subscription. For more information, see:
  
- [Create a publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Create a push subscription](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) by using the Azure SQL Edge server name and IP as the subscriber (for example, **myEdgeinstance,1433**), and a database name on the Azure SQL Edge instance as the destination database (for example, **AdventureWorks**).  

## Next steps  

- [Create a publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Create a push subscription](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of replication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoring (replication)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initialize a subscription](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


