---
title: Overview of Azure Service Fabric Mesh
description: An overview of Azure Service Fabric Mesh. With Service Fabric Mesh, you can deploy and scale your application without worrying about the infrastructure needs of your application.
services: Azure Service Fabric Mesh
keywords: 
author: rwike77
ms.author: ryanwi
ms.date: 06/12/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
#Customer intent: As a developer, I want to deploy and manage services in containers on a serverless platform.
---

# What is Service Fabric Mesh?

Azure Service Fabric Mesh is a fully managed service that enables developers to deploy containerized applications without managing virtual machines, storage, or networking. Applications hosted on Service Fabric Mesh run and scale without you worrying about the infrastructure powering it.  Service Fabric Mesh consists of clusters of thousands of machines at geo-scale.  These clusters span [Azure Availability Zones](/azure/availability-zones/az-overview) and regions for high availability and reliability. All cluster operations are hidden from the developer. Simply upload your code and specify scale, availability requirements, and resource limits.  Service Fabric Mesh automatically allocates the infrastructure needed by your application and also handles infrastructure failures, making sure your applications are highly available. You only need to care about the health and responsiveness of your application, not the infrastructure.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

This article provides an overview of the key benefits of Service Fabric Mesh.

## Great developer experience

Tooling in Visual Studio and Visual Studio Code provides a powerful editing and debugging experience.  Multiple programing languages and frameworks are supported, giving you the ability to run any code.

With Service Fabric Mesh you can:

- "Lift and shift" existing applications into containers.  Take advantage of containerization to modernize your legacy applications. 
- Build new microservices applications in containers. Deploy new applications at scale and integrate with other Azure services or legacy applications running in containers. Each containerized microservice is part of a secure, network isolated application with resource governance policies defined for CPU cores, memory, disk space, and more.
- Integrate with and extend existing applications without making changes to those applications. Bring your own virtual network that provides connectivity to the existing application from the new application.  
- Modernize your existing Cloud Services applications by migrating into containers.  

## Simple operational lifecycle

Easily manage running applications, including application upgrades and versioning, monitoring applications and debugging in production environments. These applications can be a single container instance or can consist of multiple containers isolated within their own network. Applications run efficiently with fast deployment, placement, and failover times.

With Service Fabric Mesh you can:

- Deploy and manage applications without having to explicitly provision and manage infrastructure.  Service Fabric Mesh provisions, upgrades, patches, and maintains the underlying clusters for you.
- Setup continuous integration using the integrated tooling to easily package and deploy applications.
- Leverage all the features of Azure Resource Manager resources (for example, audit trail and RBAC) because containers, services and applications are Resource Manager resources.
- Deploy and manage resources using the [Azure portal](https://portal.azure.com), Resource Manager templates, or Azure CLI/PowerShell libraries.
- Set up operational monitoring and alerting using [Application Insights](/azure/application-insights/) (or your tool of choice) to capture operational and diagnostic traces from the platform. 
- Access application diagnostics information emitted from the application model using [Application Insights](/azure/application-insights/) or your tool of choice.
- Optimize resources usage by specifying auto-scale rules for the containers in the application definition.  
- Enable chaos testing on deployed applications to verify the services ability to handle failures in production.
- Create network isolation and security boundaries for applications, a powerful feature when combined with Hyper-V containers. Isolate the network traffic to and from the containers using multiple IPs per container and isolated virtual networks per application.  

## Mission critical platform capabilities

Service Fabric Mesh creates a collection of clusters that span [Azure Availability Zones](/azure/availability-zones/az-overview) and/or geo-political boundaries. Applications are described with a set of intents such as scale, hardware requirements, durability requirements, and security policies.  When the application deploys, Service Fabric Mesh finds the optimal place to run it.

With Service Fabric Mesh you can:

- Take advantage of high availability, scaling in/out, discoverability, orchestration, message routing, reliable messaging, no-downtime upgrades, security/secrets management, disaster recovery, state management, configuration management, and distributed transactions.
- Choose between multiple application models when creating applications.
- Use platform capabilities exposed through REST endpoints by consuming language-specific bindings generated using Swagger.
- Deploy applications across [Availability Zones](/azure/availability-zones/az-overview) and multiple regions for geo-reliability.
- Use all the security and compliance features that Azure provides.

## Next steps

It only takes a few steps to deploy a sample project with the Azure CLI. For more information, see [Deploy a container](service-fabric-mesh-quickstart-deploy-container.md). 

If you're using Visual Studio, try the [Create an ASP.NET Core website](service-fabric-mesh-tutorial-create-dotnetcore.md) tutorial.



<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md