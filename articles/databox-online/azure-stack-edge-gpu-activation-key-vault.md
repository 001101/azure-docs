---
title: Integration of Azure Key Vault with Azure Stack Edge resource and device activation
description: Describes how Azure Key Vault is associated with secret management during the activation of Azure Stack Edge Pro device.
services: databox
author: alkohli

ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: alkohli
---

# Azure Key Vault integration for your Azure Stack Edge resource during device activation

Azure Key Vault is integrated with Azure Stack Edge resource for secret management. This article provides details on how Azure Key Vault is created for Azure Stack Edge resource during device activation and is then used for secret management. 


## About Key vault and Azure Stack Edge

Azure Key Vault cloud service is used to securely store and control access to tokens, passwords, certificates, API keys, and other secrets. Key Vault also makes it easy to create and control the encryption keys used to encrypt your data. 

For Azure Stack Edge service, one of the secrets used is Channel Integrity Key (CIK). This key allows you to encrypt your activation key. With the integration of key vault, the CIK is securely stored in the key vault. For more information, see [Securely store secrets and keys](../key-vault/general/overview.md#securely-store-secrets-and-keys).


## Key vault creation

A key vault is created for Azure Stack Edge resource during the process of activation key generation. 

- After you have created the Azure Stack Edge resource and you generate an activation key from the Azure portal, a key vault is created. This key vault is used for secret management and persists for as long as the Azure Stack Edge resource exists. 

    ![Key Vault created during activation key generation](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- You can choose to accept the default key name or specify a custom name for the key vault. The key vault name must be between 3 to 34 characters long and cannot You cannot use an existing key vault.

- When you create an Azure Stack Edge resource, a Managed Service Identity (MSI) is also created that persists for the lifetime of the resource. When the MSI is enabled, Azure creates a trusted identity for the Azure Stack Edge resource.The MSI is then used to authenticate to key vault to retrieve secrets. 

    ![MSI created during Azure Stack Edge resource creation](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- To prevent accidental deletion, a lock is enabled on the key vault. A soft delete is also enabled on the key vault that allows the key vault to be restored within 90 days in case of an accidental deletion. 

    If the key vault is accidentally deleted and the purge protection duration of 90 days hasn't elapsed, follow these steps to [Recover your key vault](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault). 

- If you had an existing Azure Stack Edge resource before the Azure Key Vault was integrated with Azure Stack Edge resource, you are not affected. You can continue to use your existing Azure Stack Edge resource. 


## Key vault secret management


 

## Next steps

- Learn more about how to [Generate activation key](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

