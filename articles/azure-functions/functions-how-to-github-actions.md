---
title: Use GitHub Actions to make code updates in Azure Functions
description: Learn how to use GitHub Actions to define a workflow to build and deploy Azure Functions projects in GitHub.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
---

# Continuous delivery by using GitHub Action

[GitHub Actions](https://github.com/features/actions) lets you define a workflow to automatically build and deploy your functions code to function app in Azure. 

> [!IMPORTANT]  
> GitHub Actions is currently in beta. You must first [sign-up to join the preview](https://github.com/features/actions) using your GitHub account.

In GitHub Actions, a [workflow](https://help.github.com/articles/about-github-actions#workflow) is an automated process that you define in your GitHub repository. This process tells GitHub how to build and deploy your functions app project on GitHub. 

A workflow is defined by a YAML (.yml) file in the `/.github/workflows/` path in your repository. This definition contains the various steps and parameters that make up the workflow. 

For an Azure Functions workflow, the file has three sections: 

| Section | Tasks |
| ------- | ----- |
| **Authentication** | <ol><li>Define a service principal.</li><li>Create a GitHub secret.</li></ol>|  
| **Build** | <ol><li>Set up the environment.</li><li>Build the function app.</li></ol> |
| **Deploy** | <ol><li>Deploy the function app.</li></ol>| 

## Create a service principal

You can create a [service principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) by using the [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) command in the [Azure CLI](/cli/azure/). You can run this command using [Azure Cloud Shell](https://shell.azure.com) in the Azure portal or by selecting the **Try it** button.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

In this example, replace the placeholders in the resource with your subscription ID, resource group, and function app name. The output is the role assignment credentials that provides access to your function app. Copy this JSON object, which you can use to authenticate from GitHub.

> [!IMPORTANT]
> It is always a good practice to grant minimum access. This is why the scope in the previous example is limited to the specific function app and not the entire resource group.

## Configure the GitHub secret

1. In [GitHub](https://github/com), browse your repository, select **Settings** > **Secrets** > **Add a new secret**.

    ![Add Secret](media/functions-how-to-github-actions/add-secret.png)

1. Use `AZURE_CREDENTIALS` for the **Name** and the copied command output for **Value**, then select **Add secret**. 

GitHub can now authenticate to your function app in Azure.

## Set up the environment 

Setting up the environment can be done using one of the publish setup actions.

|Language | Setup Action |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

A snippet from the yaml file for a Node JS app that uses version 10

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

## Build the function app

This depends on the language and for languages supported by Azure Functions, this section should be the standard build steps of each language.
As an example for Node JS

A snippet from the yaml file for a Node JS app

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

This depends on the language and for languages supported by Azure Functions, this section should be the standard build steps of each language

## Deploy the function app

To deploy your code to a function app, you will need to use the `Azure/functions-action` action. This action has two parameters:

|Parameter |Explanation  |
|---------|---------|
|**_app-name_** | (Mandatory) The name of your function app. |
|_**slot-name**_ | (Optional) The name of the [deployment slot](functions-deployment-slots.md) you want to deploy to. The slot must already be defined in your function app. |


The following snippet show how to use version 1 of the action

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## Next steps

There are a number of samples available in the [Azure GitHub Actions workflow samples repo](https://github.com/Azure/actions-workflow-samples). You can use these samples a starting point for your workflow.

> [!div class="nextstepaction"]
> [Learn more about GitHub Actions](https://help.github.com/en/articles/about-github-actions)
