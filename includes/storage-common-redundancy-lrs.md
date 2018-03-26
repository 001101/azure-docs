---
 title: include file
 description: include file
 services: storage
 author: tamram
 ms.service: storage
 ms.topic: include
 ms.date: 03/26/2018
 ms.author: tamram
 ms.custom: include file
---

Locally-redundant storage (LRS) is designed to provide at least 99.999999999% (11 9's) durability of objects over a given year by replicating your data within a storage scale unit. A storage scale unit is hosted in a datacenter in the region in which you created your storage account. A write request to an LRS storage account returns successfully only after the data has been written to all replicas. These replicas each reside in separate fault domains and update domains within one storage scale unit.

A storage scale unit is a collection of racks of storage nodes. A fault domain (FD) is a group of nodes that represent a physical unit of failure and can be considered as nodes belonging to the same physical rack. An upgrade domain (UD) is a group of nodes that are upgraded together during the process of a service upgrade (rollout). The replicas are spread across UDs and FDs within one storage scale unit to ensure that data is available even if hardware failure impacts a single rack or when nodes are upgraded during a rollout.

LRS is the lowest cost replication option and offers the least durability compared to other options. In the event of a datacenter level disaster (for example, fire or flooding), all replicas might be lost or unrecoverable. To mitigate this risk, using either zone-redundant storage (ZRS) or geo-redundant storage (GRS) is recommended.

* LRS provides the highest maximum bandwidth of all of the Azure Storage replication options.
* If your application stores data that can be easily reconstructed in the event of data loss, you may opt for LRS.
* Some applications are restricted to replicating data only within a country due to data governance requirements. In some cases, the paired regions across which data is replicated for GRS accounts may be in another country. For more information on paired regions, see [Azure regions](https://azure.microsoft.com/regions/).

> [!IMPORTANT]
> You can change how your data is replicated after your storage account has been created. However, you may incur an additional one-time data transfer cost if you switch from LRS or ZRS to GRS or RA-GRS.
>
