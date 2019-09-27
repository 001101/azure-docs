---
 title: include file
 description: include file
 services: storage
 author: tamram
 ms.service: storage
 ms.topic: include
 ms.date: 07/30/2019
 ms.author: tamram
 ms.custom: include file
---

[Azure Files](../articles/storage/files/storage-files-introduction.md) supports identity-based authentication over Server Message Block (SMB) through [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA) and [Active Directory Domain Services (AD DS)](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (preview). This article focuses on how Azure Files can leverage domain services, either on-premises or on Azure,  to support identity-based access to Azure Files over SMB. This allows you to easily replace your existing file servers with Azure Files and continue to use your existing directory service, maintaining seamless user access to shares. 

Azure Files enforces authorization on the user access on both share and directory/file level. Share-level permission assignment can be assigned to Azure AD user or groups managed through the typical [role-based access control (RBAC)](../articles/role-based-access-control/overview.md) model. With RBAC, the credentials you plan to use for file access should be available or synced to Azure AD. You can assign built-in RBAC roles like Storage File Data SMB Share Reader to a user of groups in Azure AD to grant read access to an Azure file share.

On directory/file level, Azure Files supports preserving, inheriting, and enforcing [NTFS DACLs](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) just like any Windows File servers. If you copy data over SMB from a file share to Azure Files, or vice versa, you can choos to keep NTFS DACLs. Whether you plan to enforce authorization or not, you can leverage Azure Files to backup ACLs along with your data. 


> [!NOTE]
> - Azure AD DS authentication for Server Message Block (SMB) access is not supported for Linux VMs. Only Windows VMs are supported.
> - Azure AD DS authentication for SMB access is not supported for Active Directory domain-joined machines. In the interim, consider using [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) to start migrating your data to Azure Files and to continue enforcing access control by using Active Directory credentials from your on-premises Active Directory domain-joined machines. 
> - Azure AD DS authentication for SMB access is available only for storage accounts created after September 24, 2018.
> - Azure AD DS authentication for SMB access and NTFS DACL persistence is not supported on Azure file shares managed by Azure File Sync.
> - Azure AD DS authentication does not support authentication against Machine Accounts created in Azure AD DS.
