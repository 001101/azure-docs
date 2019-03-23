---
title: Pricing & billing - Azure Logic Apps | Microsoft Docs
description: Learn how pricing and billing works for Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 03/25/2019
---

# Pricing model for Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview) helps you create 
and run automated integration workflows that can scale in the cloud. 
Here are the details about how billing and pricing work for Logic Apps. 
For specific pricing information, see [Azure Logic Apps Pricing](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## Consumption pricing model

For new logic apps that run in the public or "global" Logic 
Apps service, you pay only for what you use. These logic apps 
use a consumption-based plan and pricing model. In your logic app 
definition, each step is an action. Actions include the trigger, 
any control flow steps, built-in actions, and connector calls. 
Logic Apps meters all actions that run in your logic app.

<a name="fixed-pricing"></a>

## Fixed pricing model

For new logic apps that run inside an 
[*integration service environment* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), 
you pay a fixed monthly price for built-in actions 
and standard connectors. An ISE provides a way for 
you to create and run isolated logic apps that can 
access resources in an Azure virtual network.

Your ISE base unit has fixed capacity, so if you need more throughput, 
you can [add more scale units](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), 
either during creation or afterwards. Your ISE includes one free 
Enterprise connector, which includes as many connections as you want. 
Usage for additional Enterprise connectors are charged based 
on the Enterprise consumption price.

> [!NOTE]
> The ISE is in [*public preview*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> For specific pricing information, see 
> [Azure Logic Apps Pricing](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## Connectors

Connectors in Azure Logic Apps provide access to apps, services, 
and systems in the cloud or on premises, and are categorized as 
either Standard or Enterprise. Connectors can provide [triggers](#triggers), 
[actions](#actions), or both for communicating between your logic app 
and other apps, services, and systems. For an overview about these 
connectors, see [Connectors for Azure Logic Apps](../connectors/apis-list.md). 
The following sections provide more information about how billing 
for triggers and actions work.

<a name="triggers"></a>

### Triggers

Triggers are special actions that create a logic app instance 
when a specific event happens. Triggers act in different ways, 
which affect how the logic app is metered. Here are the various 
kinds of triggers that exist in Azure Logic Apps:

* **Polling trigger**: This trigger continually checks an endpoint 
for messages that satisfy the criteria for creating a logic app 
instance and starting the workflow. Even when no logic app instance 
gets created, Logic Apps meters each polling request as an execution. 
To specify the polling interval, set up the trigger through the Logic App Designer.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook trigger**: This trigger waits for a client to send a request to a 
specific endpoint. Each request sent to the webhook endpoint counts as an action 
execution. For example, the Request and HTTP Webhook trigger are both webhook triggers.

* **Recurrence trigger**: This trigger creates a logic app instance based 
on the recurrence interval that you set up in the trigger. For example, 
you can set up a Recurrence trigger that runs every three days or on a more complex schedule.

<a name="actions"></a>

### Actions

Azure Logic Apps meters "built-in" actions, such as HTTP, as native actions. 
For example, built-in actions include HTTP calls, calls from Azure Functions 
or API Management, and control flow steps such as conditions, loops, and 
switch statements. Each action has their own action type.

For example, actions that call [connectors](https://docs.microsoft.com/connectors) 
have the "ApiConnection" type. These connectors are classified as 
Standard or Enterprise connectors, which are metered based on their 
respective [pricing][pricing]. Enterprise connectors in *Preview* are 
charged as Standard connectors.

Azure Logic Apps meters all successful and unsuccessful actions as executions. 
However, Logic Apps doesn't meter these actions:

* Actions that get skipped due to unmet conditions
* Actions that don't run because the logic app stopped before finishing

For actions that run inside loops, Logic Apps counts each action per cycle in the loop. 
For example, suppose you have a "for each" loop that processes a list. 
Logic Apps meters an action in that loop by multiplying the number of list items 
with the number of actions in the loop, and adds the action that starts the loop. 
The calculation for a 10-item list is (10 * 1) + 1, which results in 11 action executions.

## Disabled logic apps

Disabled logic apps aren't charged because they 
can't create new instances while they're disabled.

> [!NOTE]
> After you disable a logic app, any currently running 
> instances might take some time before they completely stop.

## Integration accounts

Consumption-based usage applies to 
[integration accounts](logic-apps-enterprise-integration-create-integration-account.md) 
where you can explore, develop, and test the 
[B2B and EDI](logic-apps-enterprise-integration-b2b.md) 
and [XML processing](logic-apps-enterprise-integration-xml.md) 
features in Azure Logic Apps at no additional cost. 

You can have one integration account in each region. Each integration 
account can store up to specific [numbers of artifacts](../logic-apps/logic-apps-limits-and-config.md), 
which include trading partners, agreements, maps, schemas, 
assemblies, certificates, batch configurations, and so on.

Azure Logic Apps also offers Basic and Standard integration 
accounts with supported Logic Apps SLA. 

* Use Basic integration accounts when you just want message 
handling or act as a small business partner that has a 
trading partner relationship with a larger business entity.

* Use Standard integration accounts when you have more complex 
B2B relationships and want to increase the number of entities 
you can manage.

For specific pricing information, see 
[Azure Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## Data retention

All input and output data that gets stored in your logic 
app's run history gets billed based on the logic app's 
[run retention period](logic-apps-limits-and-config.md#run-duration-retention-limits). 
For specific pricing information, see 
[Azure Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps).

To view and monitor your logic app's storage consumption, 
follow these steps: 

1. In the Azure portal, on your logic app's menu, 
under **Monitoring**, select **Metrics**.

1. In the right-hand pane, under **Chart Title**, 
from the **Metric** list, select 
**Billing Usage for Storage Consumption Executions**.

   This metric gives you the number of storage 
   consumption executions that are getting billed.

## Next steps

* [Learn more about Azure Logic Apps](logic-apps-overview.md)
* [Create your first logic app](quickstart-create-first-logic-app-workflow.md)