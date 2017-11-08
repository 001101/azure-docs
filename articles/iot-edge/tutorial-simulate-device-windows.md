---
title: Install Azure IoT Edge - Windows | Microsoft Docs 
description: Install the Azure IoT Edge runtime on a simulated device in Windows and deploy your first module
services: iot-edge
keywords: 
author: kgremban
manager: timlt

ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge

---

# Deploy Azure IoT Edge on a simulated device in Windows

Azure IoT Edge moves the power of the cloud to your Internet of Things (IoT) devices. This tutorial walks you through creating a simulated IoT Edge device that generates sensor data. You learn how to:

> [!div class="checklist"]
> * Create an IoT hub
> * Register an IoT Edge device
> * Start the IoT Edge runtime
> * Deploy a module
> * View generated data

The simulated device that you create in this tutorial is a monitor on a wind turbine that generates temperature, humidity, and pressure data. You're interested in this data because your turbines perform at different levels of efficiency depending on the weather conditions. The other Azure IoT Edge tutorials build upon the work you do here by deploying modules that analyze the data for business insights. 

## Prerequisites

This tutorial assumes that you're using a computer or virtual machine running Windows to simulate an Internet of Things device. 

>[!TIP]
>If you're running Windows in a virtual machine, enable [nested virtualization][lnk-nested] and allocate at least 2GB memory. 

Docker for Windows can run either Windows containers or Linux containers. The steps for the tutorial are the same regardless of which container type you use, but the software prerequisites are different.

### Windows containers in Docker

- Supported Windows versions:
   * Windows IoT Core (Build 16299) on a x64-based device
   * Windows 10 Fall Creators Update
   * Windows Server 1709 (Build 16299)
- Run the following command in an Admin PowerShell console to install and configure the prerequisites:

   ```powershell
   iex (iwr -useb https://aka.ms/iotedgewin)
   ```

   This script provides the following:
   * Docker, configured to use Windows containers
   * Python 2.7
   * The IoT Edge control tool (iotedgectl.exe)

### Linux containers in Docker

- Supported Windows versions:
   * Windows 10 
   * Windows Server 1709
- Install [Docker for Windows][lnk-docker-windows] and make sure it's running.
- Install [Python 2.7 on Windows][lnk-python-windows] and make sure you can use the pip command.

> [!NOTE]
> During bug bash: Instead of step 1, run the following code:
>
>
> Download [scripts](https://azureiotedgepreview.blob.core.windows.net/shared/azure-iot-edge-ctl.zip)
>
> Extract all
>
> Open cmd in extracted dir
>
> `pip install -U .[win32]`

- Run the following command to download the IoT Edge control tool.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

## Create an IoT hub

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## Register an IoT Edge device

Create a device identity for your simulated device so that it can communicate with your IoT hub. Since IoT Edge devices behave and can be managed differently than typical IoT devices, you declare this to be an IoT Edge device from the beginning. 

1. In the Azure portal, navigate to your IoT hub.
1. Select **IoT Edge Explorer**.
1. Select **Add Edge device**.
1. Give your simulated device a unique device ID.
1. Confirm that the value of **Azure IoT Edge device** is set to **Yes**.
1. Select **Save** to add your device.
1. Select your new device from the list of devices. 
1. Copy the value for **Connection string--primary key** and save it. You'll use this value to configure the IoT Edge runtime in the next section. 

## Configure the IoT Edge runtime

The IoT Edge runtime is deployed on all IoT Edge devices. It comprises two modules. First, the IoT Edge agent facilitates deployment and monitoring of modules on the IoT Edge device. Second, the IoT Edge hub manages communications between modules on the IoT Edge device, and between the device and IoT Hub. 


Use the following steps to install and start the IoT Edge runtime:

1. Configure the runtime with your IoT Edge device connection string from the previous section.

   ```
   iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Start the runtime.

   ```
   iotedgectl start
   ```

1. Check Docker to see that the IoT Edge agent is running as a module.

   ```
   docker ps
   ```

## Deploy a module

One of the key capabilities of Azure IoT Edge is being able to deploy modules to your IoT Edge devices from the cloud. An IoT Edge module is an executable package implemented as a container. In this section, you deploy a module that generates telemetry for your simulated device. 

1. In the Azure portal, navigate to your IoT hub.
1. Go to **IoT Edge Explorer** and select your IoT Edge device.
1. Select **Deploy modules**.
1. Select **Add custom IoT Edge module**.
1. In the **Name** field, enter `tempSensor`. 
1. In the **Image** field, enter `edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview`. 
1. Leave the other settings unchanged, and select **Save**.
1. Back in the **Add modules** step, select **Next**.
1. In the **Specify routes** step, select **Next**.
1. In the **Review template** step, select **Submit**.
1. Return to the device details page and select **Refresh**. You should see the new tempSensor module running along the IoT Edge runtime. 

   ![View tempSensor in list of deployed modules][1]

## View generated data

You can monitor your new IoT Edge device's status by clicking on it in IoT Edge Explorer page of your IoT hub. 

You can view the telemetry the device is sending by using the [IoT Hub explorer tool][lnk-iothub-explorer].

## Next steps

In this tutorial, you created a new IoT Edge device and used the Azure IoT Edge cloud interface to deploy code onto the device. Now, you have a simulated device generating raw data about its environment. 

This tutorial is the prerequisite for all of the other IoT Edge tutorials. You can continue on to any of the other tutorials to learn how Azure IoT Edge can help you turn this data into business insights at the edge.

> [!div class="nextstepaction"]
> [Deploy your own code as a module](tutorial-create-custom-module.md)
> [Deploy Azure Stream Analytics as a module](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python-windows]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
