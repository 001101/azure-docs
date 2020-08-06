---
title: Use ARM template to publish IoT Hub, storage account, route messages 
description: Use ARM template to publish IoT Hub, storage account, route messages 
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/06/2020
ms.author: robinsh
ms.custom: mvc
---
# Quickstart: Deploy an Azure IoT Hub and a storage account using an ARM template

In this quickstart, you use an Azure Resource Manager template (ARM template) to create an IoT Hub that will route messages to Azure Storage, and a storage account to hold them. After manually adding a virtual IoT device to the hub to submit the messages, you configure that connection information in the arm-read-write application to submit messages to the hub using that device. The hub has the routing configured, so the messages sent to the hub are automatically routed by the hub to the storage account. At the end of this quickstart, you can open the storage account and see the messages sent.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## Prerequisites

If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.

## The template

If your environment meets the prerequisites and you're familiar with using ARM templates, select the **Deploy to Azure** button. The template will open in the Azure portal. If you want to deploy to Azure Government, select **Deploy to Azure US GOV**.


[![Deploy To Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

[![Deploy To Azure US Gov](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazuregov.svg?sanitize=true)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

[![Visualize](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)


The template used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages).

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json" :::

Two Azure resources are defined in the template: 
* [Microsoft.Devices/Iothubs](/azure/templates/microsoft.iothubs)
* [Microsoft.Storage/](/azure/templates/microsoft.storage)

## Overview

This section provides the steps 

1. Create the resources by deploying the ARM template.

While the template is being deployed, you can set up the arm-read-write-hub application to run. 

1. Download and unzip the C# IoT samples zip file which is [IoT Samples C#](https://Azure-Samples/azure-iot-samples-csharp).

1. Open a text editor such as notepad and paste the following into it. Then we'll retrieve the missing values from the portal.

```cmd
SET ENV_HUB_URI = <hub-name-goes-here>.azure.devices.net
SET ENV_DEVICE_KEY = "device-key-goes-here"
SET ENV_DEVICE_ID = "Contoso-Test-Device"
```

1. Log into the [Azure portal](http://portal.azure.com]. Select **Resource Groups** then select the resource group created for this quickstart.

**SCREENSHOT** 
   ![Select the resource group](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. You see the IoT Hub and storage account that were created when you deployed the ARM template.

Wait until the template is fully deployed before continuing. Then select your resource group to see your resources.

**SCREENSHOT**
   ![View resources in a resource group](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. Select the hub. Then copy the name of the hub and paste it into <hub-name-goes-here> in the environment variable file.

**SCREENSHOT**
   ![Copy the hub name](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

1. In the IoT Hub pane, look through the properties and select **IOT DEVICES**. 

   ![Select IoT Devices](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. On the right side of the screen, select **+ NEW** to add a new device. 

Fill in the new device name. This quickstart uses a name starting with **Contoso-Test-Device**. Save the device and then open that screen again to retrieve the device key. (The key is generated for you when you close the pane.) Select either the primary or secondary key and copy it to the clipboard, then paste it into the <device-key-goes-here> field in the environment variables file.

1. Set the device id to **Contoso-Test-Device** (unless you changed it in the ARM template). 
 
1. Save the environment variables file, using a file extension of `cmd`. Copy this to the solution folder where the project file arm-read-write.csproj resides. Copy each row and paste it into the command-line in the Command window and select Enter to execute each command. Do this for each of the environment variables. When you're finished, type in the command SET and select Enter -- it shows all the active variables, including the three you just set up.

1. DO NOT CLOSE THE CMD WINDOW. Since the application is a .NET Core application, you can run it without even opening the code. 

1. In the Command window, type the following command.

    `DOTNET RUN ARM-READ-WRITE

The application runs, generating and displaying messages as it sends each one to the IoT Hub. The Iot Hub uses the routing configuration to route the messages to the storage account. Let the app run for 10 to 15 minutes, then press Enter one or twice until it stops running.

** View the results

1. Log in to the (Azure portal)[https://portal.azure.com] and select the Resource Group, then select the Storage account.

1. Drill down into the storage account until you find files.

1. Select one of the files and select Save As and save the file to a location you can find later. It will have a name that's numeric, like 47. Add ".txt" to the end to make it easier to open it.

1. Double-click the file in Windows Explorer to open it. Each row is a different message sent to the IoT Hub. 

** You have created an IoT Hub and a storage account, and run a program to write messages to the hub. The messages are then routed to the storage account. 

## Clean up resources

Go to the [Azure portal](https://portal.azure.com). Select **Resource Groups**, find the resource group you used for this quickstart. Delete the resource group. It will delete all of the resources in the group.

## Next steps

```markdown
> [!div class="nextstepaction"]
> [Tutorial: Create and deploy your first ARM template](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
```