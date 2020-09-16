---
title: Enable SMB multichannel
description: Learn how to enable SMB Multichannel on Azure premium file shares.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/04/2020
ms.author: rogarana
ms.subservice: files
---

# Enable SMB Multichannel on a filestorage account (preview) 

Azure FileStorage accounts support SMB Multichannel (preview), which increases the performance from an SMB 3.x client by establishing multiple network connection to your premium file shares. This article provides step-by-step guidance to enable SMB Multichannel on an existing storage account. For detailed information on Azure Files SMB Multichannel, see SMB Multichannel performance.

## Restrictions

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### Regional availability

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## Prerequisites

[Create a FileStorage account](storage-how-to-create-premium-fileshare.md).

## Getting started

Open a PowerShell window and sign into your Azure subscription. From there, you can register for the SMB Multichannel preview with the following commands.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> Registration may take up to an hour.

### Verify that feature registration is complete

Since it may take up to an hour to enable the feature on your storage account, you may want to use the following command to validate that it is registered for your subscription:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## Enable (Disable) SMB Multichannel 
Once you have created a FileStorage account, you can follow the instructions to update SMB Multichannel settings for your storage account.

# [Portal](#tab/azure-portal)
1. Sign into the Azure portal and navigate to the FileStorage storage account you want to configure SMB Multichannel.
1. Select **File shares** under **File service**, and then select **File share settings**.
1. Toggle **SMB Multichannel** to **on** (or **off** to disable) and select **save**.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Screenshot of storage account, smb multichannel is toggled on.":::

If the SMB Multichannel option is not visible under File share settings or you get an failed to update error while updating the configuration, please make sure that your subscription is registered and your account is in one of the [supported regions](#regional-availability) with supported account type and replication. See details on the  availablity section.


# [PowerShell](#tab/azure-powershell)
Azure PowerShell does not yet support configuring SMB Multichannel. Please see the portal instructions to configure SMB Multichannel on storage account.

# [Azure CLI](#tab/azure-cli)
Azure CLI does not yet support configuring SMB Multichannel. Please see the portal instructions to configure SMB Multichannel on storage account.

> [!NOTE]
> Any changes to SMB Multichannel configuration settings will apply to all file shares under the storage account. You will need to remount the share on your client for the changes to take effect, though.


## Next steps 

- [Remount your file share](storage-how-to-use-files-windows.md) to take advantage of SMB Multichannel.
- [Troubleshoot any issues you have related to SMB Multichannel](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- To learn more about the improvements, see [SMB Multichannel performance](storage-files-smb-multichannel-performance.md)
 - To learn more about the Windows SMB Multichannel feature, see [Deploy SMB Mulitchannel](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn610980(v=ws.11)).
