---
title: Deploy Azure Blob Storage module - Azure Event Grid IoT Edge | Microsoft Docs 
description: Deploy Azure FunBlob Storage module as a Publisher from Azure portal
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: spelluru 
ms.date: 10/01/2019
ms.topic: article
ms.service: event-grid
services: event-grid
---

# Tutorial: Deploy Azure Blob Storage on IoT Edge module (Preview)

This article shows you how deploy the Azure Blob Storage on IoT module, which would act as an Event Grid publisher to send events on Blob creation and Blob deletion to Event Grid

> [!CAUTION]
> Azure Blob Storage on IoT Edge integration with Event Grid is in Preview

## Prerequisites

To complete this tutorial, you will need:-

* **Azure Event Grid module on an IoT Edge Device**. Follow the steps in described in the [Tutorial: Deploy Event Grid IoT Edge module](deploy-event-grid-portal.md) article if you don't have this set up.

## Select your IoT Edge device

1. Sign in to the [Azure portal](https://portal.azure.com)
1. Navigate to your IoT Hub.
1. Select **IoT Edge** from the menu
1. Select the ID of the target device from the list of devices.
1. Select **Set Modules**

## Configure a deployment manifest

A deployment manifest is a JSON document that describes which modules to deploy, how data flows between the modules, and desired properties of the module twins. The Azure portal has a wizard that walks you through creating a deployment manifest, instead of manually building the JSON document. It has three steps: **Add modules**, **Specify routes**, and **Review deployment**.

### Add modules

1. In the **Deployment Modules** section, select **Add**
1. From the types of modules in the drop-down list, select **IoT Edge Module**
1. Provide the name, image, and container create options of the container:

   * **Name**: azureblobstorageoniotedge
   * **Image URI**: blobstoragebuilds.azurecr.io/azure-blob-storage:linux-amd64-20190917.2-delete-event-2
   * **Container Create Options**:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
       "EVENTGRID_ENDPOINT=http://eventgridmodule:5888"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```
1. Update the JSON that you copied with the following information:

   - Replace `<your storage account name>` with a name that you can remember. Account names should be 3 to 24 characters long, with lowercase letters and numbers. No spaces.

   - Replace `<your storage account key>` with a 64-byte base64 key. You can generate a key with tools like [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). You'll use these credentials to access the blob storage from other modules.

   - Replace `<storage mount>` according to your container operating system.
     - For Linux containers, **my-volume:/blobroot**
     - For Windows containers,**C:/ContainerData:C:/BlobRoot**. Make sure "C:/ContainerData" directory exists in your IoT device.

1. Click **Save**
1. Click **Next** to continue to the routes section

 ### Setup routes

Keep the default routes, and select **Next** to continue to the review section

### Review deployment

1. The review section shows you the JSON deployment manifest that was created based on your selections in the previous section. Confirm that you see the following four modules: **$edgeAgent**, **$edgeHub**, **eventgridmodule** and **subscriber** that were deployed previously.
1. Review your deployment information, then select **Submit**.

## Verify your deployment

1. After you submit the deployment, you return to the IoT Edge page of your IoT hub.
1. Select the **IoT Edge device** that you targeted with the deployment to open its details.
1. In the device details, verify that the azureblobstorageoniotedge module is listed as both **Specified in deployment** and **Reported by device**.

    It may take a few moments for the module to be started on the device and then reported back to IoT Hub. Refresh the page to see an updated status.

## Publish Create and Delete Events

1. This module automatically creates topic **MicrosoftStorage**. Verify that it exists
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

 Sample output:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
1. Subscribers can register for events published to a topic. To receive any event, you'll need to create an Event Grid subscription for **MicrosoftStorage** topic.
    1. Create subscription.json with the following content. For details about the payload, see our [API documentation](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > The **endpointType** property specifies that the subscriber is a **Webhook**.  The **endpointUrl** specifies the URL at which the subscriber is listening for events. This URL corresponds to the Azure Function sample you deployed earlier.

    2. Run the following command to create a subscription for the topic. Confirm that you see the HTTP status code is `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    3. Run the following command to verify subscription was created successfully. HTTP Status Code of 200 OK should be returned.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Sample output:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

1. [Connect to your local storage with Azure Storage Explorer](../azure/iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## Verify Event Delivery

Follow the steps to [verify the event delivery](pub-sub-events-webhook-local.md#step-4-verify-event-delivery)
1. On uploading files as block blobs from Azure Storage Explorer you are publishing create events. Checkout the subscriber logs for create event

Sample Output:
   ```json
        Received event data [
        {
          "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
          "topic": "MicrosoftStorage",
          "subject": "/blobServices/default/containers/cont1/blobs/BurrowTeam.jpg",
          "eventType": "Microsoft.Storage.BlobCreated",
          "eventTime": "2019-10-01T21:35:17.7219554Z",
          "dataVersion": "1.0",
          "metadataVersion": "1",
          "data": {
            "api": "PutBlob",
            "clientRequestId": "00000000-0000-0000-0000-000000000000",
            "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
            "eTag": "0x8D746B740DA21FB",
            "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/BurrowTeam.jpg",
            "contentType": "image/jpeg",
            "contentLength": 858129,
            "blobType": "BlockBlob"
          }
        }
      ]
    ```

1. When you delete blobs from Azure Storage Explorer you are publishing delete events. Checkout the subscriber logs for delete event

Sample Output:
   ```json
        Received event data [
        {
          "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
          "topic": "MicrosoftStorage",
          "subject": "/blobServices/default/containers/cont1/blobs/BurrowTeam.jpg",
          "eventType": "Microsoft.Storage.BlobDeleted",
          "eventTime": "2019-10-01T21:36:09.2562941Z",
          "dataVersion": "1.0",
          "metadataVersion": "1",
          "data": {
            "api": "DeleteBlob",
            "clientRequestId": "00000000-0000-0000-0000-000000000000",
            "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
            "eTag": "0x8D746B740DA21FB",
            "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/BurrowTeam.jpg",
            "contentType": "image/jpeg",
            "contentLength": 858129,
            "blobType": "BlockBlob"
          }
        }
      ]
    ```
## Next steps

Next, learn more about [Azure Blob Storage on IoT Edge](../azure/iot-edge/how-to-store-data-blob.md) and its features.
