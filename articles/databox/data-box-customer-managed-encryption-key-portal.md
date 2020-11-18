---
title: Use the Azure portal to manage customer-managed keys for Azure Data Box
description: Learn how to use Azure portal to create and manage customer-managed keys with Azure Key Vault for an Azure Data Box. Customer-managed keys let you create, rotate, disable, and revoke access controls.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alkohli
ms.subservice: pod
---

# Use customer-managed keys in Azure Key Vault for Azure Data Box

Azure Data Box protects the device unlock key (also known as the device password), which is used to lock a device, via an encryption key. By default, this encryption key is a Microsoft managed key. For additional control, you can use a customer-managed key.

Using a customer-managed key doesn't affect how data on the device is encrypted. It only affects how the device unlock key is encrypted.

To keep this level of control throughout the order process, use a customer-managed key when you create your order. For more information, see [Tutorial: Order Azure Data Box](data-box-deploy-ordered.md).

This article shows how to change the type of encryption key for an existing Data Box order or change the customer-managed key currently in use via the [Azure portal](https://portal.azure.com/). You can change the key vault, key, or key version or change the user identity or identity type you're using to manage the key.

This article applies to Azure Data Box and Azure Data Box Heavy devices.

## Requirements

The customer-managed key for a Data Box order must meet the following requirements:

- The key must be created and stored in an Azure Key Vault that has **Soft delete** and **Do not purge** enabled. For more information, see [What is Azure Key Vault?](../key-vault/general/overview.md). You can create a key vault and key while creating or updating your order.

- The key must be an RSA key of 2048 size or larger.

## Enable key

To enable a customer-managed key for your existing Data Box order in the Azure portal, follow these steps:

1. Go to the **Overview** screen for your completed Data Box order.

    ![Overview screen of a Data Box order - 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)<!--Edit order name, subscription, subscription ID-->

2. Go to **Settings > Encryption**, and select **Customer managed key**. Then select **Select a key and key vault**.

    ![Select the customer-managed key encryption option](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)<!--EDIT order name in breadcrumbs and object name-->

   On the **Select key from Azure Key Vault** screen, your subscription is automatically populated.

 3. For **Key vault**, you can select an existing key vault from the dropdown list or select **Create new** and create a new key vault.

     ![Key vault options when selecting a customer-managed key](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)<!--EDIT order name, subscription-->

     If you're creating a new key vault, on the **Create key vault** screen, enter the subscription, a resource group, a key vault name, and other info for your key vault. In **Recovery options**, ensure that **Soft delete** and **Purge protection** are enabled. Select **Review + Create**.

      ![Review and create Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)<!--EDIT subscription-->

      Review the information for your key vault, and select **Create**. Wait for a couple minutes for key vault creation to complete.

       ![Create Azure Key Vault with your settings](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)<!--EDIT subscription-->

4. On the **Select key from Azure Key Vault** screen, you can select an existing key from the key vault or create a new one.

    ![Select key from Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   If you want to create a new key, select **Create new**. You must use an RSA key. The size can be 2048 or greater.

    ![Create new key in Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Enter a name for your new key, accept the other defaults, and select **Create**. You'll be notified that a key has been created in your key vault.

    ![Name new key](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. For **Version**, you can select an existing key version from the drop-down list.

    ![Select version for new key](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    If you want to generate a new key version, select **Create new**.

    ![Open a dialog box for creating a new key version](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Choose settings for the new key version, and select **Create**.

    ![Create a new key version](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. When you've selected a key vault, key, and key version, choose **Select**.

    ![A key in an Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    The **Encryption type** settings show the key vault and key that you chose.

    ![Key and key vault for customer-managed key](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Select the type of identity to use to manage the customer-managed key for this resource. You can use the **system assigned** identity that was generated during order creation or choose a user-assigned identity.

    A user-assigned identity is an independent resource that you can use to manage access to resources. For more information, see [Managed identity types](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Select the identity type](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    If you want to assign a user identity, select **User assigned**, select **Select a user identity** and then select your managed identity that you want to use.

    ![Select an identity to use](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    You can't create a new user identity here. To find out how to create one, see [Create, list, delete or assign a role to a user-assigned managed identity using the Azure portal](/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).

    The selected user identity is shown in the **Encryption type** settings.

    ![A selected user identity shown in Encryption type settings](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. Select **Save** to save the updated **Encryption type** settings.

     ![Save customer-managed key](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    The key URL is displayed under **Encryption type**.

    ![Customer-managed key URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## Change the key

To change the customer-managed key you're currently using for your order, follow these steps:

1. Go to the **Overview** screen for your completed Data Box order.

    ![Overview screen of a Data Box order with customer-managed key - 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Go to **Settings > Encryption**. Under **Encryption type**, DO THIS.

    ![Choose encryption option - 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

 
## Use different key version

To use a different version of your customer-managed key, follow these steps:

1. Go to the **Overview** screen for your completed Data Box order.

    ![Overview screen of a Data Box order - 3](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Go to **Settings > Encryption**. Under **Encryption type**, you can choose how you want to protect your device unlock key, by using either a Microsoft managed key or a customer-managed key. You can select either key type when you place your Data Box order and change it later.

    ![Choose encryption option - 3](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

## Change user identity for key

To change the user identity you're using to manage access to the customer-managed key for this order, follow these steps:

1. Go to the **Overview** screen for your completed Data Box order.

    ![Overview screen of a Data Box order - 4](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Go to **Settings > Encryption**. Under **Encryption type**, you can choose how you want to protect your device unlock key, by using either a Microsoft managed key or a customer-managed key. You can select either key type when you place your Data Box order and change it later.

    ![Choose encryption option - 4](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)


## Switch to system-generated key

To switch back to using a system-managed key, follow these steps:

1. Go to the **Overview** screen for your completed Data Box order.

    ![Overview screen of a Data Box order - 5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Go to **Settings > Encryption**. Under **Encryption type**, select **System managed key**.

    ![Choose encryption option - 5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

## Troubleshoot errors

If you receive any errors related to your customer-managed key, use the following table to troubleshoot.

| Error   code| Error details| Recoverable?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Could not fetch the passkey as the customer managed key is disabled.| Yes, by enabling the key version.|
| SsemUserErrorEncryptionKeyExpired| Could not fetch the passkey as the customer managed key has expired.| Yes, by enabling the key version.|
| SsemUserErrorKeyDetailsNotFound| Could not fetch the passkey as the customer managed key could not be found.| If you deleted the key vault, you can't recover the customer-managed key.  If you migrated the key vault to a different tenant, see [Change a key vault tenant ID after a subscription move](../key-vault/general/move-subscription.md). If you deleted the key vault:<ol><li>Yes, if it is in the purge-protection duration, using the steps at [Recover a key vault](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>No, if it is beyond the purge-protection duration.</li></ol><br>Else if the key vault underwent a tenant migration, yes, it can be recovered using one of the below steps: <ol><li>Revert the key vault back to the old tenant.</li><li>Set `Identity = None` and then set the value back to `Identity = SystemAssigned`. This deletes and recreates the identity once the new identity has been created. Enable `Get`, `Wrap`, and `Unwrap` permissions to the new identity in the key vault's Access policy.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Could not fetch the passkey as the customer managed key access is revoked.| Yes, check if: <ol><li>Key vault still has the MSI in the access policy.</li><li>Access policy provides permissions to Get, Wrap, Unwrap.</li><li>If key vault is in a vNet behind the firewall, check if **Allow Microsoft Trusted Services** is enabled.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Could not fetch the passkey as the associated key vault for the customer managed key could not be found. | If you deleted the key vault, you can't recover the customer-managed key.  If you migrated the key vault to a different tenant, see [Change a key vault tenant ID after a subscription move](../key-vault/general/move-subscription.md). If you deleted the key vault:<ol><li>Yes, if it is in the purge-protection duration, using the steps at [Recover a key vault](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>No, if it is beyond the purge-protection duration.</li></ol><br>Else if the key vault underwent a tenant migration, yes, it can be recovered using one of the below steps: <ol><li>Revert the key vault back to the old tenant.</li><li>Set `Identity = None` and then set the value back to `Identity = SystemAssigned`. This deletes and recreates the identity once the new identity has been created. Enable `Get`, `Wrap`, and `Unwrap` permissions to the new identity in the key vault's Access policy.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Could not fetch the passkey as the customer managed key could not be found.| Yes, check if: <ol><li>Key vault still has the MSI in the access policy.</li><li>Identity is of type System assigned.</li><li>Enable Get, Wrap and Unwrap permissions to the identity in the key vault’s Access policy.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | Adding new User Assigned Identity failed as you have reached the limit on the total number of user assigned identities that can be added. | Please retry the operation with fewer user identities or remove some user assigned identities from the resource before retrying. |
| SsemUserErrorKekUserIdentityNotFound<!--Should this be "SsemUserErrorKeyUserIdentityNotFound"?--> | Applied a customer managed key but the user assigned identity that has access to the key was not found in the active directory. <br> Note: This is for the case when user identity is deleted from Azure.| Please try adding a different user assigned identity selected to your key vault to enable access to the customer managed key. For more information, see how to [Enable the key](#enable-key). |
| SsemUserErrorUserAssignedIdentityAbsent | Could not fetch the passkey as the customer managed key could not be found. | Could not access the customer managed key. Either the User Assigned Identity (UAI) associated with the key is deleted or the UAI type has changed. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Managed identity access operation failed. <br> Note: This is for the scenario when subscription is moved to different tenant. Customer has to manually move the identity to new tenant. PFA mail for more details. | Please try adding a different user assigned identity selected to your key vault to enable access to the customer managed key. For more information, see how to [Enable the key](#enable-key). |
| SsemUserErrorKeyVaultBadRequestException<!--Second instance of this error code. Should this have a different error code?--> | Applied a customer managed key but the key access has not been granted or has been revoked, or unable to access key vault due to firewall being enabled. | Add the identity selected to your key vault to enable access to the customer managed key. If key vault has firewall enabled, switch to a system assigned identity and then add a customer managed key. For more information, see how to [Enable the key](#enable-key). |
| Generic error  | Could not fetch the passkey.| This is a generic error. Contact Microsoft Support to troubleshoot the error and determine the next steps.|

## Next steps

- [What is Azure Key Vault?](../key-vault/general/overview.md)
- [Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal](../key-vault/secrets/quick-create-portal.md)
