---
 title: include file
 description: include file
 services: storage
 author: roygara
 ms.service: storage
 ms.topic: include
 ms.date: 12/27/2019
 ms.author: rogarana
 ms.custom: include file
---
By default, standard file shares can span only up to 5 TiB, although the share limit can be increased to 100 TiB. To do this, *large file share* feature must be enabled at the storage account-level. Enabling this flag is only required for standard file shares; all premium file shares are already enabled for provisioning up to the full 100 TiB capacity.

You can enable large file shares when you create a new storage account or on an existing storage account, however this can only be done on a locally redundant or zone redundant storage account. Enabling the large file share feature on a locally redundant or zone redundant storage account also disables the ability to convert the storage account to geo-redundant or geo-zone-redundant storage in the future.

You can learn more about how to enable large file shares on a new storage account by following the steps in the [creating an Azure file share](../articles/storage/files/storage-how-to-create-file-share.md) how to guide. To enable large file shares on an existing storage account, navigate to the **Configuration** view in the storage account's table of contents, and switch the large file share rocker switch to enabled:

![A screenshot of the enable large file share rocker switch in the Azure portal](media/storage-files-tiers-enable-large-shares/enable-LFS-1.png)

You can also enable 100 TiB file shares through the [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet and the [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure CLI command.