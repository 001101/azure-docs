---
title: Azure Service Bus messaging overview | Microsoft Docs
description: Description of Service Bus messaging
services: service-bus-messaging
documentationcenter: ''
author: ChristianWolf42
manager: timlt
editor: ''

ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/08/2018
ms.custom: mvc
ms.author: chwolf;sethm

---
# What is Azure Service Bus?

Microsoft Azure Service Bus is a fully managed enterprise integration message broker. Service Bus is most commonly used to decouple applications and services from each other, and it provides a highly reliable and secure platform for asynchronous data and state transfer. Data is transferred between different applications and services using *messages*. A message is in binary format, which can contain JSON, XML, or just text. 

Some common messaging scenarios are:

* Transferring business data, such as sales or purchase orders, journals, or inventory movements.
* Improving reliability and scalability of applications and services through decoupling (client and service do not have to be online at the same time).
* Enable 1:*n* relationships between publishers and subscribers.
* Implementing workflows that require message ordering or message deferral.

## Namespaces

A namespace is a scoping container for all messaging components. Multiple queues and topics can reside within a single namespace, and namespaces often serve as application containers.

## Queues

Messages are sent to and received from *queues*. Queues enable you to store messages until the receiving application is available to receive and process them.

![Queue](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Messages in queues are ordered and timestamped on arrival. Once accepted, the message is held safely in redundant storage. 

The standard delivery mode is *pull*, which delivers messages on request.

## Topics

You can also use *topics* to send and receive messages. While a queue is often used for point-to-point communication, topics are useful in publish/subscribe scenarios.

![Topic](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Topics can have multiple, independent subscriptions. A subscriber to a topic is eligible to receive a copy of each message sent to that topic. Subscriptions are named entities, which are durably created but can optionally expire or auto-delete.

In some scenarios you may not want individual subscriptions to receive all messages sent to a topic. If so, you can use [rules and filters](topic-filters.md) to define conditions that trigger optional [actions](https://docs.microsoft.com/azure/service-bus-messaging/topic-filters), filter specified messages, and set or modify message properties.

## Advanced features

Service Bus has many advanced features that enable you to solve more complex messaging problems. For example, Service Bus supports enforcing first in, first out (FIFO) receiving via [sessions](message-sessions.md), chaining multiple entities with [auto-forwarding](service-bus-auto-forwarding.md), or [dead-letter queues](service-bus-dead-letter-queues.md) if messages cannot be processed for any reason.

The following sections describe these key features:

### Scheduled delivery

If [absolute order of messages](message-sequencing.md) is significant, or a consumer needs a trustworthy unique identifier for messages, the message broker stamps messages with a gap-free, increasing sequence number relative to the queue or topic. For partitioned entities, the sequence number is issued relative to the partition.

### Dead-lettering

Service Bus supports a [dead-letter queue](service-bus-dead-letter-queues.md) (DLQ) to hold messages that cannot be delivered to any receiver, or messages that cannot be processed. Messages can then be removed from the DLQ and inspected. An application might, with the help of an operator, correct issues and resubmit the message, log the fact that there was an error, and take corrective action.

### Auto-forwarding

The [auto-forwarding](service-bus-auto-forwarding.md) feature enables you to chain a queue or subscription to another queue or topic that is part of the same namespace. When auto-forwarding is enabled, Service Bus automatically removes messages that are placed in the first queue or subscription (source) and puts them in the second queue or topic (destination).

### Message deferral

When a queue or subscription client receives a message that it is willing to process, but for which processing is not currently possible due to special circumstances within the application, the entity has the option to [defer retrieval of the message](message-deferral.md) to a later point. The message remains in the queue or subscription, but it is set aside.

### Message sessions

To realize a FIFO guarantee in Service Bus, use sessions. [Message sessions](message-sessions.md) enable joint and ordered handling of unbounded sequences of related messages. 

### Batching

[Client-side batching](service-bus-performance-improvements.md#client-side-batching) enables a queue or topic client to delay sending a message for a certain period of time. If the client sends additional messages during this time period, it transmits the messages in a single batch. Batching does not affect the number of billable messaging operations, and is available only for the Service Bus client protocol. The HTTP protocol does not support batching.

### Transactions

A [transaction](service-bus-transactions.md) groups two or more operations together into an execution scope. Service Bus supports grouping operations against a single messaging entity (queue, topic, subscription) within the scope of a transaction. For example, you can send several messages to one queue from within a transaction scope, and the messages will only be committed to the queue's log when the transaction successfully completes.

Service Bus also supports the following features:

* [Filtering and Actions](topic-filters.md)
* [Auto-delete on idle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle?view=azure-dotnet#Microsoft_ServiceBus_Messaging_SubscriptionDescription_AutoDeleteOnIdle)
* [Duplicate detection](duplicate-detection.md)
* [SAS](service-bus-sas.md), [RBAC](service-bus-role-based-access-control.md) and [MSI](service-bus-managed-service-identity.md) support.
* [Geo-Disaster Recovery](service-bus-geo-dr.md)
NO BLURBS:
* Secure, standard [AMQP 1.0](service-bus-amqp-overview.md) and [HTTP/REST](/rest/api/servicebus/) protocols
* Client libraries for [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master), [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client) support

## Integration

Azure Event Grid 
Azure Logic Apps 
Azure Functions 
Dynamics 365  
 
Stream Analytics
 
## Next steps

To get started using Service Bus messaging, see the following articles:

* Compare messaging services (Event Grid page)
* Learn more about Azure Service Bus [Standard and Premium](https://azure.microsoft.com/pricing/details/service-bus/) tiers and their pricing.
* [Performance and Latency of Azure Service Bus Premium tier](https://blogs.msdn.microsoft.com/servicebus/2016/07/18/premium-messaging-how-fast-is-it/).
* Try the quickstarts in [.NET](service-bus-quickstart-powershell.md), [Java](service-bus-quickstart-powershell.md), or [JMS](service-bus-quickstart-powershell.md).
