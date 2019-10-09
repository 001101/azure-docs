---
title: 'Enable and work with Azure Bastion diagnostic logs  | Microsoft Docs'
description: In this article, learn how enable and work with Azure Bastion diagnostic logs.
services: bastion
author: cherylmc

ms.service: bastion
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc

---

# Enable and work with Bastion diagnostic logs

As users connect to workloads using Azure Bastion, Bastion can log diagnostics of the remote sessions. You can then use the diagnostics to view which users connected to which workloads, at what time, from where and other such relevant logging information. In order to use the diagnostics, you must enable diagnostics logs on Azure Bastion. This article helps you enable diagnostics logs, and then view the logs.

> [!IMPORTANT]
> This public preview is provided without a service level agreement and should not be used for production workloads. Certain features may not be supported, may have constrained capabilities, or may not be available in all Azure locations. See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for details.
>

## <a name="enable"></a>Enable the diagnostics log

1. In the  [Azure portal](https://aka.ms/BastionHost) for the Bastion preview, navigate to your Azure Bastion resource and select **Diagnostics settings** from the Azure Bastion page.

    ![diagnostics settings](./media/diagnostic-logs/diagnostics-settings.png)
1. Select **Diagnostics settings**, then select **+Add diagnostic setting** to add a destination for the logs.

    ![add diagnostic setting](./media/diagnostic-logs/add-diagnostic-setting.png)
1. On the **Diagnostics settings** page, select the type of storage account to be used for storing diagnostics logs.

    ![select storage location](./media/diagnostic-logs/add-storage-account.png)
1. When you complete the settings, it will look similar to this example:

    ![example settings](./media/diagnostic-logs/example-settings.png)

## <a name="view"></a>View diagnostics log

To access your diagnostics logs, you can directly use the storage account that you specified while enabling the diagnostics settings.

1. Navigate to your storage account resource, then to **Containers**. You see the **insights-logs-bastionauditlogs** blob created in your storage account blob container.

    ![diagnostics settings](./media/diagnostic-logs/navigate-to-logs.png)
1. As you navigate to inside the container, you will see various folders in your blog.  These folders indicate the resource hierarchy for your Azure Bastion resource.

    ![add diagnostic setting](./media/diagnostic-logs/resource-h.png)
1. Navigate to the full hierarchy of your Azure Bastion resource whose diagnostics logs you wish to access/view. The ‘y=’, ‘m=’, ‘d=’, ‘h=’ and ‘m=’ indicate the year, month, day, hour, and minute respectively for the diagnostic logs.

    ![select storage location](./media/diagnostic-logs/resource-location.png)
1. Locate the json file created by Azure Bastion that contains the diagnostics log data for the time-period navigated to.

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/2ff277e1-6b41-48de-80fa-0551b8b3611a/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"ashishj",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/2ff277e1-6b41-48de-80fa-0551b8b3611a/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"2ff277e1-6b41-48de-80fa-0551b8b3611a",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"712faceb-b556-449e-b7ab-101b326bfd4c"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"2ff277e1-6b41-48de-80fa-0551b8b3611a"
   }
   ```

## Next steps

Read the [Bastion FAQ](bastion-faq.md).