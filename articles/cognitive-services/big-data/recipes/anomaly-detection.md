---
title: "Recipe: Using Cognitive Services on Spark for Predictive Maintenance"
titleSuffix: Azure Cognitive Services
description: This quickstart shows how to create a searchable art database using Azure Search and MMLSpark.
services: cognitive-services
author: v-jawe
manager: nitinme

ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: recipe
ms.date: 6/10/2020
ms.author: v-jawe
ms.custom: tracking-python
---

# Recipe: Using Cognitive Services on Spark for Predictive Maintenance

This example will walk you through how you can use Azure Synapse Analytics and Cognitive Services on Spark for predictive maintenance of IoT devices. This will follow closely eample frm [CosmosDB and Synapse Link](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) sample), but simplicity's sake, we will read the data straight from a CSV file rather than streaming through CosmosDB and Synapse Link. We strongly encourage you to check out the Synapse Link sample as well.

## Business Scenario
(From CosmosDB sample) The hypothetical scenario is Power Plant, where IoT devices are monitoring  [steam turbines](https://en.wikipedia.org/wiki/Steam_turbine). The IoTSignals collection has Revolutions per minute (RPM) and Megawatts (MW) data for each turbine. Signals from steam turbines are being analyzed and nomalous signals are detected.

There could be outliers in the data in random frequency. In those situations, RPM values will go up and MW output will go down, for circuit protection. The idea is to see the data varying at the same time, but with different signals.

## Prerequisites
* An Azure subscription - [Create one for free](https://azure.microsoft.com/free/)
* [Azure Synapse workspace](https://docs.microsoft.com/en-us/azure/synapse-analytics/quickstart-create-workspace) configured with a [Spark pool](https://docs.microsoft.com/en-us/azure/synapse-analytics/quickstart-create-apache-spark-pool)

## Setup
### Create an Anomaly Detector Resource
Azure Cognitive Services are represented by Azure resources that you subscribe to. Create a resource for Translator using the  [Azure portal](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-apis-create-account)  or  [Azure CLI](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-apis-create-account-cli)  on your local machine. You can also:

-   Get a  [trial key](https://azure.microsoft.com/try/cognitive-services)  valid for 7 days for free. After signing up, it will be available on the Azure website.
-   View an existing resource in the  [Azure portal](https://portal.azure.com/).

Be sure to make a note of the endpoint and the key for this resource.

### Load data into Azure Synapse Analytics
1. Using the Data / Linked tab of your Synapse workspace, create IoTData folder within the root directory of the storage account that is attached to the Synapse workspace.
2. Upload the two CSV files downloaded from [this folder](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples/tree/master/IoT/IoTData).
3. Using the Azure Portal, go to the Access Control (IAM) tab of the storage account associated with Synapse workspace, click on the +Add and Add a role assignment and add yourself to the Data Contributor role. This is needed for any spark metadata operations such as creating databases and tables using the Azure Synapse Spark Pool.

## Read data from storage into a DataFrame
We will read the IoTSignals file into a DataFrame. Open a new notebook in your Synapse workspace and create a DataFrame from the file.
```python
dfDeviceInfo = (spark
                .read
                .csv("/IoTData/IoTSignals.csv", header=True)
              )
```
 Perform anomaly detection using Cognitive Services on Spark
Our goal is to find instances where the signals from the IoT devices were outputting anomalous values so that we can see when something is going wrong and perform predictive maintenance. To do that, we use Anomaly Detector on Spark:
```python
from pyspark.sql.functions import col
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

ateor = (SimpleDetectAnomalies()
                            .setSubscriptionKey("paste-your-key-here")
                            .setUrl("paste-your-endpoint-here")
                            .setOutputCol("anomalies")
                            .setGroupbyCol("grouping")
                            .setSensitivity(95)
                            .setGranularity("secondly"))

df_anomaly = (df_IoTSignals
                    .where(col("unitSymbol") == 'RPM')
                    .withColumnRenamed("dateTime", "timestamp")
                    .withColumn("value", col("measureValue").cast("double"))
                    .withColumn("grouping", struct("deviceId"))
                    .mlTransform(ateeor))

df_anomaly.createOrReplaceTempView('df_anomaly')
```
Replace "paste-your-key-here" and "paste-your-endpoint-here" with the key and the endpoint for your Ator resource, respectively.

 ## Visualize anomalies for one of the devices
IoTSignals.csv has signals from multiple IoT devices. We will focus on a specific device and visualize anomalous outputs from the device.
```python
df_anomaly_single_device = spark.sql("select timestamp \                                           , measureValue \
                                           , anomalies.expectedValue \
                                           , anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue \
                                           , anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue \
                                           , case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly \
                                      from df_anomaly \
                                      where deviceid = 'dev-1' and timestamp < '2020-04-29'\
                                      order by timestamp \
                                      limit 200")
```
```python
import chart_studio.plotly as py
import plotly.graph_objs as go
from plotly.offline import plot
import matplotlib.pyplot as plt
from pyspark.sql.functions import col
from matplotlib.pyplot import figure
 
adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas() 

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25)
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2)
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2)
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25)
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro')
plt.legend(['RPM-UpperMargin', 'RPM-ExpectedValue', 'RPM-ActualValue', 'RPM-LowerMargin', 'RPM-Anomaly'])
plt.title('RPM Anomalies with Expected, Actual, Upper and Lower Values')
plt.show()
```
## Next steps
Check out how to do predictive maintenance at scale using Azure Cognitive Services, Azure Synapse Analytics, and Azure CosmosDB [here](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples/tree/master/IoT).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjg4NTU2ODU5LDM1NTcwMzcwNywtNjUwNj
k3NTI2LDIwNjA1MTkyNDEsMTMxMDY0ODkzNCwtMjY3NTIzNzcx
LC03ODA2MTY2MTcsLTEwMDc2NTk2MywtMTMyMTI0NTQxOSw4OD
IyMjgyNzAsMTE3NTE4NDIzLC02NTE5ODIzNjQsLTY1MTk4MjM2
NCw0MTQyMjY2ODMsNjUxOTAzMDgwLC0xMjM5OTU0MTU2LDE4Nz
c5MDk1NTQsMTI0ODI1MTA0LC02NjgwNzQ5NzYsLTk2NDIwNzg3
XX0=
-->