---
title: Authenticate access to blob and queue data with Azure Active Directory managed identities for Azure resources - Azure Storage | Microsoft Docs
description: Azure Blob and Queue storage supports Azure Active Directory authentication with managed identities for Azure resources. You can use managed identities for Azure resources to authenticate access to blobs and queues from applications running in Azure virtual machines, function apps, virtual machine scale sets, and others. By using managed identities for Azure resources and leveraging the power of Azure AD authentication, you can avoid storing credentials with your applications that run in the cloud.  
services: storage
author: tamram

ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
---

# Authenticate access to blob and queue data with managed identities for Azure Resources

Azure Blob and Queue storage support Azure Active Directory (Azure AD) authentication with [managed identities for Azure resources](../../active-directory/managed-identities-azure-resources/overview.md). Managed identities for Azure resources can authenticate access to blobs and queues using Azure AD credentials from applications running in Azure virtual machines (VMs), function apps, virtual machine scale sets, and others. By using managed identities for Azure resources and leveraging the power of Azure AD authentication, you can avoid storing credentials with your applications that run in the cloud.  

To grant permissions to a managed identity for a blob container or queue, assign a role-based access control (RBAC) role to the managed identity that encompasses permissions for that resource at the appropriate scope. For more information about RBAC roles in storage, see [Manage access rights to storage data with RBAC](storage-auth-aad-rbac.md). 

This article shows how to authenticate access to blob or queue data with a managed identity from an Azure VM.  

## Enable managed identities on a VM

Before you can use managed identities for Azure Resources to authenticate access to blobs and queues from your VM, you must first enable managed identities for Azure Resources on the VM. To learn how to enable managed identities for Azure Resources, see one of these articles:

- [Azure portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager Template](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDKs](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## Assign an RBAC role to an Azure AD managed identity

To authenticate a managed identity from your Azure Storage application, first configure role-based access control (RBAC) settings for that managed identity. Azure Storage defines RBAC roles that encompass permissions for blob and queue data. When the RBAC role is assigned to a managed identity, the managed identity is granted those permissions to the container or queue. For more information, see [Manage access rights to Azure Blob and Queue data with RBAC](storage-auth-aad-rbac.md).

## Get a managed identity access token

To authenticate with a managed identity, your application or script must acquire an OAuth token. The [Microsoft Azure App Authentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) client library for .NET (preview) simplifies the process of acquiring and renewing a token from your code.

The App Authentication client library manages authentication automatically. The library uses the developer's credentials to authenticate during local development. Using developer credentials during local development is more secure because you do not need to create Azure AD credentials or share credentials between developers. When the solution is later deployed to Azure, the library automatically switches to using application credentials.

For more information about the App Authentication library, see [Service-to-service authentication to Azure Key Vault using .NET](../..key-vault/service-to-service-authentication.md). While this article discusses the App Authentication library in the context of Azure Key Vault, the concepts are applicable to Azure Storage.

To use the App Authentication library in an Azure Storage application, install the latest preview package from [Nuget]((https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)), as well as the latest version of the [Azure Storage client library for .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Add the following **using** statements to your code:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
```

The App Authentication library provides the **AzureServiceTokenProvider** class. 

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    const string StorageResource = "https://storage.azure.com/";  // Resource ID for requesting AAD tokens for Azure Storage

    // Use the same token provider to request a new token
    //var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource/*, pass cancellationToken here*/);
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource/*, pass cancellationToken here*/);

    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);   // Renew 5 minutes before expiration
    if (next.Ticks < 0) next = default(TimeSpan);

    // Return new token and next refresh time to refreshing TokenCredential
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```
  

The App Authentication library 

[Service-to-service authentication to Azure Key Vault using .NET](https://docs.microsoft.com/azure/key-vault/service-to-service-authentication)

To learn more about how to acquire an access token, see [How to use managed identities for Azure resources on an Azure VM to acquire an access token](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## .NET code example: Create a block blob

The code example assumes that you have a managed identity access token. The access token is used to authorize the managed identity to create a block blob.

To authorize blob and queue operations with an OAuth token, you must use HTTPS.

### Add references and using statements  

In Visual Studio, install the Azure Storage client library. From the **Tools** menu, select **Nuget Package Manager**, then **Package Manager Console**. Type the following command into the console:

```powershell
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Add the following using statements to your code:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### Create credentials from the managed identity access token

To create the block blob, use the **TokenCredentials** class. Construct a new instance of **TokenCredentials**, passing in the managed identity access token that you obtained previously:

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

- To learn more about RBAC roles for Azure storage, see [Manage access rights to storage data with RBAC](storage-auth-aad-rbac.md).
- To learn how to authorize access to containers and queues from within your storage applications, see [Use Azure AD with storage applications](storage-auth-aad-app.md).
- To learn how to sign in to Azure CLI and PowerShell with an Azure AD identity, see [Use an Azure AD identity to access Azure Storage with CLI or PowerShell](storage-auth-aad-script.md).