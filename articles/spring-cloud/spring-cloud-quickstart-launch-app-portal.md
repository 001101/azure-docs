---
title: Launch a Spring application in Azure Spring Cloud using the Azure portal | Microsoft Docs
description: In this quickstart, you deploy a sample application to the Azure Spring Cloud on the Azure portal.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''

ms.service: spring-cloud
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: v-vasuke

---
# Quickstart: Launch a Spring Cloud application in Azure Spring Cloud using the Azure portal

Azure Spring Cloud enables you to easily run Spring Cloud based microservice applications on Azure.

This quickstart shows you how to deploy an existing Spring Cloud application to Azure. [Here is a link](https://github.com/xscript/PiggyMetrics) to the sample application code used in this tutorial. When you're finished, the provided sample application will be accessible on the web and ready to be managed via the Azure portal.

## Prerequisites

To complete this quickstart:

- [Install Git](https://git-scm.com/)
- [Install JDK 8](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)
- [Install Maven 3.0 or above](https://maven.apache.org/download.cgi)
- [Sign up for an Azure subscription](https://azure.microsoft.com/free/)
- [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
	

## Install Azure CLI extension

Run below command to install Azure CLI extension.

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## Provision a service instance on the Azure portal

1. In a web browser, open the [Azure portal](https://portal.azure.com), and sign into your account.

1. Search for the **Azure Spring Cloud** and click on it to go to the overview page. Click the **Create** button to get started.

1. Fill out the form, considering the following guidelines:
    - Service Name: Specify the name of your service instance.
    - Subscription: Select the subscription you want to be billed for this resource.
    - Resource group: Creating new resource groups for new resources is generally considered the best practice.
    - Location: Location of your service instance. East US, West US 2, West Europe, and Southeast Asia are supported at this time.
    
Normally, the service instance takes about 5 minutes to be fully deployed. When it is deployed, you will be able to see the **Overview** page for the service instance.

## Set up your configuration server

1. Go to the service **Overview** page and click **Config Server** entry in the menu.

1. In the **Default repository** section, set **URI** to "https://github.com/Azure-Samples/piggymetrics". Then set **LABEL** to "config". Finally, click **Apply** to save your changes.


## Build and deploy microservice applications

1. Open up a command window and run the following command to clone the sample app repository to your local machine.
    ```
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Change directory and build the project by running below command.
    ```
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

1. Login to Azure CLI and set your active subscription.
    ```
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
    ```

1. Configure your default resource group name and cluster name by running below commands. Be sure to substitute the placeholders below with the resource group name and service name that you provisioned earlier in this tutorial.
    ```
    az configure --defaults group=<resource group name>
    az configure --defaults asc=<service instance name>
    ```

1. Run below command to create application `gateway` and deploy the JAR file.
    ```
    az asc app create -n gateway
    az asc app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Run below command to create applications `account-service` and `auth-service` and deploy the JAR files.
    ```
    az asc app create -n account-service
    az asc app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az asc app create -n auth-service
    az asc app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. It takes a few minutes to finish deploying above applications. To confirm that they have deployed, go to **Application Dashboard** in the Azure portal. You should see a line for all three apps, as below.

## Assign public endpoint to gateway
1.	Open **Application Dashboard** page.
2.	Click `gateway` application to show the **Application Details** page.
3.	Click **Assign Domain** to assign a public endpoint to gateway. This can take up to a couple minutes. You can use this public IP to access your app.
4. Use Piggy Metrics by entering the assigned public IP into an internet browser.


## Next steps

In this quickstart, you learned how to:

> [!div class="checklist"]
> * Provision a service instance
> * Set a configuration server for an instance
> * Build a microservices application locally
> * Deploy each microservice
> * Edit environment variables for applications
> * Assign public IP for your application gateway

