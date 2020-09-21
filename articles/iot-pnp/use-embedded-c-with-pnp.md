# Using IoT Plug and Play with the Embedded C SDK

## Overview

If you are developing for **constrained devices**, then you can use IoT Plug and Play with the [Embedded C SDK](https://aka.ms/embeddedcsdk). This page serves as a landing page for samples showcasing Azure IoT Plug and Play for various constrained scenarios. 

## Using the Embedded C SDK

[Fill in with overview]

### Use a real device

For a complete end to end tutorial of using the Embedded C SDK with the Device Provisioning Service and IoT Plug and Play on a real device, see the following sample repository:

- [PIC-IoT Wx Development Board](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)

### Introductory Embedded C SDK & IoT Plug and Play Samples

The Embedded C SDK repository contains [several samples](https://github.com/Azure/azure-sdk-for-c/tree/1.0.0-preview.4/sdk/samples/iot#iot-hub-plug-and-play-sample) showcasing how to use Azure IoT Plug and Play. 

*Note that these samples are shown running on Windows and Linux for educational and debugging purposes-- in production these are intended for constrained devices only.*

- [Thermostat example with Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Temperature Controller example with the Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/blob/1.0.0-preview.4/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)


## Using Azure RTOS

Azure RTOS includes a lightweight layer that adds native connectivity to Azure IoT Cloud services. This provides a simple mechanism to connect constrained devices to Azure IoT while also utilizing the advanced features of Azure RTOS.

For complete tutorials on how to get started on different devices with Azure RTOS and IoT Plug and Play, see the following samples:

### Microchip ATSAME54-XPRO
- [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) (Git)
- [IAR](https://aks.ms/azrtos-sample/e54-iar) (zip)
- [MPLAB](https://aks.ms/azrtos-sample/e54-mplab) (zip)

### MXCHIP AZ3166
- [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166) (Git)

### NXP MIMXRT1060-EVK
- [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) (Git)
- [IAR](https://aks.ms/azrtos-sample/rt1060-iar) (zip)
- [MCUXpresso](https://aks.ms/azrtos-sample/rt1060-mcuxpresso) (zip)

### STMicroelectronics 32F746GDISCOVERY
- [IAR](https://aks.ms/azrtos-sample/f746g-iar) (zip)
- [STM32Cube](https://aks.ms/azrtos-sample/f746g-cubeide) (zip)

### STMicrelectronics B-L475E-IOT01
- [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) (Git)
- [IAR](https://aks.ms/azrtos-sample/l475-iar) (zip)
- [STM32Cube](https://aks.ms/azrtos-sample/l475-cubeide) (zip)

### STMicrelectronics B-L4S5I-IOT01
- [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) (Git)


