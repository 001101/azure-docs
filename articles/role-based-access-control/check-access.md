---
title: Quickstart - Check access for a user to Azure resources - Azure RBAC
description: In this quickstart, you learn how to check the access for yourself or another user to Azure resources using the Azure portal and Azure role-based access control (Azure RBAC).
services: role-based-access-control
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.custom: contperfq2

#Customer intent: As a new user, I want to quickly see access for myself, user, group, or application, to make sure they have the appropriate permissions.

---

# Quickstart: Check access for a user to Azure resources

Sometimes you need to check who has access to a set of Azure resources. You check their access by listing their Azure role assignments. A quick way to check the access for a single user is to use the **Check access** feature.

## Check my access

Follow these steps to view your access to a set of Azure resources.

1. Open the Azure portal.

1. Open the set of resources that you want to check access for, such as **Subscriptions**, **Resource groups**, or a particular resource. This is called the [scope](scope-overview.md).

1. Click your subscription, resource group, or resource.

1. Click **Access control (IAM)**.

    The following shows an example of the Access control (IAM) page for a resource group.

    ![Resource group access control - Check access tab](./media/check-access/rg-access-control.png)

1. On the **Check access** tab, click the **View my access** button.

    An assignments pane appears that lists your access at this scope and inherited to this scope. Assignments at lower scopes are not listed. You see the following assignments:

    - Role assignments added with Azure RBAC.
    - Deny assignments added using Azure Blueprints or Azure managed apps.
    - Classic Service Administrator or Co-Administrator assignments for classic deployments. 

    ![Role and deny assignments pane](./media/check-access/rg-check-access-assignments.png)

## Check access for a security principal

You can also check the access for a single user, group, service principal, or managed identity. Follow these steps to check the access for a security principal to a set of Azure resources.

1. Open the Azure portal.

1. Open the set of resources that you want to check access for, such as **Subscriptions**, **Resource groups**, or a particular resource. This is called the [scope](scope-overview.md).

1. Click your subscription, resource group, or resource.

1. Click **Access control (IAM)**.

    ![Resource group access control - Check access tab for security principal](./media/check-access/rg-access-control.png)

1. On the **Check access** tab, in the **Find** list, select the user, group, service principal, or managed identity you want to check access for.

1. In the search box, enter a string to search the directory for display names, email addresses, or object identifiers.

    ![Check access select list](./media/shared/rg-check-access-select.png)

1. Click the security principal to open the **assignments** pane.

    On this pane, you can see the access for the selected security principal at this scope and inherited to this scope. Assignments at lower scopes are not listed.

    ![Role and deny assignments pane for a security principal](./media/shared/rg-check-access-assignments-security-principal.png)

## Next steps

> [!div class="nextstepaction"]
> [List Azure role assignments using the Azure portal](role-assignments-list-portal.md)
