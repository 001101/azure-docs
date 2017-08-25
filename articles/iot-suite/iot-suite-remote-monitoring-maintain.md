---
title: Troubleshoot and remediation on the Azure IoT Suite remote monitoring solution | Microsoft Docs
description: This tutorial shows you how to troubleshoot and remediate device issues in the remote monitoring solution.
services: ''
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 08/25/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
---

# Troubleshoot and remediate device issues

As an operator, you need to investigate the behavior of a faulty prototype device connected to the remote monitoring solution. To showcase how to do it, we will walk you through a sample scenario: in this case, Contooso is testing a new prototyping device in the field. White testing this device, the operator sees that the prototype device is erronousely triggering a temperature alarm on the dashboard.

This tutorial shows you how to use the maintenance dashboard to troubleshoot and remediate these device issues.

In this tutorial, you learn how to:

>[!div class="checklist"]
> * Use the maintenance dashboard to invetigate the alarm
> * Call a device method to remediate the issue

## Prerequisites

To follow this tutorial, you need a deployed instance of the remote monitoring solution in your Azure subscription.

If you haven't deployed the remote monitoring solution yet, you should complete the [Deploy the remote monitoring preconfigured solution](iot-suite-remote-monitoring-deploy.md) tutorial.

## Use the maintenance dashboard

Steps:

- Identify the alarm trigger in the dashboard. Use the deep link button to go to maintenance dashboard.
- In maintenance dashboard, explore the associated devices and rules to this particular event.
- Identify the device at fault and start remediation.


## Remediate the issue

- Once device is identified, remediation is to trigger a temperature release that fixes the issue and the situation gets resolved. 

## Next steps

In this tutorial, we showed you how to:

<!-- Repeat task list from intro -->
> * Use the maintenance dashboard to invetigate the alarm
> * Call a device method to remediate the issue

Now that you have learned how to manage device issues, the suggested next step is to learn how to [Test your solution with simulated devices](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->