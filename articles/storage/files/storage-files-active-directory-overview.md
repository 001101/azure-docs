---
title: Overview of Azure Active Directory integration with Azure Files (Preview) | Microsoft Docs
description: Learn how to use Azure Active Directory integration with Azure Files to grant access to file shares, directories, or files over SMB (Preview).
services: storage
author: tamram

ms.service: storage
ms.topic: article
ms.date: 07/11/2018
ms.author: tamram
---

# Overview of Azure Active Directory integration with Azure Files (Preview)

[!INCLUDE [storage-files-aad-integration-include](../../../includes/storage-files-aad-integration-include.md)]

To learn how to enable Azure AD integration with Azure Files, see [Enable Azure Active Directory integration with Azure Files (Preview)](storage-files-active-directory-enable.md).

## Glossary 

It's helpful to understand some key terms relating to Azure AD integration with Azure Files:

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) is Microsoft’s multi-tenant, cloud-based directory and identity management service that combines core directory services, application access management, and identity protection into a single solution. For more information, see [What is Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md).

-   **Azure AD Domain Services**

    Azure AD Domain Services provides managed-domain services such as domain join, group policies, LDAP, and Kerberos/NTLM authentication. These services are fully
    compatible with Windows Server Active Directory. For more information, see [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md).

-   **Azure Role Based Access Control (RBAC)**

    Azure Role-Based Access Control (RBAC) enables fine-grained access management for Azure. Using RBAC, you can manage access to resources by granting users the fewest permissions needed to perform their jobs. For more information on RBAC, see [What is role-based access control (RBAC) in Azure?](../../role-based-access-control/overview.md).

-   **Kerberos authentication**

    Kerberos is an authentication protocol that is used to verify the identity of a user or host. For more information on Kerberos, see [Configure Kerberos constrained delegation (KCD) on a managed domain](../../active-directory-domain-services/active-directory-ds-enable-kcd.md).

-  **Server Message Block (SMB) protocol**

    SMB is an industry-standard network file-sharing protocol. SMB is also known as Common Internet File System or CIFS. For more information on SMB, see [Microsoft SMB Protocol and CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## Advantages of using Azure AD integration with Azure Files

Azure AD integration with Azure Files offers several benefits over using Shared Key authentication:

-   **Extend the traditional identity-based file share access experience to the cloud with Azure AD**

    If you plan to "lift and shift" your application to the cloud, replacing traditional file servers with Azure Files, then you may want your users to authenticate with Azure AD to access your file shares. With Azure AD integration, Azure Files supports using Azure AD credentials from domain-joined VMs to access file shares, directories, or files. You can also choose to sync all of your on-premises Active Directory objects to Azure AD to preserve usernames, passwords, and other group assignments.

-   **Enforce granular access control on Azure file shares**

    With Azure AD integration for Azure Files, you can grant permissions to a specific user for Azure Files data at the share, directory, or file level. For example, suppose that you have several teams using a single Azure file share that stores checkpoint data for high availability solutions. You can grant all teams access to non-sensitive directories, while limiting access to directories containing sensitive financial data to your Finance team only. 

-   **Back up ACLs along with your data**

    You can use Azure Files to back up your existing on-premises file shares. Previously when your files were imported to Azure Files, only the data was copied, not the ACLs. All access assignments would be lost when you restored your existing file shares from Azure Files. Azure Files can now preserve your ACLs along with your data when you back up a file share to Azure Files over SMB.

## About Azure AD integration for Azure Files

Azure Files uses Azure AD Domain Services to support Kerberos authentication with Azure AD credentials for access to file resources over SMB. Before you can use Azure AD with Azure Files, you must first enable Azure AD Domain Services and join the domain from the VMs where you plan to access Azure Files. Your domain-joined VM must reside in same virtual network (VNET) as Azure AD Domain Services. 

When a user attempts to access data in Azure Files, the request is sent to Azure AD Domain Services to authenticate the user. If authentication is successful, Azure AD Domain Services returns a Kerberos token. The VM sends a request that includes the Kerberos token, and Azure Files uses the token to authorize the request. Azure Files receives the token only and does not persist Azure AD credentials.

![Screen shot showing diagram of Azure AD integration with Azure Files](media/storage-files-active-directory-overview/files-aad-overview.png)

### Enable Azure AD integration with Azure Files

You can enable Azure AD integration for Azure Files on your new and existing storage accounts. Once Azure AD integration has been enabled, you can configure role-based access control (RBAC) for your Azure AD users and security groups and assign access rights to any file shares in the storage account. 

Before enabling Azure AD integration, make sure that Azure AD Domain Services is deployed for the primary Azure AD tenant with which your storage account is associated. If you have not yet setup Azure AD Domain Services, follow the step by step guidance provided in [Enable Azure Active Directory Domain Services using the Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md).

Azure AD Domain Services deployment generally takes 10 to 15 minutes. After Azure AD Domain Services has been deployed, you can enable Azure AD integration for Azure Files. For more information, see [Enable Azure Active Directory integration for Azure Files SMB access](storage-files-active-directory-enable.md). 

### Configure share-level permissions for Azure Files

To allow users to access to an Azure file share, grant them share-level permissions to the share using RBAC.

When a user tries to mount an Azure Files file share or access a directory or file in that share, the user's credentials are verified to ensure the proper share-level permissions and NTFS permissions. For information about configuring share-level permissions, see [Enable Azure Active Directory integration with Azure Files (Preview)](storage-files-active-directory-enable.md).

### Configure directory- or file-level permissions for Azure Files 

Azure Files enforces standard NTFS file permissions at the directory and file level, including at the root directory. Configuration of directory- or file-level permissions is supported over SMB only. Mount the target file share from your VM and configure permissions using the
[icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) command tool. 

Configuring NTFS permissions through Windows File Explorer is not supported in the preview.

### Use the storage account key for superuser experience 

Users possessing the storage account key can access Azure Files with superuser permissions. Superuser permissions surpass all access control restrictions configured at the share, directory, or file level with RBAC. The user with superuser permissions can mount Azure file shares using the storage account key and Shared Key authentication. 

> [!IMPORTANT]
> Avoid sharing your storage account keys with anyone else, and leverage Azure AD permissions whenever possible.

### Preserve directory and file ACLs for data import to Azure file shares

Azure AD integration with Azure Files supports preserving directory or file ACLs when you import data to Azure file shares. In the preview release, you can copy the ACLs on your directory or file through SMB. For example, you can use [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) with flag `/copy:s` to copy both data and ACLs.

## Pricing

There is no additional service charge to enable Azure AD integration for Azure Files on your storage account. For more information on pricing, see [Azure Files pricing](https://azure.microsoft.com/pricing/details/storage/files/) and [Azure AD Domain Services pricing](https://azure.microsoft.com/pricing/details/active-directory-ds/) pages.

## Next Steps

See these resources for more information about Azure Files and Azure AD integration:

-   [Introduction to Azure Files](storage-files-introduction.md)
-   [Enable Azure Active Directory integration with Azure Files (Preview)](storage-files-active-directory-enable.md)
-   [Azure Files FAQ](storage-files-faq.md)
- - ???need link to blog post???