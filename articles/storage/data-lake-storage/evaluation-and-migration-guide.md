---
title: 
description: 
services: storage
keywords: 
author: roygara
ms.topic: article
ms.author: rogarana
manager: twooley
ms.date: 06/01/2018
ms.service: storage
---

# Migration transition article

If you have finished your evaluation and feel ready to begin planning a migration to Azure Data Lake Storage Gen2. Be aware that the ease of a migration depends on a variety of factors, including the complexity of your app, the volume of data, cross-regional migration, mode of migration (live, one time migration, continuous, etc.), ACLs, associated data such as metastores, and post migration setup.

For an initial evaluation, we recommend copying some data into Azure Data Lake Storage Gen2 so you can get a good sense of it but, before you do that, there are some things which you should consider. Chief among them being the pattern you follow when migrating.

## Migration Patterns

Typically copying large amounts of data from source to destination will cost a lot of time, and so you would do it in two stages. A Bulk (or full) load and then an incremental copy.

* Bulk / full load - Copy all the data out of source into destination, settings some sort of marker for the last data copied.
* Incremental copy - Copy all the new data after the bulk load.

The incremental copy can be done via a scheduled job which migrates the incremental copy on a regular cycle, continuously catching up in a batch window. If you already have a data pipeline, then the *Dual Channel* mode can be leveraged. This mode will continuously catchup the data being ingested while the catchup hapens for the initial load.

Be aware that if you intend on migrating an application over you typically stop accepting writes to the source system after the bulk load. Ater which you update the app to point at the new store once the incremental copy has completed. This will involve some app downtime.

* Active / active synchronization - If you want to avoid downtime and you have some ability to keep source and destination systems in sync then you can synchronize your datasets and begin moving apps one at a time.

When doing an evaluation using active / active replication - it might make sense to initially set the solution to be a one way replication. Just to ensure that the test environment doesn't get replicated to the source environment. When you are ready to go live then you can set the replication to be two way and begin moving apps over.
 
* Dual Channel - if you have a framework like Azure Data Factory in front of your source systems then you can modify it to write to two different channels. One channel pointing to the old system and one pointing to the new system. After that you can start testing your app in the second system.

This setup may require bulk load in order to initialize the data. we recommend designing what a migration might look like for you. Microsoft will offer a broad range of tooling, support, and guidance but you should begin thinking what the migration effort would look like.

Additionally, if you are using Azure Data Factory, you may only need to replace the connector from Azure Data Lake Store to Azure Data Lake Storage. This would only be available if Azure Data Factory was already in use.

(Transition sentence here) you can take a look at [copying some data into Azure Data Lake Storage](Link to ingestion article).