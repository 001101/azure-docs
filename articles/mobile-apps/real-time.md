---
title: Azure SignalR
description: Build real-time applications using Azure SignalR
author: elamalani

ms.assetid: 34a8a070-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 09/18/2019
ms.author: emalani
---

# Build real-time apps with Azure SignalR

[Azure SignalR](https://azure.microsoft.com/en-us/services/signalr-service/) allows you to easily add real-time functionality to applications. This real-time functionality allows the service to push content updates to connected clients, such as a single page web or mobile application. As a result, clients are updated without the need to poll the server, or submit new HTTP requests for updates.

## Azure SignalR Features
- **Standard based** as it provides an abstraction over a number of techniques used for building real-time applications.
- **Broad client support** as it works with wide range of clients such as web and mobile browsers, desktop apps, server process, IoT devices and games consoles.
- SignalR service allows **multiple instances to work together** to scale to millions of client connections. It also supports multiple global regions for sharding, high availability, or disaster recovery purposes.
- **Offer rich APIs for different messaging patterns** to send messages to a particular connection, all connections, or a subset of connections that belong to a specific user, or have been placed in an arbitrary group.
- **Remove the burden to self-host SignalR** - Compared to self-hosted SignalR applications, switching to SignalR Service will remove the need to manage back planes that handle the scales and client connections.

## References
   - [Azure Portal](https://portal.azure.com) to create a SignalR resource
   - [Documentation](https://docs.microsoft.com/en-us/azure/azure-signalr/signalr-overview)
   - [Tutorials](https://docs.microsoft.com/en-us/azure/azure-signalr/signalr-tutorial-authenticate-azure-functions)