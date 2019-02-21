---
title: Collecting data in Azure Sentinel | Microsoft Docs
description: Learn how to collect data in Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''

ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin

---
# Collect data from Azure Security Center


Azure Sentinel enables you to collect alerts from [Azure Security Center](../security-center/security-center-intro.md) and stream them into Azure Sentinel. 

## Prerequisites

- If you want to export alerts from Azure Security Center, you must be a contributor on the subscription whose logs you stream.

- You must have the [Azure Security Center Standard tier](../security-center/security-center-pricing.md) running on the subscription. If not, [upgrade your subscription to standard](https://azure.microsoft.com/en-us/pricing/details/security-center/).


## Connect to Azure AD

1. In Azure Sentinel, select **Data collection** and then click the **Azure Security Center** tile.
2. On the right, click **Connect** next to each subscription whose alerts you want to stream into Azure Sentinel.


## Next steps
In this document, you learned how to connect Azure Security Center to Azure Sentinel. To learn more about Azure Sentinel, see the following articles:

*