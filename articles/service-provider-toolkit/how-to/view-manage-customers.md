---
title: View and manage customers and delegated resources in the Azure portal
description: As a service provider using Azure Delegated Resource Management, you can view all of your delegated customer resources and subscriptions by going to My customers in the Azure portal. 
author: JnHs
ms.author: jenhayes
ms.service: service-provider-toolkit
ms.date: 04/03/2019
ms.topic: overview
manager: carmonm
---
# View and manage customers and delegated resources

> [!IMPORTANT]
> Azure Delegated Resource Management is currently in limited public preview. The info in this topic may change before general availability.

In this article, you'll learn how, as a service provider using [Azure Delegated Resource Management](../concepts/azure-delegated-resource-management.md), you can view all of your delegated customer resources and subscriptions by going to **My customers** in the [Azure portal](https://portal.azure.com).

To access the **My customers** page in the Azure portal, select **All services**, then search for **Service providers** and select it.You can also find it by entering “Service providers” in the search box near the top of the Azure portal.

> [!NOTE]
> Your customers can view info about service providers by navigating to **Service providers** in the Azure portal. For more info, see [View and manage service providers](view-manage-service-providers.md).

## View and manage customer details

To view customer details, select **Customers** on the left side of the **My customers** page.

For each customer, you’ll see the customer’s name, tenant ID, and the name of the offer associated with the engagement. In the **Assignments** column, you’ll see the name of their delegated subscription (or the number of subscriptions that they’ve delegated, which you can select to view all subscriptions).

Filters at the top of the page let you sort and group your customer info or filter by specific customers, offers, or keywords.

You can view the following info from this page:

- To see all of the subscriptions, offers, and access assignments associated with a customer, select the customer’s name.
- To see more details about an offer and the access assignments that are associated with it, select the offer name. 
- To view more details about a delegated subscription, select its name.

## View access assignments

Access assignments represent the users and permissions that have access to delegated subscriptions. To view this info, select **Access assignments** on the left side of the **My customers** page.

Filters at the top of the page let you sort and group your access assignment info or filter by specific customers, offers, or keywords.

The access assignments associated with each delegated subscription appear in the **Role assignments** column. You can select each entry to view the full list of users, groups, and service principals that have been granted access to a subscription. From there, you can select a particular user, group, or service principal name to get more details.

## Work in the context of a delegated subscription

You can work directly in the context of a delegated subscription within the Azure portal. To do so:

1. Select **Azure Active Directory** from the left navigation pane. (Alternately, select your user name in the upper right corner of the Azure portal.)
2. Select **Switch directory**.
3. In the **Global subscription** filter, ensure that only the box for that delegated subscription is selected. 

If you then access a service which supports cross-tenant management experiences, the service will default to the scope of the delegated subscription that you selected. You can remove this scope by following the steps above and checking the **Select all** box (or choosing one or more subscriptions to work in instead).

You can also access functionality related to delegated subscriptions from within services that support cross-tenant management experiences by modifying your scope from within that service.
