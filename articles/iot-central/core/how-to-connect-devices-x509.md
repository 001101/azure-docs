---
title: Connect devices with X.509 certificates in Azure IoT Central Application
description: How to connect devices with X.509 certificates using Node.js device SDK for IoT Central Application
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central

---

# How to connect devices with X.509 certificates using Node.js device SDK for IoT Central Application

IoT Central supports both shared access signatures (SAS) and X.509 certificates to secure the communication between a device and your application. [This tutorial](./tutorial-connect-device-nodejs.md). used SAS. In this article, learn how to modify the code sample to use X.509.  X.509 certificates are recommended in production environments. For more information, [see](./concepts-get-connected.md).

This article shows two ways of using X.509 - group enrollments typically used in a production environment, and individual enrollments useful for testing.

## Prerequisites and set up your environment

- Completion of [Create and connect a client application to your Azure IoT Central application (Node.js)](./tutorial-connect-device-nodejs.md).
- [Git](https://git-scm.com/download/).
- Download and Install [OpenSSL](https://www.openssl.org/). If you're using Windows, you can use the binaries from the [OpenSSL page on SourceForge](https://sourceforge.net/projects/openssl/).



## Generate root and device key

In this section, you will use an X.509 certificate to connect devices for enrollment groups, which are used to enroll multiple related devices.

> [!NOTE]
> This way of generating X.509 certs is just for testing. For a production environment you should use your official, secure mechanism for certificate generation.

1. Open a command prompt. Clone the GitHub repository for the certificate generation scripts:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

2. Navigate to the certificate generator script and install the required packages:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Create a root certificate and then derive a device certificate by running the script. Be sure to only use lower-case alphanumerics and hyphens for certificate name.

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

This will produce three files each for the root and the device certificate

filename | contents
-------- | --------
\<name\>_cert.pem | The public portion of the X509 certificate
\<name\>_key.pem | The private key for the X509 certificate
\<name\>_fullchain.pem | The entire keychain for the X509 certificate.


## Create a group enrollment


1. Now open your IoT Central application and navigate to **Administration**  in the left pane and click on **Device connection**. 

2. Select + **Create enrollment group**, and create a new enrollment group called _MyX509Group_ with an Attestation type of **Certificates (X.509)**:


3. Open the enrollment group you created and click **Manage Primary**. 

4. Select **file** option and upload the root certificate file called mytestrootcert_cert.pem that you generated previously:


    ![Certificate Upload](./media/how-to-connect-devices-x509/certificate-upload.png)



5. To complete the verification, copy the verification code, create an X.509 verification certificate with that code in command prompt.

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

6. Upload the signed verification certificate _verification_cert.pem_ to complete the verification.

    ![Certificate Upload](./media/how-to-connect-devices-x509/verified.png)


You can now connect devices that have an X.509 certificate derived from this primary root certificate. After you save the enrollment group, make a note of the ID Scope.


## Simulate the device


1. In the Azure IoT Central application, Click **Devices**, and create a new device with _mytestdevice_ as the **Device ID** from the Environmental Sensor device template.


2. Copy the _mytestdevice_key.pem_ and _mytestdevice_cert.pem_ to the folder where you created the environmentalSensor.js application when you completed the [Connect a device (Node.js) tutorial](./tutorial-connect-device-nodejs.md).

    ```cmd/sh
    copy .\mytestdevice_cert.pem ..\environmental-sensor\mytestdevice_cert.pem
    copy .\mytestdevice_key.pem ..\environmental-sensor\mytestdevice_key.pem
    ```

3. Navigate to folder that contains the environmentalSensor.js application and run the following command to install the X.509 package:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

4. Edit the **environmentalSensor.js** file.
    - Replace **_ID Scope_** with the `id scope` noted above. 
    - Replace **_Device ID_** with the `device id` noted in **Step 1** above.


5. Edit the `require` statements as follows:

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

6. Edit the section that creates the client as follows. Add the **scope ID** you made a note of previously:


    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = 'Your Scope ID';
    var registrationId = 'mytestdevice';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

7. Modify the section that opens the connection as follows:

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

8. Execute the script and verify the device was provisioned successfully.

    ```cmd/sh
    node environmentalSensor.js
    ```   

You can also verify that telemetry appears on the dashboard.


## Generate self-signed device key


In this section, you will use a self-signed X.509 certificate to connect devices for individual enrollment, which are used to enroll a single device. Self-signed certificates are for testing only.

Create a self-signed X.509 device certificate and then derive an intermediate device certificate from the parent device certificate by running the script. Be sure to only use lower-case alphanumerics and hyphens for certificate name.

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestprimarycert
    node create_test_cert.js device mytestsecondarycert mytestprimarycert
  ```

## Create individual enrollment

1. In the Azure IoT Central application, Click **Devices**, and create a new device with **Device ID** as _mytestprimarycert_ from the Environmental Sensor device template. 

2. Open the device you created and select **Connect**

3. Select **Individual Enrollments** as the Connect Method and **Certificates (X.509)** as mechanism.

    ![Individual enrollment](./media/how-to-connect-devices-x509/individual-device-connect.png)


4. Select **file** option under primary and upload the certificate file called mytestprimarycert_cert.pem that you generated previously. 

5. Upload _mytestsecondarycert_cert.pem_ file under Secondary and click **Save**.

    ![Individual enrollment Certificate Upload](./media/how-to-connect-devices-x509/individual-enrollment.png)

The device is now provisioned with X.509 certificate.

