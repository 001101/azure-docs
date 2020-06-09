---
title: Migrate JMS applications from ActiveMQ to Azure Service Bus | Microsoft Docs
description: This article explains how to migrate existing JMS applications that interact with Active MQ to interact with Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru

ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab

---

# Migrate existing Java Message Service (JMS) 2.0 applications from Active MQ to Azure Service Bus

Azure Service Bus supports Java/J2EE and Spring workloads that leverage the Java Message Service (JMS) 2.0 API over the AMQP protocol.

This guide describes what you should be aware of when you want to modify an existing Java Message Service (JMS) 2.0 application that interacts with a JMS Broker (specifically Apache ActiveMQ or Amazon MQ) to interact with Azure Service Bus.

## Before you start

### Differences between Azure Service Bus and Apache ActiveMQ

Azure Service Bus and Apache ActiveMQ are both message brokers that are functioning as JMS providers for client applications to send messages to and receive messages from. They both enable the point-to-point semantics with **Queues** and publish-subscribe semantics with **Topics** and **Subscriptions**. 

Even so, there are some differences in the two.

| Category | Active MQ | Azure Service Bus |
| --- | --- | --- |
| Application tiering | Monolith | Two-tier <br> (Gateway + Backend) |
| Protocol support | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Provisioning mode | <ul> <li> IaaS (on-premises) </li> <li> Amazon MQ (managed PaaS) </li> | Managed PaaS |
| Message size | Customer configurable | 1 MB (Premium tier) |
| High Availability | Customer managed | Platform managed |
| Disaster Recovery | Customer managed | Platform managed | 

TODO - more to add.

### Current supported and unsupported features

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### Caveats

TODO - separate into it's own document that can be plugged in.

The two-tiered nature of Azure Service Bus affords various business continuity capabilities (high availability and disaster recovery). However, there are some considerations when utilizing JMS features.

#### Service restarts

In the event of Service restarts or timeouts, temporary Queues or Topics will be deleted.

If the application is sensitive to data loss on temporary Queues or Topics, it is recommended to **not** use Temporary Queues or Topics and use durable Queues, Topics and Subscriptions instead.

#### Data migration

As part of migrating/modifying your client applications to interact with Azure Service Bus, the data held in ActiveMQ will not be migrated to Service Bus.

A custom application may be needed to drain the ActiveMQ queues, topics and subscriptions and replay the messages to Service Bus' queues, topics and subscriptions.

#### Authentication and Authorization

Role Based Access Control (RBAC) backed by Azure ActiveDirectory is the preferred authentication mechanism for Azure Service Bus.

However, since RBAC is not currently supported due to lack of claim based authentication support by Apache QPID JMS. This is expected to be supported in the near future.

For now, authentication is supported only with SAS keys.

## Pre-migration

### Version check

Below are the components utilized while writing the JMS applications and the specific versions that are supported. 

| Components | Version |
|---|---|
| Java Message Service (JMS) API | 1.1 or greater |
| AMQP protocol | 1.0 |

### Ensure that AMQP ports are open

Azure Service Bus supports communication over the AMQP protocol. For this purpose, communication over ports 5671 (AMQP) and 443 (TCP) needs to be enabled. Depending on where the client applications are hosted, you may need a support ticket to allow communication over these ports.

### Set up enterprise configurations (VNET, Firewall, private endpoint, etc.)

Azure Service Bus enables various enterprise security and high availability features. To learn more about them, follow the below documentation links.

  * [Virtual Network Service Endpoints](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Service side encryption with customer managed key (BYOK)](configure-customer-managed-key.md)
  * [Private endpoints](private-link-service.md)
  * [Authentication and Authorization](service-bus-authentication-and-authorization.md)

### Monitoring, alerts and tracing

Metrics are published for each Service Bus namespace onto Azure Monitor and can be leveraged for alerting and dynamic scaling of resources allocated to the namespace.

Read more about the different metrics and how to setup alerts on them at [Service Bus metrics in Azure Monitor](service-bus-metrics-azure-monitor.md).

Also, read more about [client side tracing for data operations](service-bus-end-to-end-tracing.md) and [operational/diagnostic logging for management operations](service-bus-diagnostic-logs.md)

## Migration

To migrate your existing JMS 2.0 application to interact with Azure Service Bus, the below steps need to be performed.

### Export topology from ActiveMQ and create the entities in Azure Service Bus (optional)

To ensure that the client applications can seamlessly connect with Azure Service Bus, the topology - which includes Queues, Topics and Subscriptions - need to be migrated from **Apache ActiveMQ** to **Azure Service Bus**.

> [!NOTE]
> For Java Message Service (JMS) applications, creation of Queues, Topics and Subscriptions is a runtime operation. Most Java Message Service (JMS) providers (message brokers) offer the functionality to create *Queues*, *Topics* and *Subscriptions* at runtime.
>
> Hence, this step is optional.
>
> To ensure that your application has the permissions to create the topology at runtime, please ensure that the connection string with ***SAS 'Manage'*** permissions is used.

To do this 
  * Leverage the [ActiveMQ Command Line tools](https://activemq.apache.org/activemq-command-line-tools-reference) to export the topology
  * Recreate the same topology using an [Azure Resource Manager template](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Execute the Azure Resource Manager template.


### Import the maven dependency for Service Bus JMS implementation

To ensure seamless connectivity with Azure Service Bus, the ***servicebus-jms*** package needs to be added as a dependency to the Maven `pom.xml` file as below.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### Update the application properties (for Spring applications)

If using a Spring boot application to connect to ActiveMQ.

The next step is to ***remove*** the ActiveMQ specific properties from the `application.properties` file.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Then ***add*** the Service Bus specific properties to the `application.properties` file.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

### Replace the ActiveMQConnectionFactory with ServiceBusJmsConnectionFactory

The next step is to replace the instance of ActiveMQConnectionFactory with the ServiceBusJmsConnectionFactory.

> [!NOTE] 
> The actual code changes are specific to the application and how dependencies are managed, but the below sample provides the guidance on ***what*** should be changed.
>

Previously you may be instantiating an object of the the ActiveMQConnectionFactory as below.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

This would be changed to the instantiate an object of the ServiceBusJmsConnectionFactory

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## Post-migration

Now that you have modified the application to starting sending and receiving messages from Azure Service Bus, you should verify that it works as you expect. Once that is done, you can proceed to further refine and modernize your application stack.
