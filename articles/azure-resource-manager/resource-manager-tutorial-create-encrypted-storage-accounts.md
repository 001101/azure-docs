﻿---
title: Create an Azure Resource Manager template for deploying an encrypted storage account | Microsoft Docs
description: Use Visual Studio Code to create a template for deploying an encrypted storage account.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/17/2018
ms.topic: quickstart
ms.author: jgao

---

# Tutorial: create an Azure Resource Manager template for deploying an encrypted storage account

Learn how to use the information found in the Azure REST API Reference to complete an Azure Resource Manager template.

In this tutorial, you use a base template from Azure Quickstart templates to create an Azure Storage account.  Using the REST API reference, you customize the base template to create an encrypted storage account.

This tutorial covers the following tasks:

> [!div class="checklist"]
> * Open a Quickstart template
> * Define parameters
> * Define variables
> * Edit the template
> * Deploy the template

If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To complete this article, you need:

- [Visual Studio Code](https://code.visualstudio.com/).
- Resource Manager Tools extension. To install, see [Install the Resource Manager Tools extension](./resource-manager-quickstart-create-templates-use-visual-studio-code#prerequisites).

## Open a Quickstart template

Instead of creating a template from scratch, you open a template from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/). Azure QuickStart Templates is a repository for Resource Manager templates.

The template used in this quickstart is called [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/). The template defines an Azure Storage account resource.

1. From Visual Studio Code, select **File**>**Open File**.
2. In **File name**, paste the following URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Select **Open** to open the file.
4. Select **File**>**Save As** to save the file as **azuredeploy.json** to your local computer.

## Understand the format

From VS Code, collapse the template to the root level. You have the simplest structure with the following elements:

![Resource Manager template simplest structure](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

- **$schema**: specify the location of the JSON schema file that describes the version of the template language.
- **contentVersion**: specify any value for this element to document significant changes in your template.
- **parameters**: Specify the values that are provided when deployment is executed to customize resource deployment.
- **variables**: Specify the values that are used as JSON fragments in the template to simplify template language expressions.
- **resources**: specify the resource types that are deployed or updated in a resource group.
- **outputs**: specify the values that are returned after deployment.

## Define parameters

Parameters enable you to customize the deployment by providing values that are tailored for a particular environment.

![Resource Manager template parameters](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

In this template, two parameters are defined. Notice a function is used in location.defaultValue:

```json
"defaultValue": "[resourceGroup().location]",
```
The resourceGroup() function returns an object that represents the current resource group. For a list of template functions, see [Azure Resource Manager template functions](./resource-group-template-functions.md).

To reference the parameters in the template:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## Define variables

Variables allow you to construct values that can be used throughout your template. Variables help reducing the complexity of the templates.

![Resource Manager template variables](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

This template defines one variable *storageAccountName*. In the definition, two template functions are used:

- **concat()**: concatenate strings.  For a list of array functions, see [Array and object functions for Azure Resource Manager templates](./resource-group-template-functions-array.md).
- **uniqueString()**: creates a deterministic hash string based on the values provided as parameters. For more string functions, see [String functions](./resource-group-template-functions-string.md).

## Edit the template

To find the encryption related configuration, you can use the template reference.

1. Browse to [Azure Templates](https://docs.microsoft.com/azure/templates/).
2. From the TOC on the left, select **Reference**->**Storage**->**Storage Accounts**. The page contains the information for defining an Storage Account information.
3. Explore the encryption related information.  
4. Inside the properties element of the storage account resource definition, add the following json:

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    This part enables the encryption function of the blob storage service.

## Deploy the template

There are many methods for deploying templates.  In this quickstart, you use the Cloud shell from the Azure portal. The Cloud shell supports both Azure CLI and Azure PowerShell. The instructions provided here use CLI.

1. Sign in to the [Azure portal](https://portal.azure.com)
2. Select **Cloud Shell** from the upper right corner as shown in the following image:

    ![Azure portal Cloud shell](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. Select the down arrow and then select **Bash** to switch to CLI from PowerShell.

    ![Azure portal Cloud shell CLI](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. Select **Restart** to restart the shell.
5. Select **Upload/download files**, and then select **Upload**.

    ![Azure portal Cloud shell upload file](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
4. Select the file you saved earlier in the quickstart. The default name is **azuredeploy.json**.
5. From the Cloud shell, run the **ls** command to verify the file is uploaded successfully. You can also use the **cat** command to verify the template content.

    ![Azure portal Cloud shell list file](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
6. From the Cloud shell, run the following commands:

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file <TemplateFileName>
    ```
    Here is the screenshot of a sample deployment:

    ![Azure portal Cloud shell deploy template](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    On the screenshot, these values are used:

    - **&lt;ResourceGroupName>**: myresourcegroup0709. There are two appearances of the parameter.  Make sure to use the same value.
    - **&lt;AzureLocation>**: eastus2
    - **&lt;DeployName>**: mydeployment0709
    - **&lt;TemplateFile>**: azuredeploy.json

    From the screenshot output, the storage account name is *3tqebj3slyfyestandardsa*. 

7. Run the following PowerShell command to list the newly created storage account:

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

## Clean up resources

When the Azure resources are no longer needed, clean up the resources you deployed by deleting the resource group.

1. From the Azure portal, select **Resource group** from the left menu.
2. Enter the resource group name in the **Filter by name** field.
3. Select the resource group name.  You shall see a total of six resources in the resource group.
4. Select **Delete resource group** from the top menu.

## Next steps

In this tutorial, you learned how to create a template using Visual Studio Code, and how to deploy the template using the Azure portal Cloud shell. The template used in this Quickstart only contains one Azure resource.  In the next tutorial, you develop a template with multiple resources.  Some of the resources have dependent resources.

> [!div class="nextstepaction"]
> [Create templates by using Visual Studio](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
