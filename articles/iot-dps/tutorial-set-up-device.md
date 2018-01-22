---
title: Set up device for the Azure IoT Hub Device Provisioning Service
description: Set up device to provision via the IoT Hub Device Provisioning Service during the device manufacturing process
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 01/23/2018
ms.topic: tutorial
ms.service: iot-dps

documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
---

# Set up a device to provision using the Azure IoT Hub Device Provisioning Service

In the previous tutorial, you learned how to set up the Azure IoT Hub Device Provisioning Service to automatically provision your devices to your IoT hub. This tutorial provides guidance for setting up your device during the manufacturing process, so that you can configure the Device Provisioning Service for your device based on its [Hardware Security Module (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security), and the device can connect to your Device Provisioning service when it boots for the first time. This tutorial discusses the processes to:

> [!div class="checklist"]
> * Select a Hardware Security Module
> * Build platform-specific Device Provisioning Client SDK components for the selected HSM
> * Extract the security artifacts
> * Set up the Device Provisioning Service configuration on the device

## Prerequisites

Before proceeding, create your Device Provisioning Service instance and an IoT hub, using the instructions mentioned in the previous [1 - Set up cloud resources](./tutorial-set-up-cloud.md) tutorial.

### Decide on a Hardware Security Module

As a device manufacturer, you need to select Hardware Security Modules (or HSMs) that are based on one of the following types. Currently, the [Device Provisioning Service client SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) provides support for the following HSMs: 

- [Trusted Platform Module (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): TPM is an established standard for most Windows-based device platforms, as well as a few Linux/Ubuntu based devices. As a device manufacturer, you may choose this HSM if you have either of these OSes running on your devices, and if you are looking for an established standard for HSMs. With TPM chips, you can only enroll each device individually to the Device Provisioning Service. For development purposes, you can use the TPM simulator on your Windows or Linux development machine.

- [X.509](https://cryptography.io/en/latest/x509/): X.509 based HSMs are relatively newer chips, with work currently progressing within Microsoft on RIoT or DICE chips which implement the X.509 certificates. With X.509 chips, you can do bulk enrollment in the portal. It also supports certain non-Windows OSes like embedOS. For development purpose, the Device Provisioning Service client SDK supports an X.509 device simulator. 

See [IoT Hub Device Provisioning Service security concepts](concepts-security.md) for more details on HSM types.  

>[!NOTE]
> This tutorial assumes the use of the [Azure IoT SDKs and libraries for C](https://github.com/Azure/azure-iot-sdk-c). See the IoT Hub Device Provision Service Quickstarts to the left, for details on available SDK support for additional languages.

## Build platform-specific SDK components for the selected HSM

The Device Provisioning Service Client SDK helps implement the selected security mechanism in software. The following steps show how to use the SDK to build additional components for the selected HSM chip:

1. If you followed one of the Quickstarts to [create a simulated TPM device](./quick-create-simulated-device.md) or [create a simulated X.509 device](./quick-create-simulated-device-x509.md), you are ready to build the SDK components and can jump to step #2. 

   If not, follow the **first four** steps from the section titled [Prepare the development environment](./quick-create-simulated-device.md#setupdevbox). These steps walk you through installation of required tools, and clone the GitHub repository that contains the Device Provisioning Service Client SDK. When finished, open a command prompt and change into the repositories "cmake" subdirectory:

        ```cmd/sh
        cd azure-iot-sdk-c/cmake
        ```

2. Build the SDK components for the type of HSM you have selected for your device, using either one of the following commands on the command prompt:
    - For TPM devices:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - For TPM simulator:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - For X.509 devices and simulator:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

> [!IMPORTANT]
> The Device Provisioning System Client SDK provides TPM and X.509 HSM support for devices running on Windows or Ubuntu implementations. For guidance on these supported HSMs, proceed to the section titled [Extract the security artifacts](#extractsecurity). 
>
> For all other devices, you'll need to write custom code for your particular HSM chip. The following [Support for custom TPM and X.509 devices](#customhsm) section provides guidance on porting the SDK for your specific platform needs.

<a id="customhsm"></a>
### Support for custom TPM and X.509 devices (optional)

First you will need to develop your custom HSM repository and library:

1. Develop a library to access your HSM. This project needs to produce a static library for the Device Provisioning SDK to consume.

2. Implement the functions defined in the following header file, in your library: 

    - For custom TPM: implement the Custom HSM functions defined under [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).  
    - For custom X.509: implement the Custom HSM functions defined under [HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

Once your library successfully builds on its own, you'll need to integrate it with the Device Provisioning Service Client SDK, by linking against your library:

1. Supply the custom HSM GitHub repository, the library path and its name in the following cmake command:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Open the SDK in Visual Studio and build it. 

    - The build process will compile the SDK library.
    - The SDK will attempt to link against the custom HSM defined in the cmake command.

3. Run the `\azure-iot-sdk-c\provisioning_client\samples\prov_dev_client_ll_sample\prov_dev_client_ll_sample.c` sample to verify if your HSM is implemented correctly.

<a id="extractsecurity"></a>
## Extract the security artifacts

The next step is to extract the security artifacts for the HSM on your device.

1. For a TPM device, you will need to find out the **Endorsement Key** associated with it from the TPM chip manufacturer. You can derive a unique **Registration ID** for your TPM device by hashing the endorsement key. 
2. For an X.509 device, you will need to obtain the certificates issued to your device(s) - end-entity certificates for individual device enrollments, while root certificates for group enrollments of devices.

These security artifacts are required to enroll your devices to the Device Provisioning Service. The provisioning service then waits for any of these devices to boot and connect with it at any later point in time. See [How to manage device enrollments](how-to-manage-enrollments.md) for information on how to use these security artifacts to create enrollments. 

When your device boots for the first time, the client SDK interacts with your chip to extract the security artifacts from the device, and verifies registration with your Device Provisioning service. 


## Set up the Device Provisioning Service configuration on the device

The last step in the device manufacturing process is to write an application that uses the Device Provisioning Service client SDK to register the device with the service. This SDK provides the following APIs for your applications to use:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Remember to initialize the `id_scope` constant and `hsm_type` variable as mentioned in the [Simulate first boot sequence for the device section of this quick start](./quick-create-simulated-device.md#firstbootsequence), before using them. The Device Provisioning client registration API `Prov_Device_LL_Create` connects to the global Device Provisioning Service. The *ID Scope* is generated by the service and guarantees uniqueness. It is immutable and used to uniquely identify the registration IDs. The `iothub_uri` allows the IoT Hub client registration API `IoTHubClient_LL_CreateFromDeviceAuth` to connect with the right IoT hub. 


These APIs help your device to connect and register with the Device Provisioning Service when it boots up, get the information about your IoT hub and then connect to it. The file [`provisioning_client/samples/prov_client_ll_sample/prov_client_ll_sample.c`](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_ll_sample/prov_dev_client_ll_sample.c) shows how to use these APIs. In general, you need to create the following framework for the client registration:

```C
static const char* global_uri = "global.azure-devices-provisioning.net";
static const char* id_scope = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    prov_dev_security_init(hsm_type); // initialize your HSM 

    prov_transport = Prov_Device_HTTP_Protocol;
    
    PROV_CLIENT_LL_HANDLE handle = Prov_Device_LL_Create(global_uri, id_scope, prov_transport); // Create your provisioning client

    if (Prov_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
	    do {
   		// The register_callback is called when registration is complete or fails
    		Prov_Client_LL_DoWork(handle);
	    } while (user_info.reg_complete == 0);
    }
    Prov_Client_LL_Destroy(handle); // Clean up the Provisioning client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

You may refine your Device Provisioning Service client registration application using a simulated device at first, using a test service setup. Once your application is working in the test environment, you can build it for your specific device and copy the executable to your device image. Do not start the device yet, you need to [enroll the device with the Device Provisioning Service](./tutorial-provision-device-to-hub.md#enrolldevice) before starting the device. See the Next Steps section to learn more about this process. 

## Clean up resources

At this point, you might have set up the Device Provisioning and IoT Hub services in the portal. If you wish to abandon the device provisioning setup, and/or delay using any of these services, we recommend shutting them down to avoid incurring unnecessary costs.

1. From the left-hand menu in the Azure portal, click **All resources** and then select your Device Provisioning service. At the top of the **All resources** blade, click **Delete**.  
1. From the left-hand menu in the Azure portal, click **All resources** and then select your IoT hub. At the top of the **All resources** blade, click **Delete**.  


## Next steps
In this tutorial, you learned how to:

> [!div class="checklist"]
> * Select a Hardware Security Module
> * Build platform-specific Device Provisioning Client SDK components for the selected HSM
> * Extract the security artifacts
> * Set up the Device Provisioning Service configuration on the device

Advance to the next tutorial to learn how to provision the device to your IoT hub by enrolling it to the Azure IoT Hub Device Provisioning Service for auto-provisioning.

> [!div class="nextstepaction"]
> [Provision the device to your IoT hub](tutorial-provision-device-to-hub.md)

