---
title: State storage options on Azure Service Fabric Mesh | Microsoft Docs
description: Learn about reliably storing state in Service Fabric Mesh applications running on Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords:  
author: rwike77
ms.author: ryanwi
ms.date: 10/17/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt 
---
# State management with Service Fabric
Service Fabric supports many different options for state storage. For a conceptual overview of the state management patterns and Service Fabric, see [Service Fabric Concepts: State](/azure/service-fabric/service-fabric-concepts-state). All these same concepts apply whether your services run inside or outside of Service Fabric Mesh. 

With Service Fabric Mesh, you can easily deploy a new application and connect it to an existing data store hosted in Azure. Besides using any remote database, there are several options for storing data, depending on whether the service desires local or remote storage. 

## Reliable collections
Reliable Collections give you highly available, scalable, low-latency, transactional storage for cloud applications.  You can add a library to your service which implements data structures like queues and key-value pairs. Reliable Collections do not require the Service Fabric runtime, so you can use them outside of a Service Fabric environment.

When used in a Service Fabric environment, service state is persisted to the local disk and then replicated and partitioned out to secondaries. This means that:
* All reads are local, which results in low latency and high-throughput reads.
* All writes incur the minimum number of network IOs, which results in low latency and high-throughput writes.
* Data is persisted to disk for durability against large-scale outages (for example, a datacenter power outage).
* Data is also persisted to multiple secondaries.  When a service crashes or fails over, it starts up again on a node that already has your data on disk.  Fail over is extremely fast.

![Reliable Collections][image2]

When used outside of a Service Fabric environment, service state is still persisted to the local disk.  There is no replication, however, so you do not get the reliability and availability that you would in a Service Fabric environment.

![Reliable Collections][image1]

 
## Volumes
Containers often make use of temporary disks. Temporary disks are ephemeral, however, so you get a new temporary disk and lose the information when a container crashes. It is also difficult to share information on temporary disks with other containers. Volumes are directories that get mounted inside your container instances that you can use to persist state. Volumes give you general-purpose file storage and allow you to read/write files using normal disk I/O file APIs. The Volume resource describes how to mount a directory and which backing storage to use.  You can choose either Azure File storage or Service Fabric Volume disk to store data.    

![Volumes][image3]

### Service Fabric volume disk
Service Fabric Volume disk is a Docker volume driver used to mount a local volume to a container. Service Fabric Volume disk uses [Reliable Collections](#reliable-collections) to replicate data to local disks.  Reads/writes are local operations and very fast.  Data is replicated out to secondary nodes, making it highly available. Fail over is also fast.  When a container crashes, it fails over to a node that already has a copy of your data.

### Azure Files volume driver
Azure Files volume driver is a Docker volume driver used to mount an Azure Files share to a container. Azure File storage uses network storage, so reads and writes take place over the network. Compared to Service Fabric Volume disk, Azure Files storage is less performant but provides a cheaper and fully reliable data option.  For an example, see [How to deploy an app with Azure Files volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).
    
## Next steps

For information on the application model, see [Service Fabric resources](service-fabric-mesh-service-fabric-resources.md)

[image1]: ./media/service-fabric-mesh-storing-state/ReliableCollections1.png
[image2]: ./media/service-fabric-mesh-storing-state/ReliableCollections2.png
[image3]: ./media/service-fabric-mesh-storing-state/volumes.png