---
title: Validate connectivity to Azure Sentinel | Microsoft Docs
description: Validate connectivity of your security solution to make sure CEF messages are being forwarded to Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''

ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin

---
# STEP 3: Validate connectivity

Once you have deployed your log forwarder (in Step 1) and configured your security solution to send it CEF messages (in Step 2), follow these instructions to verify connectivity between your security solution and Azure Sentinel. 

## Prerequisites

- You must have elevated permissions (sudo) on your machine.

- Make sure that you have Python on your machine using the following command: `python –version`

## How to validate connectivity

1. From the Azure Sentinel navigation menu, open **Logs**. Run a query using the **CommonSecurityLog** schema to see if you are receiving logs from your security solution.<br>
Be aware that it may take about 20 minutes until your logs start to appear in **Log Analytics**. 

1. If you don't see any results from the query, verify that events are being generated from your security solution, or try generating some, and verify they are being forwarded to the Syslog forwarder machine you designated. 

1. Run the following script to check connectivity between your security solution, the log forwarder, and Azure Sentinel. This script checks that the daemon is listening on the correct ports, that the forwarding is properly configured, and that nothing is blocking communication between the daemon and the Log Analytics agent. It also sends mock messages 'TestCommonEventFormat' to check end-to-end connectivity. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## Validation script explained



## Next steps
In this document, you learned how to connect CEF appliances to Azure Sentinel. To learn more about Azure Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](quickstart-get-visibility.md).
- Get started [detecting threats with Azure Sentinel](tutorial-detect-threats.md).
- [Use workbooks](tutorial-monitor-your-data.md) to monitor your data.

