---
title: Connect Office 365 ATP data to Azure Sentinel | Microsoft Docs
description: Ingest Office 365 Advanced Threat Protection data into Azure Sentinel to gain visibility and build automated response scenarios.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''

ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2020
ms.author: yelevin

---
# Connect alerts from Office 365 Advanced Threat Protection (ATP) 

> [!IMPORTANT]
> Ingestion of Office 365 Advanced Threat Protection alerts is currently in public preview. This feature is provided without a service level agreement, and is not recommended for production workloads. For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
[Office 365 Advanced Threat Protection](https://docs.microsoft.com/microsoft-365/security/office-365-security/office-365-atp?view=o365-worldwide) (ATP) safeguards your organization against advanced **(targeted?)** threats posed by email messages, URL links, and collaboration tools. By ingesting Office 365 ATP alerts into Azure Sentinel, you'll include information about email- and URL-based threats into your security operations. You can then more comprehensively analyze security events across your organization and build playbooks for effective and immediate response.

The connector imports the following alerts:

- A potentially malicious URL click was detected 

- Email messages containing malware removed after delivery

- Email messages containing phish URLs removed after delivery 

- Email reported by user as malware or phish 

- Suspicious email sending patterns detected 

- User restricted from sending email 

These alerts can be seen by Office customers in the **Office Security and Compliance Center**.

## Prerequisites

- You must have read and write permissions on the Azure Sentinel workspace when you enable the connector.

- You must be an administrator or a security administrator on the Azure Sentinel workspace's tenant.

- You must have a valid license for [Office 365 ATP Plan 2](https://docs.microsoft.com/microsoft-365/security/office-365-security/office-365-atp?view=o365-worldwide#office-365-atp-plan-1-and-plan-2) (included with the Office 365 E5, Office 365 A5, and Microsoft 365 E5 licenses, and available for purchase separately). 

## Connect to Office 365 Advanced Threat Protection

If Office 365 Advanced Threat Protection is deployed, and if policies have been configured, the alerts can easily be ingested into Azure Sentinel.

1. In Azure Sentinel, select **Data connectors** from the navigation menu.

1. Select **Office 365 Advanced Threat Protection** in the connectors gallery, and select **Open connector page**.

1. In the **Configuration** section, click **Connect**. 

1. In the **Create incidents** section, click **Enable**.

1. To use the relevant schema to query Office 365 ATP alerts, search for **SecurityAlert** and specify the **Provider name** as **OATP**.

1. Select the **Next steps** tab to see and use the query samples and analytics rule templates bundled with the Office 365 ATP connector.

## Next steps
In this document, you learned how to connect Microsoft Defender ATP to Azure Sentinel. To learn more about Azure Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](quickstart-get-visibility.md).
- Get started [detecting threats with Azure Sentinel](tutorial-detect-threats.md).
