---
title: Control storage account access for SQL Analytics on-demand
description: Describes how SQL Analytics on-demand accesses Azure Storage and how you can control storage access for SQL Analytics on-demand.
services: synapse-analytics 
author: filippopovic
ms.service: synapse-analytics 
ms.topic: overview
ms.subservice: 
ms.date: 10/17/2019
ms.author: fipopovi
ms.reviewer: jrasnick
---


# Control storage account access for SQL Analytics on-demand

SQL Analytics on-demand query reads files directly from Azure Storage. Since the storage account is an object that is external to SQL Analytics on-demand, appropriate credentials are required. A user needs the appropriate permissions granted to use the requisite credential. This document describes the types of credentials you can use and how credential lookup enacted for SQL and AAD logins.



## Supported storage authorization types

A user that has logged into SQL Analytics on-demand must be authorized to access and query the files in Azure Storage. Three authorization types are supported:

- [Shared access signature](#shared-access-signature)
- [Managed Identity](#managed-identity)
- [User Identity](#user-identity)

> [!NOTE]
> [AAD pass-through](#force-aad-pass-through) is the default behavior when you create a workspace. You can [disable this behavior](#disable-forcing-aad-pass-through).

Depending on the user type, different authorization types are supported (or will be supported soon).

| Authorization type                    | *SQL user*    | *AAD user*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | Supported     | Supported      |
| [Managed Identity](#managed-identity) | Not supported | Not supported  |
| [User Identity](#user-identity)       | Not supported | Supported      |

### Shared access signature

**SAS** provides delegated access to resources in a storage account. With SAS, a customer can grant clients access to resources in a storage account without sharing account keys. SAS gives you granular control
over the type of access you grant to clients who have a SAS: validity interval, granted permissions, acceptable IP address range, and the acceptable protocol (https/http).

> [!NOTE]
>
> You can get a SAS token by navigating to Azure Portal -> Storage Account -> Shared access signature -> Configure permissions -> Generate SAS and connection string. 
**When a SAS token is generated, it includes a question mark ('?') at the beginning of the token. To use the token in SQL Analytics on-demand, you must remove the question mark ('?') when creating a credential.**
> 
> Example: 
> SAS token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D
>

### User Identity

**User Identity** (also known as “pass-through”) is an authorization type where the identity of the AAD user that logged into
SQL Analytics on-demand is used to authorize the access to data. Before accessing the data, the Azure Storage administrator must grant permissions to the AAD user for data access. This authorization type uses the AAD user that logged into SQL Analytics on-demand, therefore it’s not supported for SQL user type.

> [!NOTE]
> 
>To be able to user your identity to access the data you need to have Storage Blob Data Owner/Contributor/Reader role.
> Even if you are an Owner of a Storage Account, you will still need to add yourself into one of the Storage Blob Data roles.
> [Click here to read more about access control in Azure Data Lake Store Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
>

### Managed Identity

**Managed Identity** (also known as MSI) is a feature of Azure Active Directory (Azure AD) that provides Azure services for AQL Analytics on-demand and deploys an automatically managed identity in Azure AD. This identity can be used to authorize the request for data access in Azure Storage. 

Before accessing the data, the Azure Storage administrator must grant permissions to Managed Identity for accessing the data. Granting permissions to Managed Identity is done the same way as granting permission to any other AAD user.

## Creating credentials

To query a file residing in Azure Storage, your SQL Analytics on-demand endpoint needs a server-level CREDENTIAL that contains the authentication information. A credential can be added by running the [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql?view=sql-server-2017) statement and providing a CREDENTIAL NAME argument that must match either part of the path or whole path to data in Storage (see below). Please note that the FOR CRYPTOGRAPHIC PROVIDER argument is not supported.

For all supported authorization types, credentials can point to an account, a container, any directory (non-root), or a single file. 

CREDENTIAL NAME must match the full path to the container, folder, or file, in the following format: `<prefix>://<storage_account_path>/<storage_path>` .

| External Data Source       | Prefix | Storage account path                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 `'<storage_path>'` is a path within your storage that points to the folder or file you want to read.

> [!NOTE]
> There is special CREDENTIAL NAME  `UserIdentity`  that [forces AAD pass-through](#force-aad-pass-through). Please read about the effect it has on [credential lookup](#credential-lookup) while executing queries.

Optionally, to allow a user to create or drop a credential, admin can GRANT/DENY ALTER ANY CREDENTIAL permission to a user:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name]
```

### Supported storages and authorization types

You can use the following combinations of authorization and Azure Storage types:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Supported      | Not  supported   | Supported     |
| *Managed  Identity* | Not supported  | Not supported    | Not supported |
| *User  Identity*    | Supported      | Supported        | Supported     |

### Examples


Depending on the desired [authorization type](#supported-storage-authorization-types), you can create credentials using the syntax below.

T-SQL syntax for **Shared Access Signature and Blob Storage**. Exchange <*mystorageaccountname*> with your actual storage account name, and <*mystorageaccountcontainername*> with the actual container name:
>
> ```mssql
> CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
> WITH IDENTITY='SHARED ACCESS SIGNATURE' 
> , SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D' 
> GO
> ```

T-SQL syntax for **User Identity and Azure Data Lake Store Gen1**. Exchange <*mystorageaccountname*> with your actual storage account name, and <*mystorageaccountcontainername*> with the actual container name:

>```mssql
>CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
>WITH IDENTITY='User Identity' 
>GO
>```

T-SQL syntax for **User Identity and Azure Data Lake Store Gen2**. Exchange *<*mystorageaccountname*> with your actual storage account name, and <*mystorageaccountcontainername*> with the actual container name:

>```mssql
>CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
>WITH IDENTITY='User Identity' 
>GO
>```

## Force AAD pass-through

Forcing an AAD pass-through is a default behavior. To achieve this, a special CREDENTIAL NAME `UserIdentity` is created automatically during Azure Synapse workspace provisioning. It forces the usage of an AAD pass-through for each query of every AAD login regardless of the existence of other credentials. 

> [!NOTE]
> AAD pass-through is a default behavior.

In case you [disabled forcing AAD pass-through for each query](#disable-forcing-aad-pass-through), and want to enable it again, execute:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity'
```

To enable forcing an AAD pass-through for a specific user, you can grant REFERENCE permission on credential `UserIdentity` to that particular user. The following example enables forcing an AAD pass-through for a user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name]
```

For more information on how SQL Analytics on-demand finds credential to use, see [credential lookup](#credential-lookup).

## Disable forcing AAD pass-through

You can disable [forcing AAD pass-through for each query](#force-aad-pass-through). To disable it, drop `Userdentity` credential using:

```sql
DROP CREDENTIAL [UserIdentity]
```

In case you want to enable it again, check [force AAD pass-through](#force-aad-pass-through). 

To disable forcing AAD pass-through for a specific user, you can deny a REFERENCE permission on credential `UserIdentity` for a particular user. The following example disables a forcing AAD pass-through for a user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name]
```

For more information on how SQL Analytics on-demand finds credentials to use, see [credential lookup](#credential-lookup).

## Grant permissions to use credential

To use the credential, a user must have REFERENCES permission on a specific credential. To grant a REFERENCES permission ON a storage_credential for a specific_user, execute:

`GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user]`

To ensure a smooth AAD pass-through experience, all users will, by default, have a right to use the `UserIdentity` credential. This is achieved by an automatic execution of the following statement upon Azure Synapse workspace provisioning:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public]
```

## Credential lookup

When authorizing queries, lookup of a credential to be used to access a storage account will be based on following rules:

- If the user is logged in as an AAD login

   - if UserIdentity credential exists and user has reference permissions on it, AAD pass-through will be used, otherwise [lookup credential by path](#lookup-credential-by-path)

- If the user is logged in as a SQL login

   - [lookup credential by path](#lookup-credential-by-path)

### Lookup credential by path

If forcing AAD pass-through is disabled, the lookup of a credential to be used will be based on the storage path (depth first) and existence of a REFERENCES permission on the particular credential. If there are multiple credentials that can be used to access the same file, SQL Analytics on-demand will use the most specific one.  

As an example, for a query over the  following file path: 
> "account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext" 


Credential lookup will be completed in this order:

> 1. "account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX"
> 2. "account.dfs.core.windows.net/filesystem/folder1/.../folderN"
> 3. "account.dfs.core.windows.net/filesystem/folder1"
> 4. "account.dfs.core.windows.net/filesystem"
> 5. "account.dfs.core.windows.net"

If a user has no REFERENCES permission on credential number 5, SQL Analytics on-demand will check if the user has REFERENCES permission on credential one level higher until it locates the credentials the user has REFERENCES permission on. If no such permission is found, an error message will be returned.

### Credential and path level

Depending on the desired path shape, in order to be able to run queries, the following requirements are in place: 

* If the query is targeting multiple files (folders, with or without wild cards), a user needs to have access to a credential on at least the root directory level (container level). This is needed since listing files are relative from the root directory  (Azure Storage limitations)

* If the query is targeting a single file, a user needs to have access to a credential on any level as SQL Analytics on-demand access the file directly (i.e., without listing folders)

|                  | *Account* | *Root directory* | *Any other directory* | *File*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Single file*    | Supported | Supported        | Supported             | Supported     |
| *Multiple files* | Supported | Supported        | Not supported         | Not supported |


