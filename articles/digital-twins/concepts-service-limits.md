---
title: Azure Digital Twin public preview service limits
description: Understanding Azure Digital Twins public preview service limits
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: dwalthermsft
---

# Azure Digital Twin public preview service limits

During Public Preview, Azure Digital Twins will have subscription, instance, and rate limits as described below.  

These limits will be increased and/or removed by General Availability (GA).

## Per-Subscription limits

During Public Preview, each Azure Subscription can create exactly one Azure Digital Twins instance.

Should you need to delete your instance, you can create a new one.

## Per-Instance limits

Each Azure Digital Twins instance can have:

- One `IoTHub` Resource
- One `EventHub` endpoint for event type DeviceMessage
- Up to three `EventHub`, `ServiceBus`, or `EventGrid` endpoints of event type `SensorChange`, `SpaceChange`, `TopologyOperation`, or `UdfCustom`

## Management API limits

- 100 requests per second to Management API
- A single Management API query can return up to 1000 objects

> [!IMPORTANT]
> If you exceed the 1000 object limit, you will receive an error and will need to simplify your query.

## UDF Rate limits

The following limits apply in aggregate to all of the user-defined functions running within your Azure Digital Twins instance:

- 400 client library calls per second
- 100 SendNotification calls per second

> [!NOTE]
> The following actions may cause additional rate limits to be applied temporarily:
> - Topology object metadata edits
> - UDF definition updates
> - Devices sending telemetry for the first time

## Device Telemetry limits

In aggregate, your devices may send up to 100 messages per second to your Azure Digital Twins instance.

## Next steps

Try out an Azure Digital Twins quickstart sample:

> [!div class="nextstepaction"]
> [Find Available Rooms with Fresh Air](./quickstart-view-occupancy-dotnet.md)