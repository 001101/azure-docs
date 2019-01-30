---
title: Quickstart - Create iOS app with Azure Spatial Anchors | Microsoft Docs
description: In this quickstart, you learn how to build an iOS app using Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors

ms.author: crtreasu
ms.date: 12/13/2018
ms.topic: quickstart
ms.service: azure-spatial-anchors
# ms.reviewer: MSFT-alias-of-reviewer
#Customer intent: As a Mixed Reality developer, I want to learn how to use Spatial Anchors in my iOS app that can place and locate a 3D object that persists across devices and platforms.
---
# Quickstart: Create an iOS app with Spatial Anchors

[Spatial Anchors](../overview.md) is a cross-platform developer service that allows you to create Mixed Reality experiences
using objects that persist their location across devices over time. This tutorial covers how to create an iOS app
using Spatial Anchors in either Swift or Objective-C. When you're finished, you'll have an ARKit iOS app that can save
and recall a spatial anchor.

You'll learn how to:

> [!div class="checklist"]
> * Create a Spatial Anchors account
> * Configure the Spatial Anchors endpoint and account key
> * Deploy and run on an iOS device

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

To complete this quickstart, make sure you have:

- A developer enabled macOS machine with <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 9.4+</a> and <a href="https://cocoapods.org" target="_blank">CocoaPods</a> installed.
- A developer enabled <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit compatible</a> iOS device.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## Open the sample project

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Install the necessary pods using CocoaPods:

# [Swift](#tab/openproject-swift)

Navigate to `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# [Objective-C](#tab/openproject-objc)

Navigate to `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

***

Run `pod install` to install the CocoaPods for the project.

Now open the `.xcworkspace` in Xcode.

# [Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# [Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

***

## Configure the Spatial Anchors endpoint and account key

The next step is to use the endpoint and account key recorded previously when setting up the Spatial Anchors resource to configure the app.

# [Swift](#tab/openproject-swift)

Open `iOS/Swift/SampleSwift/ViewController.swift`.

Locate the `SpatialAnchorsAccountKey` field and replace `Set me` with the account key.

Locate the `SpatialAnchorsEndpoint` field and replace `Set me` with the endpoint.

# [Objective-C](#tab/openproject-objc)

Open `iOS/Objective-C/SampleObjC/ViewController.m`.

Locate the `SpatialAnchorsAccountKey` field and replace `Set me` with the account key.

Locate the `SpatialAnchorsEndpoint` field and replace `Set me` with the endpoint.

***

## Deploy the app to your iOS device

Connect the iOS device to the Mac and set the **active scheme** to your iOS device.

![Select the device](./media/get-started-ios/select-device.png)

Select **Build and then run the current scheme**.

![Deploy and run](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> If you see a `library not found for -lPods-SampleObjC` error, you likely opened the `.xcodeproj` file instead of the
> `.xcworkspace`. Open the `.xcworkspace` and try again.

In Xcode, stop the app by pressing **Stop**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## Next steps

In this quickstart, you've created a Spatial Anchors account. You then configured and deployed an App to save and recall Spatial Anchors. To learn more about how to improve the App so that it can share Spatial Anchors with other devices, continue to the next tutorial

> [!div class="nextstepaction"]
> [Tutorial: Share Spatial Anchors across devices](../tutorials/tutorial-share-anchors-across-devices.md)
