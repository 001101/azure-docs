---
title: Limits
description: Code limitations for SDK features
author: erscorms
ms.author: erscorms
ms.date: 02/11/2020
ms.topic: conceptual
---

# Limits

A number of features have size of count limitations due to internal details of the client SDK.

## Azure Frontend

* Total AzureFrontend instances per process: 16.
* Total AzureSession instances per AzureFrontend: 16.

## Objects

* Total allowable objects of a single type (Entity, CutPlaneComponent, etc.): 16777215.
* Total allowable cut planes: 8.

## Materials

* Total allowable materials in an asset: 65535.
