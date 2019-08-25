---
title: Handling security alerts in Azure Security Center | Microsoft Docs
description: This document helps you to use Azure Security Center capabilities to handle security incidents.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''

ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin

---
# Handling Security Incidents in Azure Security Center
Triaging and investigating security alerts can be time consuming for even the most skilled security analysts, and for many it is hard to even know where to begin. By using [analytics](security-center-detection-capabilities.md) to connect the information between distinct [security alerts](security-center-managing-and-responding-alerts.md), Security Center can provide you with a single view of an attack campaign and all of the related alerts – you can quickly understand what actions the attacker took and what resources were impacted.

This document discusses how to use security alert capability in Security Center to assist you handling security incidents.

## What is a security incident?
In Security Center, a security incident is an aggregation of all alerts for a resource that align with [kill chain](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) patterns. Incidents appear in the [Security Alerts](security-center-managing-and-responding-alerts.md) tile and blade. An Incident will reveal the list of related alerts, which enables you to obtain more information about each occurrence.

## Managing security incidents
You can review your current security incidents by looking at the security alerts tile. Access the Azure Portal and follow the steps below to see more details about each security incident:

1. On the Security Center dashboard, you will see the **Security alerts** tile.

1. Click on this tile to expand it and if a security incident is detected, it will appear under the security alerts graph. Notice that the security incident description has a different icon compared to other alerts.

    ![View security incidents](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Click on an incident to view the details. The **incident** blade displays details, if the incident is still active, and the remediation steps to take. In the bottom pane, the alerts included in this incident are listed.

    [Respond to security incidents in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. To obtain more information on each alert, click on an alert. The remediation suggested by Security Center vary according to the security alert. Follow the remediation steps given for each alert. 

    ![Alert details](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

<!-- I don't understand this sentence-->
In some cases, you may have to use other Azure capabilities to implement the recommended remediation. 

For more information on the different types of alerts, read [Security alerts types](security-center-alerts-overview.md#security-alert-types).

* A new filter enables you to customize your view to Incident only, Alerts only, or both.
* The same alert can exist as part of an Incident (if applicable), as well as to be visible as a standalone alert.

## See also
In this document, you learned how to use the security incident capability in Security Center. To learn more about Security Center, see the following:

* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Azure Security Center Detection Capabilities](security-center-detection-capabilities.md)
* [Azure Security Center Planning and Operations Guide](security-center-planning-and-operations-guide.md)
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Azure Security Center FAQ](security-center-faq.md)--Find frequently asked questions about using the service.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/)--Find blog posts about Azure security and compliance.
