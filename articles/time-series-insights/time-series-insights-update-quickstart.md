---
title: 'Quickstart: Explore the Azure Time Series Insights Preview demo environment | Microsoft Docs'
description: Understand the Azure Time Series Insights Preview demo environment.
ms.service: time-series-insights 
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
---

# Quickstart: Explore the Azure Time Series Insights Preview demo environment

A quickstart is provided to get started with the Azure Time Series Insights Preview in a free demonstration environment. You'll learn how to use your web browser to visualize large volumes of IoT data and tour key features presently in general availability.

Through it, you'll learn how to use your web browser to visualize large volumes of IoT data and tour key features presently in general availability.
Azure Time Series Insights is a fully managed analytics, storage, and visualization service that simplifies how to explore and analyze billions of IoT events simultaneously. It gives you a global view of your data, letting you quickly validate your IoT solution, and avoid costly downtime to mission-critical devices. Through Azure Time Series Insights, you can discover hidden trends, spot anomalies, and conduct root-cause analyses in near real-time.

For additional flexibility, Azure Time Series Insights can be added to a pre-existing application through its powerful REST API and client SDK. The API allow you to store, query time series data, and consume time series data in a client application of your choice. You may also choose to use the client SDK to add UI components to your existing application.

The Time Series Insights explorer is a guided tour of features presently in general availability.















Time Series Insights provides an end-to-end platform as a service (PaaS) offering. It can ingest, process, store, and query highly contextualized, time-series-optimized IoT-scale data for improvised data exploration. It also provides operational analysis. Time Series Insights is a differentiated offering that's tailored to the unique needs of industrial IoT deployments.

The demo environment shows an electricity generation company, Contoso. In the environment, you use Time Series Insights to find actionable insights in Contoso data and conduct a short root-cause analysis. Contoso operates two wind turbine farms, each with 10 turbines. Each turbine has 20 sensors that report data every minute to Azure IoT Hub. The sensors gather information about weather conditions, blade pitch and yaw position, generator performance, gearbox behavior, and safety monitors.

You use Time Series Insights Preview to analyze Contoso's ever-growing dataset from the last two years, which is currently at 40 GB. It can help you to better understand and predict both critical failures and slow-moving maintenance issues.















## Explore the Time Series Insights explorer in a demo environment

The Time Series Insights Preview explorer demonstrates historical data and root causes analysis. To get started:

1. Create a <a href="https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio" target="_blank" rel="external noopener noreferrer">free Azure account</a> if one hasn't been created.

1. Navigate to the <a href="https://insights.timeseries.azure.com/preview/samples" target="_blank" rel="external noopener noreferrer">Contoso Wind Farm demo</a> environment.  

1. If you're prompted, sign in to the Time Series Insights explorer using your Azure account credentials.

### Work with historical data

1. Look at wind turbine **W7** in **Contoso Plant 1**.  

    * Update the view range to **1/1/17 20:00 to 3/10/17 20:00 (UTC)**.
    * Select the **Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed** sensor. Then review the resultant values.

       ![Quickstart one][1]

1. Recently, Contoso found a fire in wind turbine **W7**. Opinions vary about what the proximate cause of the fire was. Upon closer inspection, we see that the fire alert sensor was activated during the fire.

    * Update the view range to **3/9/17 20:00 to 3/10/17 20:00 (UTC)**.
    * Select the **Safety System** > **FireAlert** sensor.

      ![Quickstart two][2]

1. Review other events around the time of the fire to understand what occurred. Both oil pressure and active warnings spiked just before the fire.

    * Select the **Pitch System** > **HydraulicOilPressure** sensor.
    * Select the **Pitch System** > **ActiveWarning** sensor.

      ![Quickstart three][3]

1. The oil pressure and active warning sensors spiked prior to the fire. Expand the displayed time series to see other signs present leading up to the fire. Both sensors fluctuated consistently over time indicating a persistent and worrisome pattern.

    * Update the view range to **2/24/17 20:00 to 3/10/17 20:00 (UTC)**.

      ![Quickstart four][4]

1. Examining two years of historical reveals another fire event with the same sensor fluctuations.

    * Update the view range to **1/1/16 to 12/31/17** (all data).

       ![Quickstart five][5]

Using Azure Time Series Insights and our sensor telemetry, we've discovered a long-term and problematic trend hidden in our historical data. With these new insights, we can explain:

> [!div class="checklist"]
> * What actually occurred
> * Correct the problem
> * Put superior alert notification systems into place.

### Root cause analysis

1. Some scenarios require sophisticated analysis to uncover subtle clues in data. Select the windmill **W6** on date **6/25**

    * Update the view range to **6/1/17 20:00 to 7/1/17 20:00 (UTC)**
    * Then select the **Contoso Plant 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning** sensor.

       ![Quickstart six][6]

1. The warning indicates an issue with the voltage being output by the generator. The overall power output of the generator is operating within normal parameters given our current interval. By increasing our interval, another pattern emerges: there's a definite drop-off.

    * Remove the **VoltageActuatorSwitchWarning** sensor.
    * Select the **Generator System** > **ActivePower** sensor.
    * Update the interval to **3d**.

       ![Quickstart seven][7]

1. By expanding the time range, we can determine whether the issue has stopped or whether it continues.

    * Extend the time span to 60 days.

       ![Quickstart eight][8]

1. Other sensor data points can be added to provide superior context. The more sensors we can view, the fuller our understanding of the problem is. Let’s drop a marker to see the actual values. 

    * Select the **Generator System** > **GridVoltagePhase1**, **GridVoltagePhase2**, and **GridVoltagePhase3** sensors.
    * Drop a marker on the last data point in the visible area.

       ![Quickstart nine][9]

    The three voltage sensors are operating comparably and within normal parameters. It looks like the **GridVoltagePhase3** sensor is the culprit.

1. With highly contextual data added, the phase 3 drop-off appears even more as the problem. At this point, we’re ready to refer the issue to our maintenance team with a good lead on the cause of the warning.  

    * Update the display to overlay all **Generator System** sensors on the same chart scale.

       ![Quickstart ten][10]

## Next steps

You're ready to create your own Time Series Insights Preview environment:

> [!div class="nextstepaction"]
> [Plan your Time Series Insights Preview environment](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
[10]: media/v2-update-quickstart/quickstart-ten.png