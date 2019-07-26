---
title: Create a shared access signature (SAS) using Azure Active Directory credentials with Azure CLI - Azure Storage
description: Learn how to create a shared access signature (SAS) using Azure Active Directory credentials in Azure Storage using Azure CLI.
services: storage
author: tamram

ms.service: storage
ms.topic: article
ms.date: 07/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
---

# Create a shared access signature (SAS) for a container or blob using Azure Active Directory credentials with Azure CLI

A shared access signature (SAS) enables you to grant limited access to containers and blobs in your storage account. When you create a SAS, you specify its constraints, including which object or objects a client is allowed to access, what permissions they have on those objects, and how long the SAS is valid. This article shows how to use Azure Active Directory (Azure AD) credentials to create a SAS for a container or blob with Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## Use Azure AD credentials to secure a SAS

When you create a user delegation SAS using your Azure AD credentials, the signature is derived from an OAuth 2.0 token. You can acquire the OAuth 2.0 token in one of the following ways:

## Get a user delegation SAS for a container

To create a user delegation SAS with Azure CLI, call the [az storage container generate-sas](/cli/azure/storage/container#az-storage-container-generate-sas) command. Specify *login* for the `--auth-mode` parameter so that requests made to Azure Storage are authorized with your Azure AD credentials. Specify the `--as-user` parameter to indicate that the SAS returned should be a user delegation SAS. Finally, specify an expiry value for the user delegation SAS that is within 7 days of the current time.

The following example returns a user delegation SAS token with read permissions.

The **az storage container generate-sas** command returns the SAS token, which you can append to the container resource URI. Remember to replace the placeholder values in brackets with your own values:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name sample-container \
    --permissions r \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

The user delegation SAS token returned is similar to the token shown in following example:

`se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>`

## See also

- [Get User Delegation Key operation](/rest/api/storageservices/get-user-delegation-key)
- [Delegating Access with a Shared Access Signature](/rest/api/storageservices/delegating-access-with-a-shared-access-signature)
