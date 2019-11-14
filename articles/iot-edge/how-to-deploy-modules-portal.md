---
title: Deploy modules from Azure portal - Azure IoT Edge | Microsoft Docs 
description: Use the Azure portal to deploy modules to an IoT Edge device
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/13/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
---

# Deploy Azure IoT Edge modules from the Azure portal

Once you create IoT Edge modules with your business logic, you want to deploy them to your devices to operate at the edge. If you have multiple modules that work together to collect and process data, you can deploy them all at once and declare the routing rules that connect them.

This article shows how the Azure portal guides you through creating a deployment manifest and pushing the deployment to an IoT Edge device. For information about creating a deployment that targets multiple devices based on their shared tags, see [Deploy and monitor IoT Edge modules at scale](how-to-deploy-monitor.md)

## Prerequisites

* An [IoT hub](../iot-hub/iot-hub-create-through-portal.md) in your Azure subscription.
* An [IoT Edge device](how-to-register-device.md#register-in-the-azure-portal) with the IoT Edge runtime installed.

## Configure a deployment manifest

A deployment manifest is a JSON document that describes which modules to deploy, how data flows between the modules, and desired properties of the module twins. For more information about how deployment manifests work and how to create them, see [Understand how IoT Edge modules can be used, configured, and reused](module-composition.md).

The Azure portal has a wizard that walks you through creating the deployment manifest, instead of building the JSON document manually. It has three steps: **Add modules**, **Specify routes**, and **Review deployment**.

### Select device and add modules

1. Sign in to the [Azure portal](https://portal.azure.com) and navigate to your IoT hub.
1. Select **IoT Edge** from the menu.
1. Click on the ID of the target device from the list of devices.
1. Select **Set Modules**.
1. In the **Container Registry Settings** section of the page, provide the credentials to access any private container registries that contain your module images.
1. In the **IoT Edge Modules** section of the page, select **Add**.
1. Look at the types of modules from the drop-down list:

   * **IoT Edge Module** - the default option.
   * **Marketplace Module** - only modules generated from the Azure Marketplace.
   * **Azure Machine Learning Module** - only model images generated from an Azure Machine Learning workspace.

1. Select the **IoT Edge Module**.
1. For the **IoT Edge Module Name**, specify `SimulatedTemperatureSensor`.
1. On the **Module Settings** tab, for the **Image URI** specify `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
1. Fill out the optional fields if necessary. For more information about container create options, restart policy, and desired status see [EdgeAgent desired properties](module-edgeagent-edgehub.md#edgeagent-desired-properties). For more information about the module twin see [Define or update desired properties](module-composition.md#define-or-update-desired-properties).
1. Select **Create**.
1. If needed, repeat steps 6 through 12 to add additional modules to your deployment.
1. Select **Next: Routes** to continue to the routes section.

### Specify routes

On the **Routes** tab, you define how messages are passed between modules and the IoT Hub. Messages are constructed using name/value pairs. By default a route called **route** and defined as **FROM /messages/* INTO $upstream**, which means that any messages output by any modules are sent to your IoT hub.  

Add or update the routes with information from [Declare routes](module-composition.md#declare-routes), then select **Next: Review + create** to continue to the next step of the wizard.

### Review deployment

The review section shows you the JSON deployment manifest that was created based on your selections in the previous two sections. Note that there are two modules declared that you didn't add: **$edgeAgent** and **$edgeHub**. These two modules make up the [IoT Edge runtime](iot-edge-runtime.md) and are required defaults in every deployment.

Review your deployment information, then select **Create**.

## View modules on your device

Once you've deployed modules to your device, you can view all of them in the device details page of your Iot Hub. This page displays the name of each deployed module, as well as useful information like the deployment status and exit code.

## Deploy modules from Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) is an online applications and services marketplace where you can browse through a wide range of enterprise applications and solutions that are certified and optimized to run on Azure, including [IoT Edge modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Find a module and select **Get it now**.

Azure Marketplace can also be accessed through the Azure portal under **Create a Resource**. Select **Internet of Things** under **Azure Marketplace** and select **See all** to all the IoT Edge offerings that includes the **Iot Edge Modules section**.  Find a module and select **Create**.

After you have selected a IoT-Module from the Azure Marketplace, proceed with the wizard steps which may vary depending on the module you selected:

1. Acknowledge the provider's terms of use and privacy policy by selecting **Continue**. You may first have to provide contact information.
1. Choose your subscription and the IoT Hub to which the target device is attached.
1. Choose **Deploy to a device**.
1. Enter the name of the device or select **Find Device** to browse among the devices registered with the hub.
1. Select **Create** to continue the standard process of configuring a deployment manifest, including adding other modules if desired. Details for the new module such as image URI, create options, and desired properties are predefined but can be changed.

You can also quickly deploy a module from the Azure Marketplace onto your IoT Edge device in your Iot Hub in the Azure portal. Select your device, select **Set Modules on Device**, and in the **Iot Edge Modules** section click the **Add** dropdown and select **Marketplace Module** as shown here.

 ![Add module in Iot Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

The selected module is immediately configured for your subscription, resource group, and the currently selected device and appears in your list of IoT Edge modules. Some modules may require additional configuration.

## Next steps

Learn how to [Deploy and monitor IoT Edge modules at scale](how-to-deploy-monitor.md)
