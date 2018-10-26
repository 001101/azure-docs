---
title: Build highly available Azure Storage applications on zone-redundant storage (ZRS) | Microsoft Docs
description: Zone-redundant storage (ZRS) offers a simple way to build highly available applications. ZRS protects against hardware failures in the datacenter, and against some regional disasters.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.component: common
---

# Zone-redundant storage (ZRS): Highly available Azure Storage applications
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## Support coverage and regional availability
ZRS currently supports standard general-purpose v2 account types. For more information about storage account types, see [Azure storage account overview](storage-account-overview.md).

ZRS is available for block blobs, non-disk page blobs, files, tables, and queues.

ZRS is generally available in the following regions:

- US East
- US East 2
- US West 2
- US Central
- North Europe
- West Europe
- France Central
- Southeast Asia

Microsoft continues to enable ZRS in additional Azure regions. Check the [Azure Service Updates](https://azure.microsoft.com/updates/) page regularly for information about new regions.

## What happens when a zone becomes unavailable?
Your data is still available even if a zone becomes unavailable. Microsoft recommends that you continue to follow practices for transient fault handling. These practices include implementing retry policies with exponential back-off. When a zone is unavailable, Azure undertakes networking updates, such as DNS repointing. These updates may affect your application if you are accessing your data before the updates have completed.

ZRS may not protect your data against a regional disaster where multiple zones are permanently affected. Instead, ZRS offers resiliency for your data if it becomes temporarily unavailable. For protection against regional disasters, Microsoft recommends using geo-redundant storage (GRS). For more information about GRS, see [Geo-redundant storage (GRS): Cross-regional replication for Azure Storage](storage-redundancy-grs.md).

## Converting to ZRS replication
Migrating to or from LRS, GRS, and RA-GRS is straightforward. Use the Azure portal or the Storage Resource Provider API to change your account's redundancy type. With ZRS, however, migration isn't as straightforward. ZRS migration involves the physical data movement from a single storage stamp to multiple stamps within a region. 

There are two primary options for migration to or from ZRS: manually copy or move data to a new ZRS account from an existing account or request a live migration. Microsoft strongly recommends that you perform a manual migration. A manual migration provides more flexibility than a live migration, and you're in control of the migration timing.

To perform a manual migration, you have options:
- Use existing tooling like AzCopy, the storage SDK, and reliable third-party tools.
- If you're familiar with Hadoop or HDInsight, attach both source and destination (ZRS) account to your cluster. Then, parallelize the data copy process with something like DistCp.
- Build your own tooling that uses one flavor of the storage SDK.

A manual migration can result in some application downtime and sometimes you can't absorb that on your end. If downtime occurs, Microsoft provides a live migration option. A live migration is an in-place migration. Use your existing storage account while your data is migrated between source and destination storage stamps. During migration, you have the same level of durability and availability SLA as you do normally.

Live migration comes with the following restrictions:

- While Microsoft addresses your live migration request promptly, there's no guarantee as to when the migration will complete. If you need your data to be in ZRS by a certain time, then you should do a manual migration. Generally, the more data you have in your account, the longer it takes to migrate that data. 
- You may only perform a live migration from an account by using LRS or GRS replication. If your account uses RA-GRS, then you need to first migrate to one of these replication types before proceeding. This intermediary step removes the secondary read-only endpoint&mdash;which RA-GRS provides&mdash; before migration.
- Your account must contain data.
- You can only migrate data within the same region. If you want to migrate your data into a ZRS account located in a region different than the source account, then you must perform a manual migration.
- You can only use standard storage account types. 
- You can't migrate from a premium storage account.

Live migration requests go through the Azure Support portal. From the portal, you select the storage account you want to convert to ZRS.
1. Select **New Support Request**
2. Complete the Basics based on your account information. In the **Service** section, select **Storage Account Management** and the resource you want to convert to ZRS. 
3. Select **Next**. 
4. In the **Problem** section, 
    - **Severity**: Leave the default value as-is.
    - **Problem Type**: Select **Data Migration**.
    - **Category**: Select **Migrate to ZRS within a region**.
    - **Title**: Type a descriptive title, for example, **ZRS account migration**.
    - **Details**: Type additional details in the **Details** box, for example, I would like to migrate to ZRS from [LRS, GRS] in the ______ region. 
5. Select **Next**.
6. Verify that the contact information is correct on the **Contact information** blade.
7. Select **Create**.

A support person will contact you and provide any assistance you need. 

## ZRS Classic: A legacy option for block blobs redundancy
> [!NOTE]
> Microsoft will deprecate and migrate ZRS Classic accounts on March 31, 2021. More details will be provided to ZRS Classic customers before deprecation. 
>
> When ZRS becomes [generally available](#support-coverage-and-regional-availability) in a region, customers won't be able to create ZRS Classic accounts from the portal in that region. Using Microsoft PowerShell and Azure CLI to create ZRS Classic accounts is an option until ZRS Classic is deprecated.

ZRS Classic asynchronously replicates data across data centers within one to two regions. Replicated data may not be available unless Microsoft initiates failover to the secondary. A ZRS Classic account can't be converted to or from LRS, GRS, or RA-GRS. ZRS Classic accounts also don't have metrics or logging features.

ZRS Classic is available only for **block blobs** in general-purpose V1 (GPv1) storage accounts. For more information about storage accounts, see [Azure storage account overview](storage-account-overview.md).

To manually migrate ZRS account data to or from an LRS, ZRS Classic, GRS, or RA-GRS account, use one of the following tools: AzCopy, Azure Storage Explorer, Azure PowerShell, or Azure CLI. You can also build your own migration solution with one of the Azure Storage client libraries.

## See also
- [Azure Storage replication](storage-redundancy.md)
- [Locally-redundant storage (LRS): Low-cost data redundancy for Azure Storage](storage-redundancy-lrs.md)
- [Geo-redundant storage (GRS): Cross-regional replication for Azure Storage](storage-redundancy-grs.md)