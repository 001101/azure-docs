---
title: Connect to Windows Virtual Desktop from macOS  - Azure
description: How to connect to the Windows Virtual Desktop from macOS.
services: virtual-desktop
author: helohr

ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: btaintor
---
# Connect from macOS

> Applies to macOS 10.12 or later.

You can access Windows Virtual Desktop resources from your macOS devices with our downloadable client. This guide will tell you how to set up the client.

## Install the client

To get started, [download](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) and install the client on your macOS device.

## Subscribe to a feed

Subscribe to the feed your admin gave you to get the list of managed resources available to you on your macOS device.

To subscribe to a feed:

1. Select **Add Feed** on the main page to connect to the service and retrieve your resources.
2. Enter the Feed URL. This can be a URL or email address:
  - If you use a URL, use the RD Web Access server URL that your admin gave you. If you're accessessing resources from Windows Virtual Desktop, use https://rdweb.wvd.microsoft.com.
  - To use email, enter your email address. This tells the client to search for an RD Web Access server associated with your email address if your admin configured the server that way.
3. Select **Subscribe**.
4. Sign in with your user account when prompted.

After you've signed in, you should see a list of available resources.

Once you've subscribed to a feed, the feed's content will update automatically on a regular basis. Resources may be added, changed, or removed based on changes made by your administrator.

## Client documentation

To learn more about the macOS client, check out the [Remote Desktop Services documentation](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac).
