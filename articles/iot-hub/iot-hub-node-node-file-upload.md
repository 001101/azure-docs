---
title: Upload files from devices to Azure IoT Hub with Node | Microsoft Docs
description: How to upload files from a device to the cloud using Azure IoT device SDK for Node.js. Uploaded files are stored in an Azure storage blob container.
services: iot-hub
documentationcenter: nodejs
author: msebolt
manager: timlt
editor: ''

ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo

---
# Upload files from your device to the cloud with IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

This tutorial builds on the code in the [Send Cloud-to-Device messages with IoT Hub](iot-hub-node-node-c2d.md) tutorial to show you how to use the [file upload capabilities of IoT Hub](iot-hub-devguide-file-upload.md) to upload a file to [Azure blob storage](../storage/index.yml). The tutorial shows you how to:

- Securely provide a device with an Azure blob URI for uploading a file.
- Use the IoT Hub file upload notifications to trigger processing the file in your app back end.

The [Get started with IoT Hub](iot-hub-node-node-getstarted.md) and [Send Cloud-to-Device messages with IoT Hub](iot-hub-node-node-c2d.md) tutorials show the basic device-to-cloud and cloud-to-device messaging functionality of IoT Hub. However, in some scenarios you cannot easily map the data your devices send into the relatively small device-to-cloud messages that IoT Hub accepts. For example:

* Large files that contain images
* Videos
* Vibration data sampled at high frequency
* Some form of preprocessed data.

These files are typically batch processed in the cloud using tools such as [Azure Data Factory](../data-factory/introduction.md) or the [Hadoop](../hdinsight/index.md) stack. When you need to upland files from a device, you can still use the security and reliability of IoT Hub.

At the end of this tutorial you run two Node.js console apps:

* **SimulatedDevice.js**, which uploads a file to storage using a SAS URI provided by your IoT hub.
* **ReadFileUploadNotification.js**, which receives file upload notifications from your IoT hub.

> [!NOTE]
> IoT Hub supports many device platforms and languages (including C, .NET, Javascript, Python, and Java) through Azure IoT device SDKs. Refer to the [Azure IoT Developer Center] for step-by-step instructions on how to connect your device to Azure IoT Hub.

To complete this tutorial, you need the following:

* Node.js version 4.0.x or later.
* An active Azure account. (If you don't have an account, you can create a [free account](http://azure.microsoft.com/pricing/free-trial/) in just a couple of minutes.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## Upload a file from a device app

In this section, you create the device app to upload a file to IoT hub.

1. Create an empty folder called ```simulateddevice```.  In the ```simulateddevice``` folder, create a package.json file using the following command at your command prompt.  Accept all the defaults:

    ```cmd/sh
    npm init
    ```

1. At your command prompt in the ```simulateddevice``` folder, run the following command to install the **azure-iot-device** Device SDK package and **azure-iot-device-mqtt** package:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Using a text editor, create a **SimulatedDevice.js** file in the ```simulateddevice``` folder.

1. Add the following ```require``` statements at the start of the **SimulatedDevice.js** file:

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Add a ```deviceconnectionstring``` variable and use it to create a **Client** instance.  Replace ```{deviceconnectionstring}``` with the name of the device you created in the _Create an IoT Hub_ section:

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > For the sake of simplicity the connection string is included in the code: this is not a recommended practice and depending on your use-case and architecture you may want to consider more secure ways of storing this secret, like using arguments taken from the command-line.

1. Add the following code to connect the client:

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Create a callback and use the **uploadToBlob** function to upload the file.

    ```nodejs
    function printResultFor() {
      return function printResult(err) {
        if (err) console.log(op + ' error: ' + err.toString());
      };
    }
    
    fs.stat(filename, function (err, stats) {
    	const rr = fs.createReadStream(filename);
    
    	client.uploadToBlob(filename, rr, stats.size, function (err) {
          if (err) {
            console.error('Error uploading file: ' + err.toString());
          } else {
            console.log('File uploaded');
          }
        });
    });
    ```

1. Save and close the **SimulatedDevice.js** file.

1. Copy an image file to the `simulateddevice` folder and rename it `myimage.png`.

## Receive a file upload notification

In this section, you create a Node.js console app that receives file upload notification messages from IoT Hub.

You can use the **iothubowner** connection string from your IoT Hub to complete this section. You will find the connection string in the [Azure portal](https://portal.azure.com/) on the **Shared access policy** blade.

1. Create an empty folder called ```fileuploadnotification```.  In the ```fileuploadnotification``` folder, create a package.json file using the following command at your command prompt.  Accept all the defaults:

    ```cmd/sh
    npm init
    ```

1. At your command prompt in the ```fileuploadnotification``` folder, run the following command to install the **azure-iothub** SDK package:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Using a text editor, create a **FileUploadNotification.js** file in the ```fileuploadnotification``` folder.

1. Add the following ```require``` statements at the start of the **FileUploadNotification.js** file:

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Add a ```iothubconnectionstring``` variable and use it to create a **Client** instance.  Replace ```{iothubconnectionstring}``` with the connection string to the IoT hub you created in the _Create an IoT Hub_ section:

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > For the sake of simplicity the connection string is included in the code: this is not a recommended practice and depending on your use-case and architecture you may want to consider more secure ways of storing this secret, like using arguments taken from the command-line.

1. Add the following code to connect the client:

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Create a callback and use the **getFileNotificationReceiver** function to recieve status updates.

    ```nodejs
    function receiveFileUploadNotification(err, receiver){
      receiver.on('message', function (msg) {
        console.log('File upload from device:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(receiveFileUploadNotification);
      }
    });
    ```

1. Save and close the **FileUploadNotification.js** file.

## Run the applications

Now you are ready to run the applications.

At a command prompt in the `fileuploadnotification` folder, run the following command:

```cmd/sh
node FileUploadNotification.js
```

At a command prompt in the `simulateddevice` folder, run the following command:

```cmd/sh
node SimulatedDevice.js
```

The following screenshot shows the output from the **SimulatedDevice** app:

![Output from simulated-device app](media/iot-hub-node-node-upload/simulated-device.png)

The following screenshot shows the output from the **FileUploadNotification** app:

![Output from read-file-upload-notification app](media/iot-hub-node-node-upload/read-file-upload-notification.png)

You can use the portal to view the uploaded file in the storage container you configured:

![Uploaded file](media/iot-hub-node-node-upload/uploaded-file.png)

## Next steps

In this tutorial, you learned how to use the file upload capabilities of IoT Hub to simplify file uploads from devices. You can continue to explore IoT hub features and scenarios with the following articles:

* [Create an IoT hub programmatically][lnk-create-hub]
* [Introduction to C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks]

<!-- Images. -->
[50]: ./media/iot-hub-node-node-file-upload/run-apps1.png
[1]: ./media/iot-hub-node-node-file-upload/image-properties.png
[3]: ./media/iot-hub-node-node-file-upload/enable-file-notifications.png

<!-- Links -->
[Azure IoT Developer Center]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]:../storage/common/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-windows-iot-edge-simulated-device.md


