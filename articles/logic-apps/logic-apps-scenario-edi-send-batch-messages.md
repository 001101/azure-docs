﻿---
title: Batch process EDI messages as a group or collection - Azure Logic Apps | Microsoft Docs
description: Send EDI messages for batch processing in logic apps
services: logic-apps
ms.service: logic-apps
author: divswarnkar
ms.author: divswa
manager: jeconnoc
ms.topic: article
ms.date: 08/08/18
ms.reviewer: estfan, LADocs
---

# Send X12 messages in batches to trading partners with Azure Logic Apps

In business to business (B2B) scenarios, 
partners often exchange messages in groups or batches. 
By creating a batching solution with Logic Apps, 
you can send messages to trading partners and 
process those messages together in batches. 
This article shows how you can batch process X12 messages by creating 
a "batch sender" logic app and a "batch receiver" logic app. 

Batching X12 messages works like batching other messages; 
you use a batch trigger that collects messages into a batch and a batch action 
that sends messages to a batch. Also, X12 batching includes an X12 encoding step 
before the messages go to the trading partner or other destination. 
To learn more about the batch trigger and action, see 
[Batch process messages](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

In this article, you'll perform these tasks in the following order:

* [Create a "batch receiver" logic app](#receiver), 
which accepts and collects messages into a batch 
until your specified criteria is met for releasing 
and processing those messages. In this scenario, 
the batch receiver also encodes the messages in the batch 
by using the specified X12 agreement or partner identities.

* [Create a "batch sender" logic app](#sender), 
which send the messages to the previously created batch receiver. 

## Prerequisites

To follow this example, you need these items:

* An Azure subscription. If you don't have a subscription, you can 
[start with a free Azure account](https://azure.microsoft.com/free/). 
Otherwise, you can [sign up for a Pay-As-You-Go subscription](https://azure.microsoft.com/pricing/purchase-options/).

* An existing [integration account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 
associated with your Azure subscription

* At least two existing [partners](../logic-apps/logic-apps-enterprise-integration-partners.md) 
in your integration account. Each partner must use the X12 (Standard Carrier Alpha Code) 
qualifier as a business identity in the partner's properties.

* An existing [X12 agreement](../logic-apps/logic-apps-enterprise-integration-x12.md) 
in your integration account

<a name="receiver"></a>

## Create X12 batch receiver

Before you can send messages to a batch, 
you must first create the "batch receiver" 
logic app, which starts with the **Batch** trigger. 
That way, when you create the "batch sender" logic app, 
you can select this receiver logic app. The batch 
receiver continues collecting messages until your 
specified criteria is met for releasing and processing those messages. 
While batch receivers don't need to know anything about batch senders, 
the batch senders must know where to send messages. 

For this batch receiver, you specify the batch mode, name, 
release criteria, X12 agreement, and other settings. 

1. In the [Azure portal](https://portal.azure.com), 
create a logic app with this name: "BatchX12Messages"

2. [Link your logic app to your integration account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. In Logic Apps Designer, add the **Batch** trigger, 
which starts your logic app workflow. 
In the search box, enter "batch" as your filter. 
Select this trigger: **Batch messages**

   ![Add Batch trigger](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Set the batch receiver properties: 

   | Property | Value | Notes | 
   |----------|-------|-------|
   | **Batch Mode** | Inline |  |  
   | **Batch Name** | TestBatch | Available only with **Inline** batch mode | 
   | **Release Criteria** | Schedule based | Available only with **Inline** batch mode | 
   | **Interval** | 10 | Available only with **Schedule based** release criteria | 
   | **Frequency** | minute | Available only with **Schedule based** release criteria | 
   ||| 

   ![Provide Batch trigger details](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-schedule-based.png)

5. Now add an action that encodes each batch: 

   1. Under the batch trigger, choose **New step**.

   2. In the search box, enter "X12 batch" as your filter, 
   and select this action (any version): **Batch encode <*version*> - X12** 

      ![Select X12 Batch Encode action](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. If you didn't previously connect to your integration account, 
   create the connection now. Provide a name for your connection, 
   select the integration account you want, and then choose **Create**.

      ![Create connection between batch encoder and integration account](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Set these properties for your batch encoder action:

      | Property | Description |
      |----------|-------------|
      | **Name of X12 agreement** | Open the list, and select your existing agreement. <p>If your list is empty, make sure you [link your logic app to the integration account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) that has the agreement you want. | 
      | **BatchName** | Click inside this box, and after the dynamic content list appears, select the **Batch Name** token. | 
      | **PartitionName** | Click inside this box, and after the dynamic content list appears, select the **Partition Name** token. | 
      | **Items** | Close the item details box, and then click inside this box. After the dynamic content list appears, select the **Batched Items** token. | 
      ||| 

      ![Batch Encode action details](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      For the **Items** box:

      ![Batch Encode action items](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Save your logic app.

### Test your logic app

To make sure your batch receiver works as expected, 
you can add an HTTP action for testing purposes, 
and send a batched message to the 
[Request Bin service](https://requestbin.fullcontact.com/). 

1. Under the X12 encode action, choose **New step**. 

2. In the search box, enter "http" as your filter. 
Select this action: **HTTP - HTTP**
    
   ![Select HTTP action](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Set the properties for the HTTP action:

   | Property | Description | 
   |----------|-------------|
   | **Method** | From this list, select **POST**. | 
   | **Uri** | Generate a URI for your request bin, and then enter that URI in this box. | 
   | **Body** | Click inside this box, and after the dynamic content list opens, select the **Body** token, which appears in the section, **Batch encode by agreement name**. <p>If you don't see the **Body** token, next to **Batch encode by agreement name**, select **See more**. | 
   ||| 

   ![Provide HTTP action details](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Save your logic app. 

   Your batch receiver logic app looks like this example: 

   ![Save your batch receiver logic app](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## Create X12 batch sender

Now create one or more logic apps that send messages 
to the batch receiver logic app. In each batch sender, 
you specify the batch receiver logic app and batch name, 
message content, and any other settings. You can 
optionally provide a unique partition key to divide 
the batch into subsets to collect messages with that key.
While batch receivers don't need to know anything about batch senders, 
the batch senders must know where to send messages. 

1. Create another logic app with this name: "SendX12MessagesToBatch" 

2. In the search box, enter "when a http request" as your filter. 
Select this trigger: **When a HTTP request is received** 
   
   ![Add the Request trigger](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Add an action for sending sending messages to a batch.

   1. Under the HTTP request action, choose **New step**.

   2. In the search box, enter "batch" as your filter. 
   Select this action: **Choose a Logic Apps workflow with batch trigger**

      ![Select "Choose a Logic Apps workflow with batch trigger"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Now select your "BatchX12Messages" logic app that you previously created.

      ![Select "batch receiver" logic app](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Select this action: **Batch_messages - <*your-batch-receiver*>**

      ![Select "Batch_messages" action](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Set the batch sender's properties.

   | Property | Description | 
   |----------|-------------| 
   | **Batch Name** | The batch name defined by the receiver logic app, which is "TestBatch" in this example <p>**Important**: The batch name gets validated at runtime and must match the name specified by the receiver logic app. Changing the batch name causes the batch sender to fail. | 
   | **Message Content** | The content for the message you want to send, which is the **Body** token in this example | 
   ||| 
   
   ![Set batch properties](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Save your logic app. 

   Your batch sender logic app looks like this example:

   ![Save your batch sender logic app](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## Test your logic apps

To test your batching solution, post X12 messages to your batch sender logic 
app from [Postman](https://www.getpostman.com/postman) or a similar tool. 
Soon, you start getting X12 messages in your request bin, 
either every 10 minutes or in batches of five, all with the same partition key.

## Next steps

* [Process messages as batches](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
