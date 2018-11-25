---
title: How to install and run docker containers - Language Understanding (LUIS) 
titlesuffix: Azure Cognitive Services
description: The LUIS container loads your trained or published app into a docker container and provides access to the LUIS prediction runtime with API endpoints. 
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/04/2018
ms.author: diberry
---

# Install and run containers

The LUIS container loads your trained or published app into a docker container and provides access to the LUIS query prediction runtime from the container's API endpoints. You can collect query logs from the container and upload these back to the Azure LUIS service to improve the app's prediction accuracy. 

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

In order to run the LUIS container, you must have the following: 

|Required|Purpose|
|--|--|
|Docker Engine| To complete this preview, you need Docker Engine installed on a *host computer. Docker provides packages that configure the Docker environment on [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), and [Linux](https://docs.docker.com/engine/installation/#supported-platforms). For a primer on Docker and container basics, see the [Docker overview](https://docs.docker.com/engine/docker-overview/).<br><br> Docker must be configured to allow the containers to connect with and send billing data to Azure. <br><br> **On Windows**, Docker must also be configured to support Linux containers.<br><br>*The **host** is the computer that runs the docker container. It can be the local computer or any docker hosting service including Azure Kubernetes, Azure Container instances, Kubernetes cluster, and Azure Stack.|
|Familiarity with Docker | You should have a basic understanding of Docker concepts, like registries, repositories, containers, and container images, as well as knowledge of basic `docker` commands.| 
|LUIS resource and associated app |In order to use the container, you must have:<br><br>* A _Language Understanding_ Azure resource in the **F0 pricing tier**, along with the associated endpoint key and endpoint URI (used as the billing endpoint).<br>* A trained or published app packaged as a mounted input to the container with its associated App ID.<br>* The Authoring Key to download the app package and upload the query logs.<br><br>These requirements are used to create access and command-line arguments to the following variables:<br><br>**{AUTHORING_KEY}**: This key is used to get the packaged app from the LUIS service in the cloud and upload the query logs back to the cloud. The format is `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}**: This ID is used to select the App. The format is `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{ENDPOINT_KEY}**: This key is used to start the container. You can find the endpoint key in two places. The first is the Azure portal within the _Language Understanding_ resource's keys list. The endpoint key is also available in the LUIS portal on the Keys and Endpoint settings page. Do not use the starter key.<br><br>**{BILLING_ENDPOINT}**: The billing endpoint value is available on the Azure portal's Language Understanding Overview page. An example is: `https://westus.api.cognitive.microsoft.com/luis/v2.0`|

### Container requirements and recommendations

This container supports minimum and recommended values for the settings:

|Setting| Minimum | Recommended |
|-----------|---------|-------------|
|Cores<BR>`--cpus`|1 core<BR>at least 2.6 gigahertz (GHz) or faster|1 core|
|Memory<BR>`--memory`|2 GB|4 GB|
|Transactions per second<BR>(TPS)|20 TPS|40 TPS|

The `--cpus` and `--memory` settings are used as part of the `docker run` command.

## Get the container image

Use the `[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)` command to download a container image from the `mcr.microsoft.com/azure-cognitive-services/luis` repository:

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

For a full description of available tags, such as `latest` used in the preceding command, see [LUIS](https://hub.docker.com/r/microsoft/azure-cognitive-services-luis/) on Docker Hub.

> [!TIP]
> You can use the [docker images](https://docs.docker.com/engine/reference/commandline/images/) command to list your downloaded container images. For example, the following command lists the ID, repository, and tag of each downloaded container image, formatted as a table:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/luis                           latest
>  ``` 

## How to use the container

Once the container is on the host computer, use the following process to work with the container.

![Conceptual architecture of LUIS service, container, and portal](./media/luis-container-how-to/luis-container-architecture.png)

1. [Get LUIS app package](#get-packaged-app) from Azure.
1. Save package file (*.gz) to the local file system. 
1. Move package file into the required input directory on the host computer. Do not rename, alter, or decompress LUIS package file.
1. [Run the container](#run-the-container) from the host, with the required _input mount_ and billing settings. More [examples](luis-container-configuration.md#example-docker-run-commands) of the `docker run` command are available. 
1. Use container, [querying the container's prediction endpoint](#query-the-container). 
1. When you are done with the container, [upload the query log](#upload-logs-for-active-learning) to the LUIS portal and [stop](#stop-the-container) the container.
1. Use LUIS portal's [active learning](luis-how-to-review-endoint-utt.md) on the **Review endpoint utterances** page to improve the app.

The app running in the container can't be altered. In order the change the app in the container, you need to change the app in the LUIS service using the [LUIS](https://www.luis.ai) portal or use the LUIS [authoring APIs](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). Then train and/or publish, then download a new package and run the container again.

The LUIS app inside the container can't be exported back to the LUIS service. Only the query logs can be uploaded. 

## Get packaged app

The LUIS container requires a trained or published LUIS app to answer prediction queries of user utterances. In order to get the LUIS app, use either the trained or published package API. 

The default location is the `input` subdirectory in relation to where you run the `docker run` command.  

Place the package file in a directory and reference this directory as the input mount when you run the docker container. 

### Package types

The input mount directory can contain the **Production**, **Staging**, and **Trained** versions of the app simultaneously. All the packages are mounted. 



|Package Type|Query Endpoint API|Query availability|Package filename format|
|--|--|--|--|
|Trained|Get, Post|Container only|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Staging|Get, Post|Azure and container|`{APPLICATION_ID}_STAGING.gz`|
|Production|Get, Post|Azure and container|`{APPLICATION_ID}_PRODUCTION.gz`|

>**Important:** Do not rename, alter, or decompress the LUIS package files.

### Packaging prerequisites

Before packaging a LUIS application, you must have the following:

|Packaging Requirements|Details|
|--|--|
|Azure _Language Understanding_ resource instance|Supported regions include<br><br>West US (```westus```)<br>West Europe (```westeurope```)<br>Australia East (```australiaeast```)|
|Trained or published LUIS app|With no [unsupported dependencies](#unsupported-dependencies). |
|Access to the host computer's file system |The host computer must allow an [input mount](luis-container-configuration.md#mounts-settings).|
  
### Get app package from LUIS portal

TBD

### Get published app's package from API

Use the following REST API method, to package a LUIS app that you've already [published](luis-how-to-publish-app.md). Substituting your own appropriate values for the placeholders in the API call, using the table below the HTTP specification.

```http
GET /luis/webapi/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Placeholder | Value |
|-------------|-------|
|{APPLICATION_ID} | The application ID of the published LUIS app. |
|{APPLICATION_ENVIRONMENT} | The environment of the published LUIS app. Use one of the following values:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | The authoring key of the LUIS account for the published LUIS app.<br/>You can get your authoring key from the **User Settings** page on the LUIS portal. |
|{AZURE_REGION} | The appropriate Azure region:<br/><br/>```westus``` - West US<br/>```westeurope``` - West Europe<br/>```australiaeast``` - Australia East |

Use the following CURL command to download the published package, substituting your own values:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/webapi/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_{APPLICATION_ENVIRONMENT}.gz
```

If successful, the response is a LUIS package file. Save the file in the storage location specified for the input mount of the container. 

### Get trained app's package from API

Use the following REST API method, to package a LUIS application that you've already [trained](luis-how-to-train.md). Substituting your own appropriate values for the placeholders in the API call, using the table below the HTTP specification.

```http
GET /luis/webapi/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Placeholder | Value |
|-------------|-------|
|{APPLICATION_ID} | The application ID of the trained LUIS application. |
|{APPLICATION_VERSION} | The application version of the trained LUIS application. |
|{AUTHORING_KEY} | The authoring key of the LUIS account for the published LUIS app.<br/>You can get your authoring key from the **User Settings** page on the LUIS portal.  |
|{AZURE_REGION} | The appropriate Azure region:<br/><br/>```westus``` - West US<br/>```westeurope``` - West Europe<br/>```australiaeast``` - Australia East |

Use the following CURL command to download the trained package:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/webapi/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_v{APPLICATION_VERSION}.gz
```

If successful, the response is a LUIS package file. Save the file in the storage location specified for the input mount of the container. 

## Run the container 

Use the [docker run](https://docs.docker.com/engine/reference/commandline/run/) command to run the container. The command uses the following parameters:

| Placeholder | Value |
|-------------|-------|
|{ENDPOINT_KEY} | This key is used to start the container. Do not use the starter key. |
|{BILLING_ENDPOINT} | The billing endpoint value is available on the Azure portal's Language Understanding Overview page.|

Replace these parameters with your own values in the following example `docker run` command.

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

The preceding docker command uses the back slash, `\`, as a line continuation character. Replace or remove this based on your host operating system's requirements. Do not change the order of the arguments unless you are very familiar with docker containers.

This command:

* Runs a container from the LUIS container image
* Loads LUIS app from input mount at c:\input, located on container host
* Allocates two CPU cores and 6 gigabytes (GB) of memory
* Exposes TCP port 5000 and allocates a pseudo-TTY for the container
* Saves container and LUIS logs to output mount at c:\output, located on container host
* Automatically removes the container after it exits. The container image is still available on the host computer. 

More [examples](luis-container-configuration.md#example-docker-run-commands) of the `docker run` command are available. 

> [!IMPORTANT]
> The `Eula`, `Billing`, and `ApiKey` options must be specified to run the container; otherwise, the container won't start.  For more information, see [Billing](#billing).
> The ApiKey value is the **Key** from the Keys and Endpoints page in the LUIS portal and is also available on the Azure Language Understanding Resource keys page.  

## Query the container

The container provides REST-based query prediction endpoint APIs. Endpoints for published (staging or production) apps have a _different_ route than endpoints for trained apps. 

Use the host, https://localhost:5000, for container APIs. 

|Package type|Method|Route|Query parameters|
|--|--|--|--|
|Published|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]<br>[&staging]|
|Trained|Get, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]|

The query parameters configure how and what is returned in the query response:

|Query parameter|Type|Purpose|
|--|--|--|
|`q`|string|The user's utterance.|
|`timezoneOffset`|number|The timezoneOffset allows you to [change the timezone](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) used by the prebuilt entity datetimeV2.|
|`verbose`|boolean|Returns all intents and their scores when set to true. Default is false, which returns only the top intent.|
|`staging`|boolean|Returns query from staging environment results if set to true. Default is false, which returns production environment results.|
|`log`|boolean|Logs queries, which can be used later for [active learning](luis-how-to-review-endoint-utt.md). Default is true.|

### Query published app

An example CURL command for querying the container for a published app is:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
To make queries to the **Staging** environment, change the **staging** query string parameter value to true: 

`staging=true`

## Query trained app

An example CURL command for querying the container for a trained app is: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
The version name has a maximum of 10 characters and contains only characters allowed in a URL. 

### Query endpoint API from an SDK

Query operations from an SDK are available for published apps. Trained app can't be queried from an SDK. Use the [Azure Cognitive Services LUIS Client Library](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) to query the container.  

> [!IMPORTANT]
> You must have Azure Cognitive Services LUIS Client Library version 2.0.0 or later.

TBD: Example or link? 

## Upload logs for active learning

If an output mount is specified for the LUIS container, app query log files are saved in the output directory, where {INSTANCE_ID} is the container ID. The app query log contains the query, response, and timestamps for each prediction query submitted to the LUIS container. 

???TDB: if more than one app is running in container, like stage and production, is there more than 1 log?

The following location shows the nested directory structure for the container's log files.
`
/output/luis/{INSTANCE_ID}/
`

```http
POST /webapi/v2.0/apps/{APPLICATION_ID}/unlabeled HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
OCP-APIM-SUBSCRIPTION-KEY: {AUTHORING_KEY}
Content-Type: multipart/form-data
Content-Disposition: form-data; name=""; filename="{QUERY_LOG_FILE}"
```

Upload the app query log to the Language Understanding (LUIS) service by using the following REST API method, substituting the appropriate values for your own values:

| Placeholder | Value |
|-------------|-------|
|{APPLICATION_ID} | The application ID of the trained or published LUIS app. |
|{AUTHORING_KEY} | The authoring key of your LUIS account.<br/>You can get your authoring key from the **User Settings** page for your LUIS account on the LUIS portal. |
|{AZURE_REGION} | One of the following values for the appropriate Azure region:<br><br/>```westus``` - West US<br/>```westeurope``` - West Europe<br/>```australiaeast``` - Australia East |
|{QUERY_LOG_FILE} | The full path and file name of the application query log file. |


An example CURL command for uploading the query log is:

```bash
curl -X POST \
"http://westus.api.cognive.microsoft.com/luis/webapi/v2.0/apps/{APPLICATION_ID}/unlabeled" \
-H "APIM-SUBSCRIPTION-ID: {AUTHORING_KEY}" \
-H "Content-Type: multipart/form-data"
-H "Content-Disposition: form-data; name=""; filename="{QUERY_LOG_FILE}""
```

If successful, the method responds with an HTTP 200 status code. After the log is uploaded, [review the endpoint](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) utterances in the LUIS portal.

## Stop the container

To shut down the container, in the command-line environment where the container is running, press **Ctrl+C**.

## Troubleshooting

If you run the container with an output [mount](luis-container-configuration.md#mounts-setting) and logging enabled, the container generates log files that are helpful to troubleshoot issues that happen while starting or running the container. 

## Container's API documentation

The container provides a full set of documentation for the endpoints as well as a `Try it now` feature. This feature allows you to enter your settings into a web-based HTML form and make the query without having to write any code. Once the query returns, an example CURL command is provided to demonstrate the HTTP headers and body format required. 

> [!TIP]
> Read the [OpenAPI specification](https://swagger.io/docs/specification/about/), describing the API operations supported by the container, from the `/swagger` relative URI. For example:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## Billing

The LUIS container sends billing information to Azure, using a _Language Understanding_ resource on your Azure account. 

Cognitive Services containers are not licensed to run without being connected to Azure for metering. Customers need to enable the containers to communicate billing information with the metering service at all times. Cognitive Services containers do not send customer data (the utterance) to Microsoft. 

The `docker run` uses the following arguments for billing purposes:

| Option | Description |
|--------|-------------|
| `ApiKey` | The API key of the _Language Understanding_ resource used to track billing information.<br/>The value of this option must be set to an API key for the provisioned LUIS Azure resource specified in `Billing`. |
| `Billing` | The endpoint of the _Language Understanding_ resource used to track billing information.<br/>The value of this option must be set to the endpoint URI of a provisioned LUIS Azure resource.|
| `Eula` | Indicates that you've accepted the license for the container.<br/>The value of this option must be set to `accept`. |

> [!IMPORTANT]
> All three options must be specified with valid values, or the container won't start.

For more information about these options, see [Configure containers](luis-container-configuration.md).

## Unsupported dependencies

You can use a LUIS application if it **doesn't include** any of the following dependencies:

|Don't use<br>in trained app|Don't use<br>in published app|Unsupported app configurations|Details|
|--|--|--|--|
|X|X|Unsupported container cultures| German (de-DE)<br>Dutch (nl-NL)<br>Japanese (ja-JP)<br>|
|X|X|Unsupported domains|Prebuilt domains, including prebuilt domain intents and entities|
|X|X|Unsupported entities for all cultures|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) prebuilt entity for all cultures|
|X|X|Unsupported entities for English (en-US) culture|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) and [PersonName](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-person) prebuilt entities|
|X|X|Unsupported entities for Chinese (zh-CN) culture|[PersonName](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-person) prebuilt entity for Chinese| 
||X|Speech priming|External dependencies are not supported in the container.|
||X|Sentiment analysis|External dependencies are not supported in the container.|
||X|Bing spell check|External dependencies are not supported in the container.|

## Next steps

* Review [Configure containers](luis-container-configuration.md) for configuration settings
* Refer to [Frequently asked questions (FAQ)](luis-resources-faq.md) to resolve issues related to LUIS functionality.