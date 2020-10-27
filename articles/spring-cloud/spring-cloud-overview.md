---
title: Introduction to Azure Spring Cloud
description: Learn the features and benefits of Azure Spring Cloud to deploy and manage Java Spring applications in Azure.
author: bmitchell287

ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
---

# What is Azure Spring Cloud?

Azure Spring Cloud makes it easy to deploy Spring Boot microservice applications to Azure without any code changes.  The service manages the infrastructure of Spring Cloud applications so developers can focus on their code.  Azure Spring Cloud provides lifecycle management using comprehensive monitoring and diagnostics, configuration management, service discovery, CI/CD integration, blue-green deployments, and more.

## Why use Azure Spring Cloud?

Deployment of applications to Azure Spring Cloud has many benefits.  You can:
* Efficiently migrate existing Spring apps and manage cloud scaling and costs.
* Modernize apps with Spring Cloud patterns to improve agility and speed of delivery.
* Run Java at cloud scale and drive higher usage without complicated infrastructure.
* Develop and deploy rapidly without containerization dependencies.
* Monitor production workloads efficiently and effortlessly.

## Feature overview

As part of the Azure ecosystem, Azure Spring Cloud allows easy binding to other Azure services including storage, databases, monitoring, and more.  This documentation includes sections that explain how to leverage Azure Spring Cloud services.

* Quickstart
    * **Launch your first app**
    * **Provision an Azure Spring Cloud service**
    * **Set up the configuration server**
    * **Build and deploy apps**
    * **Use logs metrics and tracing**
* How-to
    * **Prepare apps**: Prepare an existing Java Spring application for deployment to Azure Spring Cloud. When configured properly, Azure Spring Cloud provides robust services to monitor, scale, and update Java Spring Cloud applications.
    * **Manage apps**:  Start, stop, and delete your Azure Spring Cloud applications. Change an application's state in Azure Spring Cloud by using either the Azure portal or the Azure CLI.
    * **Monitor apps**: Distributed tracing tools to easily debug and monitor complex issues. Azure Spring Cloud integrates Spring Cloud Sleuth with Azure's Application Insights. This integration provides powerful distributed tracing capability from the Azure portal.
    * **Secure apps**: Azure resources provide an automatically managed identity in Azure Active Directory. You can use this identity to authenticate to any service that supports Azure AD authentication, without having credentials in your code.
    * **Integration with other Azure services**: Instead of manually configuring your Spring Boot applications, you can automatically bind selected Azure services to your applications, for example, binding your application to an Azure Cosmos DB database.
    * **DevOps**: Continuous integration and continuous delivery tools let you quickly deploy updates to existing applications with minimal effort and risk. Azure DevOps helps organize and control these key tasks. 
    * **Troubleshoot**: Azure Spring Cloud diagnostics provide an interactive experience to help troubleshoot apps. No configuration is required. When you find issues, Azure Spring Cloud diagnostics identifies problems and guides you to information that helps troubleshoot and resolve issues.

## Other features

* Configuration server
* Blue/green deployments
* Application scaling
* Integration with Azure DevOps
* Application monitoring

### Spring Cloud Config Server

 Azure Spring Cloud Config Server is a central location to manage application properties across all environments. It provides externalized configuration in a distributed system with both server and client-side support. For more information see [Spring Cloud Config Server reference](https://spring.io/projects/spring-cloud-config). 

### Blue/green deployments

Azure Spring Cloud supports blue/green deployments for releasing and updating code to production environments.  This change management pattern allows developers to implement features and code changes with the security of an immediate fallback when necessary.  Developers can concentrate on writing code with multiple production environments to update or roll back code changes without interrupting the application.  To learn more about staging environments and blue/green deployments, visit this [How-To article](spring-cloud-howto-staging-environment.md).

### CI/CD pipeline automation

Azure Spring Cloud provides integration with Azure DevOps using the Azure CLI.  Using Azure DevOps, you can automate code integration and deployment to your Spring application.  To learn more, visit this [article](spring-cloud-howto-cicd.md).

### Application scaling

Azure Spring Cloud allows you to efficiently scale micro-services in your Azure Spring Cloud dashboard.  Both the number of vCPUs and the amount of memory available to micro-services can be scaled up or down to suit your requirements.  Scaling takes effect in seconds and does not require code changes or redeployment.  To learn more, complete this [tutorial](spring-cloud-tutorial-scale-manual.md).

### Application monitoring

Azure Spring Cloud's distributed tracing tools allow developers to debug and monitor the complex interconnections between microservices in an application.  By integrating [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) with Azure's [Application Insights](../azure-monitor/insights/insights-overview.md), Azure provides powerful distributed tracing capability directly from the Azure portal.  To learn more, complete this [tutorial](spring-cloud-tutorial-distributed-tracing.md).

## Next steps

To get started, complete the [Spring Cloud quickstart](spring-cloud-quickstart.md)

Samples are available on GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).