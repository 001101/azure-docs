---
title: User Access Tokens
description: Learn how to manage users and authenticate them to ACS
author: mikben
manager: jken
services: azure-project-spool

ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-project-spool


---

## Calling Client library samples


### Initialization
To initialize CallClient you have to use CallClientFactory.create method that asynchronously returns call client once it's ready
To create call client you have to pass client token, either as a string or using CommunicationUserCredential object provided
by "@ic3/communicationservices-client" module
* pass token as string


#### [Javascript](#tab/javascript)
```ts
//ARTUR INIT
const communicationUserCredential = 'communicationUserCredential';
const callClient = await CallClientFactory.create(token);
```
#### [Android (Java)](#tab/java)
```java
String rawUserToken = "<user token>";
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
Future<AdHocCallClient> callClientTask = CallClientFactory.create(rawUserToken, appContext);
```
#### [iOS (Swift)](#tab/swift)
```swift
let tokenString = "<user token>";
let callClientInstance: CallClient? = nil;
CallClientFactory.create(tokenString, completionHandler: { (callClient, error) -> Void in
    if(error != nil)
    {
        // handle error
        return;
    }
    
    callClientInstance = callClient;
}));

```
--- 

* pass token as CommunicationUserCredential using string for a token, used for one time, short lived interactions with call client
```ts
const token = 'string';
const callClient = await CallClientFactory.create(new CommunicationUserCredential(token));
```
* pass token as CommunicationUserCredential with callback, used to lazy fetch tokens from trusted service
```ts
const tokenCallback = () => {
    const tokenFromService = await Promise.resolve('communicationUserCredential');
    return tokenFromService;
}
const callClient = await CallClientFactory.create(new CommunicationUserCredential(tokenCallback));
```
above also allows you to refresh token, tokens lifetime is controlled by setting TTL when tokens are provisioned on trusted service
client tokens may eventually expire and so Calling library will call callback to refresh toke
```ts
const communicationUserCredential = new CommunicationUserCredential(() => Promise.resolve(token));
const callClient = await CallClientFactory.create(communicationUserCredential);
```

If your application is initilized with initial **communicationUserCredential** you can choose to pass it to CommunicationUserCredential
to allow it initialize without requesting initial token
```ts
const initialCredential = 'initialCredential';
const communicationUserCredential = new CommunicationUserCredential(() => Promise.resolve(token), initialCredential);
const callClient = await CallClientFactory.create(communicationUserCredential);
``` 

### Make outgoing call

To create and start a call you need to call one of the APIs on CallClient
and provide ACS Identity of a user that you've provisioned using ACS Management SDK on your trusted service
or a normalized phone number.


```ts
const callClient = await CallClientFactory.create('communicationUserCredential');
``` 
Call creation and start is synchronous, as a result you'll receive call instance, allowing you to subscribe to all events on the call.

* make 1:1 call to user, to address other participants use ACS Identities you received from ACS Management SDK when you've created these users on your trusted service

#### [Javascript](#tab/javascript)
```js
const call = callClient.call(['acsId']);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

* make 1:1 call to a pstn number, use phone numbers in e.164 format

#### [Javascript](#tab/javascript)
```js
const call = callClient.call([utils.normalizePhoneNumber('+1234567890')]);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

* make group call

#### [Javascript](#tab/javascript)
```js
const call = callClient.call(['acsId1', 'acsId2', 'acsId3']);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 


### Mid-call operations
During a call you can control modalities

#### Mute/unmute
* [Asynchronous] To mute a call:

#### [Javascript](#tab/javascript)
```js
await call.mute();
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 
* [Asynchronous] To unmute a call:

#### [Javascript](#tab/javascript)
```js
await call.unmute();
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

#### Start/stop sending local video
* [Asynchronous] start local video, pass videoDevice from deviceManager.getCameraList() API enumeration call

#### [Javascript](#tab/javascript)
```js
const localVideoStream = await call.startVideo(videoDevice);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

Once you start sending video 'LocalVideoStream' instance is added to localVideoStreams collection on a call instance

#### [Javascript](#tab/javascript)
```js
call.localVideoStreams[0] === localVideoStream;
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

* [Asynchronous] stop local video, pass localVideoStream you got from call.startVideo() API call

#### [Javascript](#tab/javascript)
```js
await call.stopVideo(localVideoStream);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 
 
### Remote participants management
All remote participants are represented by RemoteParticipant type and available through 'remoteParticipants' collection on a call instance

#### List participants in a call
List all participants in a call

#### [Javascript](#tab/javascript)
```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

Remote participant has set of properties

#### [Javascript](#tab/javascript)
```js
const identity: string = remoteParticipant.identity;

identity.id = 'acsId' | 'phoneNumber'; // can be either ACS Id or a phone number
identity.type = 'user' | 'phoneNumber';

const state: string = remoteParticipant.state; // one of 'Idle' | 'Connecting' | 'Connected' | 'OnHold' | 'InLobby' | 'EarlyMedia' | 'Disconnected';

const callEndReason: AcsError = remoteParticipant.callEndReason; // reason why participant left the call, contains code/subcode/message

const isMuted: boolean = remoteParticipant.isMuted; // indicates if participant is muted

const isSpeaking: boolean = remoteParticipant.isSpeaking; // indicates if participant is speaking

const videoStreams: RemoteVideoStream[] = remoteParticipant.videoStreams; // collection of video streams this participants has [RemoteVideoStream, RemoteVideoStream, ...]

const screenSharingStreams: RemoteVideoStream[] = remoteParticipant.screenSharingStreams; // collection of screen sharing streams this participants has, [RemoteVideoStream, RemoteVideoStream, ...]
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

#### Add participant to a call
To add a participant to a call, either a user or a phone number you have to call 'addParticipant' API
It will synchronously return remote participant instance

#### [Javascript](#tab/javascript)
```js
const remoteParticipant = call.addParticipant('acsId');
const remoteParticipant = call.addParticipant(utils.normalizePhoneNumber('+123456789'));
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

#### Remote participant from a call
To remove participant from a call, either a user or a phone number you have to call 'removeParticipant' API
It will resolve asynchronously

#### [Javascript](#tab/javascript)
```js
await call.removeParticipant(remoteParticipant);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

### Handle remote participant video streams
Remote participant may send video or screen sharing during a call, this sections coves how to discover and handle remote streams

#### Handle remote participant video/screen sharing streams
List streams
To list streams of remote participants inspect his videoStreams or screenSharingStreams collections

#### [Javascript](#tab/javascript)
```js
const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, RemoteVideoStream ...]
const screenSharingStreams = remoteParticipant.screenSharingStreams; // [RemoteVideoStream, RemoteVideoStream ...]
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
---
##### RemoteVideoStream
RemoteVideoStream represents remote video or screen sharing stream that this participant sends in a call

#### [Javascript](#tab/javascript)
```js
const remoteParticipantStream = call.remoteParticipants[0].videoStreams[0];
const remoteParticipantStream = call.remoteParticipants[0].screenSharingStreams[0];
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 
It has following properties:

#### [Javascript](#tab/javascript)
```js
const type: string = remoteParticipantStream.type; // 'Video' | 'ScreenSharing';

const type: boolean = remoteParticipantStream.isAvailable; // indicates if remote stream is available

const activeRenderers: RemoteVideoRenderer[] = remoteParticipantStream.activeRenderers; // collection of active renderers rendering given stream
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

You can subscribe to 'availabilityChanged' and 'activeRenderersChanged' events 

##### Render remote participant stream
To start rendering remote participant stream


#### [Javascript](#tab/javascript)
```js
const remoteVideoRenderer = await remoteParticipantStream.render(target, scalingMode?);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

Where:
* [HTMLNode] target - an HTML node that should be used as a placeholder for stream to render in
* [string] scalingMode - one of 'Stretch' | 'Crop' | 'Fit'

##### RemoteVideoRenderer
Represents remote video stream renderer, it has following properties

#### [Javascript](#tab/javascript)
```js
// [bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering; 
// [string] scalingMode one of 'Stretch' | 'Crop' | 'Fit'
remoteVideoRenderer.scalingMode
// HTMLNode] target an HTML node that should be used as a placeholder for stream to render in
remoteVideoRenderer.target
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 
As a result of this call remoteVideoRenderer is added to activeRenderes collection

#### [Javascript](#tab/javascript)
```js
remotePArticipantStream.activeRenderers[0] === remoteVideoRenderer;
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

RemoteVideoRenderer instance has following methods

#### [Javascript](#tab/javascript)
```js
remoteVideoRenderer.setScalingMode(scalingModel); // 'Stretch' | 'Crop' | 'Fit', change scaling mode

await remoteVideoRenderer.pause(); // pause rendering

await remoteVideoRenderer.resume(); // resume rendering
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

### DeviceManager
Device manager lets you enumerate local devices that can be used in a call to send your audio/video streams
It also allows you to request permission from user to access microphone/camera using native browser API
You can access deviceManager on a callClient object

#### [Javascript](#tab/javascript)
```js
const deviceManager = callClient.deviceManager;
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

#### Request permission to camera/microphone
To prompt user to grant permission to his camera/microphone call

#### [Javascript](#tab/javascript)
```js
const result = await deviceManager.askDevicePermission(audio: true, video: true); // resolves with Promise<IDeviceAccess>;
// result.audio = true|false
// result.video = true|false
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

You can check what's the current permission state for a given type by calling

#### [Javascript](#tab/javascript)
```js
const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 

#### Enumerate local devices
Enumeration is asynchronous

#### [Javascript](#tab/javascript)
```js
// enumerate local cameras
const localCameras = await deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
const localMicrophones = await deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
const localSpeakers = await deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 
#### Set default camera/microphone/speaker
Device mananager allows you to set a default device that will be used when starting a call
If not ACS will fallback to OS defaults

* get/select default devices

#### [Javascript](#tab/javascript)
```js

// get default camera
const defaultCamera = deviceManager.getCamera();

// [asynchronous] set default camera
await deviceManager.setCamera(VideoDeviceInfo);

// get default microphone
const defaulttMicrophone = deviceManager.gettMicrophone();

// [asynchronous] set default microphone
await devicetMicrophone.settMicrophone(AudioDeviceInfo);

// get default speaker
const defaultSpeaker = deviceManager.getSpeaker();

// [asynchronous] set default speaker
await deviceManager.setSpeaker(AudioDeviceInfo);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 
#### Local camera preview
You can use deviceManager to start render stream from your local camera, this stream won't be send to other participants, it's local preview feed
* [asynchronous] start local video preview

#### [Javascript](#tab/javascript)
```js
const previewRenderer = await deviceManager.renderPreviewVideo(VideoDeviceInfo, HTMLNode, ScalingModel);
await previewRenderer.start();
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 
Preview renderer has set of properties and methods that allows you to control it:

#### [Javascript](#tab/javascript)
```js

// [bool] isRendering
previewRenderer.isRendering
// [HTMLNode] target
previewRenderer.target
// [string] scalingMode
previewRenderer.scalingMode
// [VideoDeviceInfo] videoDeviceInfo
previewRenderer.videoDeviceInfo

// [asynchronous] start
previewRenderer.start();
// [asynchronous] stop
previewRenderer.stop();
// [asynchronous] switchDevice
previewRenderer.switchDevice(VideoDeviceInfo);
// setScalingMode
previewRenderer.setScalingMode(ScalingMode);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 
 

### Event model
Most of properties and collections can change it's value.
To subscribe to these changes you can use following:
#### Properties
To subscribe to property change event:

#### [Javascript](#tab/javascript)
```js
const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 
#### Collections
To subscribe to collection updated event:

#### [Javascript](#tab/javascript)
```js
const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);
```
#### [Android (Java)](#tab/java)
```java
// bar
```
#### [iOS (Swift)](#tab/swift)
```swift
// swift
```
--- 





