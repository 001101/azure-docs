---
title: "Quickstart: Configure and enable the Security Module for Azure RTOS"
description: Learn how to onboard and enable the Security Module for Azure RTOS service in your Azure IoT Hub.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''

ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
---

# Quickstart: Azure IoT Security Module - Azure RTOS (preview)

This article provides an explanation of the prerequisites before getting started and explains how to enable the Security Module for Azure RTOS service on an IoT Hub. If you don't currently have an IoT Hub, see [Create an IoT Hub using the Azure portal](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) to get started.

> [!NOTE]
> Azure IoT Security Module -RTOS is currently only supported in standard tier IoT Hubs.

## Prerequisites 

### Supported devices

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Download, compile, and run one of the .zip files for the specific board and tool (IAR, semi's IDE or PC) of your choice from the [Azure RTOS IoT Security GitHub resource](hhtps://github.com/azure-rtos/azure-iot-preview/releases).

  
### IoT Hub connection

An IoT Hub connection is required to get started. 

1. Open your **IoT Hub** in Azure portal.
1. Copy the IoT connection string.

The Security Module for Azure RTOS uses Azure IoT Middleware connections based on the **MQTT** protocol.
Connections credentials are taken from the user application configuration **HOST_NAME**, **DEVICE_ID**,and **DEVICE_SYMMETRIC_KEY**.



## Next steps

Advance to the next article to finish configuring and customizing your solution...

> [!div class="nextstepaction"]
> [Complete configuration and customization of your solution](how-to-azure-rtos-security-module.md)
