---
title: Deploy Resource Manager templates by using GitHub Actions
description: Describes how to deploy Azure Resource Manager templates by using GitHub Actions.
ms.topic: conceptual
ms.date: 05/01/2020
---

# Deploy Azure Resource Manager templates by using GitHub Actions

[GitHub Actions](https://help.github.com/en/actions) enables you to create custom software development life cycle (SDLC) workflows directly in your GitHub repository where your Azure Resource Manager (ARM) templates are stored. A [workflow](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) is defined by a YAML (.yml) file that is located inside the .github/workflows directory in your repository. Workflows must have at least one job, and jobs contain a set of steps that perform individual tasks. Steps can run commands or use an action. You can create your own actions or use actions shared by the GitHub community and customize them as needed. This article shows you how to deploy Resource Manager templates by using an Action called [Azure Resource Manager Template Deployment JS](https://github.com/marketplace/actions/azure-resource-manager-arm-template-deployment-js). You can find more actions from the [GitHub Marketplace](https://github.com/marketplace?type=actions).

The [ARM Template Deployment JS action](https://github.com/marketplace/actions/azure-resource-manager-arm-template-deployment-js) has two dependent actions:

- [Azure Login](https://github.com/marketplace/actions/azure-login): Login with your Azure credentials
- [Checkout](https://github.com/marketplace/actions/checkout): To checks-out your repository so the workflow can access any specified Resource Manager template.

A basic workflow for deploying an Resource Manager template can have three steps:

1. Check out a template file.
2. Sign on to Azure.
3. Deploy an Resource Manager template

## Prerequisites

You need a GitHub repository to store your Resource Manager templates and your workflow files. To create one, see [Creating a new repository](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## Configure deployment credentials

The Azure login action uses a service principal to authenticate against Azure. The principal of a CI / CD workflow typically needs the built-in contributor right in order to deploy Azure resources.

The following Azure CLI script shows how to generate an Azure Service Principal with Contributor permissions on an Azure resource group. This resource group is where the workflow will deploy the resources defined in your Resource Manager template.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Customize the value of $projectName and $location. The resource group name is the project name with **rg** appended.

The command should output a JSON object similar to this:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Copy the JSON output and store it as a GitHub secret within your GitHub repository. See [Prerequisite](#prerequisites) if you don't have a repository yet.

From your GitHub repository:

1. Select **Settings** from the top menu.
1. Select **Secret** from the left menu.
1. Enter the following values:

    - **Name**: AZURE_CREDENTIALS
    - **Value**: (Paste the JSON output)
1. Select **Add secret**.

You need to specify the secret name in the workflow.

## Add Resource Manager template

Add an Resource Manager template to the GitHub repository. If you don't have one, you can use the following template. The template creates a storage account.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

You can put the file anywhere in the repository.  The workflow sample assumes the template file is named **azuredeploy.json**, and it is stored in a folder called **templates** at the root of your repository.

## Create workflow

The workflow file must be stored in the **.github/workflow** folder at the root of your repository. The workflow file extension can be either **.yml** or **.yaml**.

You can either create a workflow file and the push or upload the file to the repository, or use the following procedure:

1. From your GitHub repository, select **Actions** from the top menu.
1. Select **New workflow**.
1. Select **set up a workflow yourself**.
1. Rename the workflow file if you prefer a different name other than **main.yml**. For example: **deployStorageAccount.yml**.
1. Replace the content of the yml file with the following:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

          - name: Deploy ARM Template
            uses: whiteducksoftware/azure-arm-action-js@v1
            with:
              resourceGroupName: myResourceGroup
              templateLocation: ./templates/azuredeploy.json
    ```

    There are three sections in the workflow:

    - **name**: The name of the workflow.
    - **on**: The name of the GitHub events that triggers the workflow. The workflow is trigger when there is a push event on the master branch, which modifies at least one of the two files specified. The two files are the workflow file and the template file.

        > ![NOTE]
        > Verify the two files and their paths match yours.
    - **jobs**: A workflow run is made up of one or more jobs. There is only one job called **deploy-storage-account-template**.  This job has three steps:

        - **Checkout source code**.
        - **Login to Azure**. Verify the secret name matches to what you saved to your repository. See [Configure deployment credentials](#configure-deployment-credentials).
        - **Deploy ARM template**. Replace the value of **resourceGroupName**.  If you used the Azure CLI script in [Configure deployment credentials](#configure-deployment-credentials), the generated resource group name is the project name with **rg** appended. Verify the value of **templateLocation**.

1. Select **Start commit**.
1. Select **Commit new file**.

## Check workflow status

1. Select the **Actions** tab. You shall see a **Create deployStorageAccount.yml** workflow listed. It takes 1-2 minutes to execute the workflow.
1. Select the workflow to open it.
1. Select **deploy-storage-account-template** (job name) from the left menu.
1. Select **Deploy ARM Template** (step name) to expand it. You can see the REST API response.

## Next steps

For a step-by-step tutorial that guides you through the process of creating a template, see [Tutorial: Create and deploy your first ARM template](template-tutorial-create-first-template.md).
