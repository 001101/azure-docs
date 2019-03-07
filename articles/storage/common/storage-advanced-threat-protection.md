---
title: Advanced Threat Protection for Azure Storage
description: Configure Azure Storage Advanced Threat Protection to detect anomalies in account activity and notify you of potentially harmful attempts to access your account.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 03/07/2019
ms.author: monhaber
ms.manager: shaik
---

# Advanced Threat Protection for Azure Storage

Advanced Threat Protection for Azure Storage provides an additional layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit storage accounts. This layer of protection allows you to address threats without the need to be a security expert or manage security monitoring systems. 

Security alerts are triggered when anomalies in activity occur.  These security alerts are integrated with [Azure Security Center](https://azure.microsoft.com/services/security-center/), and are also sent via email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats.

> [!NOTE]
> Advanced Threat Protection for Azure Storage  is currently available only for the Blob service. Complete details about the new pricing is available in the [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-pricing) pricing page. We invite you to a trial period for the first 30 days, without charge

Advanced Threat Protection for Azure Storage ingests diagnostic logs of read, write, and delete requests to Blob service for threat detection. To investigate the alerts from Advanced Threat Protection, you can view related storage activity using Storage Analytics Logging. For more information, see how to [configure Storage Analytics logging](storage-monitor-storage-account.md#configure-logging).

## Set up Advanced Threat Protection in the portal

1. Launch the Azure portal at [https://portal.azure.com](https://portal.azure.com/).

2. Navigate to the configuration page of the Azure Storage account you want to protect. In the **Settings** page, select **Advanced Threat Protection**.

3. In the **Advanced Threat Protection** configuration blade
	* Turn **ON** Advanced *Threat Protection*
	* Click **Save** to save the new or updated Advanced Threat Protection policy.

![Turn on Azure Storage advanced threat protection](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## Set up Advanced Threat Protection for storage accounts using Azure Security Center
When you subscribe to the Standard tier in Azure Security Center, ATP is set up on your storage accounts. For more information see  [Upgrade to Security Center's Standard tier for enhanced security](https://docs.microsoft.com/en-us/azure/security-center/security-center-pricing).
<!-- can I say ATP is automatically set up-->

![Standard tier in ASC](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

## Set up Advanced Threat Protection for a storage account using ARM templates

Use an Azure Resource Manager template to deploy an Azure Storage account with Advanced Threat Protection enabled.
For more information, see
[Storage account with Advanced Threat Protection](https://azure.microsoft.com/en-us/resources/templates/201-storage-advanced-threat-protection-create/).

## Set up Advanced Threat Protection for a storage account using REST API
Use Rest API commands to create, update, or get the Advanced Threat Protection setting for a specific storage account.

* [Advanced Threat Protection - Create](https://docs.microsoft.com/en-us/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection - Get](https://docs.microsoft.com/en-us/rest/api/securitycenter/advancedthreatprotection/get)

## Set up Advanced Threat Protection for storage account using Azure PowerShell

Use the following PowerShell cmdlets:
* Enable Advanced Threat Protection
* Get Advanced Threat Protection 
* Disable Advanced Threat Protection 

## Explore anomalies

When storage activity anomalies occur, you receive an email notification with information about the suspicious security event. Details of the event include:

* nature of the anomaly
* storage account name
* event time
* storage type
* potential causes 
* investigation steps
* remediation steps


The email also includes details on possible causes and recommended actions to investigate and mitigate the potential threat.

![Azure Storage advanced threat protection alert email](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

You can review and manage your current security alerts from Azure Security Center’s [Security alerts tile](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Clicking on a specific alert provides details and actions for investigating the current threat and addressing future threats.

![Azure Storage advanced threat protection alert email](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## Protection alerts

Alerts are generated by unusual and potentially harmful attempts to access or exploit storage accounts. These events can trigger the following alerts:

### Access pattern alerts

* **Access from unusual location**: This alert is triggered when there's a change in the access pattern to a storage account. For instance, when someone has accessed a storage account from an unusual geographical location. In some cases, the alert detects a legitimate action (a new application or developer’s maintenance operation). In other cases, the alert detects a malicious action (former employee, external attacker, etc.).

* **Unusual anonymous access:** This alert is triggered when there's a change in the access pattern to a storage account. For instance, suppose someone has anonymously accessed a storage account. In some cases, the alert detects a legitimate access using public read access. In other cases, the alert detects unauthorized access that exploits public read access to a container and its blobs.

* **Unusual data extraction**: This alert is triggered when there's a change in the data extraction pattern from a storage account. For instance, if someone has accessed an unusual amount of data in a storage account. In some cases, the alert detects a legitimate action (maintenance activity). In other cases, the alert detects a malicious action (data exfiltration/breach, unauthorized transfer of data).

* **Application Anomaly** This alert indicates that an unusual application has accessed this storage account. Potential causes:
   * An attacker has accessed your storage account using a new application
   * A legitimate user has used a new application/browser to access your storage account

### Extract/upload alerts

* **Unexpected delete** This alert indicates that one or more unexpected delete operations has occurred in a storage account, compared to recent activity on this account. Potential causes:
   * An attacker has deleted data from your storage account
   * A legitimate user has performed an unusual deletion

* **Upload Azure Cloud Service package** 
This alert indicates that an Azure Cloud Service package (.cspkg file) has been uploaded to a storage account in an unusual way, compared to recent activity on this account. Potential causes: 
   * An attacker has been preparing to deploy malicious code from your storage account to an Azure cloud service.
   * A legitimate user has been preparing for a legitimate service deployment. 

### Suspicious storage activities alerts

* **Access permission change** This alert indicates that the access permissions of this storage container have been changed in an unusual way. Potential causes: 
   * An attacker has changed container permissions to weaken its security
   * A legitimate user has changed container permissions

* **Access Inspection** This alert indicates that the access permissions of a storage account have been inspected in an unusual way, compared to recent activity on this account. Potential causes: 
   * An attacker has performed reconnaissance for a future attack
   * A legitimate user has performed maintenance on the storage account

* **Data Exploration** This alert indicates that blobs or containers in a storage account have been enumerated in an unusual way, compared to recent activity on this account. Potential causes: 
   * An attacker has performed reconnaissance for a future attack
   * A legitimate user or application logic has explored data within the storage account


* **Geo-anomaly**  This alert indicates that this account has been accessed successfully from an IP address that is unfamiliar and unexpected compared to the recent access pattern on this account. Potential causes:
    * An attacker has accessed your storage account
    * A legitimate user has accessed your storage account from a new location
* **Anonymous access** This alert indicates that this account has been accessed anonymously (i.e. without any authentication), which is unexpected compared to the recent access pattern on this account. Potential causes:
   * An attacker has exploited public read access to a container
   * A legitimate user or application has used public read access to a container
* **Data Exfiltration** This alert indicates that an unusually large amount of data has been extracted compared to recent activity on this storage container. Potential causes:

   * An attacker has extracted a large amount of data from a container
   * A legitimate user or application has extracted an unusual amount of data from a container





## Next steps

* Learn more about [Logs in Azure Storage accounts ](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Learn more about [Azure Security Center](../../security-center/security-center-intro.md)