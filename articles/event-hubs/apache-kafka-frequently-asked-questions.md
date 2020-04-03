---
title: Frequently asked questions - Azure Event Hubs for Apache Kafka 
description: This article shows how consumers and producers that use different protocols (AMQP, Apache Kafka, and HTTPS) can exchange events when using Azure Event Hubs. 
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: 

ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija

---

# Frequently asked questions - Event Hubs for Apache Kafka 

## Do you run Apache Kafka?

No.  We execute Kafka API operations against Event Hubs infrastructure.  Because there's a tight correlation between Apache Kafka and Event Hubs AMQP functionality (that is, produce, receive, management, so on.), we're able to bring the known reliability of Event Hubs to the Kafka PaaS space.

## Event Hubs consumer group vs. Kafka consumer group
What's the difference between an Event Hub consumer group and a Kafka consumer group on Event Hubs? Kafka consumer groups on Event Hubs are fully distinct from standard Event Hubs consumer groups.

**Event Hubs consumer groups**

- They are Managed with create, retrieve, update, and delete (CRUD) operations via portal, SDK, or Azure Resource Manager templates. Event Hubs consumer groups can't be autocreated.
- They are children entities of an event hub. It means that the same consumer group name can be reused between event hubs in the same namespace because they're separate entities.
- They aren't used for storing offsets. Orchestrated AMQP consumption is done using external offset storage, for example, Azure Storage.

**Kafka consumer groups**

- They are autocreated.  Kafka groups can be managed via the Kafka consumer group APIs.
- They can store offsets in the Event Hubs service.
- They are used as keys in what is effectively an offset key-value store. For a unique pair of `group.id` and `topic-partition`, we store an offset in Azure Storage (3x replication). Event Hubs users don't incur extra storage costs from storing Kafka offsets. Offsets are manipulable via the Kafka consumer group APIs, but the offset storage *accounts* aren't directly visible or manipulable for Event Hub users.  
- They span a namespace. Using the same Kafka group name for multiple applications on multiple topics means that all applications and their Kafka clients will be rebalanced whenever only a single application needs rebalancing.  Choose your group names wisely.
- They fully distinct from Event Hubs consumer groups. You **don't** need to use '$Default', nor do you need to worry about Kafka clients interfering with AMQP workloads.
- They aren't viewable in the Azure portal. Consumer group info is accessible via Kafka APIs.

## Next steps
To learn more about Event Hubs and Event Hubs for Kafka, see the following articles:  

- [Event Hubs for Apache Kafka - Overview](event-hubs-for-kafka-ecosystem-overview.md)
- [Quickstart: Data streaming with Event Hubs using the Kafka protocol](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Tutorial: Process Apache Kafka for Event Hubs events using Stream analytics](event-hubs-kafka-stream-analytics.md)
- [Mirror a Kafka broker in an event hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connect Apache Spark to an event hub](event-hubs-kafka-spark-tutorial.md)
- [Integrate Apache Kafka Connect with a event hub (Preview)](event-hubs-kafka-connect-tutorial.md)
- [Connect Akka Streams to an event hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Use the Spring Boot Starter for Apache Kafka with Azure Event Hubs](/azure/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub)

