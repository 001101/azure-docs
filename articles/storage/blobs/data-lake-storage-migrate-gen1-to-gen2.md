﻿---
title: Migrate Azure Data Lake Storage from Gen1 to Gen2
description: Migrate Azure Data Lake Storage from Gen1 to Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/05/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
---

# Migrate Azure Data Lake Storage from Gen1 to Gen2

You can migrate your data, workloads, and applications from Data Lake Storage Gen1 to Data Lake Storage Gen2.

‎Azure Data Lake Storage Gen2 is built on [Azure Blob storage](storage-blobs-introduction.md) and provides a set of capabilities dedicated to big data analytics. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) combines features from [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), such as file system semantics, directory, and file level security and scale with low-cost, tiered storage, high availability/disaster recovery capabilities from [Azure Blob storage](storage-blobs-introduction.md).

> [!NOTE]
> For easier reading, this article uses the term *Gen1* to refer to Azure Data Lake Storage Gen1, and the term *Gen2* to refer to Azure Data Lake Storage Gen2.

## Recommended approach

To migrate to Gen2, we recommend the following approach.

:heavy_check_mark: Step 1: Assess readiness

:heavy_check_mark: Step 2: Prepare to migrate

:heavy_check_mark: Step 3: Migrate data, workloads, and applications

:heavy_check_mark: Step 4: Cutover from Gen1 to Gen2

> [!NOTE]
> There's no way to convert a Gen1 account into Gen2 because these accounts run in separate services.

### Step 1: Assess readiness

1. Learn about the [Data Lake Storage Gen2 offering](https://azure.microsoft.com/services/storage/data-lake-storage/); it's benefits, costs, and general architecture. 

2. [Compare the capabilities](#gen1-gen2-feature-comparison) of Gen1 with those of Gen2. 

3. Review a list of [known issues](data-lake-storage-known-issues.md) to assess any gaps in functionality.

4. Gen2 supports Blob storage features such as [diagnostic logging](../common/storage-analytics-logging.md), [access tiers](storage-blob-storage-tiers.md), and [Blob storage lifecycle management policies](storage-lifecycle-management-concepts.md). If you're interesting in using any of these features, review [current level of support](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-feature-support).

5. Review the current state of [Azure ecoysystem support](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-ecosystem-support) to ensure that Gen2 supports any services that your solutions depend upon.

### Step 2: Prepare to migrate

1. Identify the data sets that you'll migrate.

   Take this opportunity to clean up data sets that you no longer use. Unless you plan to migrate all of your data at one time, Take this time to identify logical groups of data that you can migrate in phases.
   
2. Determine the impact that a migration will have on your business.

   For example, consider whether you can afford any downtime while the migration takes place. These considerations can help you to identify a suitable migration pattern, and to choose the most appropriate tools.

3. Create a migration plan. 

   We recommend these [migration patterns](#migration-patterns). You can choose one of these patterns, combine them together, or design a custom pattern of your own.

### Step 3: Migrate data, workloads, and applications

Migrate data, workloads, and applications by using the pattern that you prefer. We recommend that you validate scenarios incrementally.

1. [Create a storage account](data-lake-storage-quickstart-create-account.md) and enable the hierarchical namespace feature. 

2. Migrate your data. 

3. Configure [services in your workloads](data-lake-storage-integrate-with-azure-services.md) to point to your Gen2 endpoint. 
   
4. Update applications to use Gen2 APIs. See guides for [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) and [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Update scripts to use Data Lake Storage Gen2 [PowerShell cmdlets](data-lake-storage-directory-file-acl-powershell.md), and [Azure CLI commands](data-lake-storage-directory-file-acl-cli.md).
   
6. Search for URI references that contain the string `adl://` in code files, or in Databricks notebooks, Apache Hive HQL files or any other file used as part of your workloads. Replace these references with the [Gen2 formatted URI](data-lake-storage-introduction-abfs-uri.md) of your new storage account. For example: the Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` might become `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`. 

7. Configure the security on your account to include [Role-based access control (RBAC) roles](../common/storage-auth-aad-rbac-portal.md), [file and folder level security](data-lake-storage-access-control.md), and [Azure Storage firewalls and virtual networks](../common/storage-network-security.md).

### Step 4: Cutover from Gen1 to Gen2

After you're confident that your applications and workloads are stable on Gen2, you can begin using Gen2 to satisfy your business scenarios. Turn off any remaining pipelines that are running on Gen1 and decommission your Gen1 account. 

<a id="gen1-gen2-feature-comparison" />

## Gen1 vs Gen2 capabilities

This table compares the capabilities of Gen1 to that of Gen2.

|Area |Gen1   |Gen2 |
|---|---|---|
|Data organization|[Hierarchical namespace](data-lake-storage-namespace.md)<br>File and folder support|[Hierarchical namespace](data-lake-storage-namespace.md)<br>Container, file and folder support |
|Geo-redundancy| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Authentication|[AAD managed identity](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Service principals](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD managed identity](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Service principals](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Shared Access Key](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Authorization|Management - [RBAC](../../role-based-access-control/overview.md)<br>Data – [ACLs](data-lake-storage-access-control.md)|Management – [RBAC](../../role-based-access-control/overview.md)<br>Data -  [ACLs](data-lake-storage-access-control.md), [RBAC](../../role-based-access-control/overview.md) |
|Encryption – Data at rest|Server side – with [service managed](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#microsoft-managed-keys) or [customer managed](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#customer-managed-keys-with-azure-key-vault) keys|Server side – with [service managed](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#microsoft-managed-keys) or [customer managed](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#customer-managed-keys-with-azure-key-vault) keys|
|VNET Support|[VNET Integration](../../data-lake-store/data-lake-store-network-security.md)|[Service Endpoints](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Developer experience|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|[REST](https://review.docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2), [.NET](/data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md) (In public preview)|
|Diagnostic logs|Classic logs<br>[Azure Monitor integrated](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Classic logs](../common/storage-analytics-logging.md) (In public preview)<br>Azure monitor integration – timeline TBD|
|Ecosystem|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3.1 and above)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5.1 and above)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## Gen1 to Gen2 patterns

Choose a migration pattern, and then modify that pattern as needed.

|||
|---|---|
|**Lift and Shift**|The simplest pattern. Ideal if your data pipelines can afford downtime.|
|**Incremental copy**|Similar to *lift and shift*, but with less downtime. Ideal for large amounts of data that take longer to copy.|
|**Dual pipeline**|Ideal for pipelines that can't afford any downtime.|
|**Bidirectional sync**|Similar to *dual pipeline*, but with a more phased approach that is suited for more complicated pipelines.|

Let's take a closer look at each pattern.
 
### Lift and shift pattern

This is the simplest pattern.

:one: &nbsp;&nbsp; Stop all writes to Gen1.

:two: &nbsp;&nbsp; Move data from Gen1 to Gen2.

:three: &nbsp;&nbsp; Point ingest operations and workloads to Gen2.

:four: &nbsp;&nbsp; Decommission Gen1.

![lift and shift pattern](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

Considerations for using this pattern:

- Cutover from Gen1 to Gen2 for all workloads at the same time.
- Expect downtime during the migration and the cutover period.
- Ideal for pipelines that can afford downtime and all apps can be upgraded at one time.

> [!TIP]
> For data transfer, we recommend [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). ACLs copy with the data.

### Incremental copy pattern

:one: &nbsp;&nbsp; Start moving data from Gen1 to Gen2.

:two: &nbsp;&nbsp; Incrementally copy new data from Gen1.

:three: &nbsp;&nbsp; After all data is copied, stop all writes to Gen1, and point workloads to Gen2.

:four: &nbsp;&nbsp; Decommission Gen1.

![Incremental copy pattern](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

Considerations for using this pattern:

- Cutover from Gen1 to Gen2 for all workloads at the same time.
- Expect downtime during cutover period only.
- Ideal for pipelines where all apps upgraded at one time, but the data copy requires more time.

> [!TIP]
> For data transfer, we recommend [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). ACLs copy with the data.

### Dual pipeline pattern

:one: &nbsp;&nbsp; Move data from Gen1 to Gen2.

:two: &nbsp;&nbsp; Ingest new data to both Gen1 and Gen2.

:three: &nbsp;&nbsp; Point workloads to Gen2.

:four: &nbsp;&nbsp; Stop all writes to Gen1 and then decommission Gen1.

![Dual pipeline pattern](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

Considerations for using this pattern:

- Gen1 and Gen2 pipelines run side-by-side.
- Supports zero downtime.
- Ideal in situations where your workloads and applications can't afford any downtime, and you can ingest into both storage accounts.

> [!TIP]
> For data transfer, we recommend [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). ACLs copy with the data.

### Bi-directional sync pattern

This pattern is similar to the *dual pipeline* pattern, but it's more ideally suited for complicated pipelines that require Gen1 and Gen2 side-by-side support during migration.

:one: &nbsp;&nbsp; Set up bidirectional replication between Gen1 and Gen2.

:two: &nbsp;&nbsp; Incrementally move ingest and compute workloads to Gen2.

:three: &nbsp;&nbsp; When all moves are complete, stop all writes to Gen1 and turn off bidirectional replication.

:four: &nbsp;&nbsp; Decommission Gen1.

![Bidirectional pattern](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

Considerations for using this pattern:

- Ideal for complex scenarios that involve a large number of pipelines and dependencies where a phased approach might make more sense.  
- Migration effort is high, but it provides side-by-side support for Gen1 and Gen2.

> [!TIP]
> For bidirectional data transfer, we recommend [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). It offers a repair feature for existing data.

## Next steps

- Learn about the various parts of setting up security for a storage account. See [Azure Storage security guide](../common/storage-security-guide.md).
- Optimize the performance for your Data Lake Store. See [Optimize Azure Data Lake Storage Gen2 for performance](data-lake-storage-performance-tuning-guidance.md)
- Review the best practices for managing your Data Lake Store. See [Best practices for using Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)

