---
title: Introduction to Azure IoT Hub | Microsoft Docs
description: Learn about Azure IoT Hub. This scalable IoT service is built for scalable data ingestion, device management, and security.
services: iot-hub
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-hub

documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc

---

# What is Azure IoT Hub?

You can use Azure IoT Hub to build IoT solutions with reliable and secure communications between millions of IoT devices and a cloud-hosted solution backend. You can connect virtually any device to IoT Hub, which is a managed service hosted in the cloud.

The direction of communications can be both device-to-cloud and cloud-to-device. IoT Hub offers multiple messaging options such as device-to-cloud telemetry, file upload from devices, and request-reply methods to control your devices from the cloud. IoT Hub monitoring helps you maintain the health of your solution by tracking events such as device creation and device connections.

## What's in the box?

IoT Hub's capabilities make it easy to build scalable, full-featured IoT solutions:

* **IoT-level scale:**
  * To support your IoT workloads, IoT Hub scales to millions of simultaneously connected devices and millions of events per second.

* **Secure communications between devices and the cloud:**
  * Per-device authentication enables each device to connect securely to IoT Hub, and for each device to be managed securely.
  * Complete control over device access at per-device granularity.
  * Automatically provision devices to the right IoT hub using the [IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/).
  * Multiple authentication types to support a variety of device capabilities:
    * SAS token-based authentication to quickly get started with your IoT solution.
    * Individual X.509 certificate authentication for secure, standards-based authentication.
    * X.509 CA authentication for simple, standards-based onboarding.

* **Send device data where it needs to go:**
  * Use message routing to control where your hub sends device telemetry.
  * No additional cost to route messages to multiple endpoints.
  * No-code routing rules take the place of custom message dispatcher code.

* **Integrate events with your existing business applications:**
  * Integrate with [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) to react quickly to critical events in a reliable, scalable, and secure manner.

* **Device configuration and control:**
  * Store, synchronize, and query device metadata and state information for all your devices.
  * Set device state either per-device or based on common characteristics of devices.
  * Automatically respond to a device-reported state change with message routing integration.

## What devices can connect to IoT Hub?

Use the [Azure IoT device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) libraries to build applications that run on your devices and interact with IoT Hub. Supported platforms include multiple Linux distributions, Windows, and real-time operating systems. Supported languages include C, C#, Java, Python, and Node.js. IoT Hub and the device SDKs support the following protocols for connecting devices:

* HTTPS
* AMQP
* AMQP over WebSockets
* MQTT
* MQTT over WebSockets

If your solution cannot use the device libraries, devices can use the MQTT v3.1.1, HTTPS 1.1, or AMQP 1.0 protocols to connect natively to your hub.

If your solution cannot use one of the supported protocols, you can extend IoT Hub to support custom protocols:

  * Use [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) to create a field gateway to perform protocol translation on the edge.
  * Customize the [Azure IoT protocol gateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) to perform protocol translation the cloud.

## Availability

There's a 99.9% [Service Level Agreement for IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/). The full [Azure SLA](https://azure.microsoft.com/support/legal/sla/) explains the guaranteed availability of Azure as a whole.

## IoT Hub in your subscription

Each Azure subscription has default quota limits in place to prevent service abuse, and these limits could impact the scope of your IoT solution. The current limit on a per-subscription basis is 10 IoT hubs per subscription, which you can raise by contacting support. For more details on quota limits:

* [Azure Subscription Service Limits](../azure-subscription-service-limits.md)
* [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## Next steps

To try out an end-to-end IoT scenario, check out the IoT Hub quickstarts:

> [!div class="nextstepaction"]
> [Quickstart: Send telemetry from a device to an IoT hub](quickstart-d2c-node.md)
