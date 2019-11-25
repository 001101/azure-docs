---
title: Manage access to Azure resources using RBAC and the Azure portal | Microsoft Docs
description: Learn how to manage access to Azure resources for users, groups, service principals, and managed identities using role-based access control (RBAC) and the Azure portal. This includes how to list access, grant access, and remove access.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman

ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/24/2019
ms.author: rolyon
ms.reviewer: bagovind
---

# List role assignments using RBAC and the Azure portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] This article describes how to list role assignments using the Azure portal.

## List all role assignments for a user or group

The easist way to see the roles assigned to a user or group in a subscription is to use the **Azure resources** pane.

1. In the Azure portal, click **All services** and then select **Users** or **Groups**.

1. Click the user or group you want list the role assignments for.

1. Click **Azure resources**.

    You see a list of roles assigned to the selected user or group at various scopes such as management group, subscription, resource group, or resource. This list includes all role assignments you have permission to read.

    ![Azure resources for a user](./media/role-assignments-list-portal/azure-resources-user.png)    

1. To change the subscription, click the **Subscriptions** list.

## List all role assignments for a system-managed identity

1. In the Azure portal, open a system-managed identity.

    ![System-managed identity](./media/role-assignments-list-portal/managed-identity.png)

1. Under **Role assignments**, click **Show the Azure RBAC roles assigned to this managed identity**.

    You see a list of roles assigned to the selected system-managed identity at various scopes such as management group, subscription, resource group, or resource. This list includes all role assignments you have permission to read.

## List role assignments for a user at a scope

To list access for a user, group, service principal, or managed identity, you view their role assignments. Follow these steps to view the access for a single user, group, service principal, or managed identity at a particular scope.

1. In the Azure portal, click **All services** and then select the scope. For example, you can select **Management groups**, **Subscriptions**, **Resource groups**, or a resource.

1. Click the specific resource.

1. Click **Access control (IAM)**.

1. Click the **Check access** tab.

    ![Access control - Check access tab](./media/role-assignments-portal/access-control-check-access.png)

1. In the **Find** list, select the type of security principal you want to check access for.

1. In the search box, enter a string to search the directory for display names, email addresses, or object identifiers.

    ![Check access select list](./media/role-assignments-portal/check-access-select.png)

1. Click the security principal to open the **assignments** pane.

    ![assignments pane](./media/role-assignments-portal/check-access-assignments.png)

    On this pane, you can see the roles assigned to the selected security principal and the scope. If there are any deny assignments at this scope or inherited to this scope, they will be listed.

## List all role assignments at a scope

1. In the Azure portal, click **All services** and then select the scope. For example, you can select **Management groups**, **Subscriptions**, **Resource groups**, or a resource.

1. Click the specific resource.

1. Click **Access control (IAM)**.

1. Click the **Role assignments** tab to view all the role assignments at this scope.

   ![Access control - Role assignments tab](./media/role-assignments-portal/access-control-role-assignments.png)

   On the Role assignments tab, you can see who has access at this scope. Notice that some roles are scoped to **This resource** while others are **(Inherited)** from another scope. Access is either assigned specifically to this resource or inherited from an assignment to the parent scope.

## Next steps

* [Tutorial: Grant a user access to Azure resources using RBAC and the Azure portal](quickstart-assign-role-user-portal.md)
* [Tutorial: Grant a user access to Azure resources using RBAC and Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Troubleshoot RBAC for Azure resources](troubleshooting.md)
* [Organize your resources with Azure management groups](../governance/management-groups/overview.md)
