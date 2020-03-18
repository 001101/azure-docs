---
title: Tag resources, resource groups, and subscriptions for logical organization
description: Shows how to apply tags to organize Azure resources for billing and managing.
ms.topic: conceptual
ms.date: 03/18/2020
---
# Use tags to organize your Azure resources, resource groups and subscriptions

You apply tags to your Azure resources, resource groups, and subscriptions to logically organize them into a taxonomy. Each tag consists of a name and a value pair. For example, you can apply the name "Environment" and the value "Production" to all the resources in production.

For recommendations on how to implement a tagging strategy, see [Resource naming and tagging decision guide](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## Required access

To apply tags to resources, the user must have write access to that resource type. To apply tags to all resource types, use the [Contributor](../../role-based-access-control/built-in-roles.md#contributor) role. To apply tags to only one resource type, use the contributor role for that resource. For example, to apply tags to virtual machines, use the [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## PowerShell

### Apply tags

Azure PowerShell offers two commands for applying tags - [New-AzTag](/powershell/module/az.resources/new-aztag) and [Update-AzTag](/powershell/module/az.resources/update-aztag). You must have Azure PowerShell 3.6.1 or later to use these commands.

The **New-AzTag** replaces all tags on the resource, resource group, or subscription. When calling the command, pass in the resource ID of the entity you wish to tag.

The following example applies a set of tags to a storage account:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -resourcename demoStorage -resourcegroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

When the command completes, notice that the resource has two tags.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

If you run the command again but this time with different tags, notice that the earlier tags are removed.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

To add tags to a resource that already has tags, use **Update-AzTag**. Set the **-Operation** parameter to **Merge**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Notice all four tags have been applied to the resource.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

When you set the **-Operation** parameter to **Replace**, the existing tags are replaced by the new set of tags.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Only the new tags remain on the resource.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

The same commands also work with resource groups or subscriptions. You pass in the identifier for the resource group or subscription you want to tag.

To add a new set of tags to a resource group, use:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

To update the tags for a resource group, use:

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

To add tags to a subscription, use:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

You may have more than one resource with the same name in a resource group. In that case, you can set each resource with the following commands:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### Inherit tags

To apply tags from a subscription or resource group to the resources, see [Azure Policies - tags](tag-policies.md).

### List tags

To see the existing tags for a *resource group*, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

That script returns the following format:

```output
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

To see the existing tags for a *resource that has a specified name and resource group*, use:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Or, if you have the resource ID for a resource, you can pass that resource ID to get the tags.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

### List resources by tag

To get *resources that have a specific tag name and value*, use:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

To get *resources that have a specific tag name*, use:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

To get *resource groups that have a specific tag name and value*, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

### Remove tags

To remove specific tags, use **Update-AzTag** and set **-Operation** to **Delete**.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

The specified tags are removed.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

To remove all tags, use the [Remove-AzTag](/powershell/module/az.resources/remove-aztag) command.

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## Azure CLI

To see the existing tags for a *resource group*, use:

```azurecli-interactive
az group show -n examplegroup --query tags
```

That script returns the following format:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Or, to see the existing tags for a *resource that has a specified name, type, and resource group*, use:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

When looping through a collection of resources, you might want to show the resource by resource ID. A complete example is shown later in this article. To see the existing tags for a *resource that has a specified resource ID*, use:

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

To get resource groups that have a specific tag, use `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

To get all the resources that have a particular tag and value, use `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

When adding tags to a resource group or resource, you can either overwrite the existing tags or append new tags to existing tags.

To overwrite the existing tags on a resource group, use:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

To append a tag to the existing tags on a resource group, use:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

To overwrite the tags on a resource, use:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

To append a tag to the existing tags on a resource, use:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

### Inherit tags

To apply tags from a subscription or resource group to the resources, see [Azure Policies - tags](tag-policies.md).


If your tag names or values include spaces, you must take a couple of extra steps. The following example applies all tags from a resource group to its resources when the tags may contain spaces.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## ARM templates

To tag a resource during deployment, add the `tags` element to the resource you're deploying. Provide the tag name and value.

### Apply a literal value to the tag name

The following example shows a storage account with two tags (`Dept` and `Environment`) that are set to literal values:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

To set a tag to a datetime value, use the [utcNow function](../templates/template-functions-string.md#utcnow).

### Apply an object to the tag element

You can define an object parameter that stores several tags, and apply that object to the tag element. Each property in the object becomes a separate tag for the resource. The following example has a parameter named `tagValues` that is applied to the tag element.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### Apply a JSON string to the tag name

To store many values in a single tag, apply a JSON string that represents the values. The entire JSON string is stored as one tag that can't exceed 256 characters. The following example has a single tag named `CostCenter` that contains several values from a JSON string:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### Apply tags from resource group

To apply tags from a resource group to a resource, use the [resourceGroup](../templates/template-functions-resource.md#resourcegroup) function. When getting the tag value, use the `tags[tag-name]` syntax instead of the `tags.tag-name` syntax, because some characters aren't parsed correctly in the dot notation.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## REST API

The Azure portal and PowerShell both use the [Resource Manager REST API](/rest/api/resources/) behind the scenes. If you need to integrate tagging into another environment, you can get tags by using **GET** on the resource ID and update the set of tags by using a **PATCH** call.

## Tags and billing

You can use tags to group your billing data. For example, if you're running multiple VMs for different organizations, use the tags to group usage by cost center. You can also use tags to categorize costs by runtime environment, such as the billing usage for VMs running in the production environment.

You can retrieve information about tags through the [Azure Resource Usage and RateCard APIs](../../billing/billing-usage-rate-card-overview.md) or the usage comma-separated values (CSV) file. You download the usage file from the [Azure Account Center](https://account.azure.com/Subscriptions) or Azure portal. For more information, see [Download or view your Azure billing invoice and daily usage data](../../billing/billing-download-azure-invoice-daily-usage-date.md). When downloading the usage file from the Azure Account Center, select **Version 2**. For services that support tags with billing, the tags appear in the **Tags** column.

For REST API operations, see [Azure Billing REST API Reference](/rest/api/billing/).

## Limitations

The following limitations apply to tags:

* Not all resource types support tags. To determine if you can apply a tag to a resource type, see [Tag support for Azure resources](tag-support.md).
* Each resource or resource group can have a maximum of 50 tag name/value pairs. If you need to apply more tags than the maximum allowed number, use a JSON string for the tag value. The JSON string can contain many values that are applied to a single tag name. A resource group can contain many resources that each have 50 tag name/value pairs.
* The tag name is limited to 512 characters, and the tag value is limited to 256 characters. For storage accounts, the tag name is limited to 128 characters, and the tag value is limited to 256 characters.
* Generalized VMs don't support tags.
* Tags applied to the resource group are not inherited by the resources in that resource group.
* Tags can't be applied to classic resources such as Cloud Services.
* Tag names can't contain these characters: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Currently Azure DNS zones and Traffic Manger services also don't allow the use of spaces in the tag.


## Next steps

* Not all resource types support tags. To determine if you can apply a tag to a resource type, see [Tag support for Azure resources](tag-support.md).
* For an introduction to using the portal, see [Using the Azure portal to manage your Azure resources](manage-resource-groups-portal.md).
