---
title: Configure anonymous public read access for containers and blobs
titleSuffix: Azure Storage
description: Learn how to make containers and blobs available for anonymous access, and how to access them programmatically.
services: storage
author: tamram

ms.service: storage
ms.topic: how-to
ms.date: 06/25/2020
ms.author: tamram
ms.reviewer: fryu
---

# Configure anonymous public read access for containers and blobs

Azure Storage supports anonymous public read access for containers and blobs. By default, all requests to a container and its blobs must be authorized by using either Azure Active Directory (Azure AD) or Shared Key authorization. You can configure a container's public access level setting to permit anonymous access, so that clients can read data in that container without authorizing the request. To configure public access for a container, you must perform two steps:

1. Enable public access for the storage account
1. Configure the container's public access setting.

This article describes how to configure anonymous public read access for a container. For information about how to access blob data anonymously from a client application, see [Access public containers and blobs anonymously with .NET](anonymous-read-access-client.md).

To learn how to disable public access for all blob data in a storage account, see [Prevent anonymous public access to containers and blobs](anonymous-read-access-prevent.md).

## Enable or disable public read access for a storage account

By default, public access is disabled for a new??? storage account. Disabling public access for a storage account prevents all anonymous access to containers and blobs in that account. For improved security, Microsoft recommends that you disable public access for a storage account unless your scenario requires that users access blob resources anonymously.

To enable or disable public access for a storage account, use the Azure portal, PowerShell, or Azure CLI.

# [Azure portal](#tab/portal)

To enable or disable public access in the Azure portal, follow these steps:

1. Navigate to your storage account in the Azure portal.
1. Locate the **Configuration** setting under **Settings**.
1. Set **Blob public access** to **Disabled** or **Enabled**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Screenshot showing how to enable or disable blob public access for account":::

# [PowerShell](#tab/powershell)

Not yet???

# [Azure CLI](#tab/azure-cli)

???

---


When public access is disabled at the level of the account, a container's public access level cannot be set.

# [Azure portal](#tab/portal)

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Screenshot showing that setting container public access level is blocked when public access disabled":::


# [PowerShell](#tab/powershell)

Not yet???

# [Azure CLI](#tab/azure-cli)

???

---



## Grant anonymous users permissions to containers and blobs

By default, a container and any blobs within it may be accessed only by a user that has appropriate permissions. To grant anonymous users read access to a container and its blobs, set the container's public access level. You can configure a container with the following permissions:

- **No public read access:** The container and its blobs can be accessed only by the storage account owner. This option is the default for all new containers.
- **Public read access for blobs only:** Blobs within the container can be read by anonymous request, but container data is not available. Anonymous clients cannot enumerate the blobs within the container.
- **Public read access for container and its blobs:** All container and blob data can be read by anonymous request. Clients can enumerate blobs within the container by anonymous request, but cannot enumerate containers within the storage account.

### Set container public access level in the Azure portal

From the [Azure portal](https://portal.azure.com), you can update the public access level for one or more containers:

1. Navigate to your storage account overview in the Azure portal.
1. Under **Blob service** on the menu blade, select **Blobs**.
1. Select the containers for which you want to set the public access level.
1. Use the **Change access level** button to display the public access settings.
1. Select the desired public access level from the **Public access level** dropdown and click the OK button to apply the change to the selected containers.

The following screenshot shows how to change the public access level for the selected containers.

![Screenshot showing how to set public access level in the portal](./media/anonymous-read-access-configure/storage-manage-access-to-resources-0.png)

> [!NOTE]
> You cannot change the public access level for an individual blob. Public access level is set only at the container level.

### Set container public access level with .NET

# [\.NET v12 SDK](#tab/dotnet)

To set the permissions for a container, call the [BlobContainerClient.SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) method. 

The following example sets the container's permissions to full public read access. To set permissions to public read access for blobs only, pass the **PublicAccessType.Blob** field into the [BlobContainerClient.SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) method. To remove all permissions for anonymous users, use the **BlobContainerPublicAccessType.None** field.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_SetPublicContainerPermissions":::

# [\.NET v11 SDK](#tab/dotnet11)

To set the permissions for a container using the Azure Storage client library for .NET, first retrieve the container's existing permissions by calling one of the following methods:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Next, set the **PublicAccess** property on the [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) object that is returned by the **GetPermissions** method.

Finally, call one of the following methods to update the container's permissions:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

The following example sets the container's permissions to full public read access. To set permissions to public read access for blobs only, set the **PublicAccess** property to **BlobContainerPublicAccessType.Blob**. To remove all permissions for anonymous users, set the property to **BlobContainerPublicAccessType.Off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

---

## Access containers and blobs anonymously

A client that accesses containers and blobs anonymously can use constructors that do not require credentials. The following examples show a few different ways to reference containers and blobs anonymously.

### Create an anonymous client object

You can create a new service client object for anonymous access by providing the Blob storage endpoint for the account. However, you must also know the name of a container in that account that's available for anonymous access.

# [\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# [\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## Next steps

- [Access public containers and blobs anonymously with .NET](anonymous-read-access-client.md)
- [Prevent anonymous public access to containers and blobs](anonymous-read-access-prevent.md)
- [Authorizing access to Azure Storage](../common/storage-auth.md)
