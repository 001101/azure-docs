---
title: Set up IoT Edge Modules
description: In this section, we will set up IoT Edge modules and connections
keywords: 
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
---
# Set up IoT Edge Modules

In this tutorial, we will be setting up the below IoT Edge modules
1.  Azure SQL Edge 
2.  Data generator IoT Edge module 

Before proceeding, create an Azure Stream Analytics Module that will be used in the tutorial for predicting Iron Ore impurities. You can learn more about using streaming jobs with SQL Edge [here](https://docs.microsoft.com/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)

The Azure Stream Analytics job is created with hosting environment set as Edge. Now, proceed with setting up the Inputs and Outputs specifically for the tutorial. 

1.  For creating the **input**, click '+Add stream input'. Fill the details section as indicated below, 

Field|Value
-----|-----
Event Serialization format|JSON
Encoding|UTF-8
Event compression type|None

2. For creating the **output**, click '+Add' and choose SQL Database. For the purpose of this tutorial, fill the details section as indicated below, 
> [!NOTE]
> The password specified in this sections need to be specified for SQL SA password when deploying the SQL Edge module in section **"Deploying the Azure SQL Edge module"**
Field|Value
-----|-----
Database|IronOreSilicaPrediction
Server name|tcp:.,1433
Username|sa
Password|Specify a strong password 
Table|IronOreMeasurements1

3. Navigate to the **Query** Section and ensure that the query is set up as below 

  SELECT * INTO *name_of_your_output_stream* FROM *name_of_your_input_stream*

4. Under **Configure**, select **Publish**, and then select the **Publish** button. Save the SAS URI for use with the SQL Database Edge module.

## Container Registry Credentials
The credentials to the container registries hosting module images need to be specified. These can be found in the container registry that was created in your resource group. Navigate to **Access Keys** section. Make note of the below fields:
1. Registry name
2. Login server
3. Username
4. Password

Now, specify the container credentials in the IoT Edge module.

1. Navigate to the IoT Hub that was created in your resource group.
2. In the **IoT Edge** section under **Automatic Device Management**, click on the **Device ID**. For the purpose of this tutorial, it will be IronOrePredictionDevice.
3. Click the **Set Modules** section. 
4. Under **Container Registry Credentials** ensure the fields are filled as indicated below 
 
  _Field_|_Value_
  -------|-------
  Name|Registry name
  Address|Login server
  User Name|Username
  Password|Password
  
## Deploying the data generator module 

1. In the **IoT Edge Modules section**, click **'+ ADD'** and select **IoT Edge module**. 
2. Provide and IoT Edge Module Name and the Image URI. 
  The Image URI can be found in the container registry in the resource group. Click the **Repositories** section under **Services**. For  the purpose of this tutorial, click the repository named silicaprediction. Select the appropriate tag. The Image URI will be of the format 
```
*login server of the containerregistry*/*repository name*:*tag name*
```
Example
```
ASEdemocontregistry.azurecr.io/silicaprediction:amd64
```
3. CLick Add.

## Deploying the Azure SQL Edge module

1. First, deploy the Azure SQL Edge module by following the steps listed [here](https://docs.microsoft.com/azure/sql-database-edge/deploy-portal#deploy-sql-database-edge)

2. On the **Specify Route** of the **Set Modules** page, specify the routes for module to IoT Edge Hub communication as below 
```
FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
```
Example
```
FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
```
3. In the **module twin** settings, ensure that the SQLPackage and ASAJonInfo are updated with the relevant SAS URLs that you have saved through the demo. 

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## Next Steps

* [Deploying ML model on Azure SQL Edge using ONNX ](run-ml-model-on-sql-edge.md)