---
title: Tutorial - Create a custom role using Azure PowerShell | Microsoft Docs
description: Get started creating a custom role using Azure PowerShell.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''

ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/26/2018
ms.author: rolyon

#Customer intent: As a dev or devops, I want step-by-step instructions for how to grant custom permissions because the current built-in roles do not meet my permission needs.

---
# Tutorial: Create a custom role using Azure PowerShell

If the [built-in roles](built-in-roles.md) don't meet the specific needs of your organization, you can create your own custom roles. For this tutorial, you create a custom role named Reader Support Tickets. It allows the user to view everything in the subscription and also open support tickets.

For a user to be able to open support tickets, he must be assigned a role at the subscription scope, because all support tickets are created based on an Azure subscription.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create a custom role
> * Update a custom role
> * Delete a custom role

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

To complete this tutorial, you will need:

- Permissions to create groups in Azure Active Directory (or have an existing group)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell) -OR- [Azure PowerShell](/powershell/azure/install-azurerm-ps) with the [Azure AD module](/powershell/azure/active-directory/install-adv2)

## Create a custom role

The easiest way to create a custom role is to start with a built-in role, edit it, and then create a new role.

1. In PowerShell, use the [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) command to get the list of operations for the Microsoft.Support resource provider.

    ```azurepowershell
    Get-AzureRMProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

    It's helpful to know the operations that are available to create your permissions. You can also see a list of all the operations at [Azure Resource Manager resource provider operations](resource-provider-operations.md#microsoftsupport).

1. Use the [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) command to output the [Reader](built-in-roles.md#reader) role in JSON format.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```
    
    The following shows the JSON output for the [Reader](built-in-roles.md#reader) role. For information about the different sections, see [Understand role definitions](role-definitions.md).

    ```json
    {
        "Name":  "Reader",
        "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "IsCustom":  false,
        "Description":  "Lets you view everything, but not make any changes.",
        "Actions":  [
                        "*/read"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/"
                             ]
    }
    ```
    
1. Edit the JSON file to add the `"Microsoft.Support/*"` operation to the `Actions` section. This action will allow the user to create support tickets.

1. Get the ID of your subscription using the [Get-AzureRmSubscription](/powershell/module/azurerm.resources/get-azurermsubscription) command.

    ```azurepowershell
    Get-AzureRmSubscription
    ```

1. In `AssignableScopes`, add your subscription ID with the following format: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    You must add explicit subscription IDs, otherwise you won't be allowed to import the role into your subscription.

1. Delete the `Id` property line and change the `IsCustom` property to `true`.

1. Change the `Name` and `Description` properties to "Reader Support Tickets" and "View everything in the subscription and also open support tickets."

    Your JSON file should look like the following:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
    
1. To create the new custom role, you use the [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) command and specify the JSON role definition file.

    ```azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    After running [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition), the new custom role is available in the Azure portal and can be assigned to users just like built-in roles.
    
    ![screenshot of custom role imported in the Azure portal](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## Update the custom role

To update the custom role, you can update the JSON file or use the `PSRoleDefinition` object.

1. To update the JSON file, use the [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) command to output the role in JSON format.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```
    
1. In `Actions`, add the operation to create and manage resource group deployments `"Microsoft.Resources/deployments/*"`. Be sure to include a comma after the first previous operation.

    Your updated JSON file should look like the following:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "Id":  "22222222-2222-2222-2222-222222222222",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*",
                        "Microsoft.Resources/deployments/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
        
1. To update the custom role, use the [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) command and specify the updated JSON file.

    ```azurepowershell
    Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. To use the `PSRoleDefintion` object, first use the [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) command to get the role.

    ```azurepowershell
    $role = Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```
    
1. Add the operation to read diagnostic settings.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Use the [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) to updates the role.

    ```azurepowershell
    Set-AzureRmRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## Delete the custom role

1. Use the [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) command to get the ID of the custom role.

    ```azurepowershell
    Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```

1. Use the [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) command and specify the role ID to delete the custom role.

    ```azurepowershell
    Remove-AzureRmRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. When asked to confirm, type **Y**.

## Next steps

> [!div class="nextstepaction"]
> [Custom roles in Azure](custom-roles.md)