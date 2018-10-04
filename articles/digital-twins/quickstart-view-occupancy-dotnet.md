---
title: Find available rooms with fresh air with Azure Digital Twins quickstart (C#) | Microsoft Docs
description: In this quickstart, you run two .NET Core sample applications to send simulated motion and carbon dioxide telemetry to a space in Azure Digital Twins. The goal is to find available rooms with fresh air from Management APIs after computed processing in the cloud.
author: alinamstanciu
manager: bertvanhoof
ms.service: azure-digital-twins
services: azure-digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/21/2018
ms.author: alinast
# As a developer new to Digital Twins, I need to see how to send motion and carbon dioxide telemetry to a space in a Azure Digital Twins and how to find available rooms with fresh air using a back-end application. 
---

# Quickstart: Use Azure Digital Twins to find available rooms and monitor air quality (C#)

As an employee in a busy office, it's important to find available rooms that fit your needs where you can be productive. [Research has shown](https://www.wsj.com/articles/why-office-buildings-should-run-like-spaceships-1507467601) that air quality in rooms can have a significant impact on strategic, creative, and collaborative thinking. With Azure Digital Twins, not only can you find available rooms, but you can also find rooms where the air quality will be optimal for your safety and productivity.

This article shows how you can achieve both goals using Azure Digital Twins. The [quickstart code](https://github.com/Azure-Samples/digital-twins-samples-csharp) demonstrates two sample .NET Core console applications that use the Digital Twins APIs. The first one provisions a simple spatial graph in your Digital Twins instance. The second sample sends motion and carbon dioxide telemetry to that spatial graph. Together, these samples will find available rooms with fresh air based on real-time sensor data through Digital Twins.
 
## Prerequisites
1. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

1. The two console applications you run in this quickstart are written using C#. You will need to install [.NET Core SDK](https://www.microsoft.com/net/download) on your development machine.

1. Clone the sample C# projects repo:
    ```bash
    git clone https://github.com/Azure-Samples/digital-twins-samples-csharp.git
    ```

## Create a Digital Twins instance in Azure Portal
Create a new instance of the Digital Twins in the [portal](https://portal.azure.com) using the steps in this section. 

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]

## Grant permissions to the console applications to interact with Digital Twins Management APIs

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## Build application

You can build and run the occupancy application using the outlined steps:
1. Open a command prompt, and navigate to the project you've cloned.
1. Run `cd occupancy-quickstart/src` 
1. Run `dotnet restore`
1. Edit `appsettings.json` to update the following variables to match your Digital Twins instance:
    - `ClientId` with the value from previous step
    - `Tenant` with the value from previous step
    - `BaseUrl`, for example `https://[yourDigitalTwinsName].[yourLocation].azuresmartspaces.net/management/api/v1.0/`
1. Run `dotnet run` to build and see application usage

## Provision graph

This step provisions your Digital Twins spatial graph with several spaces, one device, two sensors, one matcher, one user-defined function and one role assignment.
1. Run `dotnet run ProvisionSample`
    >[!NOTE]
    >We use Device Login Azure CLI tool to authenticate the user to Azure AD. The user needs to enter a given code to authenticate using https://microsoft.com/devicelogin page. After code is entered, follow steps to authenticate. The user is requested to authenticate everytime when the tool is running.
    >The provisioning step might take a minute or so. It will also provision an IoT Hub within your Digital Twins instance.
1. After running this step, copy the `ConnectionString` of the device for use in device simulator sample. See image below:
    ![Provision Sample][3]

## Send sensor telemetry

You can build and run the sensor simulator application using the steps below:
1. Open a new command prompt, and navigate to the project you've cloned.
1. Run `cd device-connectivity` 
1. Run `dotnet restore`
1. Edit `appsettings.json` to update the following variables to match your Digital Twins instance:
    - `DeviceConnectionString` with the `ConnectionString` above.
    - `HardwareId` of the sensors
1. Run `dotnet run` to start sending telemetry, you should see telemetry being sent to Digital Twins service as in the image below:

 ![Device Connectivity][4]

## Find available spaces with fresh air

The sensor telemetry sample is simulating random data values for two sensors, motion, and carbon dioxide. Available spaces with fresh air are defined in our sample by no presence in the room and carbon dioxide level is under 1000 ppm. If the condition is not fulfilled, then the space is not available, or the air quality is poor.

1. Go to the `occupancy-quickstart` command prompt.
1. Run `dotnet run GetAvailableAndFreshSpaces`.
1. Look at the command prompt and the sensor telemetry command prompt side by side as outlined below. It will display one of these conditions in near real-time based on what the sensor telemetry has last sent:
    - Available rooms with fresh air
    - Occupied or poor air quality of the room

 ![Get available spaces with fresh air][5]

## Clean up resources

The tutorials go into detail about how to build an application for facility managers to increase occupant productivity and to operate the building more efficiently.

If you plan to continue to the tutorials, do not clean up the resources created in this Quickstart. If you do not plan to continue, use the following steps to delete all resources created by this Quickstart:

1. Delete the folder that was created when cloning the sample repository.
1. From the left-hand menu in the [Azure portal](http://portal.azure.com), click **All resources** and then select your Digital Twins resource. At the top of the **All resources** blade, click **Delete**.

## Next steps

If you haven't cleaned up the resources, and you wish to learn how to manipulate the simulated sensor data to create custom notifications and analyses, proceed to the following tutorial. 

> [!div class="nextstepaction"]
> [Facility Management Tutorial](tutorial-facilities-events.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/create-digital-twins-portal.png
[2]: media/quickstart-view-occupancy-dotnet/create-digital-twins-param.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[5]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png