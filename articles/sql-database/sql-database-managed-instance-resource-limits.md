---
title: Azure SQL Database resource limits - managed instance | Microsoft Docs
description: This article provides an overview of the Azure SQL Database resource limits for managed instances. 
services: sql-database
ms.service: sql-database
ms.subservice: 
ms.custom:
ms.devlang: 
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 10/02/2018
---
# Overview Azure SQL Database Managed Instance resource limits

This article provides an overview of the Azure SQL Database Managed Instance resource limits and provides information how to create request to increase default subscription limits. 

> [!NOTE]
> For other Managed Instance limitations, not specific to individual subscription, see [vCore-based purchasing model](sql-database-managed-instance.md#vcore-based-purchasing-model) and [Managed Instance service tiers](sql-database-managed-instance.md#managed-instance-service-tiers).

## Default subscription-level limits per region

Managed Instance currently supports deployment only on the following types of subscriptions:

- Enterprise Agreement (EA)
- Pay-as-you-go
- Cload Service Provider (CSP)

> [!NOTE]
> This limitation on support for only some subscription types is temporary.

Managed Instances has two default subscription-level limits per Azure region. Different subscription types have different regional limits. These limits can be increased by creating special support request in the Azure portal for the subscription with issue type **Quota**:

- **Subnet limit**: The maximum number of subnets where managed instances are deployed
- **Instance number limit**: The maximum number of instances per region

> [!IMPORTANT]
> When planning your deployments, consider that a Business Critical (BC) instance (due to added redundancy) generally consumes 4x more capacity than a General Purpose (GP) instance. So, for your calcutations, 1 GP instance = 1 instance unit and 1 BC instnace = 4 instance units. To simplify your consumption analysis against the default limits, summarize the instance units across all subnets in the region where Managed Instances are deployed and compare the results with the instance unit limits for your subscription type.

## Default limits by subscription type

|Subscription type| Max number of Managed Instance subnets | Max number of instances |Max number of GP managed instances*|Max number of BC managed instances*|
| :---| :--- | :--- |:--- |:--- |
|Pay-as-you-go|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* You can either deploy 1 BC or 4 GP instances in one subnet, so that total number of “instance units” in a region never exceeds 4.

** Maximum number of instances in one service tier applies if there are no instances in another service tier. In case you plan to mix GP and BC instances within same subnet, use the following section as a reference for allowed combinations. As a simple rule, by default total number of subnets cannon exceed 3, while total number of “instance units” cannot exceed 12. 

## Deployment options for GP and BC deployments within the same subnet 

The following examples cover deployment cases with non-empty subnets. 

|Number of subnets|Subnet 1|Subnet 2|Subnet 3|
|:---|:---|:---|:---|
|1|0 BC and up to 12 GP<br>1 BC and up to 8 GP<br>2 BC and up to 4 GP<br>3 BC|N/A| N/A|
|2|0 BC, up to 4 GP|0 BC, up to 8 GP<br>1 BC, up to 4 GP<br>2 BC|N/A|
|2|1 BC|0 BC, up to 8 GP<br>1 BC, up to 4 GP<br>2 BC|N/A|
|2|2 BC|0 BC, up to 8 GP<br>1 BC, up to 4 GP<br>2 BC|N/A|
|3|1 BC, 0 GP|1 BC, 0 GP|0 BC, up to 4 GP|
|3|1BC, 0 GP|0 BC, up to 4 GP|1 BC, 0 GP|
|3|0 BC, up to 4 GP|1 BC, 0 GP|1BC, 0 GP|

## Obtaining a larger quota for SQL Managed Instance

To initiate the process of obtaining a larger quota:

1. Open **Help + support**, and click **New support request**. 

   ![Help and Support](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. On the Basics tab for the new support request:
   - For **Issue type**, select **Service and subscription limits (quotas)**.
   - For **Subscription**, select your subscription.
   - For **Quota type**, select **SQL Database Managed Instance**.
   - For **Support plan**, select your support plan.

     ![Issue type quota](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Click **Next**.
4. On the Problem tab for the new support request:
   - For **Severity**, select the severity level of the problem.
   - For **Details**, provide additional information about your issue, including error messages.
   - For **File upload**, attach a file with more information (up to 4 MB).

     ![Problem details](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > A valid request should include: 
     > - Region in which limit needs to be changed 
     > - Required number of instances, per service tier in existing subnets after the quota increase (if any of the existing subnet needs to be expanded). 
     > - Required number of new subnets and total number of instances per service tier within the new subnets (if you need to deploy managed instances in new subnets).
5. Click **Next**.
6. On the Contact Information tab for the new support request, enter preferred contact method (email or phone) and the contact details.
7. Click **Create**.

## Next steps 

- For more information about Managed Instance see [What is a Managed Instance?](sql-database-managed-instance.md). 
- For pricing information, see [SQL Database Managed Instance pricing](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- To learn how to create your first Managed Instance, see [Quick-start guide](sql-database-managed-instance-get-started.md).