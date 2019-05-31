---
title: "include file"
description: "include file"
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: "include file"
ms.topic: "include"
ms.date: 05/30/2019
---

| Compute target | Usage | Description |
| ----- | ----- | ----- |
| [Local web service](how-to-deploy-and-where.md#local) | Testing/debug | Good for limited testing and troubleshooting.
| [Azure Kubernetes Service (AKS)](how-to-deploy-and-where.md#aks) | Real-time inference | Good for high-scale production deployments. Provides autoscaling, and fast response times. The is the only option available for the visual interface. |
| [Azure Container Instances (ACI)](how-to-deploy-and-where.md#aci) | Testing or dev | Good for low scale, CPU-based workloads. |
| [Azure Machine Learning Compute](how-to-run-batch-predictions.md) | (Preview) Batch inference | Run batch scoring on serverless compute. Supports normal and low-priority VMs. |
| [Azure IoT Edge](how-to-deploy-and-where.md#iotedge) | (Preview) IoT module | Deploy & serve ML models on IoT devices. |
| [FPGA](how-to-deploy-fpga-web-service.md) | Ultra-low latency inference | Better performance than a CPU, cheaper than a GPU-enabled VM.