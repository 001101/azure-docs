---
title: How to install Azure IoT Edge on Windows with Windows containers | Microsoft Docs
description: Azure IoT Edge installation instructions on Windows with  Windows containers
author: kgremban
manager: timlt
# this is the PM responsible
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: kgremban
---
# Install Azure IoT Edge runtime on Windows to use with Windows containers
The Azure IoT Edge runtime is deployed on all IoT Edge devices. It is composed of three components. The **IoT Edge Security Daemon**  provides and maintains security standards on the Edge device. The daemon starts on every boot and bootstraps the device by starting the IoT Edge Agent. The **IoT Edge Agent** facilitates deployment and monitoring of modules on the Edge device, including the IoT Edge Hub. The **IoT Edge Hub** manages communications between modules on the IoT Edge device, and between the device and IoT Hub.

This article lists the steps to install the Azure IoT Edge runtime on your Windows x64 (AMD/Intel) system. 

Windows support is currently in Preview.

## Supported Windows versions
Azure IoT Edge with Windows containers can be used with:
  * Windows 10/IoT Enterprise/IoT Core with April 2018 update (Build 17134).
  * Windows Server 1803

## Install the container runtime 

>[!NOTE]
>For container engine installation on Windows IoT Core, follow steps from [provision an IoT Core device article][lnk-iot-core] and then continue with instructions below.

Azure IoT Edge relies on a [OCI][lnk-oci]-compatible container runtime (e.g. Docker). You can use [Docker for Windows][lnk-docker-for-windows] for development and testing. 

**Ensure Docker for Windows is [configured to use Windows containers][lnk-docker-config]**

## Install the Azure IoT Edge Security Daemon

>[!NOTE]
>Azure IoT Edge software packages are subject to the license terms located in the packages (in the LICENSE directory). Please read the license terms prior to using the package. Your installation and use of the package constitutes your acceptance of these terms. If you do not agree with the license terms, do not use the package.

### Download the Edge daemon package and install

In an Administrator PowerShell window, execute the following commands:

```powershell
Invoke-WebRequest https://conteng.blob.core.windows.net/iotedged/iotedge.zip -o .\iotedge.zip
Expand-Archive .\iotedge.zip C:\ProgramData\iotedge -f
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Create and start `iotedge` service:

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Add Firewall exceptions for the ports used by the service:

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Create a `iotedge.reg` file with the following content, and import in to the Windows Registry by double-clicking it or using the `reg add` command:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## Configure the Azure IoT Edge Security Daemon

The daemon can be configured using the configuration file at `C:\ProgramData\iotedge\config.yaml` The edge device can be configured [automatically via Device Provisioning Service][lnk-dps] or manually using a [device connection string][lnk-dcs].

For manual configuration, enter the device connection string in `provisioning:` section of `config.yaml`

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Next, we'll need to provide the ip address and port for `workload_uri` and `management_uri` in the `connect:` section of the configuration.

For the ip address, enter `ipconfig` in your PowerShell window and select the ip address of the **vEthernet (nat)** interface as shown in the example below (the ip address on your system may be different):  

![nat][img-nat]

```yaml
connect:
  management_uri: "http://172.29.240.1:15580"
  workload_uri: "http://172.29.240.1:15581"
```

Enter the same addresses in the `listen:` section of the configuration. For example:

```yaml
listen:
  management_uri: "http://172.29.240.1:15580"
  workload_uri: "http://172.29.240.1:15581"
```

In the PowerShell window, create an environment variable `IOTEDGE_HOST` with the `management_uri` address, example:

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://172.29.240.1:15580")
```

Obtain the name of the host using the `hostname` command in the PowerShell window and set the value for `hostname:` in the configuration yaml. For example:

```powershell
###############################################################################
# Edge device hostname
###############################################################################
#
# Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
# modules.
#
###############################################################################

hostname: "edgedevice-1"
```
Finally, ensure the `network:` setting under `moby_runtime:` is uncommented and set to `nat`:

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "nat"
```

Save the configuration file and restart the service:

```powershell
Stop-Service iotedge
Start-Service iotedge
```

## Verify successful installation

You can check the status of the IoT Edge service by: 

```powershell
Get-Service iotedge
```

Examine service logs using:

```powershell
Get-WinEvent -LogName Application | 
  Where-Object {$_.ProviderName -eq "iotedged"} | 
  Select TimeCreated, Message | 
  oh -Paging
```

And, list running modules with:

```powershell
iotedge list
```

## Next steps

If you are having problems with the Edge runtime installing properly, checkout the [troubleshooting][lnk-trouble] page.


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md

