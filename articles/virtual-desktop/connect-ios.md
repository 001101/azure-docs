---
title: Connect to Windows Virtual Desktop from iOS  - Azure
description: How to connect to the Windows Virtual Desktop from iOS.
services: virtual-desktop
author: helohr

ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
---
# Connect from iOS

> Applies to: iOS 8.0 or later. Compatible with iPhone, iPad, and iPod touch.

You can access Windows Virtual Desktop resources from your iOS device with our downloadable client. This guide will tell you how to set up the iOS client.

## Install the iOS Beta client
To install the iOS Beta client:

1. Install the [Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664) app on your iOS device.
2. On your iOS device, open a browser and navigate to [aka.ms/rdiosbeta](https://aka.ms/rdiosbeta).
3. Under the label **Step 2: Join the Beta**, select **Start Testing**.
4. When you're redirected to the TestFlight app, select **Accept**, then select **Install**.

## Subscribe to a feed

Subscribe to the feed provided by your admin to get the list of managed resources you can access on your iOS device.

To subscribe to a feed:

1. In the Connection Center, tap **+**, and then tap **Add Workspace**.
2. Enter the feed URL into the **Feed URL** field. The feed URL can be either a URL or an email address.
   - If you use a URL, use the RD Web Access server URL that your admin gave you. If you're accessessing resources from Windows Virtual Desktop, use https://rdweb.wvd.microsoft.com.
   - To use email, enter your email address. This tells the client to search for an RD Web Access server associated with your email address if your admin configured the server that way.
3. Tap **Next**.
4. Provide your credentials when prompted.
   - For **User name**, give the user name with permission to access resources.
   - For **Password**, give the password associated with the user name.
   - You may also be prompted to provide additional factors if your admin configured authentication that way.
5. Tap **Save**.

After this, the Connection Center should display the remote resources.

Once subscribed to a feed, the feed's contents will update automatically on a regular basis. Resources may be added, changed, or removed based on changes made by your administrator.

## Client documentation

To learn more about how to use the iOS Beta client, check out the [Remote Desktop Services documentation](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios).
