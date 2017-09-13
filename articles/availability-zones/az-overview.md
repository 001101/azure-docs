---
title: Availability Zones Overview | Microsoft Docs
description: This article provides an overview of Availability Zones in Azure.
services: 
documentationcenter:
author: markgalioto
manager: carmonm
editor:
tags:

ms.assetid:
ms.service:
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications). 
---

# Overview of Availability Zones in Azure (Preview)

Availability Zones are fault-isolated locations within an Azure region. An Availability Zone represents one or more datacenters within a region. Each Availability Zone has independent power source, network, cooling, and is logically separate from the other Availability Zones within the Azure region. The physical and logical separation of Availability Zones within a region, protects applications and data from zone-level failures. There is a minimum of three zones in all enabled regions. [Enable](az-overview.md#get-started-with-the-availability-zones-preview) the Availability Zones Preview in my subscription.

! conceptual view of one zone going down in a region (./media/az-overview/three-zones-per-region.png) John to provide artwork that shows Availability Zones.

## Enabled regions

- East US 2
- West Europe

## Availability Zone-aware services

The Azure services that support Availability Zones are:
|Service | API version |
|------- | ------------ |
| Linux Virtual Machines | 3-30-2017   |
| Windows Virtual Machines | 3-30-2017 |
| Public IP  | 8-01-2017  |
| Zonal Virtual Machine Scale Sets| 3-30-2017  |
| Managed Disks | 3-30-2017   |
| Software Load Balancer | 8-01-2017  |

## Supported virtual machines SKUs

- A2_v2
- D2_v2
- DS2_v2

## Get started with the Availability Zones preview

The Availability Zones preview is available in the East US 2 and West Europe regions for specific Azure services. 

1. Follow the instructions to [sign up for the Availability Zones preview](http://aka.ms/azenroll) in your subscription. 
2. Choose the Azure subscription to use.
3. Choose the Availability Zones-enabled region
4. Use one of the following links to start using Availability Zones with your service. 
    - Create a virtual machine
    - Create a public IP
    - Create a zonal virtual machine scale set
    - Create a Managed Disk
    - Create a Software Load Balancer

## Next steps
- Quickstart templates - this link comes from Raj.
- [Information on Azure Resiliency](https://azure.microsoft.com/features/resiliency)