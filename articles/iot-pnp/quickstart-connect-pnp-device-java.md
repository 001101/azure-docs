---
title: Connect IoT Plug and Play Preview sample device code to IoT Hub | Microsoft Docs
description: Using Java, build and run IoT Plug and Play Preview sample device code that connects to an IoT hub. Use the Azure IoT explorer tool to view the information sent by the device to the hub.
author: baanders
ms.author: baanders
ms.date: 11/14/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc

# As a device developer, I want to see a working IoT Plug and Play device sample connecting to IoT Hub and sending properties, commands and telemetry. As a solution developer, I want to use a tool to view the properties, commands, and telemetry an IoT Plug and Play device reports to the IoT hub it connects to.

# ################################INCOMPLETE###############################
# ASSUMPTIONS/QUESTIONS:
# 27, 57, 59, 61, 64     -    Uses Microsoft Azure IoT device SDK for Java (Azure/azure-iot-sdk-java)
#    93                      -    "preview-test" branch
# 31                     -    Version of Java?
# 39-45                  -    Do I need Maven? Version?
# 69-79                  -    How do I build? This section needs to be filled in or replaced.
# 81-89                  -    Where is the project that you run? How do you run it?
#     83, 88                -    takes same parameter as C project
---

# Quickstart: Connect a sample IoT Plug and Play Preview device application to IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

This quickstart shows you how to build a sample IoT Plug and Play device application, connect it to your IoT hub, and use the Azure IoT explorer tool to view the information it sends to the hub. The sample application is written in Java and is included in the Microsoft Azure IoT SDKs for Java. A solution developer can use the Azure IoT explorer tool to understand the capabilities of an IoT Plug and Play device without the need to view any device code.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Prerequisites

To complete this quickstart, you need Java SE 7 on your development machine. You can download Java SE 7 for multiple platforms from [Java long-term support for Azure and Azure Stack](https://docs.microsoft.com/en-us/java/azure/jdk/?view=azure-java-stable), by selecting **Java 7** under **Long-term support** to get downloads for Java 7. Make sure your `PATH` and `JAVA_HOME` environment variables include the full path to the `jdk1.7.x` directory.

You can check that the environment variables are set correctly and verify the version of Java on your development machine by running the following command in a local terminal window: 

```cmd/sh
java -version
```

To build the samples, you also need to install Maven 3. You can download Maven for multiple platforms from [Apache Maven](https://maven.apache.org/download.cgi). Make sure your `PATH` environment variable includes the full path to the `apache-maven-3.x.x\bin` directory.

You can check that the environment variables are set correctly and verify the version of Maven on your development machine by running the following command in a local terminal window:

```cmd/sh
mvn --version
```

These setup steps are also included with the source code of this quickstart's sample device application, under [Java setup instructions](https://github.com/Azure/azure-iot-sdk-java-digital-twin/blob/master/digital-twin/doc/java-devbox-setup.md).

### Install the Azure IoT explorer

Download and install the latest release of **Azure IoT explorer** from the tool's [repository](https://github.com/Azure/azure-iot-explorer/releases) page, by selecting the .msi file under "Assets" for the most recent update.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## Prepare the development environment

In this quickstart, you prepare a development environment you can use to clone and build the Microsoft Azure IoT SDKs for Java.

Open a command prompt in the directory of your choice. Execute the following command to clone the [Microsoft Azure IoT SDKs for Java](https://github.com/Azure/azure-iot-sdk-java-digital-twin) GitHub repository into this location:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-java-digital-twin --recursive 
```

This operation may take several minutes to complete.

## Build the code

You use the device SDK to build the included sample code. The application you build simulates a device that connects to an IoT hub. The application sends telemetry and properties and receives commands.

1. In a local terminal window, navigate to the **/azure-iot-sdk-java-digital-twin/digital-twin** folder. Then run the following command to install the required libraries and build the simulated device application:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure the _device connection string_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<your device connection string>
    ```

## Run the device sample

Run a sample application in the SDK to simulate an IoT Plug and Play device that sends telemetry to your IoT hub. To run the sample application, use the following command:

```cmd\sh
java -jar device-samples\target\environmental-sensor-sample-with-deps.jar
```

The simulated device starts sending telemetry data to IoT Hub, listening for commands, and listening for property updates.

## Use the Azure IoT explorer to validate the code

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. To ensure the tool can read the interface model definitions from your device, select **Settings**. In the Settings menu, **On the connected device** may already appear in the list of locations to check; if it does not, select **+ Add module definition source** and then **On the connected device** to add it.

1. On the device overview page, find the device identity you created previously, and select it to view more details.

1. Expand the interface with ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** to reveal the interface and IoT Plug and Play primitives—properties, commands, and telemetry.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

## Next steps

In this quickstart, you've learned how to connect an IoT Plug and Play device to an IoT hub. To learn more about how to build a solution that interacts with your IoT Plug and Play devices, see:

> [!div class="nextstepaction"]
> [How-to: Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md)
