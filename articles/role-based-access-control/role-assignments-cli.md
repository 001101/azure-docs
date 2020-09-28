---
title: Add or remove Azure role assignments using Azure CLI - Azure RBAC
description: Learn how to grant access to Azure resources for users, groups, service principals, or managed identities using Azure CLI and Azure role-based access control (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperfq1
---
# Add or remove Azure role assignments using Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] This article describes how to assign roles using Azure CLI.

## Prerequisites

To add or remove role assignments, you must have:

- `Microsoft.Authorization/roleAssignments/write` and `Microsoft.Authorization/roleAssignments/delete` permissions, such as [User Access Administrator](built-in-roles.md#user-access-administrator) or [Owner](built-in-roles.md#owner)
- [Bash in Azure Cloud Shell](/azure/cloud-shell/overview) or [Azure CLI](/cli/azure)

## Steps to add a role assignment

In Azure RBAC, to grant access, you add a role assignment. A role assignment consists of three elements: security principal, role definition, and scope. To add a role assignment, follow these steps.

### Step 1: Determine who needs access

You can assign a role to a user, group, service principal, or managed identity. To add or remove role assignments, you might need to specify the unique ID of an object. The ID has the format: `11111111-1111-1111-1111-111111111111`. You can get the ID using the Azure portal or Azure CLI.

**User**

To get the object ID for an Azure AD user, you can use [az ad user show](/cli/azure/ad/user#az_ad_user_show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

**Group**

To get the object ID for an Azure AD group, you can use [az ad group show](/cli/azure/ad/group#az_ad_group_show) or [az ad group list](/cli/azure/ad/group#az_ad_group_list).

```azurecli
az ad group show --group "{groupName}" --query objectId --output tsv
```

**Application**

To get the object ID for an Azure AD service principal (identity used by an application), you can use [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list). For a service principal, use the object ID and **not** the application ID.

```azurecli
az ad sp list --display-name "{spName}" --query [].objectId --output tsv
```
        
### Step 2: Find the appropriate role

Permissions are grouped together into roles. You can select from a list of several [Azure built-in roles](built-in-roles.md) or you can use your own custom roles.

To list roles and get the unique role ID, you can use [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --query '[].{name:name, roleType:roleType, roleName:roleName}' --output tsv
```

Here's how to list the details of a role.

```azurecli
az role definition list --name {roleName}
```

For more information, see [List Azure role definitions](role-definitions-list.md#azure-cli).
 
### Step 3: Identify the needed scope

Azure provides four levels of scope: resource, [resource group](../azure-resource-manager/management/overview.md#resource-groups), subscription, and [management group](../governance/management-groups/overview.md). It's a best practice to grant access with least privilege, so avoid assigning a role at a broader scope.

**Resource scope**

For resource scope, you need the resource ID for the resource. You can look at the properties of the resource in the Azure portal. The resource ID will have the following format.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType1}/{resourceSubType2}/{resourceName}
```

**Resource group scope**

For resource group scope, you need the name of the resource group. You can find the name on the **Resource groups** page in the Azure portal or you can use [az group list](/cli/azure/group#az_group_list).

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Subscription scope** 

For subscription scope, you need the subscription ID, you can find the ID on the **Subscriptions** page in the Azure portal or you can use [az account list](/cli/azure/account#az_account_list).

```azurecli
az account list --query '[].{name:name, id:id}' --output tsv
```

**Management group scope** 

For management group scope, you need the management group name, you can find the name on the **Management groups** page in the Azure portal or you can use [az account management-group list](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az account management-group list --query '[].{name:name, id:id}' --output tsv
```
    
### Step 4: Add role assignment

To add a role assignment, use the [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) command.

To add a role assignment at a resource scope, the command typically has the following format.

```azurecli
az role assignment create --assignee {assignee} \
--role {roleNameOrId} \
--scope /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

To add a role assignment at a resource group scope, the command typically has the following format.

```azurecli
az role assignment create --assignee {assignee} \
--role {roleNameOrId} \
--resource-group {resourceGroupName}
```

To add a role assignment at a subscription scope, the command typically has the following format.

```azurecli
az role assignment create --assignee {assignee} \
--role {roleNameOrId} \
--subscription {subscriptionNameOrId}
```

To add a role assignment at a management group scope, the command typically has the following format. 

```azurecli
az role assignment create --assignee {assignee} \
--role {roleNameOrId} \
--scope /providers/Microsoft.Management/managementGroups/{groupName}
``` 
    
## Add role assignment examples

### Add role assignment for a specific blob container resource scope

Assigns the [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) role at a resource scope for a blob container named *blob-container-01*.

```azurecli
az role assignment create --assignee {assignee} \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

### Add role assignment for all blob containers in a storage account resource scope

Assigns the [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) role at a resource scope for a storage account named *storage12345*.

```azurecli
az role assignment create --assignee {assignee} \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

### Add role assignment for a group in a specific virtual network resource scope

Assigns the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role to the *Ann Mack Team* group with ID 22222222-2222-2222-2222-222222222222 at a resource scope for a virtual network named *pharma-sales-project-network*.

```azurecli
az role assignment create --assignee 22222222-2222-2222-2222-222222222222 \
--role "Virtual Machine Contributor" \
--scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### Add role assignment for a user at a resource group scope

Assigns the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role to *patlong\@contoso.com* user at the *pharma-sales* resource group scope.

```azurecli
az role assignment create --assignee patlong@contoso.com \
--role "Virtual Machine Contributor" \
--resource-group pharma-sales
```

### Add role assignment for a user using the unique role ID at a resource group scope

There are a couple of times when a role name might change, for example:

- You are using your own custom role and you decide to change the name.
- You are using a preview role that has **(Preview)** in the name. When the role is released, the role is renamed.

Even if a role is renamed, the role ID does not change. If you are using scripts or automation to create your role assignments, it's a best practice to use the unique role ID instead of the role name. Therefore, if a role is renamed, your scripts are more likely to work.

The following example assigns the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role to the *patlong\@contoso.com* user at the *pharma-sales* resource group scope.

```azurecli
az role assignment create --assignee patlong@contoso.com \
--role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c \
--resource-group pharma-sales
```

### Add role assignment for all blob containers at a resource group scope

Assigns the [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) role at the *Example-Storage-rg* resource group scope.

```azurecli
az role assignment create --assignee {assignee} \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

Alternately, you can specify the fully qualified resource group with the `--scope` parameter:

```azurecli
az role assignment create --assignee {assignee} \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

### Add role assignment for an application at a resource group scope

Assigns the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role to an application with object ID 44444444-4444-4444-4444-444444444444 at the *pharma-sales* resource group scope.

```azurecli
az role assignment create --assignee 44444444-4444-4444-4444-444444444444 \
--role "Virtual Machine Contributor" \
--resource-group pharma-sales
```

### Add role assignment for a new service principal at a resource group scope

If you create a new service principal and immediately try to assign a role to that service principal, that role assignment can fail in some cases. For example, if you use a script to create a new managed identity and then try to assign a role to that service principal, the role assignment might fail. The reason for this failure is likely a replication delay. The service principal is created in one region; however, the role assignment might occur in a different region that hasn't replicated the service principal yet. To address this scenario, you should specify the principal type when creating the role assignment.

To add a role assignment, use [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create), specify a value for `--assignee-object-id`, and then set `--assignee-principal-type` to `ServicePrincipal`.

```azurecli
az role assignment create --assignee-object-id {assigneeObjectId} \
--assignee-principal-type {assigneePrincipalType} \
--role {roleNameOrId} \
--resource-group {resourceGroupName} \
--scope /subscriptions/{subscriptionId}
```

The following example assigns the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role to the *msi-test* managed identity at the *pharma-sales* resource group scope:

```azurecli
az role assignment create --assignee-object-id 33333333-3333-3333-3333-333333333333 \
--assignee-principal-type ServicePrincipal \
--role "Virtual Machine Contributor" \
--resource-group pharma-sales
```

### Add role assignment for a user at a subscription scope

Assigns the [Reader](built-in-roles.md#reader) role to the *annm\@example.com* user at a subscription scope.

```azurecli
az role assignment create --assignee annm@example.com \
--role "Reader" \
--subscription 00000000-0000-0000-0000-000000000000
```

### Add role assignment for a group at a subscription scope

Assigns the [Reader](built-in-roles.md#reader) role to the *Ann Mack Team* group with ID 22222222-2222-2222-2222-222222222222 at a subscription scope.

```azurecli
az role assignment create --assignee 22222222-2222-2222-2222-222222222222 \
--role Reader \
--scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### Add role assignment for all blob containers at a subscription scope

Assigns the [Storage Blob Data Reader](built-in-roles.md#storage-blob-data-reader) role at a subscription scope.

```azurecli
az role assignment create --assignee {assignee} \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

### Add role assignment for a user at a management group scope

Assigns the [Billing Reader](built-in-roles.md#billing-reader) role to the *alain\@example.com* user at a management group scope.

```azurecli
az role assignment create --assignee alain@example.com \
--role "Billing Reader" \
--scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## Remove role assignment

In Azure RBAC, to remove access, you remove a role assignment by using [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete).

The following example removes the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role assignment from the *patlong\@contoso.com* user on the *pharma-sales* resource group:

```azurecli
az role assignment delete --assignee patlong@contoso.com \
--role "Virtual Machine Contributor" \
--resource-group pharma-sales
```

Removes the [Reader](built-in-roles.md#reader) role from the *Ann Mack Team* group with ID 22222222-2222-2222-2222-222222222222 at a subscription scope.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 \
--role "Reader" \
--subscription 00000000-0000-0000-0000-000000000000
```

Removes the [Billing Reader](built-in-roles.md#billing-reader) role from the *alain\@example.com* user at the management group scope.

```azurecli
az role assignment delete --assignee alain@example.com \
--role "Billing Reader" \
--scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## Next steps

- [List Azure role assignments using Azure CLI](role-assignments-list-cli.md)
- [Use the Azure CLI to manage Azure resources and resource groups](../azure-resource-manager/cli-azure-resource-manager.md)
