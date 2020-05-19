---
title: Partner Topics
description: Send events from 3rd party Event Grid SaaS and PaaS partners directly to Azure services with Azure Event Grid.
services: event-grid
author: banisadr

ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
---

# Partner Topics in Azure Event Grid (Preview)
Partner Topics allow you to connect 3rd party event sources, such as SaaS and PaaS solutions, directly to Event Grid. This allows you to subscribe to events from partners in the same way you are able to subscribe to events from other Azure services, such as Azure Storage and IoT Hub. 

## Available Partners
The first partner available through Event Grid Partner Topics is Auth0. The [Auth0 partner topic](auth0-overview.md) enables you to connect your Auth0 and Azure accounts in order to react to, log, and monitor Auth0 events in real time.

[Try it out](auth0-how-to.md) today by logging in to your Auth0 account and creating an Event Grid integration. Once you click create in Auth0, you’ll see a pending Auth0 Topic in your Azure account. Click activate and you’ll be able to create Event Subscriptions, route, filter, and deliver your events just as you do any other event source.

## Pricing
Partner topics are charged at the same operation rate as system topics.

## Limits
Partner Topics are in Public Preview. During public preview, Partner topics are subject to the [same limits](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) as system topics and custom topics.

## How do I become an Event Grid partner?
The infrastructure created to support this launch makes it easy and quick for new partners to integrate their eventing capabilities with Event Grid. Read the [partner onboarding documentation](partner-onboarding-how-to.md) for more.

## Next steps

- [Auth0 Partner Topic](auth0-overview.md)
- [How to use the Auth0 Partner Topic](auth0-how-to.md)
- [Become an Event Grid partner](partner-onboarding-overview.md)