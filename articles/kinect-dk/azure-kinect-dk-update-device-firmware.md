---
title: Update Azure Kinect firmware
description: Instructions for updating Azure Kinect DK device firmware
author: joylital
ms.author: joylital
ms.prod: kinect-dk
ms.date: 06/28/2019
ms.topic: conceptual
keywords: kinect, firmware, update, recovery
---

# Azure Kinect DK device firmware update

This document provides guidance on how to update device firmware on your Azure Kinect DK. Azure Kinect DK does not update firmware automatically. You can use [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md) to update firmware manually to the latest available version.

## Prepare for firmware update

1. [Download SDK](sensor-sdk-download.md).
2. Install the SDK.
3. In the SDK install location under (SDK install location)\tools\ you should find:

    - AzureKinectFirmwareTool.exe
    - A Firmware .bin file in the firmware folder, such as *AzureKinectDK_Fw_1.5.926614.bin*.

4. Connect your device to host PC and power it up also.

> [!IMPORTANT]
> Keep the USB and power supply connected during the firmware update. Removing either connection during update may put the firmware into a corrupted state.

## Update device firmware

1. Open a command prompt in the (SDK install location)\tools\ folder.
2. Update Firmware using the Azure Kinect Firmware Tool

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Example:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Wait until the firmware update finishes. This can take a few minutes depending on image size.

### Verify device firmware version

1. Verify the firmware is updated.

    `AzureKinectFirmwareTool.exe -q`

2. View the following example.

    ```
       >AzureKinectFirmwareTool.exe -q
        == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000036590812
        Current Firmware Versions:
        RGB camera firmware:      1.5.92
        Depth camera firmware:    1.5.66
        Depth config file:        6109.7
        Audio firmware:           1.5.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. If you see the above output, your firmware is updated.

4. After firmware update you can run [K4A Viewer](azure-kinect-sensor-viewer.md) to verify all sensors are working as expected.

## Troubleshooting

Firmware updates can fail for several reasons. When a firmware update fails try these mitigation steps:

1. Try to run the firmware update command a 2nd time.

2. Confirm the device is still connected by querying for the firmware version.        AzureKinectFirmareTool.exe

3. If all else fails, follow the [recovery](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) steps to revert to the factory firmware and try again.

For any additional issues see also [Microsoft support pages](https://support.microsoft.com)

## Next steps

> [!div class="nextstepaction"]
>[Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md)