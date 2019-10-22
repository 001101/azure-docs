---
title: 'Tutorial: Use Java with Azure Functions to update a Cosmos DB in response to Event Hub events'
description: This tutorial shows you how to consume Event Hub events and produce Cosmos DB updates using an Azure Function written in Java
author: KarlErickson
manager: barbkess
ms.service: azure-functions
ms.devlang: java
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: karler
---

# Tutorial: Create an Azure function in Java with an Event Hub trigger and Cosmos DB output binding

<!-- TODO figure out exact brand/usage requirements for event hub(s) -->

This tutorial shows you how to create an Azure Function in Java that is triggered by Event Hub events representing temperature and pressure inputs. The function responds to the event data by adding status entries to a Cosmos DB.

<!-- In this tutorial, you learn to: 
 -->
<!-- TODO update this list -->

<!-- > [!div class="checklist"]
> * Create Azure resources an Event Hub, Cosmos DB, and storage account 
> * Import a custom TensorFlow machine learning model into a function app
> * Build a serverless HTTP API for predicting whether a photo contains a dog or a cat
> * Consume the API from a web application

![Screenshot of finished project](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<!-- [!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] -->

## Prerequisites

* [Java Developer Kit](https://aka.ms/azure-jdks), version 8
* [Maven](https://maven.apache.org)
* [Azure CLI](/cli/azure/install-azure-cli)
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)

<!-- TODO include info on Azure CLI and Cloud Shell if needed -->

## Create Azure resources

In this topic, you'll need these resources:

* A resource group to contain the other resources.
* An Event Hubs namespace, event hub, and authorization rule.
* A Cosmos DB account, database, and collection.
* A function app and a storage account to host it.

The following sections show you how to create these resources using the Azure CLI.

### Log in to Azure

First, you'll need to access your account. You should also set the default subscription if you have access to more than one. Open a Bash prompt and run the following commands:

```azurecli
az login
az account set --subscription <the ID for the existing Azure subscription to use>
```

### Set environment variables

Next, create some environment variables for the info needed to create your resources. Use the following command, replacing the `<value>` placeholders with values of your choosing. For the `LOCATION` variable, use one of the values produced by the [az functionapp list-consumption-locations](/cli/azure/functionapp?view=azure-cli-latest#az-functionapp-list-consumption-locations) command.

```azurecli
export RESOURCE_GROUP=<value>
export EVENT_HUB_NAMESPACE=<value>
export EVENT_HUB_NAME=<value>
export EVENT_HUB_AUTHORIZATION_RULE=<value>
export COSMOS_DB_ACCOUNT=<value>
export STORAGE_ACCOUNT=<value>
export FUNCTION_APP=<value>
export LOCATION=<value>
```

### Create a resource group

Azure uses resource groups to collect all related resources in your account. That way, you can view them as a unit and delete them with a single command when you are done with them. Use the following command to create a group:

```azurecli
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### Create an event hub

Next, create an Azure Event Hubs namespace, event hub, and authorization rule using the following commands:

```azurecli
az eventhubs namespace create \
    --name $EVENT_HUB_NAMESPACE \
    --resource-group $RESOURCE_GROUP
az eventhubs eventhub create \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --resource-group $RESOURCE_GROUP \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --resource-group $RESOURCE_GROUP \
    --rights Listen Send
```

The Event Hubs namespace contains the actual event hub and its authorization rule. The authorization rule enables your functions to send messages to the hub and listen for the corresponding events. One function will send messages representing telemetry data, and another function will listen for events in order to analyze the data and store the results in a Cosmos DB.

### Create a Cosmos DB

Next, create an Azure Cosmos DB account, database, and collection using the following commands:

```azurecli
az cosmosdb create \
    --name $COSMOS_DB_ACCOUNT \
    --resource-group $RESOURCE_GROUP
az cosmosdb database create \
    --db-name TelemetryDb \
    --name $COSMOS_DB_ACCOUNT \
    --resource-group-name $RESOURCE_GROUP
az cosmosdb collection create \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --name $COSMOS_DB_ACCOUNT \
    --partition-key-path '/temperatureStatus' \
    --resource-group-name $RESOURCE_GROUP
```

<!-- TODO say something about partition keys -->

### Create a storage account and function app

Next, create a storage account to host your Azure Functions app, then create the function app. Use the following commands:

```azurecli
az storage account create \
    --name $STORAGE_ACCOUNT \
    --resource-group $RESOURCE_GROUP \
    --sku Standard_LRS
az functionapp create \
    --name $FUNCTION_APP \
    --resource-group $RESOURCE_GROUP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

## Configure your function app

Your function app will need to access the other resources in order to work correctly. The following sections use a combination of Bash, Azure CLI, and Azure Functions Core Tools commands to configure your function app and enable you to test it on your local machine.

### Retrieve resource connection strings

Use the following commands to retrieve the storage, event hub, and Cosmos DB connection strings and save them in environment variables:

```azurecli
export AZURE_WEB_JOBS_STORAGE=` \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv`
export EVENT_HUB_CONNECTION_STRING=` \
    az eventhubs eventhub authorization-rule keys list \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --resource-group $RESOURCE_GROUP \
        --query primaryConnectionString \
        --output tsv`
export COSMOS_DB_CONNECTION_STRING=` \
    az cosmosdb keys list \
        --name $COSMOS_DB_ACCOUNT \
        --resource-group $RESOURCE_GROUP \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv`
```

Each variable is set to a value retrieved using an Azure CLI command with a JMESPath query. Each query extracts the connection string from the JSON payload returned by the CLI command.

### Update your function app settings

Next, use the following command to transfer the connection string values to app settings in your Azure Functions account:

```azurecli
az functionapp config appsettings set \
    --name $FUNCTION_APP \
    --resource-group $RESOURCE_GROUP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Your Azure resources have now been created and configured to work properly together.

## Create and test your functions

Next, you will create a project on your local machine, add Java code, and test it using Maven and the Azure Functions Core Tools. Your functions will run locally, but will use the cloud-based resources you've created. After you get the functions working locally, you can deploy them to the cloud and watch your data and analytics accumulate.

### Create a local functions project

Use the following Maven command to create a functions project and add the required dependencies.

<!-- TODO update to use batch mode instead of interactive mode -->

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

This command generates several files inside a `telemetry-functions` folder:

* A `pom.xml` file for use with Maven.
* A `local.settings.json` file to hold app settings for local testing.
* A `host.json` file that enables the [Azure Functions Extension Bundle](/azure/azure-functions/functions-bindings-register#extension-bundles), required for Cosmos DB output binding in your data analysis function.
* A `Function.java` file that includes a default function implementation.
* A few test files that this topic doesn't need.

To avoid compilation errors, you will need to delete the test files. Run the following commands to navigate to the new project folder and delete the test folder:

```bash
cd telemetry-functions
rm -r src/test
```

### Retrieve your function app settings for local use

For local testing, your function project will need the connection strings that you added to your Azure Functions account earlier in this topic. Use the following Azure Functions Core Tools command to retrieve all the function app settings stored in the cloud, and add them to your `local.settings.json` file:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### Add Java code

Next, open the `Function.java` file and replace the contents with the following code. Replace `<event hub name>` with the name of the Event Hub you created inside your Event Hub namespace.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;
import com.example.TelemetryItem.status;
import java.time.*;

public class Function {

    @FunctionName("EventHubOutput-Java")
    @EventHubOutput(
        name = "event",
        eventHubName = "<event hub name>",
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: " + LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("EventHubTrigger-Java")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "<event hub name>",
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

As you can see, this file contains two functions, `generateSensorData` and `processSensorData`. The `generateSensorData` function simulates a sensor that sends temperature and pressure readings to the event hub. A timer trigger runs the function every 10 seconds, and an event hub output binding sends the return value to the event hub. When the event hub receives the message, it generates an event. The `processSensorData` function runs when it receives the event. It then processes the event data and uses a Cosmos DB output binding to send the results to Cosmos DB.

The data used by these functions is stored using a class called `TelemetryItem`, so you'll need an implementation of that. Create a new file called `TelemetryItem.java` in the same location as `Function.java` and add the following code:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature=" + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

## Run locally

You can now build and run the functions locally and see data appear in your Cosmos DB.

Use the following Maven commands to build and run the functions:

```bash
mvn clean package
mvn azure-functions:run
```

<!-- 
Cosmos DB account > **Data Explorer** > **TelemetryDb** > **TelemetryInfo** > **Items**

example:
{
    "id": "TEST222",
    "temperature": 28.8,
    "pressure": 38.8,
    "isNormalPressure": false,
    "temperatureStatus": "COOL",
    "_rid": "wIR4AK5lQHQHAAAAAAAAAA==",
    "_self": "dbs/wIR4AA==/colls/wIR4AK5lQHQ=/docs/wIR4AK5lQHQHAAAAAAAAAA==/",
    "_etag": "\"1a007499-0000-0700-0000-5d9d380b0000\"",
    "_attachments": "attachments/",
    "_ts": 1570584587
}
or screenshot -->

## Deploy to Azure

TBD

<!-- In VSCode command pallet, do >Azure Functions: Upload Local Settings. Follow prompts – Select your function app in Azure. Go to the portal -> Function App -> and in the Overview tab, under Configured Features, click Configuration. to make sure all your settings are uploaded correctly.  
Then in the command palette do >Azure Functions: Deploy to Function App and select your function app. Once deployment is successful, you should be able to in your portal, see function.json in your app.  -->

## Test in Azure

TBD

<!-- Follow the same steps that you used to test locally. This time your function app is running in Azure. Based on the function app logic, you can go to the cosmos db created in your resource group to see items being creates (as we used output binding).  -->

## Clean up resources

When you are finished with the Azure resources you created in this topic, you can delete them using the following command:

```azurecli
az group delete --name $RESOURCE_GROUP
```

## Next steps

In this tutorial, you learned how to ... TBD

<!-- build and customize an HTTP API with Azure Functions to make predictions using a TensorFlow model. You also learned how to call the API from a web application.

You can use the techniques in this tutorial to build out APIs of any complexity, all while running on the serverless compute model provided by Azure Functions.

To deploy the function app to Azure, use the [Azure Functions Core Tools](./functions-run-local.md#publish) or [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).

> [!div class="nextstepaction"]
> [Azure Functions Python Developer Guide](./functions-reference-python.md)


Advance to the next article to learn how to create...
> [!div class="nextstepaction"]
> [Next steps button](contribute-get-started-mvc.md) -->
