---
title: Build an enrollment app with React
titleSuffix: Azure Cognitive Services
description: Learn how to set up your development environment and deploy a Face enrollment app to get consent from customers.
author: PatrickFarley
manager: nitinme

ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
---

# Build an enrollment app with React

This guide will show you how to get started with the Face reference enrollment app. The app demonstrates best practices for obtaining meaningful consent to enroll users into the Face API service and how to create high-accuracy enrollments for recognition. The enrollment app is written using JavaScript and the React Native framework, offers a mobile enrollment experience for Android, and can be extended to other platforms as well.

> [!NOTE]
> The sample app in this guide can currently be deployed on Android devices. More deployment options are coming in the future.

## Prerequisites 

* An Azure subscription – [Create one for free](https://azure.microsoft.com/free/cognitive-services/).  
* Once you have your Azure subscription, [create a Face resource](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) in the Azure portal to get your key and endpoint. After it deploys, click **Go to resource**.  
  * You will need the key and endpoint from the resource you created to connect your application to Face API.  
  * For local development and testing, you can paste the API key and endpoint into the configuration file. For final deployment, store the API key in a secure location and never in the code.  

> [!IMPORTANT]
> These subscription keys are used to access your Cognitive Service API. Do not share your keys. Store them securely, for example, using Azure Key Vault. We also recommend regenerating these keys regularly. Only one key is necessary to make an API call. When regenerating the first key, you can use the second key for continued access to the service.

## Set up the development environment

1. To set up your development environment, follow the [React Native documentation](https://reactnative.dev/docs/environment-setup). Select **React Native CLI Quickstart** as your development OS and select **Android** as the target OS. Complete the sections **Installing dependencies** and **Android development environment**. 
1. Do not create a new project. Instead, clone the git repository for the reference enrollment app located here [tbd] to get started.  
1. Open the env.json file in your preferred text editor, such as [Visual Studio Code](https://code.visualstudio.com/), and fill in the configuration values. Paste your Face API endpoint and key into the appropriate fields. This is only for local testing purposes&mdash;do not check in your Face API key to your remote repository.
1. Run the app using either the Android Virtual Device emulator from Android Studio, or your own Android device. To test your app on a physical device, follow the relevant [React Native documentation](https://reactnative.dev/docs/running-on-device).  


## Create an enrollment experience  

Now that you have set up the reference enrollment app, you can tailor it to your own enrollment experience needs.  

Many issues in face recognition are caused by low quality images used for enrollment. There are several factors that can degrade the model performance, such as:
* Face size (faces that are distant from the camera)
* Face orientation (faces turned or tilted away from camera)
* Poor lighting conditions (either low light or backlighting) where the image may be poorly exposed or have too much noise
* Occlusion (partially hidden or obstructed faces) including accessories like hats or thick-rimmed glasses)
* Blur (such as by rapid face movement when the photograph was taken). 

The service provides image quality checks to help developers make the choice of whether the image is of sufficient quality to enroll the customer or attempt face recognition. This app demonstrates how to access frames from the device's camera, select the highest quality frames, and enroll the detected face into the Face API service. 

The app also offers functionality for deleting the user's enrollment and the option to re-enroll.

To extend the app's functionality to cover the full enrollment experience, read the documentation here [link to other documentation] on additional features to implement and best practices.

## Deploy the enrollment app

### Android

First, makey sure that your app is ready for production deployment: remove any keys or secrets from the app code and make sure you have followed the [security best practices](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp).

When you are ready to release your app for production, you'll generate a release-ready APK file, which is the package file format for Android apps. This APK file must be signed with a private key. With this release build, you can begin distributing the app to your devices directly. 

Follow the [Prepare for release](https://developer.android.com/studio/publish/preparing#publishing-build) documentation to learn how to generate a private key, sign your application, and generate a release APK.  

Once you have a signed APK, see the [Publish your app](https://developer.android.com/studio/publish) documentation to learn more about how to release your app.

React Native also offers documentation on this process [here](https://reactnative.dev/docs/signed-apk-android).


## Next Steps  

In this guide, you learned how to set up your development environment and get started with the reference enrollment app. If you are new to React Native, you can read their [getting started docs](https://reactnative.dev/docs/getting-started) to learn more background information. It also may be helpful to familiarize yourself with [Face API](Overview.md). Read the other sections on enrollment app documentation before you begin development.
