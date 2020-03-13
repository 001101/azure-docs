---
 title: include file
 description: include file
 services: cognitive-services
 author: erindormier
 ms.service: cognitive-services
 ms.topic: include
 ms.date: 03/11/2020
 ms.author: egeaney
 ms.custom: include
---

## Microsoft-managed keys

By default, your storage account uses Microsoft-managed encryption keys. You can see the encryption settings for your storage account in the **Encryption** section of the [Azure portal](https://portal.azure.com), as shown in the following image.

<TODO: Add image>

## Customer-managed keys with Azure Key Vault

You must use Azure Key Vault to store your customer-managed keys. You can either create your own keys and store them in a key vault, or you can use the Azure Key Vault APIs to generate keys. The Cognitive Services resource and the key vault must be in the same region and in the same Azure Active Directory (Azure AD) tenant, but they can be in different subscriptions. For more information about Azure Key Vault, see [What is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

### Enable customer-managed keys for a storage account

A new Cognitive Services resource is always encrypted using Microsoft-managed keys. It's not possible to enable customer-managed keys at the time that the resource is created. Customer-managed keys are stored in Azure Key Vault, and the key vault must be provisioned with access policies that grant key permissions to the managed identity that is associated with the Cognitive Services resource. The managed identity is available only after the resource is created.

To learn how to use customer-managed keys with Azure Key Vault for Cognitive Services encryption, see one of these articles:

- [Configure customer-managed keys with Key Vault for Cognitive Services encryption from the Azure portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)
- [Configure customer-managed keys with Key Vault for Cognitive Services encryption from PowerShell](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-powershell.md)
- [Configure customer-managed keys with Key Vault for Cognitive Services encryption from Azure CLI](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-cli.md)

> [!IMPORTANT]
> Customer-managed keys rely on managed identities for Azure resources, a feature of Azure AD. Managed identities do not currently support cross-directory scenarios. When you configure customer-managed keys in the Azure portal, a managed identity is automatically assigned under the covers. If you subsequently move the subscription, resource group, or resource from one Azure AD directory to another, the managed identity associated with the storage account is not transferred to the new tenant, so customer-managed keys may no longer work. For more information, see **Transferring a subscription between Azure AD directories** in [FAQs and known issues with managed identities for Azure resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### Store customer-managed keys in Azure Key Vault

To enable customer-managed keys, you must use an Azure Key Vault to store your keys. You must enable both the **Soft Delete** and **Do Not Purge** properties on the key vault.

Only RSA keys of size 2048 are supported with Cognitive Services encryption. For more information about keys, see **Key Vault keys** in [About Azure Key Vault keys, secrets and certificates](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### Rotate customer-managed keys

You can rotate a customer-managed key in Azure Key Vault according to your compliance policies. When the key is rotated, you must update the Cognitive Services resource to use the new key URI. To learn how to update the storage account to use a new version of the key in the Azure portal, see the section titled **Update the key version** in [Configure customer-managed keys for Cognitive Services by using the Azure portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

Rotating the key does not trigger re-encryption of data in the resource. There is no further action required from the user.

### Revoke access to customer-managed keys

To revoke access to customer-managed keys, use PowerShell or Azure CLI. For more information, see [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) or [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Revoking access effectively blocks access to all data in the Cognitive Services resource, as the encryption key is inaccessible by Cognitive Services.


