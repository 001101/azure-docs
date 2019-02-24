---
title: Deploy to Azure Functions using the Jenkins Azure Functions plugin
description: Learn how to deploy to Azure Functions using the Jenkins Azure Functions plugin
ms.service: jenkins
keywords: jenkins, azure, devops, java, azure functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
---

# Deploy to Azure Functions using the Jenkins Azure Functions plugin

[Azure Functions](/azure/azure-functions/) is a serverless compute service. Using Azure Functions, you can run code on-demand without provisioning or managing infrastructure. This tutorial shows how to deploy a Java function to Azure Functions using the Azure Functions plugin.

## Prerequisites

- **Azure subscription**: If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) before you begin.
- **Jenkins server**: If you don't have a Jenkins server installed, refer to the article, [Create a Jenkins server on Azure](./install-jenkins-solution-template.md).

 > [!TIP]
 > The source code used for this tutorial is located in the [Visual Studio China GitHub repo](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

## Create a Java function

To create a Java function with the Java runtime stack, use either the [Azure portal](https://portal.azure.com) or the [Azure CLI](/cli/azure/?view=azure-cli-latest).

The following steps show how to create a Java function using the Azure CLI:

1. Create a resource group, inserting your resource group name for the &lt;resource-group> placeholder.

    ```cli
    az group create --name <resource-group> --location eastus
    ```

1. Create an Azure storage account, replacing the placeholders with the appropriate values.
 
    ```cli
    az storage account create --name <storage-name> --location eastus --resource-group <resource-group> --sku Standard_LRS    
    ```

1. Create the test function app, replacing the placeholders with the appropriate values.

    ```cli
    az functionapp create --resource-group <resource-group> --consumption-plan-location eastus --name <app-name> --storage-account <storage-name>
    ```
    
1. Update to version 2.x runtime, replacing the placeholders with the appropriate values.

    ```cli
    az functionapp config appsettings set --name <function-app> --resource-group <resource-group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## Prepare Jenkins server

The following steps explain how to prepare the Jenkins server:

1. Deploy a [Jenkins server](https://aka.ms/jenkins-on-azure) on Azure. If you don't already have an instance of Jenkins server installed, the article, [Create a Jenkins server on Azure](./install-jenkins-solution-template.md) guides you through the process.

1. Sign in to the Jenkins instance with SSH.

1. On the Jenkins instance, install maven using the following command:

    ```terminal
    sudo apt install -y maven
    ```

1. On the Jenkins instance, install the [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) by issuing the following commands at a terminal prompt:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. In the Jenkins dashboard, install the following plugins:

    - Azure Functions Plugin
    - EnvInject Plugin

1. Jenkins needs an Azure service principal to authenticate and access Azure resources. Refer to the [Deploy to Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md) for step-by-step instructions.

1. Using the Azure service principal, add a "Microsoft Azure Service Principal" credential type in Jenkins. Refer to the [Deploy to Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) tutorial.

## Create a Jenkins Pipeline

In this section, you create the [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/).

1. In the Jenkins dashboard, create a Pipeline.

1. Enable **Prepare an environment for the run**.

1. Add the following environment variables in **Properties Content**, replacing the placeholders with the appropriate values for your environment:

    ```
    AZURE_CRED_ID=<service-principal-credential-id>
    RES_GROUP=<resource-group>
    FUNCTION_NAME=<app-name>
    ```
    
1. In the **Pipeline->Definition** section, select **Pipeline script from SCM**.

1. Enter the SCM repo URL and script path using the provided [script example](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

## Build and deploy the Java Function to Azure Functions

It's now time to run the Jenkins job.

1. First, obtain the authorization key via the instructions in the [Azure Functions HTTP triggers and bindings](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys) article.

1. In your browser, enter the app's URL. Replace the placeholders with the appropriate values and specify a numeric value for `<input-number>` as input for the Java function.

    ```
    https://<app-name>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization-key>&number=<input-number>
    ```
1. Depending on you specified an even or odd number for `input-number`, you'll see results similar to the following:

    ```output
    The number 365 is Odd.
    ```

## Clean up resources

If you're not going to continue to use this application, delete
the resources you created with the following step:

```cli
az group delete -y --no-wait -n <resource-group>
```

## Next steps

To learn more about Azure Functions, see the following resource:
> [!div class="nextstepaction"]
> [Next steps button](/azure/azure-functions/)