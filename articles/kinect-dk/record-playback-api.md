---
title: Record and playback API
description: Using record and playback API
author: xthexder
ms.author: xthexder
ms.prod: kinect-depth_track_enabled
ms.date: 02/27/2019
ms.topic: overview 
keywords: kinect, azure, sensor, sdk, depth, rgb, record, playback, matroska, mkv
---
# Record and Playback API

The sensor SDK provides an API for recording device data to a Matroska (.mkv) file. The Matroska container format stores multiple video tracks
for Color, Depth, and IR, along with other track and attachments for IMU samples and device calibration. Generated recordings using the
provided [k4arecorder](https://aka.ms/AzureKinectAPIDocs/k4a-recordplayback.md) command line utility, or if customization is needed, the record API can be used directly
(see [k4a_record_create](https://aka.ms/AzureKinectAPIDocs/api/k4a-record-create.md)).

More info on the Matroska file format specifications can be found on the [Recording File Format](https://aka.ms/AzureKinectAPIDocs/sdk-record-format.md) page.

## Use the Playback API

Recording files can be opened using the playback API, which provides access to camera data in the same format as the rest of the sensor SDK.

### Open a Record File

In the following example, we open a recording using [k4a_playback_open](https://aka.ms/AzureKinectAPIDocs/api/k4a-playback-open.md), print the recording length,
and then close the file with [k4a_playback_close](https://aka.ms/AzureKinectAPIDocs/api/k4a-playback-close.md).

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### Read Captures

Once the file is open, we can start reading captures from the recording. This next example will read each of the captures in the file.

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### Seeking Within a Recording

Once we've reached the end of the file, we may want to go back and read it again. This could be done by reading backwards with
[k4a_playback_get_previous_capture](https://aka.ms/AzureKinectAPIDocs/api/k4a-playback-get-previous-capture.md), but this may be very slow depending on the length of the recording.
Instead we can use the [k4a_playback_seek_timestamp](https://aka.ms/AzureKinectAPIDocs/api/k4a-playback-seek-timestamp.md) function to go to a specific point in the file.

In this example we specify timestamps in microseconds to seek to various points in the file.

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### Read Tag Information

Recordings can also contain various metadata such as the device serial number and firmware versions. This metadata is
stored in recording tags, which can be accessed using the [k4a_playback_get_tag](https://aka.ms/AzureKinectAPIDocs/api/k4a-playback-get-tag.md) function.

```C
// Print the serial number of the device used to record
size_t serial_number_size = 0;
char serial_number[256];
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### Record Tag List

Below is a list of all the default tags that may be included in a recording file. Many of these values are available as part of
the [k4a_record_configuration_t](https://aka.ms/AzureKinectAPIDocs/api/k4a-record-configuration-t.md) struct, and can be read with the
[k4a_playback_get_record_configuration](https://aka.ms/AzureKinectAPIDocs/api/k4a-playback-get-record-configuration.md) function.

If a tag does not exist, it is assumed to have the default value.

| Tag Name                   | Default Value      | [k4a_record_configuration_t](https://aka.ms/AzureKinectAPIDocs/api/k4a-record-configuration-t.md) Field | Notes                                          |
|----------------------------|--------------------|-----------------------------------|--------------------------------------------------------------------------------------|
| K4A_COLOR_MODE             | "OFF"              | color_format / color_resolution   | Possible values: "OFF", "MJPG_1080P", "NV12_720P", "YUY2_720P", etc...               |
| K4A_DEPTH_MODE             | "OFF"              | depth_mode / depth_track_enabled  | Possible values: "OFF, "NFOV_UNBINNED", "PASSIVE_IR", etc...                         |
| K4A_IR_MODE                | "OFF"              | depth_mode / ir_track_enabled     | Possible values: "OFF", "ACTIVE", "PASSIVE"                                          |
| K4A_IMU_MODE               | "OFF"              | imu_track_enabled                 | Possible values: "ON", "OFF"                                                         |
| K4A_CALIBRATION_FILE       | "calibration.json" | N/A                               | See [k4a_device_get_raw_calibration](https://aka.ms/AzureKinectAPIDocs/api/k4a-device-get-raw-calibration.md)        |
| K4A_DEPTH_DELAY_NS         | "0"                | depth_delay_off_color_usec        | Value in nanoseconds                                                                 |
| K4A_WIRED_SYNC_MODE        | "STANDALONE"       | wired_sync_mode                   | Possible values: "STANDALONE", "MASTER", "SUBORDINATE"                               |
| K4A_SUBORDINATE_DELAY_NS   | "0"                | subordinate_delay_off_master_usec | Value in nanoseconds                                                                 |
| K4A_COLOR_FIRMWARE_VERSION | ""                 | N/A                               | Device color firmware version, e.g. "1.x.xx"                                         |
| K4A_DEPTH_FIRMWARE_VERSION | ""                 | N/A                               | Device depth firmware version, e.g. "1.x.xx"                                         |
| K4A_DEVICE_SERIAL_NUMBER   | ""                 | N/A                               | Recording device serial number                                                       |
| K4A_START_OFFSET_NS        | "0"                | start_timestamp_offset_usec       | See [Timestamp Synchronization](https://aka.ms/AzureKinectAPIDocs/sdk-record-playback.md#timestamp-synchronization). |

### Timestamp Synchronization

When using the external sync cable to synchronize cameras, the timestamps coming off each device will be synchronized, and the first timestamp off
each device can be non-zero. Since recording files must always start at timestamp 0, this poses problems for synchronizing files recorded from multiple cameras.

The `K4A_START_OFFSET_NS` tag is used to specify a timestamp offset so that files can be re-synchronized after recording.
By adding this timestamp offset to each timestamp in the file, the original timestamps from the device can be reconstructed.
The start offset is also available in the [k4a_record_configuration_t](https://aka.ms/AzureKinectAPIDocs/api/k4a-record-configuration-t.md) struct.
