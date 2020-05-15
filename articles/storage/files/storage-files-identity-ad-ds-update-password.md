---
title: Update AD DS storage account password
description: Learn how to update the password of the active directory domain services account that represents your storage account. This prevents the storage account from being cleaned up when the password expires, preventing authentication failures.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rogarana
---

# Update the password of your storage account identity in AD DS

If you registered the AD DS identity/account that represents your storage account in an organizational unit that enforces password expiration time, you must rotate the password before the maximum password age.

To trigger password rotation, you can run the `Update-AzStorageAccountADObjectPassword` command from the [AzFilesHybrid module](https://github.com/Azure-Samples/azure-files-samples/releases). The command performs actions similar to storage account key rotation, it gets the second Kerberos key of the storage account, and uses it to update the password of the registered account in AD DS. Then, it regenerates the target Kerberos key of the storage account, and updates the password of the registered account in AD DS. You must run this command in an on-premises AD DS-joined environment.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
