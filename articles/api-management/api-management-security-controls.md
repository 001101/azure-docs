---
title: Security controls for Azure API Management
description: A checklist of security controls for evaluating API Management
services: api-management
author: msmbaldwin
manager: rkarlin
ms.service: api-management

ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin

---
# Security controls for API Management

This article documents the security controls built into API Management.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## Network

| Security Attribute | Yes/No | Notes |
|---|---|--|
| Service endpoint support| No | |
| VNet injection support| Yes | |
| Network isolation and firewalling support| Yes | Using networking security groups (NSG) and Azure Application Gateway (or other software appliance) respectively. |
| Forced tunneling support| Yes | Azure networking provides forced tunneling. |

## Monitoring & logging

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| Yes | |
| Control and management plane logging and audit| Yes | [Azure Monitor activity logs](../azure-monitor/platform/activity-logs-overview.md) |
| Data plane logging and audit| Yes | [Azure Monitor diagnostic logs](../azure-monitor/platform/diagnostic-logs-overview.md) and (optionally) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## Identity

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Authentication| Yes | |
| Authorization| Yes | |

## Data Protection

| Security Attribute | Yes/No | Notes |
|---|---|--|
| Server-side encryption at rest: Microsoft managed keys | Yes (service-side encryption only) | Sensitive data such as certificates, keys, and secret-named values are encrypted with service-managed, per service instance keys. |
| Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Yes | [Express Route](../expressroute/index.yml) and VNet encryption is provided by [Azure networking](../virtual-network/index.yml). |
| Server-side encryption at rest: customer managed keys (BYOK) | No | All encryption keys are per service instance and are service managed. |
| Column level encryption (Azure Data Services)| N/A | |
| API calls encrypted| Yes | Management plane calls are made through [Azure Resource Manager](../azure-resource-manager/index.yml) over TLS. A valid JSON web token (JWT) is required.  Data plane calls can be secured with TLS and one of supported authentication mechanisms (for example, client certificate or JWT).
 |

## Configuration management

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| Yes | Using the [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |

## Vulnerability scans false positives

This section documents common vulnerabilities, which do not affect Azure API Management.

| Vulnerability               | Description                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed is vulnerability in the implementation of the TLS SessionTicket extension found in some F5 products. It allows the leakage ("bleeding") of up to 31 bytes of data from uninitialized memory. This is caused by the TLS stack padding a Session ID, passed from the client, with data to make it 32 bits long. |
