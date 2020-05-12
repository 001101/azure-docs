---
title: 'Quickstart: Deploy IoMT connector (preview) using Azure portal'
description: In this quickstart, you'll learn how to deploy, configure, and use the IoMT connector feature of Azure API for FHIR using the Azure portal.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart 
ms.date: 05/11/2020
ms.author: punagpal
---

# Quickstart: Deploy IoMT connector (preview) using Azure portal

IoMT connector is an optional feature of Azure API for FHIR that provides the capability to ingest data from Internet of Medical Things (IoMT) devices. In this quickstart, you'll learn how to:
- Deploy and configure IoMT connector using the Azure portal
- Use a simulated device to send data to IoMT connector
- Use Postman to view resources created by  IoMT connector on Azure API for FHIR

## Prerequisites

- An active Azure subscription - [Create one for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR resource - [Deploy Azure API for FHIR using Azure portal](fhir-paas-portal-quickstart.md)

## Go to Azure API for FHIR resource

Open the [Azure portal](https://portal.azure.com) and go to the **Azure API for FHIR** resource for which you'd like to create the IoMT connector feature.

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Azure API for FHIR resource":::

<br>

On the left-hand navigation menu, click on **IoMT connector** under **Add-in** section to open **IoMT connectors** page.

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="IoMT connector feature":::

## Create new IoMT connector (preview)

Click on the **Add** button to open **Create IoMT connector** page and enter settings for the new IoMT connector. Click on **Create** button to install IoMT connector.

> [!NOTE]
> Make sure to select *Create* as the value for the **Resolution type** drop down for this installation. 

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Create IoMT connector":::

|Setting|Value|Description |
|---|---|---|
|Connector name|A unique name|Enter a name to identify your IoMT connector. This name should be unique within an Azure API for FHIR resource.<br><br>The name can only contain lowercase letters, numbers, and the hyphen (-) character. It must start and end with a letter or a number, and must be between 3-24 characters in length.|
|Resolution type|Lookup or Create|Select **Lookup** if you have an out-of-band process to create device and patient FHIR resources in your Azure API for FHIR. IoMT connector will use reference to these resources when creating an Observation resource representing device data. Select **Create** when you want IoMT connector to create bare-bones device and patient resources in your Azure API for FHIR using respective identifier values present in the device message.|

Confirm creation and await IoMT connector deployment.

## Configure IoMT connector

Select the newly deployed connector to open **IoMT connector** page.

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Create Azure API for FHIR account":::

<br>

IoMT connector needs two mapping templates to transform device message into FHIR-based Observation resource(s):

- **Device Mapping**: Device mapping template transforms a device message into a normalized schema. Select **Configure device mapping** to open **Device mapping** page. Add the following script to the JSON editor on the page and select **Save**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

<br>

- **FHIR Mapping**: FHIR mapping template transforms a normalized message to a FHIR-based Observation resource. Select **Configure FHIR mapping** to open **FHIR mapping** page. Add the following script to the JSON editor on the page and select **Save**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

## Set up a device simulator

Set up a device simulator by creating a [continuous patient monitoring application](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template) on Azure IoT Central. 

This application comes with two device simulators: Smart Vitals Patch and Smart Knee Brace. The mapping templates configured in the previous section, use heart rate value generated by **Smart Vitals Patch** to  create an FHIR-based Observation resource for it. 

## Send device simulator data to IoMT connector

Connect and start sending device data to IoMT connector by [setting up data export on your device simulator](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export). Make sure to choose *Azure Event Hubs* as the export destination, use same name as your IoMT connector for **Event Hubs namespace**, *devicedata* for **Event Hub**, and keep **Telemetry** option *On* for **Data to Export**.

## View device data in Azure API for FHIR

You can view the FHIR-based Observation resource(s) created by IoMT connector on Azure API for FHIR using Postman. Set up your [Postman to access Azure API for FHIR](access-fhir-postman-tutorial) and make a `GET` request to `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` to view Observation resources with heart rate value. 

> [!TIP]
> Ensure that your user has appropriate access to Azure API for FHIR data plane. Use [Azure Role Based Access Control](configure-azure-rbac.md) to assign required data plane roles.

## Clean up resources

When no longer needed you can delete the IoMT connector by removing associated resource group or Azure API for FHIR, or the IoMT connector itself. 

To directly remove an IoMT connector, go to **IoMT Connector** page for the connector to be deleted, and click on **Delete** button. Select **Yes** when asked for confirmation. 

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Delete IoMT connector":::

## Next steps

In this quickstart guide, you've deployed IoMT connector feature in your Azure API for FHIR resource. Select from below next steps to learn more about IoMT connector:

>[!div class="nextstepaction"]
>[IoMT connector architecture](to-be-filled.md)

>[!div class="nextstepaction"]
>[IoMT connector mapping templates](to-be-filled.md)

>[!div class="nextstepaction"]
>[Export Apple HealthKit data](to-be-filled.md)

>[!div class="nextstepaction"]
>[IoMT connector FAQs](to-be-filled.md)
