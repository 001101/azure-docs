---
title: Frequently Asked Questions for Azure Spring Cloud | Microsoft Docs
description: An FAQ of Azure Spring Cloud, which automates many aspects of deploying Java microservice applications made in Spring and provides tools for managing others.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''

ms.service: spring-cloud
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: v-vasuke

---

# Frequently Asked Questions

## General
### Why Azure Spring Cloud?
Azure Spring Cloud manages the Spring Cloud infrastructure for Spring developers, including Eureka, Config Server, Pivotal Build Service, built-in Blue-Green deployment and more coming. Azure Spring Cloud is a PaaS, and our goal is to take care of the scaffolding works from the developers as much as possible so they can focus on their business logics and binding them with Azure services, for example, CosmosDB, MySQL, Azure Cache for Redis, etc.

With Azure Spring Cloud, The application diagnostics experience for the developers and operators is also enhanced by leveraging Azure Monitor, Application Insights, and Log Analytics.

Azure Spring Cloud is secured, all application and configuration data are stored under the EU General Data Protection Regulation (GDPR). Please refer to [How secure is Azure Spring Cloud](#how-secure-is-azure-spring-cloud) for more details.

### What service plans does Azure Spring Cloud offer?
First, Azure Spring Cloud is free for private preview.

There is only one service plan for private preview:

Resource | Amount
------- | -------
vCPU | 4
Memory | 8 GBytes
App instances per Spring application | 20
Total app instances per Azure Spring Cloud service instance | 50*
Azure Spring Cloud service instances per region per subscription | 2*
Persistent volumes | 10 x 50 GBytes

*_Open a [support ticket](https://azure.microsoft.com/support/faq/) to raise the limit._

For more details, please refer to [Azure Support FAQs](https://azure.microsoft.com/support/faq/).

### How secure is Azure Spring Cloud?
Security and privacy are one of the top priorities for Azure and Azure Spring Cloud customers. Microsoft has no access to application data, logs or configurations as they are encrypted in Azure. All the service instances in Azure Spring Cloud are completely isolated to each other.

Azure Spring Cloud provides complete solution of SSL and certificate management.

Critical security patches for OpenJDK and Spring Cloud runtimes are applied to Azure Spring Cloud as soon as possible.

### Which regions Azure Spring Cloud are available?
In private preview, we only support East US and Western Europe.

### What are the known limitations in private preview?
- `spring.application.name` will be overriden by the application name used to create each application.
- `server.port` is not allowed in configuration file from Git repo. It will likely to cause your application not reachable from other applications or internet.
- Portal and ARM template do not support uploading application packages. This can only be done via application deployment by Az CLI.
- For quota limitations, please refer to [What service plans does Azure Spring Cloud offer](#what-service-plans-does-azure-spring-cloud-offer).

### How can I provide feedback and report issues?
If you have created your Spring service instances in Azure Spring Cloud, you can create an [Azure Support Request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). If you have not yet onboarded to Azure Spring Cloud, you can go to [Azure Feedback](https://feedback.azure.com/) to request features or provide feedback.

## Developement
### I am a Spring developer but new to Azure, what is the quickest way for me to learn developing Azure Spring Cloud applications?
Regardless of to create a new application or to migrate an existing one to Azure Spring Cloud, we suggest you to firstly go through [Getting-Started guide of Azure Spring Cloud](spring-cloud-quickstart-launch-app-portal.md).

### What Java runtime does Azure Spring Cloud support?
Azure Spring Cloud officially supports Java 8 and 11.

### Where can I see my Spring application logs and metrics?
The metrics can be found at App Overview blade and [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) blade.

Azure Spring Cloud supports exporting your Spring application logs and metrics to Azure Storage, EventHub, and [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). The table name in Log Analytics is `AppPlatformLogsforSpring`. To enable it, please go through [Diagnostic settings Guide](spring-cloud-tutorial-diagnostics.md).

### Does Azure Spring Cloud support distributed tracing?
Yes, please visit [Distributed Tracing](spring-cloud-tutorial-distributed-tracing.md) for more details.

### What resource types does Service Binding support?
In private preview, 3 services are supported: Azure Cosmos DB, Azure Database for MySQL and Azure Cache for Redis.

### Can I view/add/move persistent volumes from inside of my applications?
Yes.

## Deployment
### Does Azure Spring Cloud support Blue-green deployment?
Yes, please visit [staging environment guide](spring-cloud-howto-staging-environment.md) for more details.

### Can I access Kubernetes to manipulate my application containers?
No.

### Does Azure Spring Cloud support building containers from the source?
Yes, please visit [Deploy from source](spring-cloud-launch-from-source.md) for more details.

### Does Azure Spring Cloud support autoscaling in app instances?
No.

### What are the best practices for migrating existing Spring microservices to Azure Spring Cloud?
Before migrating existing Spring microservices to Azure Spring Cloud,
- you need to have the knowledge of minimum requirement for starting them.
- you need to have the configuration entries, environment variables and JVM parameters ready so you can compare them with the deployment in Azure Spring Cloud later.
- if you want to use Service Binding, please go through your Azure services to be bound. Your applications need to have proper privileges to access them.
- it is recommended to remove or disable embedded services that might conflict with services managed by Azure Spring Cloud, for example, Eureka, Config Server, etc.
- it is recommended to use official and stable Pivotal Spring libraries. Unofficial, beta or forked version of Pivotal Spring libraries have no SLA supports.

After the migration, please monitor CPU/RAM metrics and network traffics to make sure the app instances have a right scale.