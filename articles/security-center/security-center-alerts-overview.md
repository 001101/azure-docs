---
title: Security Alerts in Azure Security Center | Microsoft Docs
description: This article explains the different types of security alerts in Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''

ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/25/2019
ms.author: monhaber

---
# Security Alerts

This article introduces the different kinds of alerts available in Azure Security Center (ASC).

## What are security alerts?

Alerts are the notifications that Security Center generates when it detects threats to your resources. It prioritizes and lists the alerts along with information needed to quickly investigate the problem and recommendations for how to remediate an attack.

To detect real threats and reduce false positives, Security Center collects, analyzes, and integrates log data from your Azure resources, the network, and connected partner solutions, like firewall and endpoint protection solutions. Security Center analyzes this information, often correlating information from multiple sources, to identify threats. To learn more on detecting and responding to threats, see [How Security Center detects and responds to threats](security-center-detection-capabilities.md#asc-detects).

The following topics guide you through the different ASC alerts according to resource types:

* [IaaS VMs & Servers Alerts](security-center-alerts-iaas.md)
* [Native computer alerts](security-center-compute.md)
* [Data services alerts](security-center-data-services.md)
* [Service layer alerts](security-center-alerts-service-layer.md)

ASC monitors the resources whether deployed on Azure or deployed on other on-premises and hybrid cloud environments.  

*  

* To see which platforms and features are protected by ASC, see [Platforms and features supported by Azure Security Center](security-center-os-coverage.md).  

* To understand what are security incidents and how ASC responds to them, see [How to handle Security Incidents in Azure Security Center](security-center-incident.md). 

* To learn how to manage the alerts you receive, see [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md).  

* For information on to how validate Security Center is properly configured and to stimulate a test alert, see [Alerts Validation in Azure Security Center](security-center-alert-validation.md).  


## Upgrade to Standard for advanced detections

To set up advanced detections, upgrade to Azure Security Center Standard. 

1. From the Security Center menu, select **Security Policy**.
2. For the subscriptions you would like to move to Standard tier, click **Edit settings**. 
3. From the Settings page, select **Pricing Tier**. 
   A free trial is available for a month. To learn more see the [pricing page](https://azure.microsoft.com/en-us/pricing/details/security-center/). 

## Next steps

In this article, you learned about the different types of security alerts in Security Center. To learn more about Security Center, see the following:

* [Handling security incident in Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-incident)

* [Azure Security Center detection capabilities](https://docs.microsoft.com/en-us/azure/security-center/security-center-detection-capabilities)

* [Azure Security Center planning and operations guide](https://docs.microsoft.com/en-us/azure/security-center/security-center-planning-and-operations-guide)

* [Azure Security Center FAQ](https://docs.microsoft.com/en-us/azure/security-center/security-center-faq): Find frequently asked questions about using the service.

