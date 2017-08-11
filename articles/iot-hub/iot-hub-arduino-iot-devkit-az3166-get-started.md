---
title: IoT DevKit to cloud - Connect IoT DevKit AZ3166 to Azure IoT Hub | Microsoft Docs
description: Learn how to setup and connect IoT DevKit AZ3166 to Azure IoT Hub for it to send data to the Azure cloud platform in this tutorial.
services: iot-hub
documentationcenter: ''
author: shizn
manager: timlt
tags: ''
keywords: ''

ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi

---
# Connect IoT DevKit AZ3166 to Azure IoT Hub in the cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]


## What you do

Connect DevKit to an Azure IoT hub that you create, collect the temperature and humidity data from sensors and send the data to IoT hub.

Don't have a DevKit yet? Get a new one [here](https://aka.ms/iot-devkit-purchase).

## What you learn

* How to connect IoT DevKit to Wireless access point and prepare your development environment.
* How to create an IoT hub and register a device for MXChip IoT DevKit.
* How to collect sensor data by running a sample application on MXChip IoT DevKit.
* How to send the sensor data to your IoT hub.

## What you need

* An MXChip IoT DevKit board with a micro USB cable. [Get it now](https://aka.ms/iot-devkit-purchase)
* A computer running Windows 10 or macOS 10.10+
* An active Azure subscription
  * Activate a [free 30-day trial Microsoft Azure account](https://azureinfo.microsoft.com/us-freetrial.html)


## Prepare your Hardware
### Connect DevKit to your computer

1. Connect USB end to your PC
2. Connect Micro USB end to the DevKit
3. The green LED next to power confirms connection

![AZ3166 getting started connect](media/iot-hub-arduino-devkit-az3166-get-started/getting-started-connect.jpg)

### Upgrade to the latest firmware

If this is the first time you are developing on the DevKit, follow [Upgrading](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) guide to upgrade the DevKit firmware to the latest version.

This is a one-time effort, once you start developing on the DevKit and upload your app, you will have the latest firmware.

> [!NOTE] 
You will see the firmware version on the screen of the kit. If there is no version number, it means the DevKit is running on old version and needs an upgrade. [Versions](https://microsoft.github.io/azure-iot-developer-kit/versions/) contains the latest version number and release notes.


### Test sensors and WiFi on DevKit

Now you have connected your DevKit to computer. Follow the instructions on the screen to test sensors and WiFi:

- Press button A to test WiFi connection
- Press button B to test sensors, continue pressing and releasing the B button to cycle through each sensor

![getting started sensors](media/iot-hub-arduino-devkit-az3166-get-started/getting-started-sensors.jpg)

## Configure WiFi

IoT projects rely on Internet connectivity. Use the following instructions to setup Access Point(AP) Mode on the DevKit to enable WiFi connectivity.

### Enter AP Mode

Hold down button B, then push and release the reset button, then release button B. The screen will display the Service Set Identifier(SSID) of the DevKit as well as the configuration portal IP address:

![getting-started-wifi-ap](media/iot-hub-arduino-devkit-az3166-get-started/getting-started-wifi-ap.jpg)

### Connect to DevKit AP

Use another WiFi enabled device (PC or mobile phone) to connect to the DevKit SSID (highlighted in the screenshot above), leave the password empty.

### Configure WiFi for DevKit

Open the IP address shown on the DevKit screen in your PC or mobile phone browser, select the WiFi network you want the DevKit to connect to, then type the password. Click **'Connect'** to complete.

![getting-started-wifi-portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started-wifi-portal.png)

Once the connection succeeds, the DevKit will take a few seconds to reboot. Then, you can test the connection by clicking button A. The WiFi SSID you selected and an IP address will display on the screen.

![getting-started-wifi-ip](media/iot-hub-arduino-devkit-az3166-get-started/getting-started-wifi-ip.jpg)

> [!NOTE]
The IP address displayed in the photo may not match the actual IP assigned and displayed on the DevKit screen. This is normal as WiFi uses DHCP to dynamically assign IPs.


After WiFi is configured, your credentials will be persisted on the device for that connection, even if unplugged. For example, if you configured the DevKit for WiFi in your home and then took the DevKit to the office, you will need to reconfigure AP mode (starting at step 3A) to connect the DevKit to your office WiFi. 

## Prepare development environment

Now it's time to set up the development environment: tools and packages for you to build stunning IoT applications.

# Windows(#tab/windows)

### Download latest package

The `.zip` file you download contains all necessary tools and packages required for DevKit development.

> [!div class="button"]
[Download](https://azureboard.azureedge.net/installpackage/devkit_install_1.0.1.zip)

> [!NOTE]
> **MD5:** 01527cb5c349601452d1f5546149f3be
> The `.zip` file contains the following tools and packages. If you already have some components installed, the script will detect and skip them.
> * Node.js and Yarn: Runtime for the setup script and automated tasks
> * Python and pip: For running Azure CLI 2.0
> * [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview){:target="_blank"} - Cross-platform  command-line experience for managing Azure resources
> * [Visual Studio Code](https://code.visualstudio.com/){:target="_blank"} (VS Code): Lightweight code editor for DevKit development
> * [VS Code Arduino Extension](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino){:target="_blank"}: Enables Arduino development in VS Code
> * [Arduino IDE](https://www.arduino.cc/en/Main/Software){:target="_blank"}: VS Code Arduino Extension relies on this tool
> * DevKit Board Package: Tool chains, libraries and projects for the DevKit
> * ST-Link Utility: Essential utility and drivers

### Run installation script

In Windows File Explorer, locate the `.zip` and extract it, find `install.cmd`, right-click and select **"Run as administrator"** to start.

![getting-started-run-admin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started-run-admin.png)

During installation, you will see the progress of each tool or package.

![getting-started-install](media/iot-hub-arduino-devkit-az3166-get-started/getting-started-install.png)

### Confirm to install drivers

The VS Code for Arduino extension relies on the Arduino IDE. If this is the first time you are installing the Arduino IDE, you will be prompted to install relevant drivers:

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started-driver.png)

It should take around 10 minutes to finish installation depending on your Internet speed. Once installation is complete, you should see Visual Studio Code and Arduino IDE shortcuts on your desktop.

# macOS(#tab/macos)

### Install Azure CLI 2.0

Follow the [official guide](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli){:target="_blank"} to install Azure CLI 2.0:

Install Azure CLI 2.0 with one `curl` command:

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

And restart your command shell for changes to take effect:

```bash
exec -l $SHELL
```

### Install Arduino IDE

The Visual Studio Code Arduino extension relies on the Arduino IDE. Download and install the [Arduino IDE for macOS](https://www.arduino.cc/en/Main/Software).

### Install Visual Studio Code

Download and install [Visual Studio Code for macOS](https://code.visualstudio.com/). This will be the primary development tool for building DevKit IoT applications.

### Download latest package

The `.zip` file you download contains necessary scripts required for DevKit development in VS Code.


> [!div class="button"]
[Download](https://azureboard.azureedge.net/installpackage/devkit_install_1.0.1.zip)

> [!NOTE]
> **MD5:** 01527cb5c349601452d1f5546149f3be

Locate the `.zip` and extract it. Then launch Terminal and run the following commands to configure:

```bash
mkdir ~/azure-board-cli
cp -R [.zip extracted folder] ~/azure-board-cli/. | cd ~/azure-board-cli
npm install
```

### Install VS Code extension for Arduino

Visual Studio Code allows you to install Marketplace extensions directly in the tool, simply click the extensions icon in the left menu pane and then search. You will need two extensions:

1. C++ extension for grammar support, it is required by the Arduino extension so you need to install it first.
2. Arduino extension which makes it easy to code, build, deploy and debug your Arduino sketches in Visual Studio Code. It comes with IntelliSense, automatic project scaffolding and debugging.

![installation-extensions](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions.png)

### Install DevKit board package

You will need to add the DevKit board using the Boards Manager in Visual Studio Code.

1. Use `Cmd+Shift+P` to invoke command palette and type **Arduino** then find and select **Arduino: Boards Manager**.

2. Click **'Additional URLs'** at the bottom right.

   ![installation-additional-urls](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls.png)

3. In the `settings.json` file, add a line at the bottom of 'USER SETTINGS' pane.

   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![installation-settings-json](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json.png)

4. Now in the Boards Manager search for 'az3166' and install the latest version.
   ![installation-az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166.png)

You now have all the necessary tools and packages installed.
