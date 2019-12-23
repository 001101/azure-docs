---
title: Set up and run Unity sample project
description: How to set up and run the Unity sample project
author: FlorianBorn71
manager: jlyons
services: azure-remote-rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: tutorial
ms.service: azure-remote-rendering
---
# How to set up and run the Unity sample project

In the Unity folder of the [AzureRemoteRenderingClient](https://dev.azure.com/arrClient/arrClient/_git/arrClient) repository is a sample project called *AzureRemoteRenderingSample*.

This project illustrates how a client application can connect to an Azure Remote Rendering server, and demonstrates some interaction and local rendering.

## Prerequisites

1. Make sure you are using Unity **2019.1** and that you have UWP "Universal Windows Platform Build Support" and IL2CPP "Windows Build Support (IL2CPP)" selected in the installation.
1. Install [Windows SDK 10.0.18362.0](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk).
1. Make sure you have the newest Visual Studio 2017 version installed.
1. To view the video stream from the server on a desktop PC, it is required to have the [HEVC Video Extensions](https://www.microsoft.com/en-us/p/hevc-video-extensions/9nmzlz57r3t7) installed.
1. Download the nuget command line and a companion credential manager:
    * These tools are obtained from the [https://dev.azure.com/arrClient/arrClient](https://dev.azure.com/arrClient/arrClient) site.
    * Click on Artifacts and change the dropdown from ArrClient to ArrPackages and click on ‘Connect to feed’. 
    * If necessary, switch the dropdown in the "Get packages using Visual Studio" to "All Packages" 
    * Download the NuGet command line tool and the Credential Provider package from the link displayed. 
    * Extract this to a directory of your choosing and add the location to your Path by editing the system environment variables.
    * Add the "arrPackages" feed with the following nuget command:  
        `nuget.exe sources Add -Name "ArrPackages" -Source "https://pkgs.dev.azure.com/arrClient/_packaging/ArrPackages/nuget/v3/index.json"`

## Initial set-up

The Unity sample project expects to find two Unity packages in the Unity folder. These are:

1. `com.microsoft.azure.remote_rendering`
1. `ScriptableRenderPipeline`

These packages and the process of obtaining them is described in more detail on the [Remote Rendering Unity Package](../how-tos/install-remote-rendering-unity-package.md) page.
In brief, the steps are:

* Change to the Unity directory (that is. the one containing AzureRemoteRenderingSample).
* to update the packages you **must delete** the already existing 'com.microsoft.azure.remote_rendering'  'ScriptableRenderPipeline' directories
* Issue the following two commands, which will pull the latest version of these packages into the current directory:
  * `nuget install com.microsoft.azure.remote_rendering -ExcludeVersion`
  * `nuget install ScriptableRenderPipeline -ExcludeVersion`

## Configuring the sample

See [Rendering Your First Model](../quickstarts/rendering-your-first-model.md) for sample setup.

1. Dock the 'Game' view tab besides the 'Scene' tab.
1. Press the play button in Unity.
1. The model should be loaded on the server and the 'Game' view should show the remotely rendered image.
1. A new game object should have been added to the scene.
1. You can now explore the scene graph by selecting the new node and clicking **Show children** in the Inspector.
This has to be repeated for each game object to explore the hierarchy.
<br />![Unity Hierarchy](./media/unity-hierarchy.png)
1. You can also move remote objects or the cut plane (**InteractionSample/CutPlane**) around in the editor, to sync the transform either click **Sync now** or check the **Sync every frame** option. For component properties, just changing them is enough.
<br />![Unity Inspector](./media/unity-inspector.png)

> [!NOTE]
> Due to technical limitations it is only possible to preview local content with Unity's Play-To-VR feature. The remote connection is automatically disabled if a VR device is connected.

## Building the sample project

1. Open *File > Build Settings*.
1. Change *Platform* to 'Universal Windows Platform', set *Target Device* to 'HoloLens,' set *Architecture* to 'ARM64.'  Select *Switch to Platform* then press **Build** to select the output folder.
<br />![Unity build settings](./media/unity-build-settings.png)
1. Open the generated **UnityProject.sln** solution in Visual Studio.
1. Change the configuration to **ARM64** and **Release** and switch the debugger mode to **Remote Machine**.
1. For the project "UnityProject", go to *Properties > Debugging > Machine Name* and change the 'Remote Machine' connection to your device IP (make sure to change it for Release).
1. The sample can now be deployed to the connected device.

## Launching the sample

1. When first launched, the sample app should display a message indicating that it can't connect to localhost.
1. Change the server hostname as follows:
1. Open the device web portal
1. Go to the page: *System > File explorer*
1. Download the file: `LocalAppData / UnityProject / LocalState / connect.xml`
1. Update the XML for your account credentials and session information (or leave session information blank to autocreate a session).
1. Reupload it to the same location.
1. When the app is restarted, it should load the model on the server. To restart the app, make sure to close any open window before selecting it from the start menu again.

An example connect.xml will look like:

```xml
<?xml version="1.0" encoding="utf-8"?>
<HostInfo xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <mode>UseSession</mode>
  <useHostnameSettings>
    <hostname></hostname>
  </useHostnameSettings>
  <useSessionSettings>
    <sessionid />
    <useSessionId>false</useSessionId>
    <accountInfo>
      <AccountName />
      <AccountDomain>mixedreality.azure.com</AccountDomain>
      <AccountId>your-account-id</AccountId>
      <AccountKey>your-account-key</AccountKey>
      <AuthenticationToken />
      <AccessToken />
      <Location>westus2</Location> <!-- Equal to the enum from the editor, can be westeurope or westus2 -->
    </accountInfo>
  </useSessionSettings>
</HostInfo>
```

## Using the sample

Once started, you are asked to place the model that is hovering in front of you. To place it, look in the direction you want to place it and click by tapping your fingers.
There is a box attached to your hand, representing the 'cursor', to interact with things move the tracked hand into the box and tap / drag.

Now there should be a few buttons following you around:

* N, X, Y, Z cubes: selects the cut plane normal (None or Axis).
* P cube: restarts the model placement procedure.
* [?] cube: toggles stats display.

There are also models placed within the scene:

* A cube with a 'grab me' label. Drag it to move the model.
* A sphere, which defines the cut plane position. Drag it to move the cut plane.

## Using your own model

In AzureRemoteRenderingSample\Assets\Scripts, replace 
"builtin://UnitySampleModel" with the URL of your model. Retrieve a SAS URI for your ingested model stored in azure blob storage as described in [Using the model ingestion REST API](../how-tos/ingest-models.md)



