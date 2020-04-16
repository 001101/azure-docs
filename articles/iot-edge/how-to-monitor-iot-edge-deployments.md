---
title: Monitor IoT Edge deployments - Azure IoT Edge
description: High-level monitoring including edgeHub and edgeAgent reported properties and automatic deployment metrics. 
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/15/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
---
# Monitor IoT Edge deployments

Azure IoT Edge provides reporting that lets you monitor real-time information on the modules deployed to your IoT Edge devices. The IoT Hub service retrieves the status from the IoT Edge devices and makes them available to the operator. In addition to the need to monitor deployments made to individual devices, monitoring is important for [deployments made at scale](module-deployment-monitoring.md) that include automatic deployments and layered deployments.

Successful deployments require monitoring the following information:

* The last time the device or module connected or disconnected.

* Connectivity status of the device or module.

* Status codes of the IoT Edge runtime modules.

* Versions of deployed modules.

Both devices and modules have similar data, such as connectivity, so values are obtained according the device ID or the module ID. For an overview of the IoT Edge runtime modules and their properties, see [Properties of the IoT Edge agent and IoT Edge hub module twins](module-edgeagent-edgehub.md).

The IoT Hub service collects the data reported by device and module twins and provides aggregated counts of the various states that devices may have. The IoT Hub service organizes this data into four groups of metrics:

| Type | Description |
| --- | ---|
| Targeted | Shows the IoT Edge devices that match the Deployment targeting condition. |
| Applied | Shows the targeted IoT Edge devices that are not targeted by another deployment of higher priority. |
| Reporting Success | Shows the IoT Edge devices that have reported that the modules have been deployed successfully. |
| Reporting Failure | Shows the IoT Edge devices that have reported that one or more modules haven't been deployed successfully. To further investigate the error, connect remotely to those devices and view the log files. |

You can also define your own custom metrics.

The IoT Hub service makes this data available for you to monitor in the Azure portal and in the Azure CLI.

## Monitor a deployment in the Azure portal

To view the details of a deployment and monitor the devices running it, use the following steps:

1. Sign in to the [Azure portal](https://portal.azure.com) and navigate to your IoT Hub.
1. Select **IoT Edge** from the left pane menu.
1. Select the **IoT Edge deployments** tab.
1. Inspect the deployment list. For each deployment, you can view the following details:

    | Column | Description |
    | --- | --- |
    | ID | The name of the deployment. |
    | Type | The type of deployment, either **Deployment** or **Layered Deployment** |
    | Target Condition | The tag used to define targeted devices. |
    | Priority | The priority number assigned to the deployment. |
    | System metrics | Specifies the number of device twins in IoT Hub that match the targeting condition, and **Applied** specifies the number of devices that have had the deployment content applied to their module twins in IoT Hub. |
    | Device Metrics | The number of IoT Edge devices in the deployment  |reporting success or errors from the IoT Edge client runtime. |
    | Custom Metrics | The number of IoT Edge devices in the deployment  |reporting data for any metrics that you defined for the deployment. |
    | Creation Time | The timestamp from when the deployment was created. This timestamp is used to break ties when two deployments have the same priority. |

1. Select the deployment that you want to monitor.  
1. On the **Deployment Details** page, scroll down to the bottom section and select the **Metrics** tab. Use the **Select Metric** drop-down to choose a specific metric. When a metric is selected, the **View** button appears that will list the IoT Edge devices that match the condition for the selected metric.

   ![View metrics for a selected deployment](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

1. Select the **Edit Metrics** button to view and edit the criteria for each metric. In addition to the four default metrics, custom defined metrics will appear in this list.

   ![View IoT Edge deployment metric criteria](./media/how-to-monitor-iot-edge-deployments/metric-list.png)

### Define properties

If you developed custom code modules, such as described in [Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge](how-to-visual-studio-develop-module.md), you can monitor their values by defining custom metrics. You can also add, delete, and edit properties in the module twin after the module has been deployed to an IoT Edge device.

To define and edit properties for a deployed module:

1. In your IoT hub, select **IoT Edge** from the left pane menu.
1. Select the device with the module you want to edit.
1. On the **Modules** tab, select the module.
1. On the module details page, on the upper menu bar select **Module Identity Twin**.

![Edit properties on the module identity twin](./media/how-to-monitor-iot-edge-deployments/module-twin.png)

### Define targets

You can target the devices to receive the deployment by specifying target conditions when you create or modify a deployment. To define a custom metric:

1. In your IoT hub, select **IoT Edge** from the left pane menu.

    * If you are creating a new deployment, select **Create Deployment** from the upper menu bar. As part of completing the wizard, select the **Target Devices** tab.

    * If you are modifying a deployment, select the **IoT Edge deployments** tab and select the deployment you want to configure. Select the **Target Condition** tab.

1. Define a target condition, using the example syntax as a guide.

![Specify target conditions for a deployment](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

### Define a custom metric

You can define custom metrics in the Azure portal when you create or modify a deployment. To define a custom metric:

1. In your IoT hub, select **IoT Edge** from the left pane menu.

   * If you are creating a new deployment, select **Create Deployment** from the upper menu bar. As part of completing the wizard, select the **Metrics** tab.

   * If you are modifying a deployment, select the **IoT Edge deployments** tab and then select the deployment you want to configure. Select the **Metrics** tab.

1. Define a custom metric, using the example syntax as a guide. 

![Create a custom metric in the deployment wizard](./media/how-to-monitor-iot-edge-deployments/create-custom-metric.png)

## Monitor a deployment with Azure CLI

Use the [az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) command to display the details of a single deployment:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

The deployment show command takes the following parameters:

* **--deployment-id** - The name of the deployment that exists in the IoT hub. Required parameter.
* **--hub-name** - Name of the IoT hub in which the deployment exists. The hub must be in the current subscription. Switch to the desired subscription with the command `az account set -s [subscription name]`

Inspect the deployment in the command window. The **metrics** property lists a count for each metric that is evaluated by each hub:

* **targetedCount** - A system metric that specifies the number of device twins in IoT Hub that match the targeting condition.
* **appliedCount** - A system metric specifies the number of devices that have had the deployment content applied to their module twins in IoT Hub.
* **reportedSuccessfulCount** - A device metric that specifies the number of IoT Edge devices in the deployment reporting success from the IoT Edge client runtime.
* **reportedFailedCount** - A device metric that specifies the number of IoT Edge devices in the deployment reporting failure from the IoT Edge client runtime.

You can show a list of device IDs or objects for each of the metrics by using the [az iot edge deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) command:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

The deployment show-metric command takes the following parameters:

* **--deployment-id** - The name of the deployment that exists in the IoT hub.
* **--metric-id** - The name of the metric for which you want to see the list of device IDs, for example `reportedFailedCount`.
* **--hub-name** - Name of the IoT hub in which the deployment exists. The hub must be in the current subscription. Switch to the desired subscription with the command `az account set -s [subscription name]`.

## Next steps

Learn how [communicate with EdgeAgent using built-in direct methods](how-to-edgeagent-direct-method.md).
