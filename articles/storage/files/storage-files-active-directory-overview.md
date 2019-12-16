---
title: Overview - Azure AD Domain Services authorization - Azure Files
description: Azure Files supports identity-based authentication over SMB (Server Message Block) through Azure Active Directory (Azure AD) Domain Services. Your domain-joined Windows virtual machines (VMs) can then access Azure file shares using Azure AD credentials. 
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/07/2019
ms.author: rogarana
---

# Overview of Azure Files Azure Active Directory Domain Service (Azure AD DS) Authentication Support for SMB Access
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

To learn how to enable Azure AD DS authentication for Azure Files, see [Enable Azure Active Directory Domain Service Authentication over SMB for Azure Files](storage-files-active-directory-enable.md).

## Glossary 
It's helpful to understand some key terms relating to Azure AD Domain Service authentication over SMB for Azure Files:

-   **Kerberos authentication**

    Kerberos is an authentication protocol that is used to verify the identity of a user or host. For more information on Kerberos, see [Kerberos Authentication Overview](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server Message Block (SMB) protocol**  
    SMB is an industry-standard network file-sharing protocol. SMB is also known as Common Internet File System or CIFS. For more information on SMB, see [Microsoft SMB Protocol and CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) is Microsoft’s multi-tenant cloud-based directory and identity management service. Azure AD combines core directory services, application access management, and identity protection into a single solution. Azure AD enables your domain-joined Windows virtual machines (VMs) to access Azure file shares with your Azure AD credentials. For more information, see [What is Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services (Azure AD DS)**  
    Azure AD Domain Services (GA) provides managed domain services such as domain join, group policies, LDAP, and Kerberos/NTLM authentication. These services are fully
    compatible with Windows Server Active Directory. For more information, see [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services (AD DS, also referred as AD)**
    Active Directory Domain Services (AD DS) (preview) provides the methods for storing directory data while making it available to network users and administrators. Security is integrated with Active Directory through logon authentication and access control to objects in the directory. With a single network logon, administrators can manage directory data and organization throughout their network, and authorized network users can access resources anywhere on the network. AD DS is commonly adopted by enterpises in on-premises and use AD credentials as the identity for access control. For more information, see [Active Directory Domain Services Overview](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Azure Role Based Access Control (RBAC)**  
    Azure Role-Based Access Control (RBAC) enables fine-grained access management for Azure. Using RBAC, you can manage access to resources by granting users the fewest permissions needed to perform their jobs. For more information on RBAC, see [What is role-based access control (RBAC) in Azure?](../../role-based-access-control/overview.md).

## Common use cases

Identity-based authentication and support for NTFS ACLs on Azure Files is best leveraged for the following use cases:

### Replace on-premises file servers

Deprecating and replacing scattered on-premises file servers is a common problem that every enterprise encounters in their IT modernization journey. Azure Files with AD DS (preview) authentication is the best fit here, when you can migrate the data to Azure Files. A complete migration will allow you to take advantage of the high availability and scalability benefits while also minimizing the client side changes, especially complicated AD domain infrastructure. It provides a seamless migration experience to end users, so they can continue to access their data with the same credentials using their existing domain joined machines.

### Lift and shift applications to Azure

When you "lift and shift" applications to the cloud, you want to keep the same authentication model for your data. As we extend the identity-based access control experience to Azure Files, it eliminates the need to change your application to modern auth methods and expedite cloud adoption. Azure Files provides the option to integrate with either Azure AD DS (GA) or AD DS (preview) for authentication. If your plan is to be 100% cloud native and minimize the efforts managing cloud infrastructures, Azure AD DS would be a better fit as a fully managed domain service. If you need full compatibility with AD DS (GA) capabilities, you may want to consider extending your AD environment to cloud by self-hosting domain controllers on VMs. Either way, we provide the flexibility to choose the domain services that suits your business needs.

### Backup and disaster recovery (DR)

If you are keeping your primary file storage on-premises, Azure Files can serve as an ideal storage for backup or DR, to improve business continuity. You can use Azure Files to back up your data from existing file servers, while preserving NTFS DACLs. For DR scenarios, you can configure an authentication option to support proper access control enforcement at failover.

## Supported scenarios

The following table summarizes the supported Azure Files authentication scenarios for Azure AD DS (GA) and AD DS (preview). We recommend selecting the domain service that you adoped for your client environment for integration with Azure Files. If you have AD DS (preview) already setup on-premises or on Azure where your devices are domain joined to AD, you should choose to leverage AD DS (preview) for Azure Files authentication. Similarly, if you've already adopted Azure AD DS (GA), you should use that for Azure Files authentication.


|Azure AD DS (GA) authentication  |AD DS (preview) authentication  |
|---------|---------|
|Azure AD DS domain joined Windows machines can access Azure Files with Azure AD credentials over SMB.     |AD domain joined Windows machines can access Azure Files with AD credentials that are synched to Azure AD over SMB.         |

### Unsupported scenarios

- Azure AD DS (GA) and AD DS (preview) authentication for SMB access are not supported for Linux VMs.
- Azure AD DS (GA) or AD DS (preview) authentication for SMB access and NTFS DACL persistence is not supported on Azure file shares managed by Azure File Sync.
- Azure AD DS (GA) and AD DS (preview) authentication do not support authentication against computer accounts. You can consider using a service logon account instead.
- Azure AD DS (GA) authentication does not support authentication against Azure AD cloud joined devices.

## Advantages of Azure AD Domain Service authentication
Azure AD Domain Service authentication for Azure Files offers several benefits over using Shared Key authentication:

-   **Extend the traditional identity-based file share access experience to the cloud with Azure AD and Azure AD Domain Service**  
    If you plan to "lift and shift" your application to the cloud, replacing traditional file servers with Azure Files, then you may want your application to authenticate with Azure AD credentials to access file data. Azure Files supports using Azure AD credentials to access Azure Files over SMB from Azure AD DS domain-joined Windows VMs. You can also choose to sync all of your on-premises Active Directory objects to Azure AD to preserve usernames, passwords, and other group assignments.

-   **Enforce granular access control on Azure file shares**  
    You can grant permissions to a specific identity at the share, directory, or file level. For example, suppose that you have several teams using a single Azure file share for project collaboration. You can grant all teams access to non-sensitive directories, while limiting access to directories containing sensitive financial data to your Finance team only. 

-   **Back up ACLs along with your data**  
    You can use Azure Files to back up your existing on-premises file shares. Azure Files preserves your ACLs along with your data when you back up a file share to Azure Files over SMB.

## How it works
Azure Files supports kerberos authentication for integration with either Azure AD DS (GA) or AD DS (preview). Before you can enable authentication on Azure Files, you must first setup your domain environment. For Azure AD DS (GA) authentication, you should enable Azure AD Domain Services and domain join the VMs you plan to access file data from. Your domain-joined VM must reside in the same virtual network (VNET) as your Azure AD Domain Services. Similarly, for AD DS (preview) authentication, you need to setup your Active Directory domain controller and domain join your machines or VMs.

When an identity associated with an application running on a VM attempts to access data in Azure Files, the request is sent to Azure AD Domain Services to authenticate the identity. If authentication is successful, Azure AD Domain Services returns a Kerberos token. The application sends a request that includes the Kerberos token, and Azure Files uses that token to authorize the request. Azure Files receives the token only and does not persist Azure AD credentials.

![Screenshot showing diagram of Azure AD authentication over SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### Enable identity-based authentication

You can enable identity-based authentication with either Azure AD DS (GA) or AD DS (preview) for Azure Files on your new and existing storage accounts. Only one domain service can be used for file access authentication on the storage account, which applies to all file shares in the account. Detailed step by step guidance on setting up you file shares for authentication with Azure AD DS (GA) in our article [Enable Azure Active Directory Domain Services authentication over SMB for Azure Files](storage-files-active-directory-enable.md) and guidance for AD DS (preview) in our other article <link here>.

### Configure share-level permissions for Azure Files

Once either Azure AD DS (GA) or AD DS (preview) authentication is enabled, you can use built-in RBAC roles or configure custom roles for Azure AD identities and assign access rights to any file shares in your storage accounts. the assigned permission allows the granted identity to get access to the share only, nothing else, not even the root directory. You still need to separately configure directory or file-level permissions for Azure Files.

### Configure directory or file-level permissions for Azure Files

Azure Files enforces standard NTfS file permissions at both the directory and file level, including the root directory. Configuration of directory or file-level permissions is supported over both SMB and REST. Mount the target file share from your VM and configure permissions using Windows File Explorer, Windows [icacls](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/icacls), or the [Set-ACL](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) command.

### Use the storage account key for superuser permissions

A user possessing the storage account key can access Azure Files with superuser permissions. Superuser permissions bypass all access control restrictions.

> [!IMPORTANT]
> Our recommended security best practice is to avoid sharing your storage account keys, and leverage identity-based authentication whenever possible.

### Preserve directory and file ACLs when importing data to Azure file shares

Azure Files supports preserving directory or file level ACLs when copying data to Azure file shares. You can copy ACLs on a directory or file to Azure Files using either Azure File Sync or common file movement toolsets. For example, you can use [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) with the `/copy:s` flag to copy data as well as ACLs to an Azure file share. ACLs are preserved by default, you are not required to enable identity-based authentication on your storage account to preserve ACLs.

You can enable Azure AD Domain Service authentication for Azure Files on your new and existing storage accounts created after September 24, 2018. 

Before enabling this feature, verify that Azure AD Domain Services has been deployed for the primary Azure AD tenant with which your storage account is associated. If you have not yet set up Azure AD Domain Services, follow the step-by-step guidance provided in [Enable Azure Active Directory Domain Services using the Azure portal](../../active-directory-domain-services/tutorial-create-instance.md).

Azure AD Domain Services deployment generally takes 10 to 15 minutes. After Azure AD Domain Services has been deployed, you can enable Azure AD authentication over SMB for Azure Files. For more information, see [Enable Azure Active Directory Domain Service authentication over SMB for Azure Files](storage-files-active-directory-enable.md). 

### Configure share-level permissions for Azure Files
Once Azure AD Domain Service authentication has been enabled, you can configure custom RBAC roles for Azure AD identities and assign access rights to any file shares in the storage account.

When an application running on a domain-joined VM tries to mount an Azure file share or access a directory or file, the application's Azure AD credentials are verified to ensure the proper share-level permissions and NTFS permissions. For information about configuring share-level permissions, see [Enable Azure Active Directory Domain Service authentication over SMB](storage-files-active-directory-enable.md).


## Pricing
There is no additional service charge to enable Azure AD authentication over SMB on your storage account. For more information on pricing, see [Azure Files pricing](https://azure.microsoft.com/pricing/details/storage/files/) and [Azure AD Domain Services pricing](https://azure.microsoft.com/pricing/details/active-directory-ds/) pages.

## Next steps
For more information about Azure Files and Azure AD authentication over SMB, see these resources:

- [Introduction to Azure Files](storage-files-introduction.md)
- [Enable Azure Active Directory authentication over SMB for Azure Files](storage-files-active-directory-enable.md)
- [FAQ](storage-files-faq.md)
