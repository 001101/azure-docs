---
title: Quickstart - Add calling to an iOS app using Azure Communication Services
description: In this tutorial, you learn how to use the Azure Communication Services Calling client library for iOS
author: matthewrobertson, LoadLibrary
ms.author: marobert, aurighet
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
---

In this quick start, you'll learn how start a call using the Azure Communication Services Calling client library for iOS.

## Prerequisites

To complete this tutorial, you’ll need the following prerequisites:

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- A Mac running [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), along with a valid developer certificate installed into your Keychain.
- A deployed Communication Services resource. [Create a Communication Services resource](../../create-communication-resource.md).
- A [User Access Token](../../user-access-tokens.md) for your Azure Communication Service.

## Setting up

### Creating the Xcode project

In Xcode, create a new iOS project and select the **Single View App** template. This tutorial uses the [SwiftUI framework](https://developer.apple.com/xcode/swiftui/), so you should set the the **Language** to **Swift** and the **User Interface** to **SwiftUI**. You're not going to create unit tests or UI tests during this quick start. Feel free to uncheck **Include Unit Tests** and also uncheck **Include UI Tests**.

![Create a New Project in Xcode](../../../media/xcode-new-ios-project.png)

### Install the package

Add the Azure Communication Services Calling client library and its dependencies (AzureCore.framework and AzureCommunication.framework) to your project.

1. Download the (Azure Communication Services Calling client library for iOS)[#todo-update-when-released] and download it's contents.
2. In Xcode, click on your project file to and select the build target to open the project settings editor.
3. Under the **General** tab, scroll to the **Frameworks, Libraries, and Embedded Content** section and click the **"+"** icon.
4. In the bottom left of the dialog, chose **Add Files**, navigate to the **SpoolCallingSDK.framework** directory of the un-zipped client library package.
    1. Repeat the last step for adding **AzureCore.framework** and **AzureCommunication.framework**.
5. Open the **Build Settings** tab of the project settings editor and scroll to the **Search Paths** section. Add a new **Framework Search Paths** entry for the directory containing the **SpoolCallingSDK.framework**.
    1. Add another Framework Search Paths entry pointing to the folder containing the dependencies.


> [!NOTE]
> Currently the client library includes multiple binaries, each targeting different architectures. To build an app that runs on the simulator you should select the **SpoolCallingSDK.framework**  included under the **x86_64** directory.


![Update Framework Search Paths](../../../media/xcode-framework-search-paths.png)

### Request access to the microphone

In order to access the device's microphone, you need to update your app's Information Property List with an `NSMicrophoneUsageDescription`. You set the associated value to a `string` that will be included in the dialog the system uses to request request access from the user.

Right-click the `Info.plist` entry of the project tree and select **Open As** > **Source Code**. Add the following lines the top level `<dict>` section, and then save the file.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### Set up the app framework

Open your project's **ContentView.swift** file and add an `import` declaration to the top of the file to import the `ACSCallingclient library`.

```swift
import AzureCommunicationCalling
```

Replace the implementation of the `ContentView` struct with some simple UI controls that enable a user to initate and end a call. We will attach business logic to these controlls in this quickstart.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: ACSCallClient?    
    @State var callAgent: ACSCallAgent?
    @State var call: ACSCall?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {}

    func endCall() {}
}
```

## Object model

The following classes and interfaces handle some of the major features of the Azure Communication Services Calling client library:

| Name                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| [CallClient](../../../references/overview.md)| The CallClient is the main entry point to the Calling client library.|
| [CallAgent](../../../references/overview.md) | The CallAgent is used to start and manage calls. |
| [CommunicationUserCredential](../../../references/overview.md) | The CommunicationUserCredential is used as the token credential to instantiate the CallAgent.|

## Authenticate the client

Initialize a `CallAgent` instance with a User Access Token which will enable us to make and receive calls. Add the following code to the `onAppear` callback in **ContentView.swift**:

```swift
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = ACSCallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```


You need to replace `<USER ACCESS TOKEN>` with a valid user access token for your resource. Refer to the [user access token](../../user-access-tokens.md) documentation if you don't already have a token available.

## Start a call

The `startCall` method is set as the action that will be performed when the *Start Call* button is tapped. Update the implementation to start a call with the `ASACallAgent`:

```swift
func startCall()
{
    let callees:[CommunicationIdentifier] = [CommunicationUser(identifier: self.callee)]
    self.call = self.callAgent?.call(callees, options: ACSStartCallOptions())
}
```

You also can use the properties in `ACSStartCallOptions` to set the initial options for the call (i.e. it allows starting the call with the microphone muted).

## End a call

Implement the `endCall` method to end the current call when the *End Call* button is tapped.

```swift
func endCall()
{
    if let call = call {
        call.hangup(ACSHangupOptions(), withCompletionHandler: { (error) in
            if error != nil {
                print("ERROR: It was not possible to hangup the call.")
            }
        })
    }
}
```

## Run the code


You can build an run your app on iOS simulator by selecting **Product** > **Run** or by using the (&#8984;-R) keyboard shortcut.

![Final look and feel of the quick start app](../../../media/ios-quick-start-make-call.png)

You can make an outbound VOIP call by providing a user ID in the text field and tapping the **Start Call** button. Calling `8:echo123` connects you with an echo bot, this is great for getting started and verifying your audio devices are working. 

> [!NOTE]
> The first time you make a call, the system will prompt you for access to the microphone. In a production application, you should use the `AVAudioSession` API [check the permission status](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) and gracefully update your application's behavior when permission is not granted.


