---
title: Media Services release notes | Microsoft Docs
description: Media Services release notes
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''

ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako

---
# Azure Media Services release notes
These release notes for Azure Media Services summarize changes from previous releases and known issues.

> [!NOTE]
> We want to hear from our customers so that we can focus on fixing problems that affect you. To report a problem or ask questions, submit a post in the [Azure Media Services MSDN Forum].
> 
> 

## <a id="issues"/>Currently known issues
### <a id="general_issues"/>Media Services general issues

| Issue | Description |
| --- | --- |
| Several common HTTP headers aren't provided in the REST API. |If you develop Media Services applications by using the REST API, you find that some common HTTP header fields (including CLIENT-REQUEST-ID, REQUEST-ID, and RETURN-CLIENT-REQUEST-ID) aren't supported. The headers will be added in a future update. |
| Percent-encoding isn't allowed. |Media Services uses the value of the IAssetFile.Name property when building URLs for the streaming content (for example, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) For this reason, percent-encoding isn't allowed. The value of the **Name** property can't have any of the following [percent-encoding-reserved characters](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Also, there can be only one ‘.’ for the file name extension. |
| The ListBlobs method that is part of the Azure Storage SDK version 3.x fails. |Media Services generates SAS URLs based on the [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) version. If you want to use the Storage SDK to list blobs in a blob container, use the [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) method that is part of the Storage SDK version 2.x. The ListBlobs method that is part of the Storage SDK version 3.x fails. |
| The Media Services throttling mechanism restricts the resource usage for applications that make excessive request to the service. The service might return the "Service Unavailable" (503) HTTP status code. |For more information, see the description of the 503 HTTP status code in [Media Services error codes](media-services-encoding-error-codes.md). |
| When you query entities, a limit of 1,000 entities is returned at one time because the public REST version 2 limits query results to 1,000 results. |You need to use **Skip** and **Take** (.NET)/ **top** (REST) as described in [this .NET example](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) and [this REST API example](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Some clients can come across a repeat tag issue in the Smooth Streaming manifest. |For more information, see [this section](media-services-deliver-content-overview.md#known-issues). |
| Media Services .NET SDK objects can't be serialized and as a result don't work with Azure Caching. |If you try to serialize the SDK AssetCollection object to add it to Azure Caching, an exception is thrown. |


## <a id="rest_version_history"/>REST API version history
For information about the Media Services REST API version history, see the [Azure Media Services REST API reference].

## October 2017 release
> [!IMPORTANT] 
> Media Services is deprecating support for Azure Acess Control Service authentication keys. On June 1, 2018, you will no longer be able to authenticate with the Media Services back end via code by using Acess Control Service keys. You must update your code to use Azure Active Directory (Azure AD) per [Azure AD-based authentication](media-services-use-aad-auth-to-access-ams-api.md). Watch for warnings about this change in the Azure portal.

### Updates for October 2017
#### SDKs
* The .NET SDK was updated to support Azure AD authentication. Support for Access Control Service authentication was removed from the latest .NET SDK on Nuget.org to encourage faster migration to Azure AD. 
* The JAVA SDK was updated to support Azure AD authentication. Support for Azure AD authentication was added to the Java SDK. For information on how to use the Java SDK with Media Services, see [Get started with the Java client SDK for Azure Media Services](media-services-java-how-to-use.md)

#### File-based encoding
* You can now use the Premium Encoder to encode your content to the H.265 high-efficiency video coding (HEVC) video codec. There is no pricing impact if you choose H.265 over other codecs like H.264. For information about HEVC patent licenses, see the [Online Services Terms](https://azure.microsoft.com/support/legal/).

* For source video that is encoded with the H.265 (HEVC) video codec, such as video captured by using iOS11 or GoPro Hero 6, you now can use either the Premium Encoder or the Standard Encoder. For information about patent licenses, see [Online Services Terms](https://azure.microsoft.com/support/legal/).

* For content that contains multiple language audio tracks, the language values must be correctly labeled according to the corresponding file format specification (for example, ISO MP4). Then you can use the Standard Encoder to encode the content for streaming. The resultant streaming locator lists the available audio languages.

* The Standard Encoder now supports two new audio-only system presets, "AAC Audio" and "AAC Good Quality Audio." Both produce stereo advanced audio coding (AAC) output, at bit rates of 128 Kbps and 192 Kbps, respectively.

* The Premium Encoder now supports QuickTime/MOV file formats as input. The video codec must be one of the [Apple ProRes flavors listed here](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). The audio must be either AAC or pulse code modulation (PCM).

> [!NOTE]
> The Premium Encoder doesn't support, for example, DVC/DVCPro video wrapped in QuickTime/MOV files, as input. The Standard Encoder supports these video codecs.
>
>

* Bug fixes in encoders:
    * You can now submit jobs by using an input asset. After these finish, you can modify the asset (for example, add, delete, or rename files within the asset), and submit additional jobs. 
    * The quality of JPEG thumbnails produced by the Standard Encoder is improved.
    * The Standard Encoder handles input metadata and thumbnail generation better in very short duration videos.
    * Improvements to the H.264 decoder used in the Standard Encoder eliminate certain rare artifacts. 

#### Media Analytics
General availability of the Azure Media Redactor: This media processor performs anonymization by blurring the faces of selected individuals and is ideal for use in public safety and news media scenarios. 

For an overview on this new processor, see [this blog post](https://azure.microsoft.com/blog/azure-media-redactor/). For information on documentation and settings, see [Redact faces with Azure Media Analytics](media-services-face-redaction.md).



## June 2017 release

Media Services now supports [Azure AD-based authentication](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Currently, Media Services supports the Access Control Service authentication model. Access Control Service authorization will be deprecated on June 1, 2018. We recommend that you migrate to the Azure AD authentication model as soon as possible.

## March 2017 release

You can now use the Standard Encoder to [auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) by specifying the "Adaptive Streaming" preset string when you create an encoding task. If you want to encode a video for streaming with Media Services, use the "Adaptive Streaming" preset. If you need to customize an encoding preset for your specific scenario, you can begin with [these presets](media-services-mes-presets-overview.md).

You can now use Media Encoder Standard or Media Encoder Premium Workflow to [create an encoding task that generates fMP4 chunks](media-services-generate-fmp4-chunks.md). 

## February 2017 release

Starting April 1, 2017, any job record in your account older than 90 days is automatically deleted, along with its associated task records, even if the total number of records is below the maximum quota. If you need to archive the job/task information, you can use the code described in [Manage assets and related entities with the Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## January 2017 release

In Media Services, a streaming endpoint represents a streaming service that can deliver content directly to a client player application or to a content delivery network (CDN) for further distribution. Media Services also provides seamless Azure CDN integration. The outbound stream from a StreamingEndpoint service can be a live stream, a video on demand, or a progressive download of your asset in your Media Services account. Each  Media Services account includes a default streaming endpoint. Additional streaming endpoints can be created under the account. There are two versions of streaming endpoints, 1.0 and 2.0. Starting January 10, 2017, any newly created Media Services accounts include the version 2.0 default streaming endpoint. Additional streaming endpoints that you add to this account are also version 2.0. This change doesn't affect existing accounts. Existing streaming endpoints are version 1.0 and can be upgraded to version 2.0. There are behavior, billing, and feature changes with this change. For more information, see [Streaming endpoints overview](media-services-streaming-endpoints-overview.md).

Starting with the 2.15 version, Media Services added the following properties to the streaming endpoint entity:

* **CdnProvider** 
* **CdnProfile** 
* **FreeTrialEndTime** 
* **StreamingEndpointVersion** 

For more information on these properties, see [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## December 2016 release

 You now can use Media Services to access telemetry/metrics data for its services. You can use the current version of Media Services to collect telemetry data for live channel, streaming endpoint, and archive entities. For more information, see [Media Services telemetry](media-services-telemetry-overview.md).

## <a id="july_changes16"/>July 2016 release
### Updates to the manifest file (*.ISM) generated by encoding tasks
When an encoding task is submitted to Media Encoder Standard or Media Encoder Premium, the encoding task generates a [streaming manifest file](media-services-deliver-content-overview.md) (*.ism) in the output asset. With the latest service release, the syntax of this streaming manifest file was updated.

> [!NOTE]
> The syntax of the streaming manifest (.ism) file is reserved for internal use. It's subject to change in future releases. Do not modify or manipulate the contents of this file.
> 
> 

### A new client manifest (*.ISMC) file is generated in the output asset when an encoding task outputs one or more MP4 files
Starting with the latest service release, after the completion of an encoding task that generates one or more MP4 files, the output asset also contains a streaming client manifest (*.ismc) file. The .ismc file helps improve the performance of dynamic streaming. 

> [!NOTE]
> The syntax of the client manifest (.ismc) file is reserved for internal use. It's subject to change in future releases. Do not modify or manipulate the contents of this file.
> 
> 

For more information, see [this blog](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### Known issues
Some clients can come across a repeat tag issue in the Smooth Streaming manifest. For more information, see [this section](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>April 2016 release
### Media Analytics
 Media Services introduced Media Analytics for powerful video intelligence. For more information, see [Media Services Analytics overview](media-services-analytics-overview.md).

### Apple FairPlay (Preview)
Media Services now enables you to dynamically encrypt your HTTP Live Streaming (HLS) content with Apple FairPlay. You also can use the Media Services license delivery service to deliver FairPlay licenses to clients. For more information, see "Use Azure Media Services to stream your HLS content protected with Apple FairPlay."

## <a id="feb_changes16"></a>February 2016 release
The latest version of the Media Services SDK for .NET (3.5.3) contains a Google Widevine-related bug fix. The problem was that AssetDeliveryPolicy couldn't be reused for multiple assets encrypted with Widevine. As part of this bug fix, the following property was added to the SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>January 2016 release
Encoding reserved units were renamed to reduce confusion with encoder names.

The Basic, Standard, and Premium encoding reserved units were renamed to S1, S2, and S3 reserved units, respectively. Customers who use Basic encoding reserved units today see S1 as the label in the Azure portal (and in the bill). Customers who use Standard and Premium see the labels S2 and S3, respectively. 

## <a id="dec_changes_15"></a>December 2015 release

### Media Encoder deprecation announcement

 Media Encoder will be deprecated starting in approximately 12 months from the release of Media Encoder Standard.

### Azure SDK for PHP
The Azure SDK team published a new release of the [Azure SDK for PHP](http://github.com/Azure/azure-sdk-for-php) package that contains updates and new features for Media Services. In particular, the Media Services SDK for PHP now supports the latest [content protection](media-services-content-protection-overview.md) features. These features are dynamic encryption with AES and DRM (PlayReady and Widevine) with and without token restrictions. It also supports scaling [encoding units](media-services-dotnet-encoding-units.md).

For more information, see:

* The [Media Services SDK for PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) blog.
* The following [code samples](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) to help you get started quickly:
  * **vodworkflow_aes.php**: This is a PHP file that shows how to use AES-128 dynamic encryption and the key delivery service. It's based on the .NET sample explained in [Use AES-128 dynamic encryption and the key delivery service](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: This is a PHP file that shows how to use PlayReady dynamic encryption and the  license delivery service. It's based on the .NET sample explained in [Use PlayReady and/or Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: This is a PHP file that shows how to scale encoding reserved units.

## <a id="nov_changes_15"></a>November 2015 release
 Media Services now offers the Widevine license delivery service in the cloud. For more information, see [this blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Also, see [this tutorial](media-services-protect-with-playready-widevine.md) and the [GitHub repository](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Widevine license delivery services provided by Media Sevices are in preview. For more information, see [this blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>October 2015 release
Media Services is now live in the following data centers: Brazil South, India West, India South, and India Central. You can now use the Azure portal to [create Media Service accounts](media-services-portal-create-account.md) and perform various tasks described in the [Media Services documentation webpage](https://azure.microsoft.com/documentation/services/media-services/). Live Encoding isn't enabled in these data centers. Further, not all types of encoding reserved units are available in these data centers.

* Brazil South:                                          Only Standard and Basic encoding reserved units are available.
* India West, India South, and India Central:             Only Basic encoding reserved units are available.

## <a id="september_changes_15"></a>September 2015 release
Media Services now offers the ability to protect both video on demand and live streams with Widevine modular DRM technology. You can use the following delivery services partners to help you deliver Widevine licenses:
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

For more information, see [this blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
You can use the [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (starting with version 3.5.1) or the REST API to configure AssetDeliveryConfiguration to use Widevine.  
* Media Services added support for Apple ProRes videos. You can now upload your QuickTime source videos files that use Apple ProRes or other codecs. For more information, see [this blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* You can now use Media Encoder Standard to do subclipping and live archive extraction. For more information, see [this blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* The following filtering updates were made: 
  
  * You can now use the Apple HLS format with an audio-only filter. You can use this update to remove an audio-only track by specifying (audio-only=false) in the URL.
  * When defining filters for your assets, you now have the ability to combine multiple (up to three) filters in a single URL.
    
    For more information, see [this](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.
* Media Services now supports I-frames in HLS version 4. I-frame support optimizes fast-forward and rewind operations. By default, all HLS version 4 outputs include the I-frame playlist (EXT-X-I-FRAME-STREAM-INF).
  For more information, see [this blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>August 2015 release
* The Media Services SDK for the Java v0.8.0 release and new samples are now available. For more information, see:
  
  * [This blog post](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [The Java samples repository](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* The Azure Media Player was updated with multi-audio stream support. For more information, see [this blog post](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>July 2015 release
* he general availability of Media Encoder Standard was announced. For more information, see [this blog post](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard uses presets, as described in [this section](http://go.microsoft.com/fwlink/?LinkId=618336). When you use a preset for 4k encodes, get the Premium reserved unit type. For more information, see [Scale encoding](media-services-scale-media-processing-overview.md).
* Live real-time captions were used with Media Services and the Media Player. For more information, see [this blog post](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

### Media Services .NET SDK updates
The Media Services .NET SDK is now version 3.4.0.0. The following functionality was added in this release:  

* Implemented support for live archive. You can't download an asset that contains a live archive.
* Implemented support for dynamic filters.
* Implemented functionality that allows users to keep a storage container while deleting an asset.
* Made bug fixes related to retry policies in channels.
* Enabled Media Encoder Premium Workflow.

## <a id="june_changes_15"></a>June 2015 release
### Media Services .NET SDK updates
The Media Services .NET SDK is now version 3.3.0.0. The following functionality was added in this release:  

* Support for the OpenId Connect Discovery spec
* Support for handling keys rollover on the identity provider side

If you use an identity provider that exposes an OpenID Connect discovery document (as Azure AD, Google, and Salesforce do), you can instruct Media Services to obtain signing keys for validation of JSON Web Tokens (JWT) from the OpenID connect discovery spec. 

For more information, see [Use JSON web keys from the OpenID Connect discovery spec to work with JWT authentication in Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>May 2015 release
Announcing the following new features:

* [A preview of live encoding with Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamic manifest](media-services-dynamic-manifest-overview.md)
* [A preview of the Azure Media Hyperlapse media processor](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>April 2015 release
### General Media Services updates
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) was announced.
* Starting with the Media Services REST 2.10, channels that are configured to ingest a Real-Time Messaging Protocol (RTMP) are created with primary and secondary ingest URLs. For more information, see [Channel ingest configurations](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer was updated.
* Support for Spanish language was added.
* A new configuration for the XML format was added.

For more information, see [this blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### Media Services .NET SDK updates
The Media Services .NET SDK is now version 3.2.0.0. The following updates were made:

* Breaking change: TokenRestrictionTemplate.Issuer and TokenRestrictionTemplate.Audience were changed to be of a string type.
* Updates were made related to creating custom retry policies.
* Bug fixes were made related to uploading and downloading files.
* The MediaServicesCredentials class now accepts primary and secondary access control endpoints to authenticate against.

## <a id="march_changes_15"></a>March 2015 release
### General Media Services updates
* Media Services now provides Azure CDN integration. To support the integration, the CdnEnabled property was added to StreamingEndpoint. CdnEnabled can be used with REST APIs starting with version 2.9. For more information, see [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled can be used with the .NET SDK starting with version 3.1.0.2. For more information, see [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* The Media Encoder Premium Workflow was announced. For more information, see [Introducing Premium encoding in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>February 2015 release
### General Media Services updates
The Media Services REST API is now version 2.9. Starting with this version, you can enable the Azure CDN integration with streaming endpoints. For more information, see [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>January 2015 release
### General Media Services updates
The general availability of content protection with dynamic encryption was announced. For more information, see [Media Services enhances streaming security with general availability of DRM technology](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### Media Services .NET SDK updates
The Media Services .NET SDK is now version 3.1.0.1.

This release marked the default Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate constructor as obsolete. The new constructor takes TokenType as an argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>December 2014 release
### General Media Services updates
* Some updates and new features were added to the Media Indexer. For more information, see [Azure Media Indexer version 1.1.6.7 release notes](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* A new REST API was added that you can use to update encoding reserved units. For more information, see  [EncodingReservedUnitType with REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* CORS support was added for the key delivery service.
* Performance improvements were made to querying authorization policy options.
* In the China data center, the [key delivery URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) is now per customer (just like in other data centers).
* HLS auto target duration was added. When doing live streaming, HLS is always packaged dynamically. By default, Media Services automatically calculates the HLS segment packaging ratio (FragmentsPerSegment) based on the keyframe interval (KeyFrameInterval). This method is also referred to as a group of pictures (GOP) that is received from the live encoder. For more information, see [Work with Media Services live streaming].

### Media Services .NET SDK updates
* [Azure Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) is now version 3.1.0.0. The following updates were made:

* Upgraded the .Net SDK dependency to the .NET 4.5 Framework.
* Added a new API that you can use to update encoding reserved units. For more information, see [Update reserved unit type and increase encoding reserved units by using .NET](media-services-dotnet-encoding-units.md).
* Added JWT support for token authentication. For more information, see [JWT token authentication in Media Services and dynamic encryption](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Added relative offsets for BeginDate and ExpirationDate in the PlayReady license template.

## <a id="november_changes_14"></a>November 2014 release
* Media Services now enables you to ingest a live Smooth Streaming (FMP4) content over an SSL connection. To ingest over SSL, make sure to update the ingest URL to HTTPS.  Currently, Media Services doesn’t support SSL with custom domains.  For more information about live streaming, see [Working with Azure Media Services Live Streaming].
* Currently, you cannot ingest an RTMP live stream over an SSL connection.
* You can only stream over SSL if the streaming endpoint from which you deliver your content was created after September 10, 2014. If your streaming URLs are based on the streaming endpoints created after September 10, the URL contains “streaming.mediaservices.windows.net” (the new format). Streaming URLs that contain “origin.mediaservices.windows.net” (the old format) do not support SSL. If your URL is in the old format and you want to be able to stream over SSL, [create a new streaming endpoint](media-services-portal-manage-streaming-endpoints.md). Use URLs created based on the new streaming endpoint to stream your content over SSL.

## <a id="october_changes_14"></a>October 2014 release
### <a id="new_encoder_release"></a>Media Services Encoder release
Announcing the new release of Media Services Azure Media Encoder. With the latest Azure Media Encoder, you are only charged for output GBs, but otherwise the new encoder is feature compatible with the previous encoder. For more information [Media Services Pricing Details]).

### <a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK for .NET extensions is now version 2.0.0.3.

Media Services SDK for .NET is now version 3.0.0.8. The following changes were made:

* Refactoring in retry policy classes.
* Adding user agent string to http request headers.
* Adding NuGet restore build step.
* Fixing scenario tests to use x509 cert from repository.
* Validating settings when updating channel and streaming end.

### New GitHub repository to host Media Services samples
Samples are located in [Azure Media Services samples GitHub repository](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>September 2014 release
Media Services REST metadata is now version 2.7. For more information about the latest REST updates, see [Azure Media Services REST API Reference].

Media Services SDK for .NET is now version 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Breaking changes
* **Origin** was renamed to [StreamingEndpoint].
* A change in the default behavior when using the **Azure portal** to encode and then publish MP4 files.

### <a id="sept_14_GA_changes"></a>New features/scenarios that are part of the general availability release
* **Indexer Media Processor**. For more information, see [Indexing Media Files with Azure Media Indexer].
* You can use the [StreamingEndpoint] entity to add custom domain (host) names.
  
    To use a custom domain name as the Media Services streaming endpoint name, add custom host names to your streaming endpoint. Use the Media Services REST APIs or .NET SDK to add custom host names.
  
    The following considerations apply:
  
  * You must have the ownership of the custom domain name.
  * The ownership of the domain name must be validated by Azure Media Services. To validate the domain, create a CName that maps <MediaServicesAccountId> <parent domain> to verify DNS.<mediaservices-dns-zone>. 
  * You must create another CName that maps the custom host name (eg: sports.contoso.com) to your Media Services StreamingEndpont’s host name (for example, amstest.streaming.mediaservices.windows.net).

    For more information, see the **CustomHostNames** property in the [StreamingEndpoint] article.

### <a id="sept_14_preview_changes"></a>New features/scenarios that are part of the public preview release
* Live Streaming Preview. For more information, see [Working with Azure Media Services Live Streaming].
* Key Delivery Service. For more information, see [Using AES-128 Dynamic Encryption and Key Delivery Service].
* AES Dynamic Encryption. For more information, see [Using AES-128 Dynamic Encryption and Key Delivery Service].
* PlayReady License Delivery Service. For more information, see [Using PlayReady Dynamic Encryption and License Delivery Service].
* PlayReady Dynamic Encryption. For more information, see [Using PlayReady Dynamic Encryption and License Delivery Service].
* Media Services PlayReady License Template. For more information, see [Media Services PlayReady License Template Overview].
* Streaming storage encrypted assets. For more information, see [Streaming Storage Encrypted Content].

## <a id="august_changes_14"></a>August 2014 release
When you encode an asset, an output asset is produced upon completion of the encoding job. Until this release, Azure Media Services Encoder produced metadata about output assets. Starting with this release the encoder also produces metadata about input assets. For more information, see the [Input Metadata] and [Output Metadata] articles.

## <a id="july_changes_14"></a>July 2014 Release
The following bug fixes were made for the Azure Media Services Packager and Encryptor:

* Only audio plays back when transmuxing a live archive asset to HLS – this has been fixed and now both audio and video are played.
* When packaging an asset to HLS and AES 128-bit envelope encryption, the packaged streams do not play back on Android devices – this bug has been fixed and the packaged stream plays back on Android devices that support HLS.

## <a id="may_changes_14"></a>May 2014 release
### <a id="may_14_changes"></a>General Media Services updates
You can now use [Dynamic Packaging] to stream HLS version 3. To stream HLS version 3, add the following format to the origin locator path: * .ism/manifest(format=m3u8-aapl-v3). For more information, see [Nick Drouin's Blog].

Dynamic Packaging now also supports delivering HLS (version 3 and version 4) encrypted with PlayReady based on Smooth Streaming statically encrypted with PlayReady. For information on how to encrypt Smooth Streaming with PlayReady, see [Protecting Smooth Stream with PlayReady].

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK updates
The following improvements are included in the Media Services .NET SDK 3.0.0.5 release:

* Better speed and resilience for uploading/downloading media assets.
* Improvements in retry logic and transient exception handling: 
  
  * Transient error detection and retry logic were improved for exceptions that are caused by querying, saving changes, uploading or downloading files. 
  * When getting Web Exceptions (for example, during an Access Control Service token request), you notice that fatal errors are failing faster now.

For more information, see [Retry Logic in the Media Services SDK for .NET].

## <a id="april_changes_14"></a>April 2014 Encoder release
### <a name="april_14_enocer_changes"></a>Media Services Encoder updates
* Added support for ingesting AVI files authored using the Grass Valley EDIUS nonlinear editor, where the video is lightly compressed using Grass Valley HQ/HQX codec. For more information, see [Grass Valley Announces EDIUS 7 Streaming Through the Cloud].
* Added support for specifying the naming convention for the files produced by the Media Encoder. For more information, see [Controlling Media Service Encoder Output Filenames].
* Added support for video and/or audio overlays. For more information, see [Creating Overlays].
* Added support for stitching together multiple video segments. For more information, see [Stitching Video Segments].
* Fixed a bug related to transcoding of MP4s where the audio has been encoded with MPEG-1 Audio layer 3 (aka MP3).

## <a id="jan_feb_changes_14"></a>January/February 2014 releases
### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1, 3.0.0.2 and 3.0.0.3
The changes in 3.0.0.1 and 3.0.0.2 include:

* Fixed issues related to usage of LINQ queries with OrderBy statements.
* Split test solutions in [GitHub] into Unit-based tests and Scenario-based tests.

For more information about the changes, see: [Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases].

The following changes were made in 3.0.0.3:

* Upgraded Azure storage dependencies to use version 3.0.3.0. 
* Fixed backward compatibility issue for 3.0.*.* releases. 

## <a id="december_changes_13"></a>December 2013 release
### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3.0.x.x releases are not backward compatible with 2.4.x.x releases.
> 
> 

The latest version of the Media Services SDK is now 3.0.0.0. You can download the latest package from NuGet or get the bits from [GitHub].

Starting with the Media Services SDK version 3.0.0.0, you can reuse the [Azure AD Access Control Service] tokens. For more information, see the section "Reuse Access Control Service Tokens" in [Connect to Media Services with the Media Services SDK for .NET].

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK extensions 2.0.0.0
The Azure Media Services .NET SDK extensions is a set of extension methods and helper functions that simplify your code and make it easier to develop with Media Services. You can get the latest bits from [Media Services .NET SDK extensions].

## <a id="november_changes_13"></a>November 2013 release
### <a name="nov_13_donnet_changes"></a>Azure Media Services .NET SDK changes
Starting with this version, the Media Services SDK for .NET handles transient fault errors that might occur when calls are made to the Media Services REST API layer.

## <a id="august_changes_13"></a>August 2013 release
### <a name="aug_13_powershell_changes"></a>Media Services PowerShell cmdlets included in Azure SDK Tools
The following Media Services PowerShell cmdlets are now included in [azure-sdk-tools]:

* Get-AzureMediaServices 
  
    For example, `Get-AzureMediaServicesAccount`.
* New-AzureMediaServicesAccount 
  
    For example, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.
* New-AzureMediaServicesKey 
  
    For example, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.
* Remove-AzureMediaServicesAccount 
  
    For example, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <a id="june_changes_13"></a>June 2013 release
### <a name="june_13_general_changes"></a>Azure Media Services changes
The changes mentioned in this section are updates included in the June 2013 Media Services releases.

* Ability to link multiple storage accounts to a Media Service account. 
  
    StorageAccount
  
    Asset.StorageAccountName and Asset.StorageAccount
* Ability to update Job.Priority. 
* Notification-related entities and properties: 
  
    JobNotificationSubscription
  
    NotificationEndPoint
  
    Job
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Azure Media Services .NET SDK changes
The following changes are included in June 2013 Media Services SDK releases. The latest Media Services SDK is available on GitHub.

* Starting with version 2.3.0.0, the Media Services SDK supports linking multiple storage accounts to a Media Services account. The following APIs support this feature:
  
    The IStorageAccount type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts property.
  
    The StorageAccount property.
  
    The StorageAccountName property.
  
    For more information, see [Manage Media Services Assets across multiple storage accounts].
* Notification-related APIs. Starting with version 2.2.0.0, you can listen to Azure Queue storage notifications. For more information, see [Handle Media Services job notifications].
  
    The Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions property.
  
    The Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState type.
* Dependency on the Storage client SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).
* Dependency on OData 5.5 (Microsoft.Data.OData.dll).

## <a id="december_changes_12"></a>December 2012 release
### <a name="dec_12_dotnet_changes"></a>Azure Media Services .NET SDK changes
* IntelliSense: Added missing IntelliSense documentation for many types.
* Microsoft.Practices.TransientFaultHandling.Core: Fixed an issue where the SDK still had a dependency to an old version of this assembly. The SDK now references version 5.1.1209.1 of this assembly.

Fixes for issues found in the November 2012 SDK:

* IAsset.Locators.Count: This count is now correctly reported on new IAsset interfaces after all locators are deleted.
* IAssetFile.ContentFileSize: This value is now properly set after an upload by IAssetFile.Upload( filepath).
* IAssetFile.ContentFileSize: This property can now be set when you create an asset file. It was previously read only.
* IAssetFile.Upload( filepath): Fixed an issue where this synchronous upload method was throwing the following error when uploading multiple files to the asset. The error was "Server failed to authenticate the request. Make sure the value of Authorization header is formed correctly including the signature."
* IAssetFile.UploadAsync: Fixed an issue where no more than five files could be uploaded simultaneously.
* IAssetFile.UploadProgressChanged: This event is now provided by the SDK.
* IAssetFile.DownloadAsync( string, BlobTransferClient, ILocator, CancellationToken): This method overload is now provided.
* IAssetFile.DownloadAsync: Fixed an issue where no more than five files could be downloaded simultaneously.
* IAssetFile.Delete(): Fixed an issue where calling delete might throw an exception if no file was uploaded for the IAssetFile.
* Jobs: Fixed an issue where chaining an "MP4 to Smooth Streams task" with a "PlayReady Protection Task" by using a job template didn't create any tasks at all.
* EncryptionUtils.GetCertificateFromStore(): This method no longer throws a null reference exception due to a failure in finding the certificate based on certificate configuration issues.

## <a id="november_changes_12"></a>November 2012 release
The changes mentioned in this section were updates included in the November 2012 (version 2.0.0.0) SDK. These changes might require any code written for the June 2012 preview SDK release to be modified or rewritten.

* Assets
  
    IAsset.Create(assetName) is the *only* asset creation function. IAsset.Create no longer uploads files as part of the method call. Use IAssetFile for uploading.
  
    The IAsset.Publish method and the AssetState.Publish enumeration value were removed from the Services SDK. Any code that relies on this value must be rewritten.
* FileInfo
  
    This class was removed and replaced by IAssetFile.
  
    IAssetFiles
  
    IAssetFile replaces FileInfo and has a different behavior. To use it, instantiate the IAssetFiles object, followed by a file upload either by using the Media Services SDK or the Storage SDK. The following IAssetFile.Upload overloads can be used:
  
  * IAssetFile.Upload(filePath): A synchronous method that blocks the thread and is recommended only when you upload a single file.
  * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): An asynchronous method that is the preferred upload mechanism. 
    
    Known bug: Using the cancellation token cancels the upload. The cancellation state of the tasks can be any of a number of states. You must properly catch and handle exceptions.
* Locators
  
    The Origin-specific versions were removed. The SAS-specific context.Locators.CreateSasLocator (asset, accessPolicy) will be marked deprecated, or removed by general availability. See the "Locators" section under "New functionality" for updated behavior.

## <a id="june_changes_12"></a>June 2012 Preview release
The following functionality was new in the November release of the SDK.

* Deleting entities
  
    IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey objects are now deleted at the object level, that is, IObject.Delete(), instead of requiring a delete in the Collection, that is cloudMediaContext.ObjCollection.Delete(objInstance).
* Locators
  
    Locators must now be created by using the CreateLocator method and use the LocatorType.SAS or LocatorType.OnDemandOrigin enum values as an argument for the specific type of locator you want to create.
  
    New properties were added to Locators to make it easier to obtain usable URIs for your content. This redesign of Locators was meant to provide more flexibility for future third-party extensibility and increase ease of use for media client applications.
* Asynchronous method support
  
    Asynchronous support was added to all methods.

## Media Services learning paths
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Provide feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN Forum]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API Reference]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services Pricing Details]: http://azure.microsoft.com/pricing/details/media-services/
[Input Metadata]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Output Metadata]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Delivering Content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indexing Media Files with Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Working with Azure Media Services Live Streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Using AES-128 Dynamic Encryption and Key Delivery Service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Using PlayReady Dynamic Encryption and License Delivery Service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady License Template Overview]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming Storage Encrypted Content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://manage.windowsazure.com
[Dynamic Packaging]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's Blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protecting Smooth Stream with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Retry Logic in the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley Announces EDIUS 7 Streaming Through the Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Controlling Media Service Encoder Output Filenames]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Creating Overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitching Video Segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Connecting to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Managing Media Services Assets across Multiple Storage Accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handling Media Services Job Notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx

