---
title: Authenticate access to blobs and queues with Azure Active Directory managed identities for Azure resources (Preview) - Azure Storage | Microsoft Docs
description: Azure Blob and Queue storage supports Azure Active Directory authentication with managed identities for Azure resources. You can use managed identities for Azure resources to authenticate access to blobs and queues from applications running in Azure virtual machines, function apps, virtual machine scale sets, and others. By using managed identities for Azure resources and leveraging the power of Azure AD authentication, you can avoid storing credentials with your applications that run in the cloud.  
services: storage
author: tamram

ms.service: storage
ms.topic: article
ms.date: 09/05/2018
ms.author: tamram
ms.component: common
---

# Authenticate access to blobs and queues with Azure managed identities for Azure Resources (Preview)

Azure Blob and Queue storage support Azure Active Directory (Azure AD) authentication with [managed identities for Azure resources](../../active-directory/managed-service-identity/overview.md). You can use managed identities for Azure resources to authenticate access to blobs and queues from applications running in Azure virtual machines (VMs), function apps, virtual machine scale sets, and others. By using managed identities for Azure resources and leveraging the power of Azure AD authentication, you can avoid storing credentials with your applications that run in the cloud.  

To grant permissions to a managed service identity for storage containers or queues, you assign an RBAC role encompassing storage permissions to the managed identity. For more information about RBAC roles in storage, see [Manage access rights to storage data with RBAC (Preview)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> This preview is intended for non-production use only. Production service-level agreements (SLAs) will not be available until Azure AD integration for Azure Storage is declared generally available. If Azure AD integration is not yet supported for your scenario, continue to use Shared Key authorization or SAS tokens in your applications. For additional information about the preview, see [Authenticate access to Azure Storage using Azure Active Directory (Preview)](storage-auth-aad.md).
>
> During the preview, RBAC role assignments may take up to five minutes to propagate.

This article shows how to authenticate to Azure Blob or Queue storage with a managed identity from an Azure VM.  

## Enable managed identities for Azure Resources on the VM

Before you can use managed identities for Azure Resources to authenticate access to blobs and queues from your VM, you must first enable managed identities for Azure Resources on the VM. To learn how to enable managed identities for Azure Resources, see one of these articles:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager Template](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Azure SDKs](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## Get a managed identity access token

To authenticate with a managed identity, your application or script must acquire a managed identity access token. To learn about how to acquire an access token, see [How to use managed identities for Azure resources on an Azure VM to acquire an access token](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## .NET code example: Create a block blob

The code example assumes that you have a managed identity access token. The access token is used to authorize the managed identity to create a block blob.

### Add references and using statements  

In Visual Studio, install the preview version of the Azure Storage client library. From the **Tools** menu, select **Nuget Package Manager**, then **Package Manager Console**. Type the following command into the console:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Add the following using statements to your code:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### Create credentials from the managed identity access token

To create the block blob, use the **TokenCredentials** class provided by the preview package. Construct a new instance of **TokenCredentials**, passing in the managed identity access token that you obtained previously:

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Azure AD integration with Azure Storage requires that you use HTTPS for Azure Storage operations.

## Next steps

- To learn more about RBAC roles for Azure storage, see [Manage access rights to storage data with RBAC (Preview)](storage-auth-aad-rbac.md).
- To learn how to authorize access to containers and queues from within your storage applications, see [Use Azure AD with storage applications](storage-auth-aad-app.md).
- To learn how to log into Azure CLI and PowerShell with an Azure AD identity, see [Use an Azure AD identity to access Azure Storage with CLI or PowerShell (Preview)](storage-auth-aad-script.md).
- For additional information about Azure AD integration for Azure Blobs and Queues, see the Azure Storage team blog post, [Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
