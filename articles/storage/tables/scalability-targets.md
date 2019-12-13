---
title: Scalability and performance targets for Table storage
titleSuffix: Azure Storage
description: Learn about scalability and performance targets Azure storage accounts and for Table storage.
services: storage
author: tamram

ms.service: storage
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: tamram
ms.subservice: tables
---

# Scalability and performance targets for Table storage

This article details the scalability and performance targets for Azure storage accounts and for Table storage. The scalability and performance targets listed here are high-end targets, but are achievable. In all cases, the request rate and bandwidth achieved by your storage account depends upon the size of objects stored, the access patterns utilized, and the type of workload your application performs.

Be sure to test your application to determine whether its performance meets your requirements. If possible, avoid sudden spikes in the rate of traffic and ensure that traffic is well-distributed across partitions. For more information about how to design your application for optimal performance, see [Performance and scalability checklist for Table storage](storage-performance-checklist.md).

## Storage account scale limits

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## Table storage scale limits

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## See also

[Performance and scalability checklist for Table storage](storage-performance-checklist.md)
