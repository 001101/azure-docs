---
title: 'Planned maintenance for Azure SQL Data Warehouse | Microsoft Docs'
description: Learn how to prepare for planned maintenance events to your Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''

ms.service: sql-data-warehouse
ms.custom: mvc,DBs & servers
ms.workload: "Active"
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: concepts
ms.date: 03/12/2018
ms.author: barbkess

---
# Planning for maintenance on your Azure SQL data warehouse

Learn how to prepare for planned maintenance events on your Azure SQL data warehouse.

## What is a planned maintenance event?
A planned maintenance event is a pre-planned time in which Microsoft performs an operation on your data warehouse service. To rollout upgrades, new features and patches, Microsoft needs to perform essential planned maintenance on the service from time to time. 

## How often is the service maintained?
On average, at least one planned maintenance event occurs each month. 

## Downtime notifications
Microsoft sends a notification prior to the start of each maintenance period. To perform the maintenance event, the service maintenance process cancels all running queries and drops connectivity to the data warehouse. The expected downtime for each maintenance event is approximately 30 minutes. The service maintenance process sends a notification when the maintenance is complete.

## Set up alerts with Azure Monitor

We recommend using [Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) to set up planned maintenance log alerts. The alerts enable you to plan for the required maintenance to occur at a time that minimizes the impact to your business. 

To setup notifications, see these [step by step instructions](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## Next steps
To learn more about monitoring your SQL data warehouse, see [Monitor your workload](sql-data-warehouse-manage-monitor.md)
