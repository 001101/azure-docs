---
title: Configure containers
titlesuffix: Text Analytics - Azure Cognitive Services
description: Text Analytics provides each container with a common configuration framework, so that you can easily configure and manage storage, logging and telemetry, and security settings for your containers.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
---
# Configure Text Analytics docker containers

Text Analytics provides each container with a common configuration framework, so that you can easily configure and manage storage, logging and telemetry, and security settings for your containers.

## Configuration settings

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> The [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting), and [`Eula`](#eula-setting) settings are used together, and you must provide valid values for all three of them; otherwise your container won't start. For more information about using these configuration settings to instantiate a container, see [Billing](how-to/text-analytics-how-to-install-containers.md#billing).

## ApiKey configuration setting

The `ApiKey` setting specifies the Azure resource key used to track billing information for the container. You must specify a value for the ApiKey and the value must be a valid key for the _Text Analytics_ resource specified for the [`Billing`](#billing-setting) configuration setting.

This setting can be found in the following place:

* Azure portal: **Text Analytics's** Resource Management, under **Keys**

## ApplicationInsights setting

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## Billing configuration setting

The `Billing` setting specifies the endpoint URI of the _Text Analytics_ resource on Azure used to meter billing information for the container. You must specify a value for this configuration setting, and the value must be a valid endpoint URI for a __Text Analytics_ resource on Azure.

This setting can be found in the following place:

* Azure portal: **Text Analytics's** Overview, labeled `Endpoint`

|Required| Name | Data type | Description |
|--|------|-----------|-------------|
|Yes| `Billing` | String | Billing endpoint URI<br><br>Example:<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0` |

## Eula setting

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## Fluentd settings


[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## Logging settings
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## Mount settings

Use bind mounts to read and write data to and from the container. You can specify an input mount or output mount by specifying the `--mount` option in the [docker run](https://docs.docker.com/engine/reference/commandline/run/) command.

The Text Analytics containers don't use input or output mounts to store training or service data. 

The exact syntax of the host mount location varies depending on the host operating system. Additionally, the [host computer](luis-container-howto.md#the-host-computer)'s mount location may not be accessible due to a conflict between permissions used by the docker service account and the host mount location permissions. 

|Optional| Name | Data type | Description |
|-------|------|-----------|-------------|
|Not allowed| `Input` | String | Text Analytics containers do not use this.|
|Optional| `Output` | String | The target of the output mount. The default value is `/output`. This is the location of the logs. This includes container logs. <br><br>Example:<br>`--mount type=bind,src=c:\output,target=/output`|

## Hierarchical settings

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]

## Example docker run commands 

The following examples use the configuration settings to illustrate how to write and use `docker run` commands.  Once running, the container continues to run until you [stop](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) it.

* **Line-continuation character**: The docker commands in the following sections use the back slash, `\`, as a line continuation character. Replace or remove this based on your host operating system's requirements. 
* **Argument order**: Do not change the order of the arguments unless you are very familiar with docker containers.

Replace {_argument_name_} with your own values:

| Placeholder | Value | Format or example |
|-------------|-------|---|
|{ENDPOINT_KEY} | The endpoint key of the Text Analytics resource. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | The billing endpoint value is available on the Azure portal's Text Analytics Overview page.|https://westus.api.cognitive.microsoft.com/text/analytics/v2.0|

> [!IMPORTANT]
> The `Eula`, `Billing`, and `ApiKey` options must be specified to run the container; otherwise, the container won't start.  For more information, see [Billing](how-tos/text-analytics-how-to-install-containers.md#billing).
> The ApiKey value is the **Key** from the Azure Text Analytics Resource keys page. 


### Basic example for keyphrase extraction

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/languagekeyphrase Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 
  ```

### ApplicationInsights example

### Logging example with command-line arguments

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

### Logging example with environment variable

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase  Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```

## Example docker run commands for language detection

### Basic example

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

### ApplicationInsights example

### Logging example with command-line arguments

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

### Logging example with environment variable

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language  Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```
 
## Example docker run commands for sentiment analysis

### Basic example

### ApplicationInsights example

### Logging example with command-line arguments

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

### Logging example with environment variable

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```

## Next steps

* Review [How to install and run containers](how-tos/text-analytics-how-to-install-containers.md)