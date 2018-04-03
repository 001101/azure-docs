---
title: Send telemetry to Azure IoT Hub quickstart (Java) | Microsoft Docs
description: In this quickstart, you run two sample Java applications to send simulated telemetry to an IoT hub and to read telemetry from from the IoT hub for processing in the cloud.
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/02/2018
ms.author: dobett

# As a developer new to IoT Hub, I need to see how IoT Hub sends telemetry from a device to an IoT hub and how to read that telemetry data from the hub using a back-end application. 
---

# Quickstart: Send telemetry from a device to an IoT hub and read the telemetry from the hub with a back-end application (Java)

IoT Hub is an Azure service that enables you to ingest high volumes of telemetry from your IoT devices into the cloud for storage or processing. In this quickstart, you send telemetry from a simulated device application, through IoT Hub, to a back-end application for processing.

The quickstart uses two pre-written Java applications, one to send the telemetry and one to read the telemetry from the hub. Before you run these two applications, you create an IoT hub and register a device with the hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you don’t have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

The two sample applications you run in this quickstart are written using Java. You need Java SE 8 or later on your development machine.

You can download Java for multiple platforms from [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

You can verify the current version of Java on your development machine using the following command:

```cmd/sh
java --version
```

To build the samples, you need to install Maven 3. You can download Maven for multiple platforms from [Apache Maven](https://maven.apache.org/download.cgi).

You can verify the current version of Maven on your development machine using the following command:

```cmd/sh
mvn --version
```

Download the sample Java project from https://github.com/Azure-Samples/iot-hub-quickstarts-java/archive/master.zip and extract the ZIP archive.

## Create an IoT hub

The first step is to use the Azure portal to create an IoT hub in your subscription. The IoT hub enables you to ingest high volumes of telemetry into the cloud from many devices. The hub then enables one or more back-end services running in the cloud to read and process that telemetry.

1. Sign in to the [Azure portal](http://portal.azure.com).

1. Select **Create a resource** > **Internet of Things** > **IoT Hub**.

    ![Select to install IoT Hub](media/quickstart-send-telemetry-java/selectiothub.png)

1. To create your IoT hub, use the values in the following table:

    | Setting | Value |
    | ------- | ----- |
    | Name | The following screenshot uses the name **qs-iot-hub**. You must choose your own unique name when you complete this step. |
    | Pricing and scale tier | F1 Free |
    | IoT Hub units | 1 |
    | Device-to-cloud partitions | 2 partitions |
    | Subscription | Select your Azure subscription in the drop-down. |
    | Resource group | Create new. This quickstart uses the name **qs-iot-hub-rg**. |
    | Location | This quickstart uses **West US**. You can choose the location closest to you. |
    | Pin to dashboard | Yes |

    ![Hub settings](media/quickstart-send-telemetry-java/hubdefinition.png)

1. Click **Create**. It can take several minutes for the hub to be created.

1. Make a note of the IoT hub name you chose. You use this value later in the quickstart.

## Register a device

A device must be registered with your IoT hub before it can connect. In this quickstart, you use the Azure CLI to register a simulated device.

1. Add the IoT Hub CLI extension and create the device identity. Replace `{YourIoTHubName}` with the name you chose for your IoT hub:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName}--device-id MyJavaDevice
    ```

1. Run the following command to get the _device connection string_ for the device you just registered:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Make a note of the device connection string, which looks like `Hostname=...=`. You use this value later in the quickstart.

1. You also need the _Event Hubs-compatible endpoint_, _Event Hubs-compatible path_, and _iothubowner primary key_ from your IoT hub to enable the back-end application to connect to your IoT hub and retrieve the messages. The following commands retrieve these values for your IoT hub:

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name iothubowner --query primaryKey --hub-name {your IoT Hub name}
    ```

    Make a note of these three values, which you use later in the quickstart.

## Send simulated telemetry

The simulated device application connects to a device-specific endpoint on your IoT hub and sends simulated temperature and humidity telemetry.

1. In a terminal window, navigate to the root folder of the sample Java project. Then navigate to the **simulated-device** folder.

1. Open the **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** file in a text editor of your choice.

    Replace the value of the `connString` variable with the device connection string you made a note of previously. Then save your changes to **SimulatedDevice.java** file.

1. In the terminal window, run the following commands to install the required libraries and build the simulated device application:

    ```cmd/sh
    mvn clean package
    ```

1. In the terminal window, run the following commands to run the simulated device application:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    The following screenshot shows the output as the simulated device application sends telemetry to your IoT hub:

    ![Run the simulated device](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## Read the telemetry from your hub

The back-end application connects to the service-side **Events** endpoint on your IoT Hub. The application receives the device-to-cloud messages sent from your simulated device. An IoT Hub back-end application typically runs in the cloud to receive and process device-to-cloud messages.

1. In another terminal window, navigate to the root folder of the sample Java project. Then navigate to the **read-d2c-messages** folder.

1. Open the **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** file in a text editor of your choice.

    Replace the value of the `eventHubsCompatibleEndpoint` variable with the Event Hubs-compatible endpoint you made a note of previously.

    Replace the value of the `eventHubsCompatiblePath` variable with the Event Hubs-compatible path you made a note of previously.

    Replace the value of the `iotHubSasKey` variable with the iothubowner primary key you made a note of previously. Then save your changes to the **ReadDeviceToCloudMessages.java** file.

1. In the terminal window, run the following commands to install the required libraries and build the back-end application:

    ```cmd/sh
    mvn clean package
    ```

1. In the terminal window, run the following commands to run the simulated device application:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    The following screenshot shows the output as the back-end application receives telemetry sent by the simulated device to the hub:

    ![Run the back-end application](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## Clean up resources

If you plan to complete the next quickstart, leave the resource group and IoT hub and reuse them later.

If you don't need the IoT hub any longer, delete it and the resource group in the portal. To do so, select the **qs-iot-hub-rg** resource group that contains your IoT hub and click **Delete**.

## Next steps

In this quickstart, you've setup an IoT hub, registered a device, sent simulated telemetry to the hub using a Java application, and read the telemetry from the hub using a simple back-end app.

To learn how to control your simulated device from a back-end app, continue to the next quickstart.

> [!div class="nextstepaction"]
> [Quickstart: Control a device connected to an IoT hub](quickstart-control-device-java.md)