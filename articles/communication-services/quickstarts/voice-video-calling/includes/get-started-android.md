---
title: Quickstart - Add VOIP calling to an Android app using Azure Communication Services
description: In this tutorial, you learn how to use the Azure Communication Services Calling client library for Android
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
---

In this quickstart, you'll learn how start a call using the Azure Communication Services Calling client library for Android.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio), for creating your Android application.
- A [User Access Token](../../user-access-tokens.md) for your Azure Communication Service.
- A deployed Communication Services resource. [Create a Communication Services resource](../../create-communication-resource.md).
- **Azure Communication Services (ACS) client libraries** For this quickstart, you'll need to install the Azure Communication Services Common and Calling client libraries.

## Create an Android app with an Empty Activity

From Android Studio, select Start a new Android Studio project.

![Screenshot showing the "Start a new Android Studio Project" button selected in Android Studio.](../media/android/studio-new-project.png)

Select "Empty Activity" project template under "Phone and Tablet".

![Screenshot showing the "Empty Activity" option selected in the Project Template Screen.](../media/android/studio-blank-activity.png)

Select Minimum client library of "API 26: Android 8.0 (Oreo)" or greater.

![Screenshot showing the minimum client library being selected.](../media/android/studio-calling-min-api.png)

## Add the Azure Communication Services Calling client library to Your App

<!-- TODO: update with instructions [here](TODO-MISSING-LINK.md) -->

## Add permissions to application manifest

In order to request permissions required to make a call, they must first be declared in the Application Manifest (`app/src/main/AndroidManifest.xml`). Replace the content of file with the following:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart" xmlns:tools="http://schemas.android.com/tools">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.READ_PRIVILEGED_PHONE_STATE" tools:ignore="ProtectedPermissions" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

## Setup the layout for the app

Two inputs are needed: a text input for the callee id, and a button for placing the call. These can be added through the designer or by editing the layout xml. Create a button with an id of `call_button` and a text input of `callee_id`. Navigate to (`app/src/main/res/layout/activity_main.xml`) and replace the content of file with the following:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

## Create the main activity scaffolding and bindings

With the layout created the bindings can be added as well as the basic scaffolding of the activity. The activity will handle requesting runtime permissions, creating the call agent, and placing the call when the button is pressed. Each will be covered in its own section. The `onCreate` method will be overridden to invoke `getAllPermissions` and `createAgent` as well as add the bindings for the call button. This will occur only once when the activity is created. For more information on `onCreate`, see the guide [Understand the Activity Lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle).

Navigate to **MainActivity.java** and replace the content with the following code:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.communication.calling.CallAgent;
import com.azure.communication.calling.CallClient;
import com.azure.communication.calling.CommunicationUser;
import com.azure.communication.calling.StartCallOptions;
import com.azure.communication.calling.VideoOptions;
import com.azure.communication.common.client.CommunicationUserCredential;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent agent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
    }
}

```

## Request permissions at runtime

For Android 6.0 and higher (API level 23) and `targetSdkVersion` 23 or higher, permissions are granted at runtime instead of when the app is installed. In order to support this, `getAllPermissions` can be implemented to call `ActivityCompat.checkSelfPermission` and `ActivityCompat.requestPermissions` for each required permission.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> When designing your app, consider when these permissions should be requested. Permissions should be requested as they are needed, not ahead of time. For more information see the [Android Permissions Guide.](https://developer.android.com/training/permissions/requesting)

## Create an agent from the user access token

With the user token an authenticated call agent can be instantiated. Generally this token will be generated from a service with authentication specific to the application. For more information on user acces tokens check the [User Access Tokens](../../user-access-tokens.md) guide. For the quickstart, replace `<User_Access_Token>` with a user access token generated for your Azure Communication Service resource.

```java
/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String UserToken = "<User_Access_Token>";

    try {
        CommunicationUserCredential credential = new CommunicationUserCredential(UserToken);
        agent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}
```

## Start a call using the call agent

Placing the call can be done via the call agent, and just requires providing a list of callee ids and the call options. For the quickstart, the default call options without video and a single callee id from the text input will used.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();
    options.setVideoOptions(new VideoOptions(null));
    agent.call(
        getApplicationContext(),
        new CommunicationUser[] {new CommunicationUser(calleeId)},
        options);
}
```


## Launch the app and call the echo bot

The app can now be launched using the "Run App" button on the toolbar (Shift+F10). Verify you are able to place calls by calling `8:echo123`. A pre-recorded message will play then repeat your message back to you.

![Screenshot showing the completed application.](../media/android/quickstart-android-call-echobot.png)
