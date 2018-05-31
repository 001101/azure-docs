---
title: Troubleshoot errors with Update Management
description: Learn how to troubleshoot issues with Update Management
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
---
# Troubleshooting issues with Update Management

If you encounter issues while attempting to onboard the solution or a virtual machine, check the **Application and Services Logs\Operations Manager** event log on the local machine for events with  event ID 4502 and event message containing **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. The following table highlights specific error messages and a possible resolution for each.

| Message | Reason | Solution |
|----------|----------|----------|
| Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account. "} | Machine is already onboarded to another workspace for Update Management | Perform cleanup of old artifacts by [deleting the hybrid runbook group](../automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|
| Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server cannot communicate, because they do not possess a common algorithm | Proxy/Gateway/Firewall blocking communication | [Review network requirements](../automation-hybrid-runbook-worker.md#network-planning)|
| Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value. | Proxy/Gateway/Firewall blocking communication | [Review network requirements](../automation-hybrid-runbook-worker.md#network-planning)|
| The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication. |Proxy/Gateway/Firewall blocking communication | [Review network requirements](automation-hybrid-runbook-worker.md#network-planning)|
| Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied. | Self-signed cert generation failure | Verify system account has read access to folder: **C:\ProgramData\Microsoft\Crypto\RSA**|
