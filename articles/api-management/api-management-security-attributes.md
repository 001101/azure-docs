---
title: Common security attributes for API Management
description: A checklist of common security attributes for evaluating API Management
services: load-balancer
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: load-balancer

ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin

---
# Common security attributes for API Management

Security is integrated into every aspect of an Azure service. This article documents the common security attributes built into Azure SQL Database

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## Preventative

| Security Attribute | Yes/No | Notes |
|---|---|--|
| Encryption at rest:<ul><li>Server-side encryption</li><li>Server-side encryption with customer-managed keys</li><li>Other encryption features (such as client-side, always encrypted, etc.)</ul>| Yes | Sensitive data such as certificates, keys, and secret-named values are encrypted with service-managed, per service instance keys. |
| Encryption in Transit:<ul><li>Express route encryption</li><li>In Vnet encryption</li><li>VNet-VNet encryption</ul>| Yes | [Express Route](../expressroute/index.yml) and VNET encryption is provided by [Azure networking](../virtual-network/index.yml). |
| Encryption Key Handling (CMK, BYOK, etc.)| No | All encryption keys are per service instance and are service managed. |
| Column Level Encryption (Azure Data Services)| N/A | |
| API calls encrypted| Yes | Management plane calls are made through [Azure Resource Manager](../azure-resource-manager/index.yml) over TLS. A valid JSON web token (JWT) is required.  Data plane calls can be secured with TLS and one of supported authentication mechanisms (e.g. client certificate or JWT).
 |

## Network Segmentation

| Security Attribute | Yes/No | Notes |
|---|---|--|
| Service Endpoint support| No | |
| vNET Injection support| Yes | |
| Network Isolation / Firewalling support| Yes | Using networking security groups (NSG) and Azure Application Gateway (or other software appliance) respectively. |
| Support for forced tunneling | Yes | Azure networking provides forced tunneling. |

## Detection

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| Yes | |

## IAM Support

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Access management - Authentication| Yes | |
| Access management - Authorization| Yes | |


## Audit Trail

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Control/Management Plan Logging and Audit| Yes | [Azure Monitor activity logs](../azure-monitor/platform/activity-logs-overview.md) |
| Data plane Logging and Audit| Yes | [Azure Monitor diagnostic logs logs](../azure-monitor/platform/diagnostic-logs-overview.md) and (optionally) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## Configuration Management

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| Yes | Using the [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |
