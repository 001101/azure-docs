---
title: Tutorial - Deploy a remote Azure Resource Manager template
description: Learn how to an Azure Resource Manager template that is stored in a remote location.
ms.date: 02/20/2020
ms.topic: tutorial
ms.author: jgao
---

# Tutorial: Deploy a remote Azure Resource Manager template

In the [previous tutorial](./deployment-tutorial-options.md), you learned how to deploy a local template. Instead of storing Resource Manager templates on your local machine, you may prefer to store them in an external location. You can store templates in a source control repository (such as GitHub). Or, you can store them in an Azure storage account for shared access in your organization. In this tutorial, you learn how to deploy a template that is stored in an Azure storage account.  It takes about **12 minutes** to complete.

## Prerequisites

We recommend that you complete the [first deployment tutorial](./deployment-tutorial-local-template.md), but it's not required.

## Review template

In the previous tutorial, you deploy a template that creates a storage account, App Service plan, and web app. The template used is:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

## Store and share the template

The following PowerShell script creates a storage account, creates a container, copies the template from a github repository to the container. At the end of the execution, the script returns the URI of the  template. You will use the URI when you deploy the template.

Select **Try-it** to open the cloud shell, select **Copy** to copy the PowerShell script, and right-click the shell pane to paste the script:

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "templates" # The name of the Blob container to be created.

$templateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json" # The template used in this tutorial.
$fileName = "azuredeploy.json" # A file name used for downloading and uploading the template.

# Download the template
Invoke-WebRequest -Uri $templateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Get-azStorageBlob -container $containerName -Context $context | Select Name

Write-Host "The blob URI is https://${storageAccountName}.blob.core.windows.net/${containerName}/${fileName}".

Write-Host "Press [ENTER] to continue ..."
```

Make a note of the blob URI.

## Deploy template

Use either Azure CLI or Azure PowerShell to deploy a template.

If you haven't created the resource group, see [Create resource group](template-tutorial-create-first-template.md#create-resource-group). The example assumes you've set the **templateFile** variable to the path to the template file, as shown in the [first tutorial](./deployment-tutorial-options.md#deploy-template).

# [PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# [Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## Clean up resources

If you're moving on to the next tutorial, you don't need to delete the resource group.

If you're stopping now, you might want to clean up the resources you deployed by deleting the resource group.

1. From the Azure portal, select **Resource group** from the left menu.
2. Enter the resource group name in the **Filter by name** field.
3. Select the resource group name.
4. Select **Delete resource group** from the top menu.

## Next steps

You learned how to deploy a linked template. In the next tutorial, you learn how to secure the linked template by using SAS token.

> [!div class="nextstepaction"]
> [Add tags](deploy-tutorial-secure-linked-template.md)
