---
title: How to Troubleshoot Azure Monitor Container Insights | Microsoft Docs
description: This article describes how you can troubleshoot and resolve issues with Azure Monitor Container Insights solution.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: 

ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2018
ms.author: magoedte
---

# Troubleshooting Azure Monitor Container Insights

When you configure monitoring of your Azure Kubernetes Service (AKS) cluster with Container Insights, you may encounter an issue preventing data collection or reporting status. This article details some common issues and troubleshooting steps.

## Solution is enabled but not reporting any information
If Container Insights is successfully enabled and configured, but you cannot view status information or no results are returned from a Log Analytics log query, you diagnose the problem by performing the following: 

1. Check the status of the agent by running the following command: 

    `kubectl get ds omsagent --namespace=kube-system`

    The output should resemble the following, which indicates that it was deployed properly:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Check the solution deployment status with agent version *06072018* or later using the following command:

    `kubectl get deployment omsagent-rs -n=kube-system`

    The output should resemble the following, which indicates that it was deployed properly:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Check the status of the pod to verify that it is running using the following command: `kubectl get pods --namespace=kube-system`

    The output should resemble the following with a status of *Running* for the omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Check the agent logs. When the containerized agent gets deployed, it runs a quick check by running OMI commands and displays the version of the agent and provider. 

5. To verify that the agent has been onboarded successfully, run the following command: `kubectl logs omsagent-484hw --namespace=kube-system`

    The status should resemble the following:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
	:
	:
	instance of Container_HostInventory
	{
	    [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
	    Computer=aks-nodepool1-39773055-0
	    DockerVersion=1.13.1
	    OperatingSystem=Ubuntu 16.04.3 LTS
	    Volume=local
	    Network=bridge host macvlan null overlay
	    NodeRole=Not Orchestrated
	    OrchestratorType=Kubernetes
	}
	Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
	omi 1.4.2.2
	omsagent 1.6.0.23
	docker-cimprov 1.0.0.31
    ```

## Next steps
With monitoring enabled to capture health metrics for both the AKS cluster nodes and pods, these health metrics are available in the Azure portal. To learn how to use Container Insights, see [View Azure Kubernetes Service health](monitoring-container-insights-analyze.md).