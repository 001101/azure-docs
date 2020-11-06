---
title: Use the Azure portal to manage customer-managed keys for Azure Data Box
description: Learn how to use Azure portal to create and manage customer-managed keys with Azure Key Vault for an Azure Data Box. Customer-managed keys let you create, rotate, disable, and revoke access controls.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.subservice: pod
---

# Manage customer-managed keys in Azure Key Vault for Azure Data Box

Azure Data Box protects the device unlock key (also known as device password) that is used to lock the device via an encryption key. By default, the device unlock key for a Data Box order is encrypted with a Microsoft managed key. For additional control over the device unlock key, you can instead use a customer-managed key.

Customer-managed keys must be created and stored in an Azure Key Vault. For more information about Azure Key Vault, see [What is Azure Key Vault?](../key-vault/general/overview.md).

You can configure a customer-managed key when you place a Data Box order or add one after the order is processed. For information about configuring a customer-managed key when you order your Data Box, see [Tutorial: Order Azure Data Box](data-box-deploy-ordered.md).  

This article shows how to use customer-managed keys with Azure Data Box in the [Azure portal](https://portal.azure.com/). This article applies to both Azure Data Box devices and Azure Data Box Heavy devices.

## Requirements

- The key is created and stored in an Azure Key Vault. For more information about Azure Key Vault, see [What is Azure Key Vault?](../key-vault/general/overview.md). 

  You can create a key vault when add a customer-managed key to a new or completed Data Box order or separately.To learn how to create a key vault using the Azure portal, see [Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal](../key-vault/secrets/quick-create-portal.md).

- The key vault is in the same region as the storage accounts used for your data. Multiple storage accounts can be linked with your Azure Data Box resource.

- **Soft delete** and **Do not purge** are set on the key vault. These properties are not enabled by default. To enable these properties, see the **Enabling soft-delete** and **Enabling Purge Protection** sections in one of the following articles:
   - [How to use soft-delete with PowerShell](../key-vault/general/soft-delete-powershell.md).
   - [How to use soft-delete with CLI](../key-vault/general/soft-delete-cli.md).

- The key is an RSA key of 2048 size or larger.

## Add a key

To add a customer-managed key in the Azure portal after your Data Box order is complete, follow these steps.

> [!IMPORTANT]
> You can disable the Microsoft managed key and move to a customer-managed key at any stage of your Data Box order. However, once you create a customer-managed key, you can't switch back to the Microsoft-managed key.

1. Go to the **Overview** screen for your completed Data Box order.

    ![Overview screen of a Data Box order](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Go to **Settings > Encryption**. Under **Encryption type**, you can choose how you want to protect your device unlock key. By default, a Microsoft managed key is used to protect your device unlock password. 

    ![Choose encryption option](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Select **Customer managed key** as the encryption type. Then select **Select a key vault and key**.

    ![Select customer-managed key](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

4. On the **Select key from Azure Key Vault** screen, the subscription is automatically populated. For **Key vault**, you can select an existing key vault from the dropdown list.

    ![Create new Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    You can also select **Create new** to create a new key vault. On the **Create key vault** screen, enter the resource group and a key vault name. Ensure that the **Soft delete** and **Purge protection** are enabled. Accept all other defaults. Select **Review + Create**.

    ![Create new Azure Key Vault 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

5. Review the information associated with your key vault and select **Create**. Wait for a couple minutes for key vault creation to complete.

    ![Create Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

6. In the **Select key from Azure Key Vault**, you can select a key in the existing key vault.

    ![Create new key in Azure Key Vault 3](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

7. If you want to create a new key, select **Create new**. RSA key size can be 2048 or greater.

    ![Create new key in Azure Key Vault 4](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

8. Enter a name for your key, accept the other defaults, and select **Create**.

    ![Create new key](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

9. You are notified that a key is created in your key vault. Select the **Version** and then choose **Select**.

    ![New key created in key vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

10. In the **Encryption type** settings, you can see the key vault and the key selected for your customer-managed key.

    ![Key and key vault for customer-managed key](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

11. Save the key.

    ![Save customer-managed key](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    The key URL is displayed under **Encryption type**.

    ![Customer-managed key URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

## Troubleshoot errors

If you receive any errors related to your customer-managed key, use the following table to troubleshoot.<!--Two new error codes expected?-->

| Error   code| Error details| Recoverable?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Could not fetch the passkey as the customer managed key is disabled.| Yes, by enabling the key version.|
| SsemUserErrorEncryptionKeyExpired| Could not fetch the passkey as the customer managed key has expired.| Yes, by enabling the key version.|
| SsemUserErrorKeyDetailsNotFound| Could not fetch the passkey as the customer managed key could not be found.| If you deleted the key vault, you can't recover the customer-managed key.  If you migrated the key vault to a different tenant, see [Change a key vault tenant ID after a subscription move](../key-vault/general/move-subscription.md). If you deleted the key vault:<ol><li>Yes, if it is in the purge-protection duration, using the steps at [Recover a key vault](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>No, if it is beyond the purge-protection duration.</li></ol><br>Else if the key vault underwent a tenant migration, yes, it can be recovered using one of the below steps: <ol><li>Revert the key vault back to the old tenant.</li><li>Set `Identity = None` and then set the value back to `Identity = SystemAssigned`. This deletes and recreates the identity once the new identity has been created. Enable `Get`, `Wrap`, and `Unwrap` permissions to the new identity in the key vault's Access policy.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Could not fetch the passkey as the customer managed key access is revoked.| Yes, check if: <ol><li>Key vault still has the MSI in the access policy.</li><li>Access policy provides permissions to Get, Wrap, Unwrap.</li><li>If key vault is in a vNet behind the firewall, check if **Allow Microsoft Trusted Services** is enabled.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Could not fetch the passkey as the associated key vault for the customer managed key could not be found. | If you deleted the key vault, you can't recover the customer-managed key.  If you migrated the key vault to a different tenant, see [Change a key vault tenant ID after a subscription move](../key-vault/general/move-subscription.md). If you deleted the key vault:<ol><li>Yes, if it is in the purge-protection duration, using the steps at [Recover a key vault](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>No, if it is beyond the purge-protection duration.</li></ol><br>Else if the key vault underwent a tenant migration, yes, it can be recovered using one of the below steps: <ol><li>Revert the key vault back to the old tenant.</li><li>Set `Identity = None` and then set the value back to `Identity = SystemAssigned`. This deletes and recreates the identity once the new identity has been created. Enable `Get`, `Wrap`, and `Unwrap` permissions to the new identity in the key vault's Access policy.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Could not fetch the passkey as the customer managed key could not be found.| Yes, check if: <ol><li>Key vault still has the MSI in the access policy.</li><li>Identity is of type System assigned.</li><li>Enable Get, Wrap and Unwrap permissions to the identity in the key vault’s Access policy.</li></ol>|
| Generic error  | Could not fetch the passkey.| This is a generic error. Contact Microsoft Support to troubleshoot the error and determine the next steps.|

## Next steps

- [What is Azure Key Vault?](../key-vault/general/overview.md)
- [Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal](../key-vault/secrets/quick-create-portal.md)
