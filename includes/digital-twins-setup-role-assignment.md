---
author: baanders
description: include file for the access permissions step in Azure Digital Twins setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
---

Azure Digital Twins uses [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) for role-based access control (RBAC). This means that before a user can make data plane calls to your Azure Digital Twins instance, that user must first be assigned a role with permissions to do so.

For Azure Digital Twins, this role is _**Azure Digital Twins Owner (Preview)**_. You can read more about roles and security in [*Concepts: Security for Azure Digital Twins solutions*](concepts-security.md).

This section will show you how to create a role assignment for a user in the Azure Digital Twins instance, through their email associated with the AAD tenant on your Azure subscription. Depending on your role and your permissions on your Azure subscription, you will either set this up for yourself, or set this up on behalf of someone else who will be managing the Azure Digital Twins instance.

### Assign the role

To give a user permissions to manage an Azure Digital Twins instance, you must assign them the *Azure Digital Twins Owner (Preview)* role within the instance. This is different from the *Owner* role on the entire Azure subscription, as it is scoped to this individual Azure Digital Twins resource.