---
title: Project Acoustics Unreal and Wwise integration
titlesuffix: Azure Cognitive Services
description: This article describes integration of the Project Acoustics Unreal and Wwise plug-ins into your project.
services: cognitive-services
author: NoelCross
manager: nitinme

ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
---

# Project Acoustics Unreal and Wwise integration
This article describes how to integrate the Project Acoustics plug-in package into your existing Unreal and Wwise game project.

Software requirements:
* [Unreal Engine](https://www.unrealengine.com/) 4.20+
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Wwise plugin for Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  If you're using a direct integration of the Wwise SDK instead of the Wwise Unreal plugins, consult the Project Acoustics Unreal plug-in and adjust Wwise API calls.

To use Project Acoustics with an audio engine other than Wwise, make an enhancement request on the [Project Acoustics discussion forum](https://github.com/microsoft/ProjectAcoustics/issues). You can use the Project Acoustics Unreal plug-in to query acoustics data and then make API calls to your engine.

## Download Project Acoustics
If you haven't already, download the [Project Acoustics Unreal and Wwise plug-in package](https://www.microsoft.com/download/details.aspx?id=58090)).

We've included an Unreal Engine plug-in and a Wwise mixer plug-in in the package. The Unreal plug-in provides editor and runtime integration. During gameplay, the Project Acoustics Unreal plug-in computes parameters such as occlusion for each game object each frame. These parameters are translated to Wwise API calls.

## Review integration steps

These are these main steps to install the package and deploy it in your game:
1. Install the Project Acoustics Wwise mixer plug-in.
2. Deploy Wwise to your game. This step propagates the mixer plug-in into your game project.
3. Add the Project Acoustics Unreal plug-in to your game.
4. Extend Wwise's Unreal plug-in functionality.
5. Build game, and check that Python is enabled.
6. Set up your Wwise project to use Project Acoustics.
7. Do Audio setup in Unreal.

### 1. Install the Project Acoustics mixer plug-in
1. Open Wwise launcher. On the **Plug-ins** tab, under **Install New Plug-ins**, select **Add from Directory**.

    ![Install a plugiiin in Wwise launcher](media/wwise-install-new-plugin.png)

1. Select the *AcousticsWwisePlugin\ProjectAcoustics* directory that was included in the package that you downloaded. It contains the Wwise mixer plug-in bundle.

   Wwise will install the plug-in. Project Acoustics should now show appear on the installed plugins list in Wwise.  
![The Wwise installed plug-ins list after Project Acoustics installation](media/unreal-integration-post-mixer-plugin-install.png)

### 2. Dedeploy Wwise into your game
Redeploy Wwise into your game even if you've already integrated Wwise. This step picks up the Project Acoustics Wwise plug-in.

   > [!NOTE]
   > **Engine plugin:** If you have Wwise installed as a game plug-in in an Unreal C++ project, skip this step. If it's installed instead as an engine plugin, for instance because your Unreal project is Blueprint only, Wwise deployment with our mixer plug-in is more complex. Create a dummy empty Unreal C++ project, close the Unreal editor if it opens, and follow the remaining procedure to deploy Wwise into the dummy project. Then copy out the deployed Wwise plug-in.
 
1. From the Wwise launcher, select the **Unreal Engine** tab. Select the "hamburger" menu icon next to **Recent Unreal Engine Projects** and then **Browse for project**. Open your game's Unreal project *.uproject* file.

    ![The Wwise launcher Unreal tab](media/wwise-unreal-tab.png)

1. Select **Integrate Wwise in Project** or **Modify Wwise in Project**. This step integrates Wwise binaries into your project, now including the Project Acoustics mixer plug-in.

   > [!NOTE]
   > **Engine plugin:** If you're using Wwise as an engine plugiin and created the dummy project as described earlier, copy the folder Wwise deployed: *[DummyUProject]\Plugins\Wwise*. Paste it over *[UESource]\Engine\Plugins\Wwise*. *[DummyUProject]* is the empty Unreal C++ project path, and *[UESource]* is where the Unreal Engine sources are installed. After copying, you can delete the dummy project.

### 3. Add the Project Acoustics Unreal plug-in to your game
 
1. Copy the *Unreal\ProjectAcoustics* folder in the plug-in package. Create a new folder *[UProjectDir]\Plugins\ProjectAcoustics*, where *[UProjectDir]* is your game's project folder that contains the *.uproject* file.


   > [!NOTE]
   > **Engine plugin**: If you're using Wwise as an engine plug-in, you should use Project Acoustics as an Unreal engine plug-in as well. Instead of the destination directory cited above, use *[UESource]\Engine\Plugins\ProjectAcoustics*.

1. Confirm that you see a *Wwise* folder alongside the *ProjectAcoustics* folder. It contains the Wwise plug-in along with binaries for the mixer plug-in that you deployed in step 2.

### 4. Extend Wwise's Unreal plug-in functionality
* The Project Acoustics Unreal plug-in requires that additional behavior be exposed from the Wwise Unreal plug-in API per [these guidelines](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). We've included a batch file to automate the patching procedure. 

* Inside `Plugins\ProjectAcoustics\Resources`, run `PatchWwise.bat`. The example image below uses our AcousticsGame sample project.

    ![Screenshot of Windows Explorer window highlighting provided script to patch Wwise](media/patch-wwise-script.png)

* If you don't have the DirectX SDK installed, depending on the version of Wwise you're using, you might need to comment out the line that contains `DXSDK_DIR` in `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`:

    ![Screenshot of code editor showing DXSDK commented out](media/directx-sdk-comment.png)

* If you compile with Visual Studio 2019, to work around a linking error with Wwise, manually edit the default `VSVersion` value in `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` to `vc150`:

    ![The code editor showing "VSVersion" changed to "vc150"](media/vsversion-comment.png)

### 5. Build the game and check that Python is enabled

1. Compile your game and make sure that it builds correctly. If it doesn't, check the previous steps carefully before you proceed. 

1.  Open your project in Unreal Editor.

    > [!NOTE]
    > **Engine plugin:** If using ProjectAcoustics as engine plugin, also ensure that it's enabled, listed under "built-in" plugins. 

    You should see a new mode, which indicates Project Acoustics has been integrated.

    ![Acoustics Mode Full in Unreal](media/acoustics-mode-full.png)

1.  Confirm you have the Python plugin for Unreal enabled. This is required for the editor integration to function correctly.

    ![Screenshot of enabling Python extensions in Unreal editor](media/ensure-python.png)

### 6. Wwise project setup

An example Wwise project is included with the samples download. We recommend that you view it alongside these instructions. The screenshots later in this article are from this example project.

#### Bus setup
* The Project Acoustics Unreal plugin will look for the associated mixer plugin on a bus with this ***exact*** name: `Project Acoustics Bus`. Create a new audio bus with this name. The mixer plugin can work in various configurations, but for now we assume it will be used to do only reverb processing. This bus will carry the mixed reverb signal for all sources that use Acoustics. It can mix upstream into any bus mixing structure, an example is shown below, taken from our Wwise sample project included in the sample download.

    ![Screenshot of Wwise busses showing Project Acoustics Bus](media/acoustics-bus.png)

* The channel configuration on the bus needs to be set to one of: `1.0, 2.0, 4.0, 5.1 or 7.1`. Other configs will result in no output on this bus.

    ![Screenshot of channel config options for Project Acoustics Bus](media/acoustics-bus-channel-config.png)

* Now go into the Project Acoustics bus details, and ensure you can see the Mixer Plug-in tab

    ![Screenshot of Wwise showing how to enable the Mixer Plug-In tab for the Project Acoustics Bus](media/mixer-tab-enable.png)

* Then go to the Mixer Plug-in tab and add the project acoustics mixer plug-in to the bus

    ![Screenshot of Wwise bus showing how to add the Project Acoustics Mixer Plugin](media/add-mixer-plugin.png)

#### Actor-mixer hierarchy setup
* For performance reasons, Project Acoustics applies audio DSP to all sources simultaneously. This requires the plugin to operate as a mixer plugin. Wwise requires mixer plugins to be on the output bus, though the output bus usually carries the dry output signal. Project Acoustics requires the dry signal be routed through aux busses while the wet signal is carried on the `Project Acoustics Bus`. The following process supports gradual migration to this signal flow.

* Say you have an existing project with an actor-mixer hierarchy containing Footsteps, Weapons, and others at the top level. Each has corresponding output bus for its dry mix. Lets say you want to migrate Footsteps to use acoustics. First create a corresponding aux bus to carry their dry submix that is a child of the Footsteps output bus. For instance, we've used a "Dry" prefix in image below to organize these, although the exact name isn't important. Any meters or effects you had on the Footsteps bus will still function as before.

    ![Screenshot of recommended Wwise Dry Mix Setup](media/wwise-dry-mix-setup.png)

* Then modify the bus output structure for the Footsteps actor-mixer as follows, with Project Acoustics Bus set as Output Bus, and Dry_Footsteps set as a user-defined aux bus.

    ![Screenshot of recommended Wwise Actor Mixer Bus Setup](media/actor-mixer-bus-settings.png)

* Now all footsteps get acoustics treatment and output their reverb on the Project Acoustics Bus. The dry signal is routed through Dry_Footsteps and spatialized as usual.

* Project Acoustics only applies to sounds that have a 3D location in the world. Following [Wwise documentation](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), the positioning properties must be set as shown. The "3D Spatialization" setting can be either "Position" or "Position + Orientation" as needed.

    ![Screenshot of recommended Wwise Actor Positioning Settings](media/wwise-positioning.png)

* Setting the Output Bus to some other bus that mixes upstream into **Project Acoustics Bus** won't work. Wwise imposes this requirement on mixer plugins.

* If you want a child in the Footsteps actor-mixer hierarchy to not use acoustics, you can always use "override parent" on it to opt it out.

* If you're using game- or user-defined sends for reverb on any actor-mixer in the game, turn those off on this actor-mixer to avoid applying reverb twice.

#### Spatialization
By default, the Project Acoustics Wwise mixer plugin applies convolution reverb, leaving Wwise to do panning spatialization. When using Project Acoustics in this default reverb-only configuration, you're free to use any channel configuration and spatialization method on your dry mix, allowing you to mix and match almost any spatializer with Project Acoustics' reverb. Your options include [Ambisonics-based binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) and [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Project Acoustics includes an optional spatializer that supports both object-based high-resolution HRTF rendering, and panning. Check the "Perform Spatialization" checkbox on the mixer plugin settings, and choose between HRTF or Panning and disable user-defined aux sends set up above to all the dry busses to avoid spatializing twice, both with Project Acoustics mixer plugin and Wwise. The spatialization mode can't be changed in real time, because it requires a sound bank regeneration. You must restart Unreal, then regenerate soundbanks before hitting play to pick up mixer plugin config changes such as the 'Perform Spatialization' checkbox.

![Screenshot of Wwise Mixer Plugin Spatialization settings](media/mixer-spatial-settings.png)

Unfortunately, other object-based spatializer plugins can't be supported at this time as they are implemented as mixer plugins, and Wwise doesn't currently allow multiple mixer plugins assigned to a single actor-mixer.  

### 7. Audio setup in Unreal
* First you'll need to bake your game level to produce an acoustics asset, which will be placed in `Content\Acoustics`. Consult the [Unreal Bake Tutorial](unreal-baking.md) and resume here. Some pre-baked levels are included in the sample package.
* Create an Acoustics Space actor in your scene. Only create one of these actors in a level as it represents the acoustics for the whole level. 

    ![Screenshot of Unreal editor showing creation of Acoustics Space actor](media/create-acoustics-space.png)

* Now assign the baked acoustic data asset to the Acoustics Data slot on the Acoustics Space actor. Your scene now has acoustics!

    ![Screenshot of Unreal editor showing acoustics Asset assignment](media/acoustics-asset-assign.png)

* Now add an empty actor and do the following:

    ![Screenshot of Unreal editor showing Acoustics Component usage in an empty actor](media/acoustics-component-usage.png)

1. Add an Acoustics Audio component to the actor. This component extends the Wwise audio component with functionality for Project Acoustics.
2. The Play on Start box is checked by default, which will trigger associated Wwise event on level startup.
3. Use the Show Acoustics Parameters checkbox to print on-screen debug information about the source.  
    ![Screenshot of Unreal editor Acoustics panel on sound source with debug values enabled](media/debug-values.png)
4. Assign a Wwise event per the usual Wwise workflow
5. Ensure that Use Spatial Audio is turned off. At this time, if you use Project Acoustics for a particular audio component, you can't simultaneously use Wwise's Spatial Audio engine for acoustics.

You're all set. Move around the scene and explore the acoustic effects!

## Next steps
* [Design](unreal-workflow.md) tutorial for Project Acoustics in Unreal/Wwise
* [Learn how to do bakes](unreal-baking.md) for your game scenes 
