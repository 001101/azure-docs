---
title: Update device firmware through Azure IoT Hub | Microsoft Docs
description: Implement a device firmware update process using jobs and device twins.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt


ms.assetid: 
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2018
ms.author: dobett
ms.custom: mvc
#Customer intent: As a developer, I want to implement a device firmware update process that can be triggered from a back-end application connected to my IoT hub
---

<!-- **TODO** Update publish config with repo paths before publishing! -->

# Tutorial: Implement a device firmware update process

You may need to update the firmware on the devices connected to your IoT hub. For example, you might want to add new features to the firmware or apply security patches. In many IoT scenarios, it's impractical to physically visit and then manually apply firmware updates to your devices. This tutorial shows how you can start and monitor the firmware update process remotely through a back-end application connected to your hub.

To begin the firmware update process, this tutorial submits a _job_ in a back-end application that updates a set of _device twin desired properties_ on all your chiller devices. The desired properties specify the details of the firmware update that's required. While the device is running the firmware update, it reports its status to the back-end application using _device twin reported properties_. The back-end application can monitor the reported properties sent from the device to track the firmware update process to completion:

![Firmware update process](media/tutorial-firmware-update/Process.png)

In this tutorial, you complete the following tasks:

> [!div class="checklist"]
> * Create an IoT hub and add a test device to the device identity registry.
> * Create a job to initiate the firmware update process on the test device.
> * Simulate the firmware update process on a device.
> * Receive status updates from the device as the firmware update progresses.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you don’t have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

The two sample applications you run in this quickstart are written using Node.js. You need Node.js v4.x.x or later on your development machine.

You can download Node.js for multiple platforms from [nodejs.org](https://nodejs.org).

You can verify the current version of Node.js on your development machine using the following command:

```cmd/sh
node --version
```

Download the sample Node.js project from https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip and extract the ZIP archive.

## Set up Azure resources

To complete this tutorial, your Azure subscription must have an IoT hub with a device added to the device identity registry. The entry in the device identity registry enables the simulated device you run in this tutorial to connect to your hub.

If you don't already have an IoT hub set up in your subscription, you can set one up with following CLI script. This script uses the name **tutorial-iot-hub** for the IoT hub, you should replace this name with your own unique name when you run it. The script creates the resource group and hub in the **Central US** region, which you can change to a region closer to you. The script retrieves your IoT hub service connection string, which you use in the back-end sample application to connect to your IoT hub:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-cli-iot-ext

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku S1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --hub-name $hub-name -o table

```

This tutorial uses a simulated device called **MyFirmwareUpdateDevice**. The following script adds this device to your device identity registry, sets a tag value, and retrieves its connection string:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## Start the firmware update

You submit a job in the back-end application to begin the firmware update process on all devices tagged with a **devicetype** of chiller. In this section, you see how to:

* Submit a job from a back-end application.
* Monitor the job to completion.

### Use desired properties to start the firmware upgrade from the back-end application

To view the back-end application code that submits the job, navigate to the **iot-hub/Tutorials/FirmwareUpdate** folder in the sample Node.js project you downloaded. Then open the ServiceClient.js file in a text editor.

The back-end application sends the following the desired properties to all the devices to update:

[!code-javascript[Desired properties patch](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=firmwarePatch "Desired properties patch")]

The back-end application uses the following query to select the devices to update:

[!code-javascript[Select devices](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=queryCondition "Select devices")]

The back-end application uses the following code to submit the job to set the desired properties:

[!code-javascript[Submit job](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=scheduleJob "Submit job")]

After it submits the job, the application monitors the job to completion:

[!code-javascript[Monitor job](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorJob "Monitor job")]

### Respond to the firmware upgrade request on the device

To view the simulated device code that handles the firmware desired properties sent from the back-end application, navigate to the **iot-hub/Tutorials/FirmwareUpdate** folder in the sample Node.js project you downloaded. Then open the SimulatedDevice.js file in a text editor.

The simulated device application creates a handler for updates to the **properties.desired.firmware** desired properties in the device twin. In the handler, it carries out some basic checks on the desired properties before launching the update process:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## Update the firmware

The **initiateFirmwareUpdateFlow** function runs the update. This function uses the **waterfall** function to run the phases of the update process in sequence. In this example, the firmware update has four phases. The first phase downloads the image, the second phase verifies the image using a checksum, the third phase applies the image, and the last phase reboots the device:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

During the update process, the simulated device reports on progress using reported properties:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

The following snippet shows the implementation of the download phase. During the download phase, the simulated device uses reported properties to send status information to the back-end application:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

The simulated device uses reported properties to send status updates to the back-end application during the firmware update process. The back-end application runs a query every five seconds to view the firmware reported properties sent by the devices that are updating their firmware:

[!code-javascript[Query reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=queryTwins "Query reported properties")]

## Run the sample

In this section, you run two sample applications to observe as a back-end application submits the job to trigger the firmware update process on the simulated device.

To run the simulated device and back-end applications, you need the device and service connection strings. You made a note of the connection strings when you created the resources at the start of this tutorial.

To run the simulated device application, open a shell or command prompt window and navigate to the **iot-hub/Tutorials/FirmwareUpdate** folder in the Node.js project you downloaded. Then run the following commands:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

To run the back-end application, open another shell or command prompt window. Then navigate to the **iot-hub/Tutorials/FirmwareUpdate** folder in the Node.js project you downloaded. Then run the following commands:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

The following screenshot shows the output from the simulated device application and showing how it responds to the firmware desired properties update from the back-end application:

![Simulated device](./media/tutorial-firmware-update/SimulatedDevice.png)

The following screenshot shows the output from the back-end application and highlights how it creates the job to update the firmware desired properties:

![Back-end application](./media/tutorial-firmware-update/BackEnd1.png)

The following screenshot shows the output from the back-end application and highlights how it queries for status updates from the simulated device:

![Back-end application](./media/tutorial-firmware-update/BackEnd2.png)

Because of latency in the IoT Hub device identity registry, you may not see every status update sent to the back-end application.

## Clean up resources

If you plan to complete the next tutorial, leave the resource group and IoT hub and reuse them later.

If you don't need the IoT hub any longer, delete it and the resource group in the portal. To do so, select the **tutorial-iot-hub-rg** resource group that contains your IoT hub and click **Delete**.

Alternatively, use the CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## Next steps

In this tutorial, you learned how to synchronize state information between your devices and your IoT hub by performing the following tasks:

> [!div class="checklist"]
> * Create an IoT hub and add a test device to the identity registry.
> * Use desired properties to send state information to your simulated device.
> * Use reported properties to receive state information from your simulated device.

Advance to the next tutorial to learn how to use device twins to implement a firmware update process.

> [!div class="nextstepaction"]
[Use device management to initiate a device firmware update](iot-hub-node-node-firmware-update.md)
