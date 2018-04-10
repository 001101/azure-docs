---
# Mandatory fields. See more on aka.ms/skyeye/meta.
title: Visualize Device Data with Time Series Insights | Microsoft Docs
description: Learn how to set up a Time Series Insights environment, then explore and analyze the time series data of your IoT solutions or connected things.
services: ''
suite: iot-suite
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/06/2018
ms.topic: article
ms.service: iot-suite
---
# Visualize Device Data with Time Series Insights

Learn how to set up a Time Series Insights environment in order to explore and analyze the data of your IoT solutions or connected things.

## Prerequisites

To complete this tutorial, you should have the following:

* An active Azure subscription.
* An Azure Iot hub under your subscription.

## Create a consumer group in your IoT hub

Consumer groups are used by applications to pull data from Azure IoT Hub. Create a consumer group to be used by Time Series Insights for data exploration. 

The following command will create a new consumer group:

     ```azurecli-interactive
    az iot hub consumer-group create --hub-name howto-iot-hub --name timeseriesinsights --resource-group howto-rg
    ```

## Create a new Time Series Insights environment 

Azure Time Series Insights is a fully managed analytics, storage, and visualization service for managing IoT-scale time-series data in the cloud. It provides massively scalable time-series data storage and enables you to explore and analyze billions of events streaming in from all over the world in seconds. Use Time Series Insights to store and manage terabytes of time-series data, explore, and visualize billions of events simultaneously, conduct root-cause analysis, and to compare multiple sites and assets. 

1. Sign in to the [Azure portal](http://portal.azure.com/).

1. Select **Create a resource** > **Internet of Things** > **Time Series Insights**.

    ![Create Time Series Insights](media/iot-suite-time-series-insights/01_Create_Time_Series_Insights.png)

1. To create your Time Series Insights environment, use the values in the following table:

    | Setting | Value |
    | ------- | ----- |
    | Environment Name | The following screenshot uses the name **howto-iot-tsi**. Choose your own unique name when you complete this step. |
    | Subscription | Select your Azure subscription in the drop-down. |
    | Resource group | **Create new**. We are using the name **howto-rg**. |
    | Location | We are using **West US**. You can choose the location closest to you. |
    | Sku |**S1** |
    | Capacity | **1** |
    | Pin to dashboard | **Yes** |

    ![Create Time Series Insights](media/iot-suite-time-series-insights/02_Create_Time_Series_Inisghts_Submit.png)

1. Click **Create**. It can take a moment for the environment to be created.

1. Make a note of the Time Series Insights environment name you chose. 

## Create Event Source

Create a new Event Source to connect to your IoT hub. Make sure that you use the consumer group created in the previous steps. Time Series Insights requires each service to have a dedicated consumer group not in use by another service.

1. Navigate to your new Time Series Environment.

2. On the left, select **Event Sources** and then click **Add**.

![Create Event Source](media/iot-suite-time-series-insights/03_Create_Event_Source.png)

3. To configure your IoT hub as a new event source, use the values in the following table:

    | Setting | Value |
    | ------- | ----- |
    | Event source Name | The following screenshot uses the name **howto-iot-hub**. Use your own unique name when you complete this step. |
    | Source | **IoT Hub** |
    | Import option | **Use IoT Hub from available subscriptions** |
    | Subscription Id | Select your Azure subscription in the drop-down. |
    | Iot hub name | **howto-iot-hub** Use the name of your IoT hub. |
    | Iot hub policy name | **iothubowner** Ensure the policy used is an owner policy. |
    | Iot hub policy key | This field is populated automatically. |
    | Iot hub consumer group | **timeseriesinsights** |
    | Event serialization format | **JSON**     | Timestamp property name | Leave blank |

![Imported Script](media/iot-suite-time-series-insights/04_Create_Event_Source_Submit.png "Create Event Source")

4. Click **Create**.

## Configure data access

To allow additional users to explorer data in Time Series Insights, configure your Data Access Policy with the appropriate permissions.

    NOTE: A user will see a Data Access Policy Error if they don't have permission to explore the data.

![Data Access Policy Error](media/iot-suite-time-series-insights/16_data_access_poliy_error.png)

1. Navigate to the Environment Topology and select **Data Access Policies**.

![Select Data Access Policy](media/iot-suite-time-series-insights/15_data_access_policy.png)

2. Click **Add**.

![Add User and Role](media/iot-suite-time-series-insights/17_add_user_role.png)

3. Click **Select role** and select the **Contributor** role.

![Select Contributor Role](media/iot-suite-time-series-insights/18_select_controbutor_role.png)

4. Click **Select user**, search for your user name or email address, then click **Select**.

![Imported Script](media/iot-suite-time-series-insights/19_select_user.png "Select User")

For more information on Time Series Insights data access policies, see [Grant data access to a Time Series Insights environment using Azure portal](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access).

## Time Series Insights Explorer

The Time Series Insights explorer is a web app that helps you create visualizations of your data.

1. Select the **Overview** tab.

2. Click **Go To Environment**, which will open the Time Series Insights explorer web app.

![Time Series Insights Explorer](media/iot-suite-time-series-insights/05_GoTo_TSI_Explorer.png)

To learn about how to explore and query data in the Time Series Insights explorer, see [Azure Time Series Insights explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).