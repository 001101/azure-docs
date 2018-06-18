---
title: Azure Data Lake Storage Introduction
description:  Introduction to Azure Data Lake Storage
services: storage
documentationcenter: ''
author: jamesbak
manager: jahogg

ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
---

# Introduction to Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 is Microsoft's hyperscale storage service designed for big data analytics workloads. This is the storage service that underpins all first and third party analytics engines on Azure.

Azure Data Lake Storage allows you to interface with your data using both file system and object storage paradigms. This makes Azure Data Lake Storage the only cloud-based _multi-modal_ storage service, allowing you to extract analytics value from all of your data.

Azure Data Lake Storage Gen2 features all qualities that are required for the full lifecycle of analytics data. This results from converging the capabilities of our two existing storage services. Features from [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), such as file system semantics, file-level security and scale are combined with regional availability, low-cost, tiered storage, high availability/disaster recovery capabilities and a large SDK/tooling ecosystem from [Azure Blob Storage](../blobs/storage-blobs-introduction.md). In Azure Data Lake Storage, all the qualities of object storage remain while adding the advantages of a file system interface optimized for analytics workloads.

## Designed for enterprise big data analytics

Azure Data Lake Storage is the foundational storage service for building _enterprise data lakes (EDL)_ on Azure. Designed from the start to service multiple petabytes of information while sustaining hundreds of gigabits of throughput, Azure Data Lake Storage gives you an easy way to manage massive amounts of data. 

A fundamental feature of Azure Data Lake Storage is the introduction of the [Hierarchical Namespace service (HNS)](./namespace.md) which organizes blobs into a hierarchy of directories for performant data access. The HNS also enables Azure Data Lake Storage to support both object store and file system paradigms at the same time. For instance, a common object store naming convention employs slashes in the name to mimic a hierarchical folder structure. This structure becomes real with Azure Data Lake Storage. Operations such as renaming or deleting a directory become single atomic metadata operations on the directory rather than enumerating and processing all objects that share the name prefix of the directory. POSIX-compliant permissions are also enabled by HNS.

In the past, cloud-based analytics had to compromise in areas of performance, management, and security. Azure Data Lake Storage addresses each of these aspects in the following ways:

- **Performance** is optimized because you do not need to copy or transform data as a prerequisite for analysis. The HNS greatly improves the performance of directory management operations which improves overall job performance.
 
- **Management** is easier because you can manipulate files through directories and subdirectories.

- **Security** is enforceable because you can define POSIX permissions on folders or individuals files.

- **Cost Effective** is made possible as Azure Data Lake Storage is built on top of the low-cost [Azure Blob Storage](../blobs/storage-blobs-introduction.md). The additional features further lower the total cost of ownership for running big data analytics on Azure.

## Key features of Azure Data Lake Storage

> [!NOTE]
> During the public preview of Azure Data Lake Storage, some of the features listed below may vary in their availability. As new features and regions are released during the preview program, this information will be communicated.
> Sign up to the public preview of Azure Data Lake Storage Gen 2 [here](https://aka.ms/adlsgen2signup).  
 

- **Hadoop compatible access**: Azure Data Lake Storage allows you to manage and access data just as you would with a [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). The new [ABFS driver](./abfs-driver.md) is available within Apache Hadoop environments to access data stored in Azure Data Lake Storage.
 
- **A superset of POSIX permissions**: The security model for Azure Data Lake Storage fully supports ACL and POSIX permissions along with some extra granularity specific to Azure Data Lake Storage. Settings may be configured through admin tools or through frameworks like Hive and Spark. 

    Authentication and identities are provided courtesy of integration with [Azure Active Directory](../../active-directory/index.md).

- **Multi-protocol and multi-model data access**: Azure Data Lake Storage is considered a **Multi-Modal** storage service as it provides both object store and file system interfaces to the _same_ data **at the same time**. This is achieved by providing multiple protocol endpoints that are able to access the same data. 
    
    Unlike other analytics solutions, data stored in Azure Data Lake Storage does not need to move or be transformed before you can run a variety of analytics tools. You can access data via traditional [Blob Storage APIs](../blobs/storage-blobs-introduction.md) (for example: ingest data via [Event Hubs Capture](../../event-hubs/event-hubs-capture-enable-through-portal.md)) and process that data using HDInsight or Azure Databricks at the same time. 

- **Cost effective**: Azure Data Lake Storage features low-cost storage capacity and transactions. As data transitions through the data lifecycle, billing rates change keeping costs to a minimum via built-in features such as [Azure Blob Storage Lifecycle](../common/storage-lifecycle-managment-concepts.md).

- **Available in all regions**: Azure Data Lake Storage is available for Azure Storage (StorageV2) accounts in all Azure regions. 

- **Works with Blob Storage tools, frameworks, and apps**: Azure Data Lake Storage is able to interface with a wide array of tools, frameworks, and applications that exist today for Blob Storage. 

- **Optimized protocol**: The `abfs` protocol is [optimized specifically](./abfs-driver.md) for big data analytics.

## Scalability
Azure Data Lake Storage and Azure Blob Storage are scalable by design. Each service is able to store and serve *many exabytes of data*. This amount of storage is available with throughput measured in gigabits per second (Gbps) at high levels of input/output operations per second (IOPS). Beyond just persistence, processing is executed at near-constant per-request latencies that are measured at the service, account, and file levels.

## Cost effectiveness
The architecture of Azure Data Lake Storage saves you significant amounts of money. One of the many benefits of building Azure Data Lake Storage on top of Azure Blob Storage is the [low-cost](https://azure.microsoft.com/pricing/details/storage) of storage capacity and cloud-based object storage transactions.  Unlike other cloud storage services, Azure Data Lake Storage enjoys several orders of magnitude of lower costs because data is not moved or transformed before performing analysis.

Additionally, features such as the [Hierarchical Namespace Service](./namespace.md) significantly improve the overall performance of many analytics jobs. This improvement in performance means that you require less compute power to process the same amount of data, resulting in a lower total cost of ownership (TCO) for the end to end analytics job.

<!--- ## Tailored for creating your data lake

The objective for an *Enterprise Data Lake (EDL)* is to refine vast amounts of raw data into fit-for-purpose data sets. In addition to a scalable and performant storage service, an EDL must also feature the following capabilities to realize this objective:

* **Universal Metadata Service:** This facility handles the assignment, management, and querying of metadata tags on any of the data in the EDL. ADFS provides XXXX which meets this requirement.

* **Universal Schema Catalog:** Data stored in an EDL flows through a process of ongoing refinement and enrichment by application of various analytics frameworks (eg. Hive and Spark). Many of the higher level analytics engines apply [Schema on Read] techniques that, while providing significant flexibility for semi-structured data, do eventually require a definition of the layout, or schema, of files. This schema information must be stored in a schema catalog.

    In order for this information to be leveraged by the full range of analytics engines available on the platform, the schema information must be able to be accessed in a variety of formats, ranging from the Hadoop-centric [Apache HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog) to those formats required by [SQL Data Warehouse](../../sql-data-warehouse/index.md) and [Analysis Services](../../analysis-services/index.md). 

    The Schema Catalog must also be available to be shared across all instances of analytics services used by an enterprise. To this end, the Schema Catalog is a stand-alone service.

* **Data Governance and Lineage:** TODO: Multiple drivers for this - veracity & accuracy of data... 

* **Data Sharing:** TODO: Sharing of data both within & without the organization, without copying and retaining lifecycle control...
-->

## Related concepts

The following articles describe some of the main concepts of Azure Data Lake Storage and detail how to store, access, manage, and gain insights from your data:

* [Hierarchical Namespace](./namespace.md)

<!--- TODO: Update links when articles available
* Scalability and Performance(./scalability-checklist.md)
* Benchmark Results(./benchmarks.md)
-->

## Next steps

* [Create a Storage account](./quickstart-create-account.md)
* [Create an HDInsight cluster with Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Use an Azure Data Lake Storage account in Azure Databricks](./quickstart-create-databricks-account.md) 