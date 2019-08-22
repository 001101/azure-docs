---
title: Azure HPC Cache prerequisites
description: Prerequisites for using Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
---

# Prerequisites for Azure HPC Cache

Before using the Azure Portal to create a new Azure HPC Cache, make sure your environment meets these requirements.

## Supported regions

Azure HPC Cache is available in these Azure regions: 

* East US 
* East US 2 
* North Europe
* West Europe
* Southeast Asia
* West US 2

## Azure subscription

A paid subscription is recommended. [ xxx Have we tested this with free subscriptions or partner subscriptions yet? Will we? xxx ]

## Network infrastructure

Two network-related options should be set up before you can use your cache:

* A dedicated subnet for the Azure HPC Cache instance
* DNS support so that the cache can access storage and other resources 

### Cache subnet

The Azure HPC Cache needs a dedicated subnet with these qualities:

* The subnet must have at least 64 IP addresses available.
* The subnet cannot host any other VMs, even for related services like client machines.
* If you use multiple cache instances, each one needs its own subnet.

The best practice is to create a new subnet for the cache. You can create a new virtual network and subnet when you create the Azure HPC Cache instance.

### DNS access

The Azure HPC Cache needs DNS to access resources outside its virtual network. Depending on which resources you are using, you might need to set up a customized DNS server and configure forwarding between that server and Azure DNS servers: 

* To access Azure Blob storage endpoints and other internal resources, you need the Azure-based DNS server.
* To access on-premises storage, you need to configure a custom DNS server that can resolve your storage hostames.

If you only need access to Blob storage, you can use the default Azure-provided DNS server for your cache. However, if you need access to other resources, you should create a custom DNS server and configure it to forward any Azure-specific resolution requests to the Azure DNS server. (A simple DNS server also can be used to load balance client connections among all the available cache mount points.)

Learn more about Azure virtual networks and DNS server configurations in [Name resolution for resources in Azure virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).


xxx stopped here 08/19 xxx 

## Permissions

* The Azure HPC Cache needs to be able to create virtual network interfaces (NICs). The user who creates the cache must have sufficient privileges in the subscription to create NICs. There are several ways to authorize this access; read [Additional prerequisites](media/preview-prereqs.md) to learn more.

* If using Blob storage, the Azure HPC Cache instance needs authorization to access your storage account as a Storage Account Contributor. Details are [below](#add-the-access-control-role-to-your-account).

## Storage infrastructure

* If you want to use Azure Blob storage with the Azure HPC Cache, you need a compatible storage account and an empty Blob container.

  You can add storage targets at the time you create the cache, or afterward.

  To create a compatible storage account, use these settings:

  * Performance: **Standard**
  * Account kind: **StorageV2 (general purpose v2)**
  * Replication: **Locally redundant storage (LRS)**
  * Access tier (default): **Hot**

  It is a good practice to use a storage account in the same location as your cache.

* Authorize the cache to access your Azure storage account by assigning it the Storage Account Contributor role as described [below](#add-the-access-control-role-to-your-account). If you are not the storage account owner, have the owner do this step.

* On-premises NFS storage must be a compatible hardware/software platform. Contact the Azure HPC Cache team for details.