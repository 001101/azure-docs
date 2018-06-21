---
title: Terminology for Azure Service Fabric Mesh | Microsoft Docs
description: Learn about commonly asked questions and answers for Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: 
author: mikkelhegn
ms.author: mikhegn
ms.date: 06/21/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
---
# Service Fabric Mesh terminology

Azure Service Fabric Mesh is a fully managed service enabling developers to deploy containerized applications without managing virtual machines, storage, or networking resources. This article details the terminology used by Azure Service Fabric Mesh to understand the terms used in the documentation.

## Service Fabric

Service Fabric is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices. Service Fabric is the orchestrator that powers Service Fabric Mesh.  Service Fabric provides options for how you can build and run your microservices applications. You can use any framework to write your services and choose where to run the application from multiple environment choices.

## Frameworks

Service Fabric as a platform is agnostic to the framework you choose for writing services.  For example, can write services using ASP.NET, Java, Node.js, or Python. The Service Fabric ecosystem also provides frameworks, which enable you to take advantage of platform features (such as highly available state). [Reliable services](/azure/service-fabric/service-fabric-reliable-services-introduction) and [reliable actors](/azure/service-fabric/service-fabric-reliable-actors-introduction) are specific examples of Service Fabric frameworks.

## Application types

**Service Fabric application**: Any application that is described by the YAML, resource-based deployment model. These applications can run in any Service Fabric environment.

**Reliable services application**: Any application described by the XML-based [application model](/azure/service-fabric/service-fabric-application-model). Reliable Services applications currently cannot be deployed to Service Fabric Mesh.

## Deployment models

To deploy your services to an environment, you need to describe how they should run. Service Fabric supports three different deployment models.

**Resource model**: Describes Service Fabric applications and resources. Resources are defined using a YAML file or JSON file (using the Azure Resource Model schema). Applications described using the resource model can run in any Service Fabric environment and are deployed as running instances.

**Manifest model**: Describes reliable services applications.  Application and service types are defined using XML manifest files and are registered with the cluster.  Multiple instances of application and service types can run at the same time.

**Docker Compose**: A tool for defining and running multi-container Docker applications. [Compose](https://docs.docker.com/compose/) is part of the Docker project. Service Fabric provides limited support for deploying applications using the Compose model.

## Environments

Service Fabric is the platform technology, which several different services and products are based on. You can run your applications in the following environments:

**Service Fabric Mesh**: A server-less platform that runs on top of [Service Fabric](/azure/service-fabric/service-fabric-overview) and is hosted on Microsoft Azure.

**Azure Service Fabric**: The Azure hosted Service Fabric offering. It provides integration between Service Fabric and the Azure infrastructure, along with upgrade and configuration management of Service Fabric clusters.

**Service Fabric standalone**: A set of installation and configuration tools to [deploy Service Fabric clusters anywhere](/azure/service-fabric/service-fabric-deploy-anywhere) (on-premises or on any cloud provider). Not managed by Azure.

**Service Fabric onebox or development cluster**: Provides the developer experience on Windows, Linux, or Mac for creating Service Fabric applications.

## Environment, framework, and deployment model support matrix
Different environments have different level of support for frameworks and deployment models. The following tables describe the supported combinations.

|Frameworks\Deployment Model |Resource model |Manifest model | Compose|
|---|---|---|---|
|Reliable Actors and Reliable Services |Not supported |Supported |Not supported |
|Any other framework |Supported in containers |Supported as processes and in containers |Supported in containers |


|Environment\Deployment Model |Resource model |Manifest model |Compose |
|---|---|---|---|
|Azure Service Fabric Mesh |Supported |Not supported|Not supported |
|All other environments |Supported (Some resources have prerequisites to work in an environment) |Supported |Limited Support |

## Next steps

To learn more about Service Fabric Mesh, read the overview:
- [Service Fabric Mesh overview](service-fabric-mesh-overview.md)