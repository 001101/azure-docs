---
title: Azure Media Services v3 overview | Microsoft Docs
description: This article provides a high-level overview of Media Services and provides links to articles for more details.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline

ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/27/2018
ms.author: juliako
ms.custom: mvc
#Customer intent: As a developer or a content provider, I want to encode, stream (on demand or live), analyze my media content so that my customers can: view the content on a wide variety of browsers and devices, gain valuable insights from recorded content.
---

# What is Azure Media Services v3 (preview)?

> [!NOTE]
> The latest version of Azure Media Services is v3. This version is in preview. Find information about the previous (stable) version (v2) [here](https://docs.microsoft.com/azure/media-services/media-services-overview).

Azure Media Services is an extensible cloud-based platform that enables application developers to build solutions that achieve broadcast-quality video streaming, enhance accessibility and distribution, analyze content, and much more. You do not need to be a media content creator to use Azure Media Services. You might be an expert in developing web or mobile apps but don’t have any experience working with and streaming videos. Media Services is for anybody who wants to deliver media experiences of outstanding quality to their viewers. Whether you are a call center, a government agency, an entertainment company, Media Services will help you to easily create media applications that can reach large audiences on today’s most popular mobile devices and browsers. 

The latest version of Media Services is based on a unified API surface which exposes both management and operations functionality built on Azure Resource Manager. This version provides the following capabilities:  

* A templated workflow resource called a [Transform](transform-concept.md). Transforms help you define simple workflows of media processing or analytics tasks - essentially a recipe for processing your video and audio files. You can then apply it repeatedly to process all the files in your content library, by submitting jobs to the Transform.
* Jobs can be submitted using HTTP(s) URLs, SAS URLs, AWS S3 Token URLs, or paths to files located in Azure Blob storage. 
* Notifications are integrated with the Azure Event Grid notification system. You can easily subscribe to events on several resources in Azure Media Services. For example, Job progress or states, or Live Channel start/stop and error events. 
* You can use Azure Resource Management templates to create and deploy Transforms, Streaming Endpoints, Channels, and more.
* Role-based access control can be set at the resource level, allowing you to lock down access to specific resources like Transforms, Content Keys, Channels, and more.
* Client SDKs in multiple languages: .NET, .NET core, Python, Go, Java, and Node.js.

## What can I do with Media Services?

Media Services enables you to build a variety of media workflows in the cloud, the following are some examples of what can be accomplished with Media Services.  

* Deliver videos in various formats so they can be played on a wide variety of browsers and devices. For both on-demand and live streaming delivery to various clients (mobile devices, TV, PC, etc.) the video and audio content needs to be encoded and packaged appropriately. To see how to deliver and stream such content, see [Quickstart: Encode and stream files](stream-files-dotnet-quickstart.md).
* Stream live sporting events to a large online audience, such as soccer, baseball, college and high school sports, and more. 
* Broadcast public meetings and events such as town halls, city council meetings, and legislative bodies.
* Gain valuable insights from recorded videos or audio content. For example, to achieve higher customer satisfaction, organizations can extract speech-to-text and build search indexes and dashboards. Then, they can extract intelligence around common complaints, sources of complaints, and other relevant data. If these are surveillance videos, clients can utilize motion detection and face detection to make the process of reviewing videos fast and accurate.  
* Create a subscription video service and stream DRM protected content when a customer (for example, a movie studio) needs to restrict the access and use of proprietary copyrighted work.
* Deliver offline content for playback on airplanes, trains, and automobiles. A customer might need to download content onto their phone or tablet for playback when they anticipate to be disconnected from the network.
* Add subtitles and captions to videos to cater to a broader audience (for example, people with hearing disabilities or people who want to read along in a different language). 
* Implement an educational e-learning video platform with Azure Media Services and [Azure Cognitive Services APIs](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) for speech-to-text captioning, translating to multi-languages, etc.
* Enable Azure CDN to achieve large scaling to better handle instantaneous high loads (for example, the start of a product launch event.) 

## How can I get started?

As a developer, you can use Media Services [REST API](https://docs.microsoft.com/rest/api/media/) or client libraries that allow you to interact with the REST API, to easily create, manage, and maintain custom media workflows. Microsoft generates and supports the following client libraries: 

* .NET languages
* .NET Core 
* Java
* Node.js
* Python
* Go
* Azure CLI 2.0

Media Services provides [Swagger files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) that you can use to generate SDKs for your preferred language/technology.  

## Next steps

To see how easy it is to start encoding and streaming video files, check out [Stream files](stream-files-dotnet-quickstart.md). 

