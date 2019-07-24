---
title: Manage integration service environments in Azure Logic Apps
description: Find and manage connections, logic apps, and integration accounts in your integration service environment (ISE) for Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/24/2019
---

# Manage your integration service environment (ISE) in Azure Logic Apps

Periodically, you might want to review and manage the connections, logic apps, and integration accounts that exist in your [integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). This article shows how you can complete these tasks.

## Find your ISE

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the portal's search box, enter "integration service environments", and then select **Integration Service Environments**.

   ![Find integration service environments](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. From the results list, select your integration service environment.

   ![Select integration service environment](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

## Find logic apps

1. On your ISE menu, under **Settings**, select **Logic apps**.

   ![Find logic apps](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. To remove logic apps from your ISE when no longer needed, select those logic apps, and then select **Delete**.

## Find connections and connectors

* To view the API connections that were created by logic apps running in your ISE, on your ISE menu, under **Settings**, select **API connections**.

  To remove connections from your ISE when no longer needed, select those connections, and then select **Delete**.

  ![Find API connections](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

* To view custom connectors that were created in your ISE, on your ISE menu, under **Settings**, select **Custom connectors**.

  To remove custom connectors from your ISE when no longer needed, select those connectors, and then select **Delete**.

  ![Find custom connectors](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

## Find integration accounts

1. On your ISE menu, under **Settings**, select **Integration accounts**.

   ![Find integration accounts](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. To remove integration accounts from your ISE when no longer needed, select those integration accounts, and then select **Delete**.

## Next steps

* Learn how to [connect to Azure virtual networks from isolated logic apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
