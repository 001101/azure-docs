---
title: What is Azure SignalR | Microsoft Docs
description: An overview of the Azure SignalR service.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: 

ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: zhshang
#Customer intent: As a developer, I want to push real-time data in my ASP.NET apps. So that my clients are updated without the need to poll, or request updates.
---

# What is Azure SignalR Service

Microsoft Azure SignalR Service is currently in [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure SignalR Service simplifies the process of adding real-time web functionality to applications over HTTP. This real-time functionality allows the web server to push content updates to connected clients. As a result, clients are updated without the need to poll the server, or submit new HTTP requests for updates.

This article provides an overview of the Azure SignalR Service.

## What is SignalR Service used for? 

There are many application types that require real-time content updates. The following example application types are good candidates for using the Azure SignalR Service:

* Apps that require high frequency updates from the server. Examples are gaming, social networks, voting, auction, maps, and GPS apps.
* Dashboards and monitoring apps. Examples include company dashboards, instant sales updates, or travel alerts.
* Collaborative apps. Whiteboard apps and team meeting software are examples of collaborative apps.
* Apps that require notifications. Social networks, email, chat, games, travel alerts, and many other apps use notifications.

Internally, SignalR is an abstraction over a number of techniques used for building real-time web applications. [WebSockets](https://wikipedia.org/wiki/WebSocket) is the optimal transport, but other techniques like [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) and Long Polling are used when other options aren't available. SignalR automatically detects and initializes the appropriate transport based on the features supported on the server and client.


## How to use Azure SignalR

There are three ways to use Azure SignalR:

- **[Scale an ASP.NET Core SignalR App](signalr-overview-scale-aspnet-core.md)** - Integrate Azure SignalR with an ASP.NET Core SignalR app to scale out to tens of thousands of connections
- **[Build serverless real-time apps](signalr-overview-azure-functions.md)** - Use Azure Functions' integration with Azure SignalR to build serverless real-time applications in languages such as JavaScript and C#
- **[Integrate with REST API](#)** - Use Azure SignalR's REST API to build real-time apps on any platform

