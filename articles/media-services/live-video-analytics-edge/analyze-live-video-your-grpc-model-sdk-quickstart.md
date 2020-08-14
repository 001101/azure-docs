---
title:  Analyze live video by using your own gRPC model using SDK - Azure
description: In this quickstart, you'll apply computer vision to analyze the live video feed from a (simulated) IP camera. 
ms.topic: quickstart
ms.date: 08/13/2020

---
# Quickstart: Analyze live video by using your own gRPC model using SDK

This quickstart shows you how to use Live Video Analytics on IoT Edge to analyze a live video feed from a (simulated) IP camera. You'll see how to apply a computer vision model to detect objects. A subset of the frames in the live video feed is sent to an inference service. The results are sent to IoT Edge Hub.

This quickstart uses an Azure VM as an IoT Edge device, and it uses a simulated live video stream. It's based on sample code written in C#, and it builds on the [Detect motion and emit events](detect-motion-emit-events-quickstart.md) quickstart.

## Prerequisites

* An Azure account that includes an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) if you don't already have one.
* [Visual Studio Code](https://code.visualstudio.com/), with the following extensions:

    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* If you didn't complete the [Detect motion and emit events](detect-motion-emit-events-quickstart.md) quickstart, then be sure to [set up Azure resources](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> When installing Azure IoT Tools, you might be prompted to install Docker. You can ignore the prompt.

## Review the sample video

When you set up the Azure resources, a short video of highway traffic is copied to the Linux VM in Azure that you're using as the IoT Edge device. This quickstart uses the video file to simulate a live stream.
Open an application such as [VLC media player](https://www.videolan.org/vlc/). Select Ctrl+N and then paste a link to [the highway intersection sample video](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) to start playback. You see the footage of many vehicles moving in highway traffic.
In this quickstart, you'll use Live Video Analytics on IoT Edge to detect objects such as vehicles and persons. You'll publish associated inference events to IoT Edge Hub.

## Overview

![Overview](./media/quickstarts/overview-grpc.png)

This diagram shows how the signals flow in this quickstart. An [edge module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulates an IP camera hosting a Real-Time Streaming Protocol (RTSP) server. An [RTSP source](media-graph-concept.md#rtsp-source.md) node pulls the video feed from this server and sends video frames to the [frame rate filter processor](media-graph-concept.md#frame-rate-filter-processor) node. This processor limits the frame rate of the video stream that reaches the [gRPC extension processor](media-graph-concept.md#grpc-extension-processor) <TODO: fix link> node.

The gRPC extension node plays the role of a proxy. It converts the video frames to the specified image type. Then it relays the image over gRPC to another edge module that runs an AI model behind a gRPC endpoint. In this example, that edge module is built by using the [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) model, which can detect many types of objects. The gRPC extension processor node gathers the detection results and publishes events to the [IoT Hub sink](media-graph-concept.md#iot-hub-message-sink) node. The node then sends those events to [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

In this quickstart, you will:

1. Create and deploy the media graph.
1. Interpret the results.
1. Clean up resources.

## Create and deploy the media graph

### Examine and edit the sample files

As part of the prerequisites, you downloaded the sample code to a folder. Follow these steps to examine and edit the sample files.

1. In Visual Studio Code, go to *src/edge*. You see your .env file and a few deployment template files.

    The *deployment.grpcyolov3icpu.template.json* refers to the deployment manifest for the edge device. It includes some placeholder values. The .env file includes the values for those variables.
1. Go to the *src/cloud-to-device-console-app* folder. Here you see your *appsettings.json* file and a few other files:

    * c2d-console-app.csproj - The project file for Visual Studio Code.
    * Program.cs - The sample program code. This code:

        * Loads the app settings.
        * Creates a list of operations (Step) for the program to run in order.
        * Using the SDK facilities, calls each Live Video Analytics counterpart direct method on IoT Edge module.
        * Pauses so that you can examine the program's output in the TERMINAL window and examine the events that were generated by the module in the OUTPUT window.
        * Invokes direct methods (through SDK invocations) to clean up resources.

### Generate and deploy the IoT Edge deployment manifest

1. Right-click the *src/edge/* *deployment.grpcyolov3icpu.template.json* file and then select **Generate IoT Edge Deployment Manifest**.

    ![Generate IoT edge deployment manifest grpc](./media/quickstarts/generate-iot-edge-deployment-manifest-grpc.png)

    The *deployment.grpcyolov3icpu.amd64.json* manifest file is created in the *src/edge/config* folder.
1. If you completed the [Detect motion and emit events](detect-motion-emit-events-quickstart.md) quickstart, then skip this step.

    Otherwise, near the **AZURE IOT HUB** pane in the lower-left corner, select the **More actions** icon and then select **Set IoT Hub Connection String**. You can copy the string from the *appsettings.json* file. Or, to ensure you've configured the proper IoT hub within Visual Studio Code, use the [Select IoT hub command](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).

    ![iot hub connection string grpc](./media/quickstarts/iot-hub-connection-string-grpc.png)

1. Right-click *src/edge/config/* *deployment.grpcyolov3icpu.amd64.json* and select **Create Deployment for Single Device**.

    ![create deployment single device grpc](./media/quickstarts/create-deployment-single-device-grpc.png)
1. When you're prompted to select an IoT Hub device, select **lva-sample-device**.
1. After about 30 seconds, in the lower-left corner of the window, refresh Azure IoT Hub. The edge device now shows the following deployed modules:

    * The Live Video Analytics module, named **lvaEdge**.
    * The **rtspsim** module, which simulates an RTSP server and acts as the source of a live video feed.
    * The **lvaExtension** module, which is the YOLOv3 object detection model that uses gRPC as the communication method and applies computer vision to the images and returns multiple classes of object types.

    ![lvaextension grpc](./media/quickstarts/lvaextension-grpc.png)

### Prepare to monitor events

Right-click the Live Video Analytics device and select **Start Monitoring Built-in Event Endpoint**. You need this step to monitor the IoT Hub events in the OUTPUT window of Visual Studio Code.

![start monitoring built event endpoint grpc](./media/quickstarts/start-monitoring-built-event-endpoint-grpc.png)

### Run the sample program

1. In Visual Studio Code, go to *src/cloud-to-device-console-app*.
1. Start a debugging session by selecting the F5 key. The **TERMINAL** window will display some messages. 

    Then you’ll be prompted to pick a topology: choose `5. Inference using gRPC Extension`
1. The Step list starts off with calls to GraphTopologyList and GraphInstanceList, invoking methods from the SDK itself (ILiveVideoAnalyticsEdgeClient). If you cleaned up resources after you completed previous quickstarts, then this process will return empty lists and then pause. To continue, select the Enter key.
    
    ```
    --------------------------------Executing operation GraphTopologyList-----------------  
    {
      "Value": [],
      "ContinuationToken": null
    }
    --------------------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```

    The **TERMINAL** window shows the next set of direct method calls:

    * A call to `GraphTopologySet` that uses the picked topology constructed in its entirety by the SDK.
    * A call to `GraphInstanceSet` constructed in its entirety by the SDK shows the following body:
    
    ```
    {
      "@apiVersion": "1.0",
      "name": "Sample-Graph-1",
      "properties": {
        "topologyName": "InferencingWithGrpcExtension",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
          },
          {
            "name": "rtspUserName",
            "value": "testuser"
          },
          {
            "name": "rtspPassword",
            "value": "testpassword"
          }
        ]
      }
    }
    ```

    * A call to `GraphInstanceActivate` that starts the graph instance and the flow of video.
    * A second call to `GraphInstanceList` that shows that the graph instance is in the running state.
1. The output in the **TERMINAL** window pauses at a Press Enter to continue prompt. Don't select Enter yet. Scroll up to see the JSON response payloads for the direct methods you invoked.
1. Switch to the **OUTPUT** window in Visual Studio Code. You see messages that the Live Video Analytics on IoT Edge module is sending to the IoT hub. The following section of this quickstart discusses these messages.
1. The media graph continues to run and print results. The RTSP simulator keeps looping the source video. To stop the media graph, return to the **TERMINAL** window and select Enter.
1. The next series of calls cleans up resources:

    * A call to `GraphInstanceDeactivate` deactivates the graph instance.
    * A call to `GraphInstanceDelete` deletes the instance.
    * A call to `GraphTopologyDelete` deletes the topology.
    * A final call to GraphTopologyList shows that the list is empty.

## Interpret results

When you run the media graph, the results from the HTTP extension processor node pass through the IoT Hub sink node to the IoT hub. The messages you see in the **OUTPUT** window contain a body section and an `applicationProperties` section. For more information, see [Create and read IoT Hub messages](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).
In the following messages, the Live Video Analytics module defines the application properties and the content of the body.

### MediaSessionEstablished event

When a media graph is instantiated, the RTSP source node attempts to connect to the RTSP server that runs on the rtspsim-live555 container. If the connection succeeds, then the following event is printed. The event type is `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

In this message, notice these details:

* The message is a diagnostics event. `MediaSessionEstablished` indicates that the RTSP source node (the subject) connected with the RTSP simulator and has begun to receive a (simulated) live feed.
* In `applicationProperties`, subject indicates that the message was generated from the RTSP source node in the media graph.
* In `applicationProperties`, `eventType` indicates that this event is a diagnostics event.
* The `eventTime` indicates the time when the event occurred.
* The body contains data about the diagnostics event. In this case, the data comprises the [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) details.

### Inference event

The gRPC extension processor node receives inference results from the lvaExtension module. It then emits the results through the IoT Hub sink node as inference events.

In these events, the type is set to entity to indicate it's an entity, such as a car or truck. The `eventTime` value is the UTC time when the object was detected.

In the following example, two cars were detected in the same video frame, with varying levels of confidence.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

In the messages, notice the following details:

* In `applicationProperties`, subject references the node in the graph topology from which the message was generated.
* In `applicationProperties`, `eventType` indicates that this event is an analytics event.
* The `eventTime` value is the time when the event occurred.
* The body section contains data about the analytics event. In this case, the event is an inference event, so the body contains inferences data.
* The inferences section indicates that the type is entity. This section includes additional data about the entity.

## Clean up resources

If you intend to try other quickstarts, keep the resources you created. Otherwise, go to the Azure portal, go to your resource groups, select the resource group where you ran this quickstart, and delete all the resources.

## Next steps

Try running different media graph topologies using gRPC protocol.
