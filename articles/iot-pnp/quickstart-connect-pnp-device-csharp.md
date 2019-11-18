---
title: Connect IoT Plug and Play Preview sample device code to IoT Hub | Microsoft Docs
description: Using C# (.NET), build and run IoT Plug and Play Preview sample device code that connects to an IoT hub. Use the Azure IoT explorer tool to view the information sent by the device to the hub.
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
# 26, 48, 50, 52, 55     -    Uses Microsoft Azure IoT device SDK for C# (Azure/azure-iot-sdk-csharp)
#    55                      -    "preview" branch
# 30                     -    Should I give a minimum version of .NET?
# 60-80                  -    How do I build? This section needs to be replaced.
# 86-90                  -    Where is the project that you run? How do you run it?
#     84, 89                 -    takes same parameter as C project
---

# Quickstart: Connect a sample IoT Plug and Play Preview device application to IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

This quickstart shows you how to build a sample IoT Plug and Play device application, connect it to your IoT hub, and use the Azure IoT explorer tool to view the information it sends to the hub. The sample application is written in C# (with .NET), and is included in the Microsoft Azure IoT SDK for .NET. A solution developer can use the Azure IoT explorer tool to understand the capabilities of an IoT Plug and Play device without the need to view any device code.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Prerequisites

To complete this quickstart, you need to install .NET Core 2.2 on your development machine. You can download this version of the .NET Core SDK for multiple platforms from [Download .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2).

You can verify the version of .NET that's on your development machine by running the following command in a local terminal window: 

```cmd/sh
dotnet --version
```

### Install the Azure IoT explorer

Download and install the latest release of **Azure IoT explorer** from the tool's [repository](https://github.com/Azure/azure-iot-explorer/releases) page, by selecting the .msi file under "Assets" for the most recent update.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## Prepare the development environment

In this quickstart, you prepare a development environment you can use to clone and build the Microsoft Azure IoT SDK for .NET.

Open a command prompt in the directory of your choice. Execute the following command to clone the [Microsoft Azure IoT SDK for .NET](https://github.com/Azure/azure-iot-sdk-csharp-digital-twin) GitHub repository into this location:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-csharp-digital-twin --recursive
```

This operation may take several minutes to complete.

## Build the code

You use the device SDK to build the included sample code. The application you build simulates a device that connects to an IoT hub. The application sends telemetry and properties and receives commands.

1. In a local terminal window, navigate to the **/azure-iot-sdk-csharp-digital-twin/digitaltwin/device/sample/EnvironmentalSensorSample** folder. 

1. Configure the _device connection string_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## Run the device sample

Run a sample application in the SDK to simulate an IoT Plug and Play device that sends telemetry to your IoT hub. To build the necessary packages and run the sample application, use the following command:

```cmd\sh
    dotnet run
```

The simulated device starts sending telemetry data to IoT Hub, listening for commands, and listening for property updates.

## Use the Azure IoT explorer to validate the code

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. To ensure the tool can read the interface model definitions from your device, select **Settings**. In the Settings menu, **On the connected device** may already appear in the Plug and Play configurations; if it does not, select **+ Add module definition source** and then **On the connected device** to add it.

1. On the device overview page, find the device identity you created previously, and select it to view more details.

1. Expand the interface with ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** to reveal the interface and IoT Plug and Play primitives—properties, commands, and telemetry.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## Next steps

In this quickstart, you've learned how to connect an IoT Plug and Play device to an IoT hub. To learn more about how to build a solution that interacts with your IoT Plug and Play devices, see:

> [!div class="nextstepaction"]
> [How-to: Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md)
