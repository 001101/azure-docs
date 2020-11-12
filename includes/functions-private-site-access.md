---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
---
[Azure Private Endpoint](../private-link/private-endpoint-overview) is a network interface that connects you privately and securely to a service powered by Azure Private Link.  Private Endpoint uses a private IP address from your virtual network, effectively bringing the service into your virtual network.

You can use Private Endpoint for your functions hosted in the [Premium](../articles/azure-functions/functions-premium-plan.md) and [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) plans.

When creating an inbound private endpoint connection for functions, you will also need a DNS record to resolve  the private address.  By default a private DNS record will be created for you when creating a private endpoint using the Azure portal.

To call other services that have a private endpoint connection, such as storage or service bus, be sure to configure your app to make [outbound calls to private endpoints](#Private-endpoints).

To learn more, see [using Private Endpoints for Web Apps](../articles/app-service/networking/private-endpoint).