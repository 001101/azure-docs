---
title: Troubleshoot errors onboarding Update Management, Change Tracking, and Inventory
description: Learn how to troubleshoot onboarding errors with the Update Management, Change Tracking, and Inventory solutions
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/25/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
---
# Troubleshoot errors when onboarding solutions

When onboarding solutions you may encounter errors. The following is a listing of the common errors you may run across.

## ComputerGroupQueryFormatError

**Reason for the error:**

This error code means that the saved search computer group query used to target the solution was not formatted correctly. You may have altered the query, or it may have been altered by the system.

**Troubleshooting tips:**

You can delete the query for this solution, and reonboard the solution, which recreates the query. The query can be found within your workspace, under **Saved searches**. The name of the query is **MicrosoftDefaultComputerGroup**, and the category of the query is the name of the solution associated with this query. If multiple solutions are enabled, the **MicrosoftDefaultComputerGroup** shows multiple times under **Saved Searches**.

## PolicyViolation

**Reason for the error:**

This error code means that the deployment failed due to violation of one or more policies.

**Troubleshooting tips:**

In order to successfully deploy the solution, you need to consider altering the indicated policy. As there are many different types of policies that can be defined, the specific changes required depend on the policy that is violated. For example, if a policy was defined on a resource group that denied permission to change the contents of certain types of resources within that resource group, you could, for example, do any of the following:

* Remove the policy altogether.
* Try to onboard to a different resource group.
* Revise the policy, by, for example:
  * Re-targeting the policy to a specific resource (such as to a specific Automation account).
  * Revising the set of resources that policy was configured to deny.

Check the notifications in the top right corner of the Azure portal or navigate to the resource group that contains your automation account and select **Deployments** under **Settings** to view the failed deployment. To learn more about Azure Policy visit: [Overview of Azure Policy](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## MMA Extension deployment failures

When deploying a solution, a variety of related resources are deployed. One of those resources is the Microsoft Monitoring Agent Extension. This is a Virtual Machine Extension installed by the virtual machine’s Guest Agent that is responsible for communicating with the configured Operations Management Suite (OMS) Workspace, for the purpose of later coordination of the downloading of binaries and other files that the solution you are onboarding depend on once it begins execution.
You typically first become aware of MMA installation failures from a notification appearing in the Notifications Hub. Clicking on that notification gives further information about the specific failure. Navigation to the Resource Groups resource, and then to the Deployments element within it also provides details on the deployment failures that occurred.
Installation of MMA can fail for a variety of reasons, and the steps to take to address these failures vary, depending on the issue. Specific troubleshooting steps follow.

The following section describes various issues that you can encounter when onboarding that cause a failure in the deployment of the MMA extension.

### Guest Agent Connectivity

When onboarding the MMA extension, if the Guest Agent on the virtual machine is unable to communicate with external resources deployment fails.

**Reason for the error:**

You receive one of the following messages:

```
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

**Troubleshooting tips:**

These errors could occur due to one or more of the following reasons:

* There is a proxy configured in the VM, that only allows specific ports.

* A firewall setting has blocked access to the required ports and addresses. For a list of ports and addresses, see [planning your network](../automation-hybrid-runbook-worker.md#network-planning).

### Transient MMA State Conflict

When onboarding the MMA extension, there is a transient error that makes the deployment fail.

**Reason for the error:**

When deploying the MMA extension you may receive one of the following messages:

```
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

**Troubleshooting tips:**

When errors of this type occur, they are normally due to transient environment issues (such as when a domain join extension triggers a reboot, and in the meantime the guest agent happens to start trying to install the MMA extension as the machine is shutting down), simply retrying to deploy the solution addresses the issue.

### MMA Extension Installation Timeout


### VMExtensionProvisioningTimeout