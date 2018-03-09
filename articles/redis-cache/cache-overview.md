---
title: What is Azure Redis Cache? | Microsoft Docs
description: Learn what Azure Redis Cache is and how it is commonly used.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: overview
ms.date: 02/15/2018
ms.author: wesmc
ms.custom: mvc

---
# What is Azure Redis Cache?

Caching is a common technique that typically aims to improve the performance and scalability of systems that rely heavily on backend data-stores. These improvements are made by temporarily copying frequently accessed data to fast storage located close to the application. With open source [Redis cache](https://redis.io/), this fast storage is located in-memory with the application. Depending on the features of the caching technology used, the cache can be used for more than just caching.

Azure Redis Cache is based on the popular open source [Redis cache](https://redis.io/) which is used as an in-memory data structure store, distributed non-relational database, cache, and message broker. It is used to improve the performance of your application by taking advantage of the low-latency, high-throughput performance of the Redis engine. You can read more about Redis Cache from the [Introduction to Redis](https://redis.io/topics/introduction) page.

Using Azure Redis Cache gives you access to a secure, dedicated [Redis cache](https://redis.io/), managed by Microsoft, hosted within Azure, and accessible to any application within Azure. 


## Common patterns for using Azure Redis Cache

There are many common patterns where Redis Cache is used to support application architecture or to improve application performance.  Some of the most common would include the following:

* **Cache-Aside** -  Since a database can be large, loading an entire database into a cache is not a recommended approach. It is common to use the [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) pattern to load data items into the cache only as needed. When the system makes changes to the backend data, it can at that time also update the cache, which is distributed with other clients. Additionally, the system can set an expiration on data items, or use an eviction policy to cause data updates to be reloaded into the cache.

  For an example of the cache-aside pattern with a leaderboard ASP.NET application, see the [Create a cache-aside leaderboard on ASP.NET](cache-web-app-cache-aside-leaderboard.md).

* **Content Caching** - Most web pages are generated from templates with headers, footers, toolbars, menus, etc. They don't actually change often and should not be generated dynamically. Using an in-memory cache, like Azure Redis Cache, will give your web servers quick access to this type of static content compared to backend datastores. This pattern reduces processing time and server load that would be required to generate content dynamically. This pattern allows web servers to be more responsive, and can allow you to reduce the number of servers needed to handle loads.

  Azure Redis Cache provides the Redis Output Cache Provider to help support this pattern with ASP.NET. For more information, see [Output cache provider](cache-aspnet-output-cache-provider.md).

* **User session caching** - This pattern is commonly used with shopping carts and other user history type information that a web application may want to associate with user cookies. Storing too much in a cookie can have a negative impact on performance as the cookie size grows and is passed and validated with every request. A typical solution is use the cookie as a key to query the data in a backend database. Using an in-memory cache, like Azure Redis Cache, to associate information with a user is much faster than interacting a full relational database.

  Azure Redis Cache provides the Redis Session State Provider to help support this pattern with ASP.NET. For more information, see [Session state provider](cache-aspnet-session-state-provider.md).

* **Job and message queuing** - When applications receive requests, often the operations associated with the request take additional time to execute. It is a common pattern to defer longer running operations by adding them to a queue, which is processed later, and possibly by another server. This method of deferring work is called task queuing. There are many software components designed to support task queues. Redis Cache is also serves this purpose well as a distributed queue.

* **Distributed transactions** - It is a common requirement for applications to be able to execute a series of commands against a backend data-store as a single operation (atomic). All commands must succeed, or all must be rolled back to the initial state. Redis Cache supports executing a batch of commands as a single operation in the form of [Transactions](https://redis.io/topics/transactions).


## Azure Redis Cache offerings

Azure Redis Cache is available in the following tiers: 

  * **Basic** - Single node, multiple sizes, ideal for development/test and non-critical workloads. The Basic tier has no service-level agreement (SLA).

  * **Standard** - A replicated cache in a two-node primary/secondary configuration managed by Microsoft, with a high-availability SLA.

  * **Premium** - The Premium-tier is an Enterprise ready tier, which includes all of the Standard tier features and more listed below.


## Advantages of Premium-Tier caches

The following points describe some of the advantages of Premium-tier Azure Redis Cache. The [Redis Cache Pricing](https://azure.microsoft.com/pricing/details/cache/) page also provides a detailed comparison of each tier.

To scale to the premium tier from one of the other tiers, choose one of the premium tiers in the Change pricing tier blade. You can also scale your cache using PowerShell and CLI. For step-by-step instructions, see [How to Scale Azure Redis Cache](cache-how-to-scale.md) and [How to automate a scaling operation](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

  
  * **Better performance compared to Standard or Basic tier** - Caches in the Premium tier are deployed on hardware that has faster processors and provides better performance compared to the Basic or Standard Tier. Premium tier Caches have higher throughput and lower latencies. This advantage means the throughput for a cache of the same size will be higher in Premium compared to Standard tier. For example, current [Azure Redis Cache performance benchmarks](cache-faq.md#azure-redis-cache-performance) for a 1 KB payload, using non-SSL, show the throughput for a 53 GB P4 (Premium) cache to be 400 K requests per second. The same benchmark for a 53 GB C6 (Standard) cache is 126 K requests per second. For more information about size, throughput, and bandwidth with premium caches, see [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

  * **Redis data persistence** - The Premium tier allows you to persist the cache data in an Azure Storage account. In a Basic/Standard cache, all the data is stored only in memory. In the event of underlying infrastructure issues, there can be potential data loss. We recommend using the Redis data persistence feature in the Premium tier to increase resiliency against data loss. Azure Redis Cache offers RDB and AOF (coming soon) options in [Redis persistence[(http://redis.io/topics/persistence).

    For instructions on configuring persistence, see [How to configure persistence for a Premium Azure Redis Cache](cache-how-to-premium-persistence.md).

  * **Redis cluster** - If you want to create caches larger than 53 GB or want to shard data across multiple Redis nodes, you can use Redis clustering, which is available in the Premium tier. Each node consists of a primary/replica cache pair managed by Azure for high availability. Redis clustering gives you maximum scale and throughput. Throughput increases linearly as you increase the number of shards (nodes) in the cluster. For example, if you create a P4 cluster of 10 shards, then the available throughput is 250 K *10 = 2.5 Million requests per second. For more information about size, throughput, and bandwidth with premium caches, see the [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

    To get started with clustering, see [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-clustering.md).

  * **Enhanced security and isolation** - Caches created in the Basic or Standard tier are accessible on the public internet. Access to the Cache is restricted based on the access key. With the Premium tier, you can further ensure that only clients within a specified network can access the Cache. You can deploy Redis Cache in an [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). You can use all the features of VNet such as subnets, access control policies, and other features to further restrict access to Redis.

    For more information, see [How to configure Virtual Network support for a Premium Azure Redis Cache](cache-how-to-premium-vnet.md).

  * **Import/Export** - Import/Export is an Azure Redis Cache data management operation that allows you to import data into Azure Redis Cache or export data from Azure Redis Cache. These import/export operations use a Redis Cache Database (RDB) snapshot from a Premium cache to a page blob in an Azure Storage Account. This advantage enables you to migrate between different Azure Redis Cache instances or populate the cache with data before use.

    Import can be used to bring Redis compatible RDB file(s) from any Redis server running in any cloud or environment, including Redis running on Linux, Windows, or any cloud provider such as Amazon Web Services and others. Importing data is an easy way to create a cache with pre-populated data. During the import process, Azure Redis Cache loads the RDB files from Azure storage into memory and then inserts the keys into the cache.

    Export allows you to export the data stored in Azure Redis Cache to Redis compatible RDB file(s). You can use this feature to move data from one Azure Redis Cache instance to another or to another Redis server. During the export process, a temporary file is created on the VM that hosts the Azure Redis Cache server instance, and the file is uploaded to the designated storage account. When the export operation completes with either a status of success or failure, the temporary file is deleted.

    For more information, see [How to import data into and export data from Azure Redis Cache](cache-how-to-import-export-data.md).

  * **Schedule updates** - The scheduled updates feature allows you to designate a maintenance window for your cache. When the maintenance window is specified, any Redis server updates are made during this window. To designate a maintenance window, select the desired days and specify the maintenance window start hour for each day. The maintenance window time is expressed in UTC.

    > [!NOTE]
    > Only Redis server updates are made during the scheduled maintenance window. The maintenance window does not apply to Azure updates or updates to the VM operating system.
    > 
    > 

    For more information, see [Schedule updates](cache-administration.md#schedule-updates) and [Schedule updates FAQ](cache-administration.md#schedule-updates-faq).

  * **Geo-replication** - Geo-replication provides a mechanism for linking two Premium tier Azure Redis Cache instances. One cache is designated as the primary linked cache, and the other as the secondary linked cache. The secondary linked cache becomes read-only, and data written to the primary cache is replicated to the secondary linked cache. This functionality can be used to replicate a cache across Azure regions. This replication is not required to cross any geographical region. It also can be used as an intra region resiliency feature.

    For more information, see [How to configure Geo-replication for Azure Redis Cache](cache-how-to-geo-replication.md).


## Features common to all cache tiers

  The following are some of the features that are available for all tiers:

  * **Firewall rules** - This feature allows you to configure firewall rules on all tiers. This feature further enhances security by restricting IP addresses or IP address ranges that are allowed to communicate with your cache. By default, Connections are allowed from any IP address.

    For more information, see [Firewall configuration](cache-configure.md#firewall).

  * **Reboot** - The feature allows you to reboot one or more nodes of your cache on-demand. This feature allows you to test your application for resiliency in the event of a failure. 
  
    >[!NOTE]
    >Reboot functionality is now enabled for all Azure Redis Cache tiers.
    >
    >    
  
    You can reboot the following nodes:

    * Master node of your cache
    * Slave node of your cache
    * Both master and slave nodes of your cache
    * When using a premium cache with clustering, you can reboot the master, slave, or both nodes for individual shards in the cache

    For more information, see [Reboot](cache-administration.md#reboot) and [Reboot FAQ](cache-administration.md#reboot-faq).



## Next Steps

* [Create your Azure Redis Cache](cache-create-first-cache.md)  
  Jump right in and create your first Azure Redis Cache using the Azure Portal. 
* [ASP.NET Web App Quickstart](cache-web-app-howto.md)  
  Create a simple ASP.NET web app that uses an Azure Redis Cache.
* [.NET Quickstart](cache-dotnet-how-to-use-azure-redis-cache.md)  
  Create a .NET app that uses an Azure Redis Cache.
* [Node.js Quickstart](cache-nodejs-get-started.md)  
  Create a simple Node.js app that uses an Azure Redis Cache.
* [Java Quickstart](cache-java-get-started.md)  
  Create a simple Java app that uses an Azure Redis Cache.
* [Python Quickstart](cache-python-get-started.md)  
  Create a Python app that uses an Azure Redis Cache.
