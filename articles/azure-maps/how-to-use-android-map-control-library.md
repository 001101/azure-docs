---
title: Getting started with Android map control
description: Become familiar with the Microsoft Azure Maps Android SDK. See how to create a project in Android Studio, install the SDK, and create an interactive map.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/17/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
---

# Getting started with Azure Maps Android SDK

The Azure Maps Android SDK is a vector map library for Android. This article guides you through the processes of installing the Azure Maps Android SDK and loading a map.

## Prerequisites

### Create an Azure Maps account

1. [Make an Azure Maps account](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtain a primary subscription key](quick-demo-map-app.md#get-the-primary-key-for-your-account), also known as the primary key or the subscription key.
For more information on authentication in Azure Maps, see [manage authentication in Azure Maps](./how-to-manage-authentication.md).
3. [Download and install Google's Android Studio](https://developer.android.com/studio/).

## Create a project in Android Studio

Complete these steps to create an Android Studio project:

1. Launch Android Studio.
2. Click **+ Create New Project**.
3. On the **Phone and Tablet** tab, click **Empty  Activity**. Click **Next**.
4. Under **Configure your project**, select `API 21: Android 5.0.0 (Lollipop)` as the minimum SDK. This is the earliest version supported by the Azure Maps Android SDK.
5. Select `Java` as the Language.
6. Accept the default `Name` for the project. Click **Finish**.

See the [Android Studio documentation](https://developer.android.com/studio/intro/) for more help with installing Android Studio and creating a new project.

![Create a project in Android studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## Set up a device

To test your application during development, you can use an Android phone or an Android emulator.

You can learn more about setting up an AVD (Android Virtual Device) in the [Android Studio documentation](https://developer.android.com/studio/run/managing-avds).

## Install the Azure Maps Android SDK

The next step in building your application is to install the Azure Maps Android SDK.

Complete these steps to install the SDK:

1. In the Project tab, expand **Gradle Scripts**. Open **build.gradle (Project: My_Application)**, and add the following code to the **all projects**, `repositories`  section:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Open **build.gradle (Module: My_Application)**.

3. Make sure that **minSdkVersion** in the `defaultConfig` section is at API 21 or higher.

4. Add the following code to the Android section:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Add the following code to the `dependencies` section:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. Click **File** on the main toolbar, and then select **Sync Project with Gradle Files**.

7. Open `activity_main.xml` (res \> layout \> activity_main.xml). Click on `Code` view in the upper right hand corner. Add the following XML inside the `<androidx.constraintlayout.widget.ConstraintLayout>` element.
    
    ```XML
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

8. In the **MainActivity.java** file ((java \> com.example.myapplication \> MainActivity.java) you'll need to:

    * Add imports for the Azure Maps SDK.
    * Set your Azure Maps authentication information.
    * Get the map control instance in the **onCreate** method.

    To avoid having to add your authentication information for every application view, we'll set authentication information globally in the `AzureMaps` class by using either the `setSubscriptionKey` or `setAadProperties` methods.

    The map control contains its own lifecycle methods for managing Android's OpenGL lifecycle. These lifecycle methods must be called directly from the containing Activity. For the app to correctly call the map control's lifecycle methods, we'll override the following lifecycle methods in the Activity that contains the map control.

    * onCreate(Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState(Bundle)
    * onLowMemory()

    Edit the **MainActivity.java** file as follows:

    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {

        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.

            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

Android Studio will take a few seconds to build the application. After the build is complete, you can test your application in the emulated Android device. You should see a map like this one:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Maps in Android application":::

## Localizing the map

The Azure Maps Android SDK provides three different ways of setting the language and regional view of the map. The following code shows how to set the language to French ("fr-FR") and the regional view to "auto".

The first option is to pass the language and view regional information into the `AzureMaps` class using the static `setLanguage` and `setView` methods globally. This will set the default language and regional view across all Azure Maps controls loaded in your app.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

The second option is to pass the language and view information into the map control XML.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

The third option is to programmatically set the language and regional view of the map using the maps `setStyle` method. This can be done at any time to change the language and regional view of the map.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Here is an example of Azure Maps with the language set to "fr-FR" and regional view set to "auto".

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps, map image showing labels in French":::

A complete list of supported languages and regional views is documented [here](supported-languages.md).

## Navigating the map

There are several different ways in which the map can be zoomed, panned, rotated, and pitched. The following details all the different ways to navigate the map.

**Zoom the map**

- Touch the map with two fingers and pinch together to zoom out or spread the fingers apart to zoom in.
- Double tap the map to zoom in one level.
- Double tap with two fingers to zoom the map out one level.
- Tap twice; on second tap, hold your finger on the map and drag up to zoom in, or down to zoom out.

**Pan the map**

- Touch the map and drag in any direction.

**Rotate the map**

- Touch the map with two fingers and rotate.

**Pitch the map**

- Touch the map with two fingers and drag them up or down together.

## Next steps

Learn how to add overlay data on the map:

> [!div class="nextstepaction"]
> [Add a symbol layer to an Android map](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Add shapes to an Android map](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Change map styles in Android maps](./set-android-map-styles.md)