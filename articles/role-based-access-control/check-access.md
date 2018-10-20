---
title: Quickstart - View the roles assigned to a user using the Azure portal | Microsoft Docs
description: Learn how to view the role-based access control (RBAC) permissions assigned to a user, group, or application using the Azure portal.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''

ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/20/2018
ms.author: rolyon
ms.reviewer: bagovind

#Customer intent: As a new user, I want to see how to quickly see how to check what access a user, group, or application has, to make sure they have the appropriate permissions.

---

# Quickstart: View the roles assigned to a user using the Azure portal

You can use the **Access control (IAM)** blade in [role-based access control (RBAC)](overview.md) to view the role assignments for multiple users, groups, and applications, but sometimes you just need to quickly view role assignments for a single user, group, or application. The easiest way to do this is to use the **Check access** feature in the Azure portal.

## View the role assignments

Follow these steps to view the role assignments for a single user, group, or application at the subscription scope.

1. In the Azure portal, click **All services** and then **Subscriptions**.

1. Click your subscription.

1. Click **Access control (IAM)**.

    On the **Tasks** tab, you see a section named **Check access**.

    ![Access control - Tasks tab](./media/check-access/access-control-tasks.png)

1. In the **Check Access for** list, select the type of security principal you want to check access for.

1. In the **Select** list, select a security principal. If you don't see the security principal in the list, you can type in the **Select** box to search the directory for display names, email addresses, and object identifiers.

    ![Check access select list](./media/check-access/check-access-select.png)

1. Click the security principal to open the **permissions and roles** pane.

    ![permissions and roles pane](./media/check-access/check-access-permissions-roles.png)

    On this pane, you can see the roles assigned to the selected security principal and the scope. If there are any deny assignments, they will also be listed.

## Next steps

> [!div class="nextstepaction"]
> [Tutorial: Grant access for a user using RBAC and the Azure portal](quickstart-assign-role-user-portal.md)
