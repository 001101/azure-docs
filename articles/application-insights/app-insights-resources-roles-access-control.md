---
title: Resources, roles and access control in Azure Application Insights | Microsoft Docs
description: Owners, contributors and readers of your organization's insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm

ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: mbullwin

---
# Resources, roles, and access control in Application Insights

You can control who has read and update access to your data in Azure [Application Insights][start], by using [Role-based access control in Microsoft Azure](../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Assign access to users in the **resource group or subscription** to which your application resource belongs - not in the resource itself. Assign the **Application Insights component contributor** role. This ensures uniform control of access to web tests and alerts along with your application resource. [Learn more](#access).

## Resources, groups and subscriptions

First, some definitions:

* **Resource** - An instance of a Microsoft Azure service. Your Application Insights resource collects, analyzes and displays the telemetry data sent from your application.  Other types of Azure resources include web apps, databases, and VMs.
  
    To see your resources, open the [Azure portal][portal], sign in, and click All Resources. To find a resource, type part of its name in the filter field.
  
    ![List of Azure resources](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Resource group**][group] - Every resource belongs to one group. A group is a convenient way to manage related resources, particularly for access control. For example, into one resource group you could put a Web App, an Application Insights resource to monitor the app, and a Storage resource to keep exported data.

* [**Subscription**](https://portal.azure.com) - To use Application Insights or other Azure resources, you sign in to an Azure subscription. Every resource group belongs to one Azure subscription, where you choose your price package and, if it's an organization subscription, choose the members and their access permissions.
* [**Microsoft account**][account] - The username and password that you use to sign in to Microsoft Azure subscriptions, XBox Live, Outlook.com, and other Microsoft services.

## <a name="access"></a> Control access in the resource group

It's important to understand that in addition to the resource you created for your application, there are also separate hidden resources for alerts and web tests. They are attached to the same [resource group](#resource-group) as your application. You might also have put other Azure services in there, such as websites or storage.

To control access to these resources it's therefore recommended to:

* Control access at the **resource group or subscription** level.
* Assign the **Application Insights Component contributor** role to users. This allows them to edit web tests, alerts, and Application Insights resources, without providing access to any other services in the group.

## To provide access to another user

You must have Owner rights to the subscription or the resource group.

The user must have a [Microsoft Account][account], or access to their [organizational Microsoft Account](../active-directory/fundamentals/sign-up-organization.md). You can provide access to individuals, and also to user groups defined in Azure Active Directory.

#### Navigate to resource group or directly to the resource itself

Choose **Access control (IAM)** from the left-hand menu.

![Screenshot of Access control button in Azure portal](./media/app-insights-resources-roles-access-control/001-access-control.png)

Select **Add**

![Screenshot of Access control button in Azure portal](./media/app-insights-resources-roles-access-control/002-add.png)

The **Add permissions** view below is specific to Application Insights resources, if you were viewing the access control permissions from a higher level like resource groups, you would see additional non-Application Insights-centric roles.

![Screenshot of Access control button in Azure portal](./media/app-insights-resources-roles-access-control/003-user-roles.png)

#### Select a role

| Role | In the resource group |
| --- | --- |
| Owner |Can change anything, including user access. |
| Contributor |Can edit anything, including all resources. |
| Application Insights Component contributor |Can edit Application Insights resources, web tests and alerts. |
| Reader |Can view but not change anything. |
| Application Insights Snapshot Debugger | Gives the user permission to use Application Insights Snapshot Debugger features. |
| Azure Service Deploy Release Management Contributor | Contributor role for services deploying through Azure Service Deploy. |
| Data Purger | Special role for purging personal data. See our [guidance for personal data](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) for more information.   |
| ExpressRoute Administrator | Can create delete and manage express routes.|
| Log Analytics Contributor | Can read all Log Analytics monitoring data and edit associated monitoring settings.  |
| Log Analytics Reader | Can view and search all Log Analytics monitoring data as well as associated monitoring settings. |
| masterreader | Allows a user to view everything but not make changes. |
| Monitoring Contributor | Can read all monitoring data and update monitoring settings. |
| Monitoring Metrics Publisher | Enables publishing metrics against Azure resources. |
| Monitoring Reader | Can read all monitoring data. |
| Resource Policy Contributor (Preview) | Backfilled users from EA, with rights to create/modify resource policy, create support ticket and read resource/hierarchy.  |
| User Access Administrator | Allows a user to manage access for other users to Azure resources.|
| Website Contributor | Allows a user to manage websites.|

'Editing' includes creating, deleting and updating:

* Resources
* Web tests
* Alerts
* Continuous export

#### Select the user

If the user you want isn't in the directory, you can invite anyone with a Microsoft account.
(If they use services like Outlook.com, OneDrive, Windows Phone, or XBox Live, they have a Microsoft account.)

## Related content

* [Role based access control in Azure](../role-based-access-control/role-assignments-portal.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md
