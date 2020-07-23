---
title:  "Understanding app and deployment in Azure Spring Cloud"
description: The distinction between application and deployment in Azure Spring Cloud.
author:  MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
---

# Understanding app and deployment in Azure Spring Cloud

**App** and **Deployment** are the two key concepts in the resource model of Azure Spring Cloud. In Azure Spring Cloud, an *App* is an abstraction of one business app or one microservice.  One version of code or binary you deployed as the *App* runs in a *Deployment*.

 ![Apps and Deployments](./media/spring-cloud-app-and-deployment/app-deployment.png)

Azure Spring Cloud standard tier allows one App to have one production deployment and one staging deployment, so that you can do blue/green deployment on it easily.

## App
The following features/properties are defined on App level.

| Enum | Definition |
|:--:|:----------------:|
| Public Endpoint | The URL to access the app |
| Custom Domain |  |
| Service Binding |  |
| Managed Identity | |
| Persistent Storage| |

## Deployment

The following features/properties are defined on Deployment level, and will be exchanged when swapping production/staging deployment.

| Enum | Definition |
|:--:|:----------------:|
| CPU | Number of vcores per App instance |
| Memory |  |
| Instance Count| |
| Auto-Scale | |
| JVM Options | |
| Environment Variables | |
| Runtime Version| Java 8/Java 11|

## Restrictions

* **An App must have one production Deployment**: Deleting a production Deployment is blocked by the API. It should be swapped to staging before deleting.
* **An App can have at most two Deployments**: Creating more than two deployments is blocked by the API. Deploy your new binary to either the existing production or staging deployment.
* **Deployment management is not available in Basic Tier**: Use Standard tier for Blue-Green deployment capability.

## See also
* [Set up a staging environment in Azure Spring Cloud](spring-cloud-howto-staging-environment.md)