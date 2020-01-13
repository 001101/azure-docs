---
title: What is Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Overview of Azure Load Balancer features, architecture, and implementation. Learn how the Load Balancer works and how to use it in the cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for. 
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2019
ms.author: allensu

---

# What is Azure Load Balancer?

*Load balancing* refers to evenly distributing load (incoming network traffic) across a group of backend resources or servers. Azure offers a [variety of load balancing options](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) that you can choose from based on your need. This document covers the Azure Load Balancer.

Azure Load Balancer operates at layer four of the Open Systems Interconnection (OSI) model. It's the single point of contact for clients. Load balancer distributes new inbound flows that arrive at the load balancer's front end to back-end pool instances. These flows are according to configured load-balancing rules and health probes. The back-end pool instances can be Azure Virtual Machines or instances in a virtual machine scale set.

>[!NOTE]
> Microsoft recommends Standard Load Balancer.
Standalone VMs, availability sets, and virtual machine scale sets can be connected to only one SKU, never both. Load Balancer and the public IP address SKU must match when you use them with public IP addresses. Load Balancer and public IP SKUs aren't mutable.

## Why use Azure Load Balancer?
With Azure Load Balancer, you can scale your applications and create highly available services. 
Load balancer supports both inbound and outbound scenarios. Load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.

A **[public load balancer](./concepts-limitations.md#publicloadbalancer)** can provide outbound connections for virtual machines (VMs) inside your virtual network. These connections are accomplished by translating their private IP addresses to public IP addresses. Public Load Balancers are used to load balancer internet traffic to your VMs.

An **[internal (or private) load balancer](./concepts-limitations.md#internalloadbalancer)** is used where private IPs are needed at the frontend only. Internal load balancers are used to load balance traffic inside a virtual network. A load balancer frontend can be accessed from an on-premises network in a hybrid scenario.

<div align="center">
  <img src='./media/load-balancer-arm/load-balancer-arm.png'>
</div>

For more information on the individual load balancer components, see [Azure Load Balancer components and limitations](./concepts-limitations.md)

Internet clients send webpage requests to the public IP address of a web app on TCP port 80. Azure Load Balancer distributes the requests across the three VMs in the load-balanced set. For more information about Load Balancer algorithms, see [Load Balancer concepts](load-balancer-overview.md#load-balancer-concepts).

Azure Load Balancer distributes network traffic equally among multiple VM instances by default. You can also configure session affinity. For more information, see [Configure the distribution mode for Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Internal Load Balancer

An internal Load Balancer directs traffic only to resources that are inside a virtual network or that use a VPN to access Azure infrastructure, in contrast to a public Load Balancer. Azure infrastructure restricts access to the load-balanced front-end IP addresses of a virtual network. Front-end IP addresses and virtual networks are never directly exposed to an internet endpoint. Internal line-of-business applications run in Azure and are accessed from within Azure or from on-premises resources.

An internal Load Balancer enables the following types of load balancing:

* **Within a virtual network**: Load balancing from VMs in the virtual network to a set of VMs that are in the same virtual network.
* **For a cross-premises virtual network**: Load balancing from on-premises computers to a set of VMs that are in the same virtual network.
* **For multi-tier applications**: Load balancing for internet-facing multi-tier applications where the back-end tiers aren't internet-facing. The back-end tiers require traffic load balancing from the internet-facing tier. See the next figure.
* **For line-of-business applications**: Load balancing for line-of-business applications that are hosted in Azure without additional load balancer hardware or software. This scenario includes on-premises servers that are in the set of computers whose traffic is load balanced.

![Internal Load Balancer example](./media/load-balancer-overview/IC744147.png)

*Figure: Balancing multi-tier applications by using both public and internal Load Balancer*

## Pricing

Standard Load Balancer usage is charged.

* Number of configured load-balancing and outbound rules. Inbound NAT rules don't count in the total number of rules.
* Amount of data processed inbound and outbound independent of rules.

For Standard Load Balancer pricing information, see [Load Balancer pricing](https://azure.microsoft.com/pricing/details/load-balancer/).

Basic Load Balancer is offered at no charge.

## SLA

For information about the Standard Load Balancer SLA, see [SLA for Load Balancer](https://aka.ms/lbsla).

## Next steps

See [Create a public Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) to get started with using a Load Balancer.

For more information on Azure Load Balancer limitations and components, see [Azure Load Balancer concepts and limitations](./concepts-limitations.md)
