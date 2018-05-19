---
title: Authenticating access to Azure Storage | Microsoft Docs
description: Learn about the different ways to authenticate access to Azure Storage, including Azure Active Directory, Shared Key authentication, or shared access signatures.  
services: storage
author: tamram
manager: jeconnoc

ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
---

# Authenticating access to Azure Storage

Each time you access data in your storage account, your client makes a request over HTTP/HTTPS to Azure Storage. Every request to a secure resource must be authenticated, so that the service ensures that the client has the right permissions to access the right data at the right time. Azure Storage offers these options for authentication:

- **Azure Active Directory (Azure AD) integration (Preview)** for blobs and queues. Azure identity management provides role-based authentication (RBAC) for fine-grained control over a client's access to resources in a storage account. For more information, see [Authenticating requests to Azure Storage using Azure Active Directory (Preview)](storage-auth-aad.md).
- **Shared Key authentication** for blobs, files, queues, and tables. A client using Shared Key passes a header with every request that is signed using the storage account access key. For more information, see [Authenticating requests to Azure Storage using Shared Key](storage-authentication-shared-key.md).
- **Shared access signatures** for blobs, files, queues, and tables. Shared access signatures (SAS) provide delegated access to resources in a storage account. A client can use a SAS to access a resource in your storage account without the account access key. For more information, see [Using shared access signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).

By default, all resources in Azure Storage are secured, and are available only to the account owner. You can use any of the authentication strategies outlined above to grant clients access to resources in your storage account. You may also enable anonymous public read access for containers and blobs, so that authentication is not required. For more information, see [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md).  


