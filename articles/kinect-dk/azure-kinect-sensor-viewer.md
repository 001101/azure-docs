---
title: Azure Kinect sensor viewer
description: Kinect for Azure sensor data stream viewer homepage
author: joylital
ms.author: joylital
ms.prod: kinect-dk
ms.date: 04/08/2019
ms.topic: conceptual
keywords: azure, kinect, sensor, viewer, visualization, depth, rgb, color, imu, audio, microphone, point cloud
---

# Azure Kinect sensor viewer

Azure Kinect viewer `k4aviewer.exe` can be used to visualize all device data streams to:

* Verify sensors are working correctly.
* Help positioning the device.
* Experiment with camera settings.
* Playback recordings made with [Azure Kinect Recorder](azure-kinect-dk-recorder.md).

The tool works also as sample application on how to use API and enables to configured device properties or read information out from device.

## Use viewer

The viewer can operate in two modes; with live data from the sensor or from recorded data ([Azure Kinect Recorder](azure-kinect-dk-recorder.md)).

### Start application

1. Launch the application by running `k4aviewer.exe`. 

![Checking device firmware version using viewer](./media/how-to-guides/open-viewer.png)

### Use the viewer with live data

1. In the **Open Device** section, select the **Serial Number** of the device to open. Then, select **Refresh**, if the device is missing.
2. Select the **Open Device** button.
3. Select **Start** to begin streaming data with the default settings.

### Use the viewer with recorded data

In **Open Recording** section, navigate to the recorded file, and select it.

## Check device firmware version

Access the device firmware version in the configuration window, as shown in the following image.

![Checking device firmware version using viewer](./media/how-to-guides/check-firmware-update.png)

For example, in this case, the depth camera ISP is running FW 1.5.63.

## Depth camera

The depth camera viewer will show two windows: 

* One is called *Active Brightness* which is a grayscale image showing IR brightness.
* The second is called *Depth*, which has a colorized representation of the depth data.

Hover your cursor, at the pixel in the the depth window, to see the value of the depth sensor, as shown below.

![Depth view](./media/how-to-guides/depth-camera.png)

## RGB camera

The image below shows the color camera view.

![RGB view](./media/how-to-guides/viewer-rgb-camera.png)

You can control RGB camera settings from the configuration window during the streaming.

![RGB camera controls](./media/how-to-guides/rgb-camera-settings.png)

## Inertial Measurement Unit (IMU)

The IMU window has two components, an accelerometer and a gyroscope.

The top half is the accelerometer and shows linear acceleration in meters/second^2.  It includes acceleration from gravity, so if it's lying flat on a table, the Z axis will probably show around -9.8m/s^2.

The bottom half is the gyroscope portion and shows rotational movement in radians/second

![Motion sensor view](./media/how-to-guides/viewer-mu-settings.png)

## Microphone input

The microphone view shows a representation of the sound heard on each microphone. If there's no sound, the graph is shown as empty.  If there is sound, you'll see a dark blue waveform with a (usually smaller) light blue waveform overlaid on top of it.

The dark wave represents the minimum and maximum values observed by the microphone over that time slice. The light wave represents the root mean square of the values observed by the microphone over that time slice.

![Microphone input view](./media/how-to-guides/microphone-data.png)

## Point cloud visualization

Depth visualized in 3D let's you move in the image using instructed keys.

![Depth point cloud](./media/how-to-guides/depth-point-cloud.png)

## Synchronization control

You can use the viewer to configure the device as standalone (default), master or subordinate mode when configuring multi-device synchronization.
When changing configuration or inserting/removing synchronization cable, select **Refresh** to update.

![External synchronization control](./media/how-to-guides/sync-control.png)

## Next steps

 [External synchronization setup guide](https://aka.ms/AzureKinectAPIDocs/external-sync-setup.md).

## Troubleshooting

See [troubleshooting](https://aka.ms/AzureKinectAPIDocs/troubleshooting.md)
