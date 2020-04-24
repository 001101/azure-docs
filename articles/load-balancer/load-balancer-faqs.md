---
title: Frequently asked questions
description: Answers to frequently asked questions about the Azure Load Balancer. 
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
---
# Frequently Asked Questions

## Are there data throughput limits for the Load Balancer?
There are no hard limits on data throughput. For public load balancers, the recommend ingress rate is 10 Gbps for Standard SKU and XXX Gbps for Basic SKU, with no egress limit. Internal load balancers have no ingress or egress throughput limits. It is important to consider the data throughput of your backend resources in all configurations.

 ## What are the different Load Balancing options in Azure?
 See the [load balancer technology guide](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)  for the available load balancing options and an in-depth explanation on the differences and recommended uses for each.

 ## How can I upgrade from a Basic Load Balancer to a Standard Load Balancer?
 See the [upgrade from Basic to Standard](upgrade-basic-standard.md) article for an automated script and guidance on upgrading from Basic SKU to Standard SKU.

 ## How is the load distributed across backend instances?
