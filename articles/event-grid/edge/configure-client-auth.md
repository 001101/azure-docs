---
title: Configure client authentication of incoming calls - Azure Event Grid IoT Edge | Microsoft Docs 
description: Configure API protocols exposed by Event Grid on IoT Edge.  
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: 
ms.date: 07/26/2019
ms.topic: article
ms.service: event-grid
services: event-grid
---

# Configure client authentication of incoming calls

This guide gives examples of the possible client authentication configurations an Event Grid module can be deployed under. Event Grid module supports two types of client authentication:-

* SASKey based
* certificate-based

See [Security and authentication](security-authentication.md) guide for all the possible configurations.

## Example 1: Enable certificate-based client authentication, no self-signed certificates

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## Example 2: Enable certificate-based client authentication, allow self-signed certificates

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Set the property **inbound:clientAuth:clientCert:allowUnknownCA** to **true** only in test environments as you might typically use self-signed certificates. For production workloads we recommend them to be set to **false**.

## Example 3: Enable certificate-based and sas-key based client authentication

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>SASKey based client authentication will allow for e.g., an non-IoT edge module to do management and runtime operations assuming of course the API ports are accessible outside the IoT Edge network.
