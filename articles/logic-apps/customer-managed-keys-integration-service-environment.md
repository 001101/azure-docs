---
title: Set up customer-managed keys to encrypt data at rest in ISEs
description: Create and manage your own encryption keys to secure data at rest for integration service environments (ISEs) in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/14/2020
---

# Set up customer-managed keys to encrypt data at rest for integration service environments (ISEs) in Azure Logic Apps

Azure Logic Apps relies on Azure Storage to store data at rest and automatically [encrypt that data](../storage/common/storage-service-encryption.md) by using Azure Storage Service Encryption (Azure SSE). This encryption protects your data and helps you meet your organizational security and compliance commitments. By default, Azure Storage uses Microsoft-managed keys to encrypt your data.

When you create an [integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) for hosting your logic apps, and you want more control over the encryption keys used by Azure Storage, you can set up, use, and manage your own key by using [Azure Key Vault](../key-vault/key-vault-overview.md). This capability is also known as "Bring Your Own Key" (BYOK), and your key is called a "customer-managed key".

This topic shows how to set up and specify your own encryption key to use when you create your ISE. 

## Considerations

* You can specify a customer-managed key *only* when you create an ISE, not after you create your ISE. After you create an ISE that uses a customer-managed key, you can't disable that key. Currently, no support exists for rotating this key for your ISE.

* Your ISE must use an [external access endpoint](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access) and the [system-assigned managed identity](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) to access resources in other Azure Active Directory (Azure AD) tenants. The managed identity authenticates access for your ISE so that you don't have to sign in with your own credentials.

* Within 30 minutes after you create your ISE, you must add your ISE's system-assigned managed identity to your Azure key vault's access policies. Otherwise, your ISE deployment fails.

## Prerequisites

* An Azure subscription. If you don't have an Azure subscription, [sign up for a free Azure account](https://azure.microsoft.com/free/).

* An Azure key vault that has the **Soft Delete** and **Do Not Purge** properties enabled plus a customer-managed key that has these properties:

  * Key type: **RSA**
  * Key size: **2048**

  ![Create your customer-managed encryption key](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  If you're new to Azure Key Vault, learn [how to create a key vault](../key-vault/quick-create-portal.md#create-a-vault) and [how to configure customer-managed keys](../storage/common/storage-encryption-keys-portal.md) by using the Azure portal. Or, use these Azure PowerShell commands: [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) and [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* A tool that can create your ISE by sending a PUT request, for example, Postman or even a logic app

## Create ISE that supports your key

When you create your ISE, enable support for these items:

* External access endpoint
* The customer-managed key in your key vault
* The system-assigned managed identity that your ISE uses to access that key in your key vault

Here are the properties and values to use in your ISE definition:

```json
{
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-virtual-network-resource-group>/providers/Microsoft.Network/virtualNetworks/<Azure-virtual-network-name>/subnets/<subnet-name-1>",
               "type": "Microsoft.Network/virtualNetworks/subnets"
            },
            {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-virtual-network-resource-group>/providers/Microsoft.Network/virtualNetworks/<Azure-virtual-network-name>/subnets/<subnet-name-1>",
               "type": "Microsoft.Network/virtualNetworks/subnets"
            },
            {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-virtual-network-resource-group>/providers/Microsoft.Network/virtualNetworks/<Azure-virtual-network-name>/subnets/<subnet-name-1>",
               "type": "Microsoft.Network/virtualNetworks/subnets"
            },
            {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-virtual-network-resource-group>/providers/Microsoft.Network/virtualNetworks/<Azure-virtual-network-name>/subnets/<subnet-name-1>",
               "type": "Microsoft.Network/virtualNetworks/subnets"
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "name": "<key-vault-name>",
               "id": "subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-key-vault-resource-group>/providers/Microsoft.KeyVault/vaults/<key-vault-name>",
               "type": "Microsoft.KeyVault/vaults"
            },
            "keyName": "<customer-managed-key-name>",
            "keyVersion": "<key-version-number>"
         }
      }
   },
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-ISE-resource-group>/providers/Microsoft.Logic/integrationServiceEnvironments/<ISE-name>",
   "name": "<ISE-name>",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "<Azure-datacenter-region>",
   "identity": {
      "type": "SystemAssigned"
   }
}
```

For example:

```json
{
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/fabrikam-vnet-rg/providers/Microsoft.Network/virtualNetworks/fabrikam-vnet/subnets/subnet-1",
               "type": "Microsoft.Network/virtualNetworks/subnets"
            },
            {
               "id": "/subscriptions/********************/resourceGroups/fabrikam-ise-rg/providers/Microsoft.Network/virtualNetworks/fabrikam-vnet/subnets/subnet-2",
               "type": "Microsoft.Network/virtualNetworks/subnets"
            },
            {
               "id": "/subscriptions/********************/resourceGroups/fabrikam-ise-rg/providers/Microsoft.Network/virtualNetworks/fabrikam-vnet/subnets/subnet-3",
               "type": "Microsoft.Network/virtualNetworks/subnets"
            },
            {
               "id": "/subscriptions/********************/resourceGroups/fabrikam-ise-rg/providers/Microsoft.Network/virtualNetworks/fabrikam-vnet/subnets/subnet-4",
               "type": "Microsoft.Network/virtualNetworks/subnets"
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "name": "fabrikam-key-vault",
               "id": "subscriptions/********************/resourceGroups/fabrikam-key-vault-rg/providers/Microsoft.KeyVault/vaults/fabrikam-key-vault",
               "type": "Microsoft.KeyVault/vaults"
            },
            "keyName": "fabrikam-customer-managed-key",
            "keyVersion": "********************"
         }
      }
   },
   "id": "/subscriptions/********************/resourceGroups/fabrikam-ise-rg/providers/Microsoft.Logic/integrationServiceEnvironments/fabrikam-ISE",
   "name": "fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS",
   "identity": {
      "type": "SystemAssigned"
   }
}
```

<a name="identity-access-to-key-vault"></a>

## Give identity access to key vault

Within *30 minutes* after you create your ISE, you must grant the ISE's system-assigned identity access to your key vault . Otherwise, creation and deployment for your ISE fails, and you get a permissions error. You can use either Azure PowerShell ([Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) command) or follow these steps for the Azure portal:

1. In the [Azure portal](https://portal.azure.com), open your Azure key vault. From your key vault's menu, select **Access control (IAM)**.

1. On the toolbar, select **Add** > **Add role assignment**. 

1. On the **Add role assignment** pane, select these values:

   * **Role**: Contributor
   * **Assign access to**: Azure AD user, group, or service principal
   * **Select**: The name for your ISE

1. When you're done, select **Save**.

For more information, see [Add or remove role assignments using Azure RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md).

## Next steps

* Learn more about [Azure Key Vault](../key-vault/key-vault-overview.md)