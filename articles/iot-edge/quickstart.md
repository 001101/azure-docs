---
title: Quickstart Azure IoT Edge + Windows | Microsoft Docs 
description: Try out Azure IoT Edge by running analytics on a simulated edge device
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/24/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc

experimental: true
experiment_id: 
---

# Quickstart: Deploy your first IoT Edge module from the Azure portal to a Windows device - preview

In this quickstart, use the Azure IoT Edge cloud interface to deploy prebuilt code remotely to an IoT Edge device. To accomplish this task, first use your Windows device to simulate an IoT Edge device, then you can deploy a module to it.

In this quickstart you learn how to:

1. Create an IoT Hub.
2. Register an IoT Edge device to your IoT hub.
3. Install and start the IoT Edge runtime on your device.
4. Remotely deploy a module to an IoT Edge device.

![Tutorial architecture][2]

The module that you deploy in this quickstart is a simulated sensor that generates temperature, humidity, and pressure data. The other Azure IoT Edge tutorials build upon the work you do here by deploying modules that analyze the simulated data for business insights. 

>[!NOTE]
>The IoT Edge runtime on Windows is in public preview.

If you don't have an active Azure subscription, create a [free account][lnk-account] before you begin.

## Prerequisites

This quickstart assumes that you're using a computer or virtual machine running Windows as an Internet of Things device. If you're running Windows in a virtual machine, enable [nested virtualization][lnk-nested] and allocate at least 2GB memory. 

>[!IMPORTANT]
>**bug bash only**
>Use the canary portal 
>Add the following line to the hosts file of the computer from which you'll access the Azure Portal 
>`40.76.74.59 main.iothub.ext.azure.com` 
> 
>The hosts file can be found at: 
>* Linux - /etc/hosts 
>* Windows - C:\Windows\System32\drivers\etc\hosts 
>
> Remember to remove this line when you're done testing

Have the following prerequisites ready on the machine that you're using for an IoT Edge device:

1. Make sure you're using a supported Windows version:
   * Windows 10 or newer
   * Windows Server 2016 or newer
2. Install [Docker for Windows][lnk-docker] and make sure it's running.
3. Configure Docker to use [Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

You use the Azure CLI to complete many of the steps in this quickstart, and Azure IoT has an extension to enable additional functionality. You can complete these steps in the cloud shell in the Azure portal.

1. Sign in to the [Azure portal][lnk-portal]. 
2. Select the **Cloud Shell** button. 

   ![Cloud Shell button][1]

3. Add the Azure IoT extension to the cloud shell instance.

   ```azurecli
   az extension add --name azure-cli-iot-ext
   ```

## Create an IoT hub

Start the quickstart by creating your IoT Hub in the Azure portal.
![Create IoT Hub][3]

The free level of IoT Hub works for this quickstart. If you've used IoT Hub in the past and already have a free hub created, you can use that IoT hub. Each subscription can only have one free IoT hub. 

1. In the Azure cloud shell, create a resource group. The following code creates a resource group called **IoTEdge** in the **West US** region:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Create an IoT hub in your new resource group. The following code creates a free **F1** hub called **MyIotHub** in the resource group **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## Register an IoT Edge device

Register an IoT Edge device with your newly created IoT Hub.
![Register a device][4]

Create a device identity for your simulated device so that it can communicate with your IoT hub. Since IoT Edge devices behave and can be managed differently than typical IoT devices, you declare this to be an IoT Edge device from the beginning. 

1. In the Azure cloud shell, enter the following command to create a device named **myEdgeDevice** in your hub **MyIoTHub**

   ```azurecli
   az iot hub device-identity create --device-id myEdgeDevice --hub-name MyIoTHub --edge-enabled
   ```

1. Retrieve the connection string for your device, which links your physical device with its identity in IoT Hub. 

   ```azurecli
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name MyIoTHub
   ```

1. Copy the connection string and save it. You'll use this value to configure the IoT Edge runtime in the next section. 

## Install and start the IoT Edge runtime

Install and start the Azure IoT Edge runtime on your IoT Edge device. 
![Register a device][5]

The IoT Edge runtime is deployed on all IoT Edge devices. It's composed of three components. The **IoT Edge security daemon** starts each time an Edge device boots and bootstraps the device by starting the IoT Edge agent. The **IoT Edge agent** facilitates deployment and monitoring of modules on the IoT Edge device, including the IoT Edge hub. The **IoT Edge hub** manages communications between modules on the IoT Edge device, and between the device and IoT Hub. 

The instructions in this section configure the IoT Edge runtime with Linux containers. If you want to use Windows containers, see [Install Azure IoT Edge runtime on Windows to use with Windows containers](how-to-install-iot-edge-windows-with-windows.md).

### Download and install the IoT Edge service

1. On your IoT Edge device, open PowerShell as an administrator.

2. Download the IoT Edge service package.

   ```powershell
   Invoke-WebRequest https://conteng.blob.core.windows.net/iotedged/iotedge.zip -o .\iotedge.zip
   Expand-Archive .\iotedge.zip C:\ProgramData\iotedge -f
   $env:Path += ";C:\ProgramData\iotedge"
   SETX /M PATH "$env:Path"
   ```

3. Create and start the IoT Edge service.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

4. Add firewall exceptions for the ports that the IoT Edge service uses.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

5. Create a new file called **iotedge.reg** and open it with a text editor. 

6. Add the following content and save the file. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

7. Navigate to your file in File Explorer and double-click it to import the changes to the Windows Registry. 

### Configure the IoT Edge runtime 

Configure the runtime with your IoT Edge device connection string that you copied when you registered a new device. Then, sconfigure the runtime network. 

1. Open the IoT Edge configuration file, which is located at `C:\ProgramData\iotedge\config.yaml`. 

2. Find the **provisioning** section and set the value of **device_connection_string** to the string that you copied. 

3. In your administrator PowerShell window, retrieve the hostname for your IoT Edge device and copy the output. 

   ```powershell
   hostname
   ```

4. In the configuration file, find the **Edge device hostname** section. Update the value of **hostname** to the hostname that you copied from PowerShell.

3. In your administrator PowerShell window, retrieve the IP address for your IoT Edge device. 

   ```powershell
   ipconfig
   ```

4. Copy the value for **IPv4 Address** in the **vEthernet (DockerNAT)** section of the output. 

5. Create an environment variable called **IOTEDGE_HOST**, replacing *\<ip_address\>* with the IP Address for your IoT Edge device. 

   ```powershell
   [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
   ```

6. In the `config.yaml` file, find the **connect** section. Update the **management_uri** and **workload_uri** values with your IP address and the ports that you opened in the previous section. 

   ```yaml
   connect: 
     management_uri: "http://<ip_address>:15580"
     workload_uri: "http://<ip_address>:15581"
   ```

7. Find the **listen** section and add the same values for **management_uri** and **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<ip_address>:15580"
     workload_uri: "http://<ip_address:15581"
   ```

8. Find the **moby_runtime** section and verify that the value for **network** is set to `nat`.

9. Save the configuration file. 

10. In PowerShell, restart the IoT Edge service.

   ```powershell
   Stop-Service iotedge
   Start-Service iotedge
   ```

### View the IoT Edge runtime status

Verify that the runtime was successfully installed and configured.

1. Check the status of the IoT Edge service.

   ```powershell
   Get-Service iotedge
   ```

2. If you need to troubleshoot the service, retrieve the service logs. 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Today} |
  select TimeCreated, Message | Sort-Object -Descending
   ```

3. View all the modules running on your IoT Edge device.

   ```powershell
   iotedge list
   ```

## Deploy a module

Manage your Azure IoT Edge device from the cloud to deploy a module that will send telemetry data to IoT Hub.
![Register a device][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## View generated data

In this quickstart, you created a new IoT Edge device and installed the IoT Edge runtime on it. Then, you used the Azure portal to push an IoT Edge module to run on the device without having to make changes to the device itself. In this case, the module that you pushed creates environmental data that you can use for the tutorials. 

Open the command prompt on the computer running your simulated device again. Confirm that the module deployed from the cloud is running on your IoT Edge device. 

```cmd
docker ps
```

![View three modules on your device](./media/tutorial-simulate-device-windows/docker-ps2.png)

View the messages being sent from the tempSensor module to the cloud. 

```cmd
iotedge logs tempSensor -f
```

![View the data from your module](./media/tutorial-simulate-device-windows/docker-logs.png)

You can also view the telemetry the device is sending by using the [IoT Hub explorer tool][lnk-iothub-explorer] or the [Azure IoT Toolkit extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## Clean up resources

<!--
If you want to remove the simulated device that you created, along with the Docker containers that were started for each module, use the following command: 

```cmd
iotedgectl uninstall
```
-->
When you no longer need the IoT Hub you created, you can use the [az iot hub delete][lnk-delete] command to remove the resource and any devices associated with it:

```azurecli
az iot hub delete --name MyIoTHub --resource-group IoTEdge
```

## Next steps

This quickstart is the prerequisite for all of the IoT Edge tutorials. You can continue on to any of the other tutorials to learn how Azure IoT Edge can help you turn this data into business insights at the edge.

> [!div class="nextstepaction"]
> [Filter sensor data using an Azure Function](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png


<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
