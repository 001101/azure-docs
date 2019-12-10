﻿---
title: Migrate Azure Data Lake Storage from Gen1 to Gen2
description: Migrate Azure Data Lake Storage from Gen1 to Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/05/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
---

# Migrate Azure Data Lake Storage from Gen1 to Gen2

You can migrate data, workloads, and applications from Data Lake Storage Gen1 to Data Lake Storage Gen2.

Data Lake Storage Gen2 is Microsoft's latest Data Lake Storage repository. It combines Gen1 features such as file system semantics, directory and file level security, and scale with low-cost, tiered storage, high availability/disaster recovery capabilities from Azure Blob Storage. 

To learn more, see [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

> [!NOTE]
> For easier reading, this article uses the term *Gen1* to refer to Azure Data Lake Storage Gen1, and the term *Gen2* to refer to Azure Data Lake Storage Gen2.

## Gen1 to Gen2 road map

To migrate to Gen2, we recommend the following path.

:heavy_check_mark: Step 1: Assess whether to migrate

:heavy_check_mark: Step 2: Prepare to migrate

:heavy_check_mark: Step 3: Migrate data

:heavy_check_mark: Step 4: Update analytic workloads

:heavy_check_mark: Step 5: Update applications

> [!NOTE]
> There's no way to convert a Gen1 account into Gen2 because these accounts run in separate services.

### Step 1: Assess whether to migrate

1. Learn about the [Data Lake Storage Gen2 offering](https://azure.microsoft.com/services/storage/data-lake-storage/); it's benefits, costs, and general architecture. 

2. [Compare the capabilities](#gen1-gen2-feature-comparison) of Gen1 with those of Gen2. 

3. Review a list of [known issues](data-lake-storage-known-issues.md) to gauge product state and stability and assess any gaps.

4. If you're interested in leveraging blob storage features, review the [current level of support](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-feature-support) for those features.

5. Review the current state of [Azure ecoysystem support](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-ecosystem-support) to ensure that services that your solutions depend upon support  Gen2.

### Step 2: Prepare to migrate

1. Identify the data sets that you'll migrate.

3. Determine the impact that a migration will have on your business.

4. Create a migration plan. We recommend any of these [migration patterns](#migration-patterns).

5. Choose a [data transfer tool](#data-transfer-tools).

### Step 3: Migrate data

1. [Create a storage account](data-lake-storage-quickstart-create-account.md) and enable the hierarchical namespace feature. 

2. Migrate data by using the data transfer tool that you've chosen.

3. Secure the data in the storage account. 

   - [Assign role based access security (RBAC) roles](../common/storage-auth-aad-rbac-portal.md) to security principles in the context of your storage account, resource group, or subscription. 
   
   - Optionally [apply file and folder level security](data-lake-storage-access-control.md).

   For a complete guide to security, see [Azure Storage security guide](../common/storage-security-guide.md).

### Step 4: Update analytic workloads

1. Configure services in your workloads to point to your Gen2 endpoint. 

   For a list of articles that can help you get started, see [Integrate Azure Data Lake Storage with Azure services](data-lake-storage-integrate-with-azure-services.md).

2. Search for URI references that contain the string `adl://` in Databricks notebooks, Apache Hive HQL files or any other file used as part of your workloads. 

3. Replace these references with the [Gen2 formatted URI](data-lake-storage-introduction-abfs-uri.md) of your new storage account.

   For example: the Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` might become `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`. 

### Step 5: Update applications

1. Update applications to use Gen2 APIs. See guides for [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), and [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 

2. Update scripts to use Data Lake Storage Gen2 [PowerShell cmdlets](data-lake-storage-directory-file-acl-powershell.md), and [Azure CLI commands](data-lake-storage-directory-file-acl-cli.md).

3. In code files, search for URI references that contain the string `adl://` in Databricks notebooks, Apache Hive HQL files or any other file used as part of your workloads. 

4. Replace these references with the [Gen2 formatted URI](data-lake-storage-introduction-abfs-uri.md) of your new storage account.

   For example: the Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` might become `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`. 

<a id="gen1-gen2-feature-comparison" />

## Gen1 vs Gen2 capabilities

This table compares the capabilities of Gen1 to that of Gen2.

|Area |Gen1   |Gen2 |
|---|---|---|
|Data organization|Hierarchical namespace<br>File and folder support|Hierarchical namespace<br>File and folder support |
|Geo-redundancy| LRS| LRS, ZRS, GRS, RA-GRS |
|Authentication|AAD managed identity<br>Service principals|AAD managed identity<br>Service principals<br>Shared Access Key|
|Authorization|Management - RBAC<br>Data – ACLs|Management – RBAC<br>Data -  ACLs, RBAC |
|Encryption – Data at rest|Server side – with service managed or customer managed keys|Server side – with service managed or customer managed keys|
|VNET Support|VNET Integration|Service Endpoints|
|Developer experience|REST APIs, SDKs, Powershell|REST APIs<br>Roadmap – SDKs, Powershell for Blob Operations(In public preview)|
|Diagnostic logs|Classic logs<br>Azure Monitor integrated|Roadmap – Classic logs (In public preview)<br>Azure monitor integration – timeline TBD|
|Ecosystem|HDInsight (3.6), Azure Databricks (3.1 and above), SQL DW, ADF|HDInsight (3.6, 4.0), Azure Databricks (5.1 and above), SQL DW, ADF|

<a id="migration-patterns" />

## Gen1 to Gen2 migration patterns

> [!div class="checklist"]
> * Lift and shift
> * Incremental copy
> * Dual pipeline
> * Bi-directional sync

### Lift and shift

Put something here.

### Incremental copy

Put something here.

### Dual pipeline

Put something here.

### Bi-directional sync

Put something here.

<a id="data-transfer-tools" />

## Gen1 to Gen2 data transfer tools

## Get help

Put links to stack tags, yammer groups, etc.

## Next steps

Need a good link here.

