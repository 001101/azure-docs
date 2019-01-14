---
title: Azure IoT Hub device streams tutorial | Microsoft Docs
description: In this tutorial, you will learn how to use IoT Hub device streams.
author: rezasherafat
manager: briz
services: iot-hub
ms.service: iot-hub
ms.devlang: csharp
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/18/2018
ms.author: rezas
---

# IoT Hub Device Streams Tutorial
IoT Hub device streams enable secure communication to devices that are located behind firewalls or inside of private networks. As shown in the figure below, this is achieved by leveraging IoT Hub's cloud endpoints as a proxy to tunnel application traffic between device and services.

In our terminology, a _service_ is an entity that authenticates using IoT Hub's _service connect_ credentials and attempts to initiate a connection to a device. A device, on the other hand, has access to device credentials and awaits incoming streaming requests from the service. As such, the interaction between the service and device mimics a client/server interaction where service acts as the client connecting to the device (acting as server).
<p>
    <img style="margin:auto;display:block;background-color:white;width:50%" src="./media/iot-hub-device-streams-tutorial/iot-hub-device-streams.svg">
</p>

The device and service programs can interact using a variety of standards-based or properietary protocols. As such, device streams treat application traffic simply as an opague bytes stream passing through. This makes device streams application and protocol agnostic and hence allow this feature to act as a building block for end-to-end connectivity between device and service applications.


## Overview
Establishing a device stream involves an initiation handshake that takes place between the device, service, and IoT Hub. As part of the handshake, both device and service authenticate with IoT Hub and each create a WebSocket client through which they exchange application data.

The data sent over the WebSocket clients on each side will be tunneled transparently through IoT hub while maintaining reliability and ordering gaurantees that are on par with TCP.



## Device Streams Initiation Handshake
The device streams initiation handshake can be thought of as a control-plane operation to be contrasted with the actual exchange of data between the device and service as a data-plane operation. As part of the handshake, device and service each receive a temporary authentication token and the URI of a _device streaming endpoint_ to connect to for sending and receiving data. The authentication token is valid for 60 seconds and device and service must use it prior to expiry to create and authenticate a WebSocket client with the streaming endpoint.

The details of the steps involved in this process is depicted in the figure below:
<p>
    <img style="margin:auto;display:block;background-color:white;width:50%" src="./media/iot-hub-device-streams-tutorial/iot-hub-device-streams-handshake.svg">
</p>

1. Device connects to IoT Hub and authenticates using its device credentials (this step usually takes place when the device first comes online). The device also registers a callback to be notified when new streams are initiated towards it from the service-side. The callback is registered for a specific stream name which the device expects to be receiving in the future.

2. The service authenticates with IoT Hub using valid _service connect_ credentials and initiates a stream by providing the target device ID and the stream name - the latter must match the name used by the device in step 1.

3. IoT hub notifies the device's program via invoking the callback registered in step 1 for the corresponding stream name. The URI of the streaming endpoint and the authentication token will also be passed to the device via this callback. At this point, the device has an option to accept or reject the stream initiation request. The following steps assume that the device has accepted the stream request.

4. The device uses the authentication token received in step 3 to authenticate with the streaming endpoint using a WebSocket client. Once established, the device uses the WebSocket client to send and recevieve data to the service. This data is tunneled through the streaming endpoint.

5. The service will also be given the URI of the streaming endpoint and the authentication token which it uses to create a WebSocket client. Once the WebSocket client is established, the service can use the WebSocket client to send and recevieve data to the device. This data is similartly tunneled through the streaming endpoint.


## Connectivity Requirements
Both the device and service sides of a device stream must be capable of establishing TLS-enabled connections to IoT Hub and its streaming endpoint. This requires outbound connectivity over port 443 to these endpoints. The hostname associated with these endpoints can be found on the _Overview_ tab of IoT Hub, as shown in the figure below:
<p>
    <img src="./media/iot-hub-device-streams-tutorial/device-stream-portal.png">
</p>

Alternatively, the endpoints information can use be retrieved using Azure CLI under the hub's properties section (specifically, `property.hostname` and `property.deviceStreams` keys).

```azurecli-interactive
az iot hub show --name <YourIoTHubName>
```


## Troubleshooting

## Diagnostic Logs
Use Azure log analytics and Azure monitor for troubleshooting.
Details: [TODO]

### Error Codes


### Common Problems
* Ensure outbound firewall port is open to your IoT Hub's main and streaming endpoint.
* During preview, ensure your IoT Hub is in a supported region.



## Next Steps

Use the resources below to learn how to use IoT Hub device streams programmatically:
- [QuickStart: Sample echo programs using device streams in C#](./iot-hub-device-streams-csharp-echo-quickstart.md)
- [QuickStart: Sample echo program using device streams in C](./iot-hub-device-streams-c-echo-quickstart.md)
- [QuickStart: Sample echo program using device streams in NodeJS](./iot-hub-device-streams-nodejs-echo-quickstart.md)

Use the resources below to learn how to use IoT Hub device streams in an existing application:
- [QuickStart: Sample proxy programs using device streams in C#](./iot-hub-device-streams-csharp-proxy-quickstart.md)
- [QuickStart: Sample proxy program using device streams in C](./iot-hub-device-streams-c-proxy-quickstart.md)
- [QuickStart: Sample proxy program using device streams in NodeJS](./iot-hub-device-streams-nodejs-proxy-quickstart.md)
