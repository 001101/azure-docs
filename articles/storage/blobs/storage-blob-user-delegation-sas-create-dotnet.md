---
title: Create a shared access signature (SAS) using Azure Active Directory credentials with .NET - Azure Storage
description: Learn how to create a shared access signature (SAS) using Azure Active Directory credentials in Azure Storage using the .NET client library.
services: storage
author: tamram

ms.service: storage
ms.topic: article
ms.date: 07/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
---

# Create a shared access signature (SAS) for a container or blob using Azure Active Directory credentials with .NET

A shared access signature (SAS) enables you to grant limited access to containers and blobs in your storage account. When you create a SAS, you specify its constraints, including which object or objects a client is allowed to access, what permissions they have on those objects, and how long the SAS is valid. This article shows how to use Azure Active Directory (Azure AD) credentials to create a SAS for a container or blob with the [Azure Storage client library for .NET](/dotnet/api/overview/azure/storage/client).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## Use Azure AD credentials to secure a SAS

When you create a user delegation SAS using your Azure AD credentials, the signature is derived from an OAuth 2.0 token. You can acquire the OAuth 2.0 token in one of the following ways:

- If your code is running in an Azure environment, such as an Azure virtual machine (VM), then you can use managed identities for Azure resources to acquire the OAuth token. For more information, see [Authorize access to blobs and queues with Azure Active Directory and managed identities for Azure Resources](../common/storage-auth-aad-msi.md).
- If your code is running in a native application or a web application, then you can use the OAuth 2.0 authorization code flow to acquire the OAuth token. In your code to acquire the token, specify the built-in `user_impersonation` scope, which indicates that the token is being requested on behalf of the user whose credentials are provided. For more information, see [Authenticate with Azure Active Directory from an application for access to blobs and queues](../common/storage-auth-aad-app.md).

This article shows how to use a managed identity to acquire the token that is used to get the user delegation key and create the SAS.

## Acquire the OAuth 2.0 token

[!INCLUDE [storage-app-auth-lib-include](../../../includes/storage-app-auth-lib-include.md)]

Next, create a callback method that acquires the token and renews it before it expires:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting an OAuth 2.0 token for Azure Storage.
    const string StorageResource = "https://storage.azure.com/";

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

The following example uses the token to create the service client:

```csharp
private static CloudBlobClient GetClientWithTokenCredentials(Uri blobEndpoint)
{
    // Get the initial access token and the interval at which to refresh it.
    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
    var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider,
                                                CancellationToken.None).GetAwaiter().GetResult();

    // Create storage credentials using the initial token, and connect the callback function 
    // to renew the token just before it expires.
    TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                            TokenRenewerAsync,
                                                            azureServiceTokenProvider,
                                                            tokenAndFrequency.Frequency.Value);

    // Create the Blob service client object using the token.
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);
    CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, storageCredentials);

    return blobClient;
}
```

## Get the user delegation key

Every SAS is signed with a key. To create a user delegation SAS, you must first request a user delegation key, which is then used to sign the SAS. The user delegation key is analogous to the account key used to sign a service SAS or an account SAS, except that it relies on your Azure AD credentials. When a client requests a user delegation key using an OAuth 2.0 token, Azure Storage returns the user delegation key on behalf of the user.

Once you have the user delegation key, you can use that key to create any number of user delegation shared access signatures, over the lifetime of the key. The user delegation key is independent of the OAuth 2.0 token used to acquire it, so the token does not need to be renewed so long as the key is still valid. You can specify that the key is valid for a period of up to 7 days.

Use one of the following methods to request the user delegation key:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

The following example gets the user delegation key and writes out its properties:

```csharp
private static async Task<UserDelegationKey> GetKeyUserDelegationSas(CloudBlobClient blobClient)
{
    UserDelegationKey key = null;

    try
    {
        // Get a user delegation key for the Blob service that's valid for one day.
        key = await blobClient.GetUserDelegationKeyAsync(
                                                            new DateTimeOffset(DateTime.UtcNow),
                                                            new DateTimeOffset(DateTime.UtcNow.AddDays(1)));

        // Read the key's properties.
        Console.WriteLine("User delegation key properties:");
        Console.WriteLine("Key signed start: {0}", key.SignedStart);
        Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
        Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
        Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
        Console.WriteLine("Key signed service: {0}", key.SignedService);
        Console.WriteLine("Key signed version: {0}", key.SignedVersion);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return key;
}
```

## Create a user delegation SAS

Next, use one of the following methods to create the SAS, depending on whether you are creating the SAS on a container or on a blob:

- [GetUserDelegationSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getuserdelegationsharedaccesssignature) (**CloudBlobContainer** object)
- [GetUserDelegationSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getuserdelegationsharedaccesssignature) (**CloudBlob** object)

### Example: Create a user delegation SAS for a container

The following code passes the user delegation key to the [CloudBlobContainer.GetUserDelegationSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getuserdelegationsharedaccesssignature) method to get a SAS token for the specified container:

```csharp
private static async Task<string> GetContainerSasWithToken(CloudBlobClient blobClient, UserDelegationKey key, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    string userDelegationSasToken = null;

    try
    {
        // Create the container if it does not already exist.
        await container.CreateIfNotExistsAsync();

        // Define the access policy for the SAS.
        SharedAccessBlobPolicy policy = new SharedAccessBlobPolicy()
        {
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write |
                            SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Create,
            SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1)
        };

        // Provide the user delegation key to get the SAS.
        userDelegationSasToken = container.GetUserDelegationSharedAccessSignature(key, policy);

        Console.WriteLine("User delegation SAS token for blob container {0}: {1}", container.Name, userDelegationSasToken);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return userDelegationSasToken;
}
```

### Example: Test the container SAS

The following example shows how to test the user delegation SAS to make sure that a client can use it to access a resource. In this case, the code uses the container SAS created in the previous example to get a reference to the container and then create a blob in that container.

To run this sample, make sure that the container SAS has **Create** permissions. If the SAS does not have appropriate permissions, if the SAS is not valid, or if the user delegation key is no longer valid, the code handles the error to provide additional information about the failure.

```csharp
private static async Task TestCreateBlobWithSasAsync(Uri containerUri, string sasToken, string blobName, string blobContent)
{
    // Try performing a container operation with the SAS token provided.
    CloudBlobContainer container = new CloudBlobContainer(containerUri, new StorageCredentials(sasToken));

    // Return a reference to a blob to be created in the container.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    // Create operation: Upload a new blob to the container.
    try
    {
        MemoryStream msWrite = new MemoryStream(System.Text.Encoding.UTF8.GetBytes(blobContent))
        {
            Position = 0
        };
        using (msWrite)
        {
            await blob.UploadFromStreamAsync(msWrite);
        }

        Console.WriteLine("Create operation succeeded for SAS {0}", sasToken);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        // If the SAS is not valid or does not provide appropriate permissions,
        // Azure Storage returns HTTP status code 403.
        if (e.RequestInformation.HttpStatusCode == 403)
        {
            Console.WriteLine("Create operation failed for SAS {0}", sasToken);
            Console.WriteLine("Additional error information: {0}", e.Message);
            // Get additional details on why the request failed.
            Console.WriteLine("Extended error code: {0}",
                e.RequestInformation.ExtendedErrorInformation.ErrorCode);
            foreach (var errorDetail in 
                e.RequestInformation.ExtendedErrorInformation.AdditionalDetails)
            {
                Console.WriteLine("Error detail: {0}", errorDetail.Value);
            }
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

### Example: Get a user delegation SAS for a blob

The following example uses an existing user delegation key to create a user delegation SAS for a blob. The code passes the user delegation key to the [CloudBlob.GetUserDelegationSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getuserdelegationsharedaccesssignature) method to get the SAS token for the blob:

```csharp
private static async Task<string> GetBlobSasWithToken(CloudBlobClient blobClient, UserDelegationKey key)
{
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    CloudBlockBlob blob = container.GetBlockBlobReference("blob1.txt");
    string userDelegationSasToken = null;

    try
    {
        // Create the container if it does not already exist.
        await container.CreateIfNotExistsAsync();

        // Write some text to the blob.
        await blob.UploadTextAsync("This is a blob.");

        // Define the access policy for the SAS.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessBlobPolicy policy = new SharedAccessBlobPolicy()
        {
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Create |
                            SharedAccessBlobPermissions.Write,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
        };

        // Provide the user delegation key to get the SAS.
        userDelegationSasToken = blob.GetUserDelegationSharedAccessSignature(key, policy);

        Console.WriteLine("User delegation SAS token for blob {0}: {1}", blob.Name, userDelegationSasToken);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    if (userDelegationSasToken != null)
    {
        return blob.Uri + userDelegationSasToken;
    }
    else
    {
        return null;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## See also

- [Get User Delegation Key operation](/rest/api/storageservices/get-user-delegation-key)
- [Create a user delegation SAS](/rest/api/storageservices/create-a-user-delegation-sas)
