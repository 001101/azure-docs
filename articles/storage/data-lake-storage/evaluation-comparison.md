---
title: Azure Data Lake Storage Comparison
description: Comparing Data Lake Stoarge Gen1 to Gen2
services: storage
keywords: 
author: roygara
ms.topic: article
ms.author: rogarana
manager: twooley
ms.date: 06/01/2018
ms.service: storage
ms.component: data-lake-storage-gen2
---

# Comparing Azure Data Lake Storage Gen2 and Azure Data Lake Storage Gen1

The table in this article summarizes the differences between Azure Data Lake Storage Gen1 and Azure Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 offers the same rich support for the Hadoop Compatible File System and a full hierarchical namespace just like Azure Data Lake Storage Gen1 does. Additionally, it brings all of Azure Blobs offerings to the table so functionality like data tiering and lifecycle management, durability options for HA and DR, event grid support, enhanced network security, and compatibility with all of Blob APIs.

|Azure Data Lake Storage Gen1  |Azure Data Lake Storage Gen2  |
|---------|---------|
|Compatible to HDFS standard – customer can perform analysis using the Hadoop analytic framework.  Applications or services that use the Hadoop file system API can easily integrate with Data Lake Storage Gen1.  Data Lake Storage Gen1 exposes a HDFS-compatible REST interface for applications.     |Hadoop File System Compatible – customers can use Hadoop analytics frameworks that leverage the Hadoop FileSystem API. WASB & Prague is part of the Apache Hadoop project.          |
|HDFS extensions – customers can use non-destructive concat, set expiry, acquire leases on files being written, etc.       |         |
|Hierarchical file system – customer can store files into a true hierarchical folder structure.     |Same as Data Lake Storage Gen1         |
|Atomic renames/moves – customer can rename or move folders and files atomically.     |Same as Data Lake Storage Gen1           |
|File system consistency - customer can ensure that their data is consistent.  Data is readable immediately after it is written.     |Same as Data Lake Storage Gen1         |
|Partition management handled  – customer can set up their file architecture logically.  Data is partitioned appropriately so that partitions are not throttled or have reduced performance when they become hot.  This avoids customers having to rearchitect their directory structure or having unnatural directory structures to get scalable performance.     |         |
|No fixed throughput/IOPS limit – customer can have massive throughput to support any size of analytics.      |Throughput/IOPS limit – customer can have massive throughput to support any size of analytics.         |
|Massively parallel read and writes – customer can spread data across many servers  to enable large amounts of data to be read or written in parallel.  Customer can perform an unlimited number of IOPs per file.     |Massively parallel read and writes – customer can spread data across many servers to enable large amounts of data to be read or written in parallel.         |
|Unlimited storage – customer can store an unlimited amount of data for an unlimited amount of time.  There are no limits to accounts sizes, files sizes, and number of files.     |Storage capacity – customer can store a massive amount of data for an unlimited amount of time.           |
|Petabyte-sized files – customer can store large petabyte-sized files.  There is no limit on file sizes.     |         |
|Unlimited appends per file – customer can read and write to unlimited append blocks in parallel.     |         |
|Millions of files per folder - customer can have millions of files in a single folder at any level in the folder hierarchy     |         |
|"Unlimited throughput per file - customer can use all the throughput for the account to read or write to a specific file"     |         |
|First-class integration with AAD – customer can use AAD features including multi-factor authentication, conditional access, role-based access control, user lifecycle management, application usage monitoring, security monitoring, and alerting.     |Same as Data Lake Storage Gen1         |
|POSIX-compliant Access Control Lists – users can have fine grained access control on the file and folder level.  It is understand access control because it is defined using the POSIX access control model.     |Same as Data Lake Storage Gen1         |
|Encryption – customer can have encryption enabled by default.  Customer data is encrypted at rest, movement within Data Lake Storage Gen1, and over the wire when communicating outside Data Lake Storage Gen1.     |Same as Data Lake Storage Gen1         |
|Allow secure communication only via HTTPS – customer can have secure communication into Data Lake Storage Gen1 because all data ingested into Data Lake Storage Gen1 must be encrypted.     |Allow secure communication only via HTTPS – customer can have secure communication into Data Lake Storage Gen2 because all data ingested into Prague must be encrypted.         |
|Key management – customer can have keys managed by Azure or customer-managed keys via Key Vault.     |Same as Data Lake Storage Gen1         |
|VNET support – customer can allow access to Data Lake Storage Gen1 from a specific VNET to restrict access.     |VNET support – customer can allow access to Data Lake Storage Gen2 from a specific VNET and/or IP addresses to restrict access.         |
|High availability – customer can have their data available at least 99.9% of the time.     |High availability – customer can have their data available at least 99.99% of the time.         |
|Regional availability – customer can use Data Lake Storage Gen1 in East US 2, Central US, North Europe, West Europe     |Regional availability – customer can use Data Lake Storage Gen2 in West US 2 and West Central US         |
|First party integration – customer can have integration with first party applications (for example, HDInsight, Azure VMs, ASA, Event Hubs, ADF, Powershell, ExpressRoute, Azure Import/Export, Azure CLI, R, SQL DW via Polybase, AAD, Key Vault, Data Catalog, Power BI, Analysis Services, Visual Studio).     |First party integration – customer can have integration with first party applications (for example, HDInsight, Azure Databricks, Azure VMs, ASA, Event Hubs, ADF, Powershell, ExpressRoute, Azure Import/Export, Azure Data box, Azure CLI, R, SQL DW via Polybase, AAD, Key Vault, Data Catalog, Power BI, Azure Analysis Services, EventGrid, Visual Studio).         |
|Third party integration – customer can have integration with third party applications (for example, Cloudera, Hortonworks, Nifi, Qubole, Informatica, StreamSets, ImanisData, Paxata, Trifacta).     |Third party integration – customer can have integration with third party applications (for example, Hortonworks, MapR, Nifi, Qubole, Informatica, StreamSets, ImanisData, Paxata, Trifacta).         |
|Open Source Software – customer can use Spark, Hive, Tez, MapReduce, Storm, HBase, Sqoop, HCatalog, Mahout, Pig, Pig Latin, Oozie, Zookeeper.     |Same as Data Lake Storage Gen1         |
|Data lifecycle policy: File expiration – customer can delete files at a specified date and time to reduce cost and manage compliance.     |Lifecycle Management Policies – customers will be able to define policies to control the automatic movement of data across the multiple tiers         |
|Auditing – customer can have request and diagnostic logs collect data to access audit trails.     |         |
|APIs – customer can use REST APIs over HTTPS.     |APIs – customer can use REST APIs over HTTP/HTTPS.         |
|Many ways to read and write data – customer can use SDKs (.NET, Java, Python, R), Azure portal, and tools (Powershell, Azure CLI, AdlCopy, Distcp, ADF, Azure Import/Export) to read and write their data.     |Many ways to read and write data – Azure portal, and tools (Azure Storage Explorer, Powershell, Azure CLI, AzCopy, Distcp, ADF, Azure Import/Export, Azure Data box) to read and write their data.         |

**Next step**

If you think that Data Lake Storage Gen2 might suit your needs, proceed to the [migration guide](evaluation-migration-guide.md) to begin planning a migration.