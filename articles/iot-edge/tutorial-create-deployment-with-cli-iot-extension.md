---
# Mandatory fields. See more on aka.ms/skyeye/meta.
title: Deploy modules to IoT Edge devices using IoT extension for Azure CLI 2.0 | Microsoft Docs 
description: Deploy modules to an IoT Edge device using IoT extension for Azure CLI 2.0
services: iot-edge
keywords: 
author: chrissie926
manager: timlt

ms.author: menchi
ms.date: 02/05/2018
ms.topic: article
ms.service: iot-edge

ms.custom: mvc
ms.reviewer: kgremban
---

# Deploy modules to an IoT Edge device using IoT extension for Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) is an open source cross platform command line tool for managing Azure resources such as IoT Edge. Azure CLI 2.0 is available on Windows, Linux and MacOS.

Azure CLI 2.0 enables you to manage Azure IoT Hub resources, device provisioning service instances, and linked-hubs out of the box. The new IoT extension enriches Azure CLI 2.0 with features such as device management and full IoT Edge capability.

In this tutorial, you first complete the steps to set up Azure CLI 2.0 and the IoT extension. Then you learn how to deploy modules to an IoT Edge device using the available CLI commands.

## Prerequisites

* An Azure account. If you don't have one yet, you can [create a free account](https://azure.microsoft.com/free/?v=17.39a) today. 

* [Python 2.7x or Python 3.x](https://www.python.org/downloads/).

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) in your environment. At a minimum, your Azure CLI 2.0 version must be 2.0.24 or above. Use `az –-version` to validate. This version supports az extension commands and introduces the Knack command framework. One simple way to install on Windows is to download and install the [MSI](https://aka.ms/InstallAzureCliWindows).

* [The IoT extension for Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Run `az extension add --name azure-cli-iot-ext`. 
   2. After installation, use `az extension list` to validate the currently installed extensions or `az extension show --name azure-cli-iot-ext` to see details about the IoT extension.
   3. To remove the extension, use `az extension remove --name azure-cli-iot-ext`.


## Create an IoT Edge device
This article gives instructions to create an IoT Edge deployment. The example shows you how to sign in to your Azure account, create an Azure Resource Group (a container that holds related resources for an Azure solution), create an IoT Hub, create three IoT Edge devices identity, set tags and then create an IoT Edge deployment that targets those devices. 

Log in to your Azure account. After you enter the following login command, you're prompted to use a web browser to sign in using a one-time code: 

   ```cli
   az login
   ```

Create a new resource group called **IoTHubBlogDemo** in the East US region: 

   ```cli
   az group create -l eastus -n IoTHubBlogDemo
   ```

   ![Create resource group][2]

Create an IoT Hub called **blogDemoHub** in the newly created resource group:

   ```cli
   az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
   ```

   ![Create IoT Hub][3]

Create an IoT Edge device:

   ```cli
   az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled
   ```

   ![Create IoT Edge device][4]

## Configure the IoT Edge device

1. Save your deployment JSON template locally as a txt file. You will need the path to the file when you run the apply-configuration command.

   Below is a sample deployment JSON template which contains one tempSensor module:

   ```json
   {
     "moduleContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": ""
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {},
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

2. Apply the configuration to your IoT Edge device:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<configuration.txt file path>
   ```

   ![Apply configuration][5]

3. View the modules on your IoT Edge device:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub
   ```

   ![List modules][6]

## Next steps

* Learn how to [use an IoT Edge device as a gateway](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

