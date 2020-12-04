---
title: Troubleshooting client response timeouts and errors with API Management
description: Troubleshoot intermittent connection errors and related latency issues in API Management
author: vladvino
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.author: apimpm
ms.service: api-management
---

# Troubleshooting client response timeouts and errors with API Management

This article helps you troubleshoot intermittent connection errors and related latency issues in [Azure API Management](./api-management-key-concepts.md). Specifically, this topic will provide information and troubleshooting for the exhaustion of source address network translation (SNAT) ports. If you require more help, contact the Azure experts at [Azure Community Support](https://azure.microsoft.com/support/community/) or file a support request with [Azure Support](https://azure.microsoft.com/support/options/).

## Symptoms

Clients using your API Management (APIM) product may exhibit one or more of the following symptoms:

* Slow response times when calling your APIs
* Intermittent HTTP 500 errors
* Timeout error messages

## Cause

Often these symptoms occur when a large volume of concurrent client connections results in slow response times from your backend API due to source network address translation (SNAT) port limits.

Whenever a client calls one of your APIM APIs, Azure API Management service opens a SNAT port to access your backend API. As discussed in [Outbound connections in Azure](../load-balancer/load-balancer-outbound-connections.md), Azure uses source network address translation (SNAT) and a Load Balancer (not exposed to customers) to communicate with end points outside Azure in the public IP address space, as well as end points internal to Azure that are not taking advantage of [Virtual Network service endpoints](../virtual-network/virtual-network-service-endpoints-overview.md).

Each instance of API Management service is initially given a pre-allocated number of SNAT ports. That limit affects opening connections to the same host and port combination. SNAT ports are used up when you have repeated calls to the same address and port combination. Once a SNAT port has been released, the port is available for reuse as needed. The Azure Network load balancer reclaims SNAT ports from closed connections only after waiting four minutes.

A rapid succession of client requests to your API can quickly exhaust the pre-allocated quota of SNAT ports for your API Management instance.

## Mitigations and solutions

Avoiding the SNAT port problem means avoiding the creation of repetitive connections to the same host and port.

General strategies for mitigating SNAT port exhaustion are discussed in [Troubleshooting outbound connections failures](../load-balancer/troubleshoot-outbound-connection.md) from *Azure Load Balancer* documentation. Of these strategies, the following are applicable to API Management.

### Scale or upgrade your APIM instance

Each API Management instance is allocated a number of SNAT ports, based on APIM units and service tier.

TODO: Do we have [units/tier x ports] numbers for a table here?

You can allocate additional SNAT ports by scaling your API Management instance with additional units and/or upgrading to a higher service tier. For more info, see [Upgrade and scale an Azure API Management instance](upgrade-and-scale.md)

> [!NOTE]
> SNAT port usage is currently not available as a metric for autoscaling API Management units.

### Split backend URLs if multiple APIs are landing on the same IP

TODO more details

### Place your APIM and App Service together in a virtual network

TODO what about App Service Environment?

If your backend API is hosted on an Azure service that supports [service endpoints](../virtual-network/virtual-network-service-endpoints-overview.md) such as App Service, you can avoid SNAT port exhaustion issues by using [regional VNet Integration](./web-sites-integrate-with-vnet.md) and service endpoints or private endpoints. When you use regional VNet Integration and place service endpoints on the integration subnet, outbound traffic from your APIM instance to those services will not have outbound SNAT port restrictions. Likewise, if you use regional VNet Integration and private endpoints, you will not have any outbound SNAT port issues to that destination.

For details, see [How to use Azure API Management with virtual networks](api-management-using-with-vnet.md) and [Integrate App Service with an Azure virtual network](../app-service/web-sites-integrate-with-vnet.md).

### Place your APIM in a virtual network and route outbound calls to Azure firewall

TODO more details


## See also

* [Azure Load Balancer: Troubleshooting outbound connections failures](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: Troubleshooting intermittent outbound connection errors](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
