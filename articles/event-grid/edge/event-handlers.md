---
title: Event Handlers - Azure Event Grid IoT Edge | Microsoft Docs 
description: Event Handlers in Event Grid on IoT Edge.  
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: 
ms.date: 07/18/2019
ms.topic: article
ms.service: event-grid
services: event-grid
---

# Event Handlers

Event handlers refer to entities that subscribe to events. They are entities that can handle HTTP POST requests. Subscribers can be other IoT Edge modules, non-IoT Applications, Webhooks (for example, Azure Functions) as well as Event Grid in the cloud.

## Possible Event Handler(s)
* Other IoT Edge Modules (on the same device or different device)
* Webhook (same device, different device, cloud)
* EdgeHub (same device)
* Event Grid in the cloud

>[!IMPORTANT]
>[Security and Authentication](security-authentication.md) documentation describes the various options available to securely expose Event Grid functionality to Non-IoT Applications.