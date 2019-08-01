---
title: Limits and quotas IoT Plug and Play Preview | Microsoft Docs
description: Understand the limits, quotas, and throttling that apply when you use IoT Plug and Play Preview.
author: miagdp
ms.author: miag
ms.date: 08/01/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
---

# IoT Plug and Play Preview limits, quotas, and throttling

This article explains the IoT Plug and Play-specific limits, quotas, and throttling that apply in the public preview. There are existing [IoT Hub quotas and throttling](../iot-hub/iot-hub-devguide-quotas-throttling.md) that also apply.

## IoT Hub

For the public preview, the following limits and quotas apply to an IoT hub:

| Limits, restrictions, and throttles | Value | Notes |
|-----|-----|-----|
| Number of device capability models (DCMs) or interfaces that can be registered per hub | 1500 ||
| Maximum number of interfaces that can be registered per device | 40 ||
| Maximum number of DCMs that can be registered per device | 1 ||
| Maximum size of an interface name | 256 chars ||
| Maximum size of a property name - regular twin  | 64 bytes, 5 levels in depth (and the first level is reserved for `$iotin`) | Allowed characters: a-z, A-Z, 0-9 (not as the first character), and underscore. |
| Maximum size of a property name - large twin  | 4096 bytes, 10 levels in depth (and the first level is reserved for `$iotin`) | Allowed characters: a-z, A-Z, 0-9 (not as the first character), and underscore.|
| Maximum size of a property value - regular twin | 512 bytes ||
| Maximum size of a property value - large twin | 4096 bytes ||
| Maximum size of a command name | 100 bytes ||
| Resolution API calls across SKU (regardless of units) | 100 request/second |

## Model Repository

For the public preview, the following limits and quotas apply to a model repository:

| Limits, restrictions, and throttles| Value |
|-----|-----|
| Number of company model repositories per Azure Active Directory tenant | 1 |
| Number of authorization keys per model repository | 10  |
| Number of models (DCMs or interfaces) per company model repository| 1500  |
| Number of models (DCMs or interfaces) in the public model repository per Azure Active Directory tenant| 1500  |
| Number of DCMs or interfaces being deleted in a company model repository | 10 queries per second (qps)|
| Number of model repositories being created/updated by a tenant| 1 qps |
| Number of authorization keys being created/updated/deleted in a model repository | 1 qps|
| Number of DCMs being created in a company model repository | 10 qps |
| Number of interfaces being created in a company model repository | 10 qps|
| Number of DCMs being created in the public model repository | 10 qps|
| Number of interfaces being created in the public model repository | 10 qps|

## Parser library

The parser library follows the limits that apply to the [Digital Twin Definition Language](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## Next steps

A suggested next step is to learn how to [Connect to and interact with an IoT Plug and Play device](./howto-develop-solution.md).
