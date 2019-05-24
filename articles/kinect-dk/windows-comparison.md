---
title: Azure Kinect DK Windows comparison
description: Hardware and software differences between Azure Kinect DK and Kinect for Windows v2
author: joylital
ms.author: joylital
ms.prod: kinect-dk
ms.date: 06/17/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, comparison, SDK, differences, hardware, software 
---

# Azure Kinect and Kinect Windows v2 comparison

The Azure Kinect DK hardware and Software Development Kits have differences from Kinect for Windows v2. Any existing Kinect for Windows v2 applications will not work directly with Azure Kinect DK and require porting.  

## Hardware

High-level differences between the Azure Kinect development kit and Kinect for Windows v2 are listed in the following table.

|    |      | Azure Kinect DK |  Kinect for Windows v2 |
|----------|---------------|--------| ------------|
| **Audio** | Details  | 7-mic circular array | 4-mic linear phased array |
| **Motion sensor** | Details | 3-axis accelerometer  3-axis gyro | 3-axis accelerometer |
| **RGB Camera**    | Details | 3840 x 2160 px @30 fps | 1920 x 1080 px @30 fps |
| **Depth Camera**  | Method   | Time-of-Flight | Time-of-Flight |
| *Depth Camera* | Resolution/FOV | 640 x 576 px @30 fps | 512 x 424 px @ 30 fps |
| *Depth Camera* | Resolution/FOV | 512 x 512 px @30 fps | 1024x1024 px @15 fps |
| **Connectivity** | Data | USB3.1 Gen 1 with type USB-C  | USB 3.1 gen 1|
|  | Power | External PSU or USB-C | External PSU |
|  | Synchronization | RGB & Depth internal, external device-to-device| RGB & Depth internal only |
| **Mechanical** | Dimensions | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | Mass | 440 g | 970 g |
| | Mounting | One ¼-20 UNC. Four internal screw points | One ¼-20 UNC |

Find additional details in the [Azure Kinect DK hardware](hardware-specification.md) document.

## Sensor access

The following table provides low-level device sensor access capability comparison.

| **Functionality**| **Azure Kinect** | **Kinect for Windows** | **Notes** |
|---------|---------|------------|---------|
| **Depth** | ✔️ | ✔️ |    |   |
| **IR** | ✔️ | ✔️ |  |
| **Color** | ✔️ | ✔️ | Color format supports differences, Azure Kinect DK supports these camera controls: Exposure, white balance, brightness, contrast, saturation, sharpness, and gain control |
| **Audio** | ✔️ | ✔️ | Azure Kinect DK mics are accessed via Speech SDK or Windows native API |
| **IMU** | ✔️ |  | Partial (1-axis) |
| **Calibration data** | ️️️️️✔️ ️️️| ✔️ | OpenCV compatible camera model calibration |
| **Depth-RGB internal sync** | ✔️ | ✔️ |  |
| **External Sync**| ✔️|  | K4A allows programmable delay for external sync |
| **Share access with multiple clients** | | ✔️ | The Azure Kinect Sensor SDK relies on WinUSB/libUSB to access device and does not have a service implemented to enable sharing device access with multiple processes |
| **Stream record / playback tool** | ✔️ | ✔️ | Azure Kinect DK uses an open-source Matroska container-based implementation |

## Map features

The Azure Kinect SDK feature set is different from Kinect for Windows v2, as detailed below:

| **Kinect v2 Feature** | **Kinect v2 Data Type** | **Azure Kinect SDK/Service** |
|--------|--------|------|
| Sensor Data Access |DepthFrame| [Sensor SDK - Retrieve images](retrieve-images.md) 
| |InfraredFrame | [Sensor SDK - Retrieve images](retrieve-images.md) 
| | ColorFrame | [Sensor SDK - Retrieve images](retrieve-images.md) | 
| | AudioBeamFrame |Not currently supported 
| Body Tracking | BodyFrame | Body Tracking SDK |
| | BodyIndexFrame | Body Tracking SDK  |
| Coordinate Mapping|CoordinateMapper| [Sensor SDK - Image transformations](use-image-transformation.md) |
|Face Tracking | FaceFrame | [Cognitive Services: Face](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Speech   Recognition    |    N/A                      |    [Cognitive Services: Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## Next steps

You can also review

> [!div class="nextstepaction"]
>[Kinect for Windows developer pages](https://developer.microsoft.com/windows/kinect)
