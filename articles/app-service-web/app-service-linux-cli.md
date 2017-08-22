---
title: Manage Web App on Linux using Azure CLI 2.0 | Microsoft Docs
description: Manage Web App on Linux using Azure CLI.
keywords: azure app service, web app, cli, linux, oss
services: app-service
documentationCenter: ''
authors: ahmedelnably
manager: erikre
editor: ''

ms.assetid:
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably

---

# Manage Web App on Linux using Azure CLI

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Using this tutorial you will be able to create and manage a Web App on Linux using Azure CLI 2.0.
You can start using the new version of the CLI in two ways:

1. [Installing Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) on your machine.
2. Using [Azure Cloud Shell (Preview)](../cloud-shell/overview.md)


## Create a Linux App Service Plan

To create a Linux App Service Plan, you can simply use the following command

```azurecli-interactive
az appservice plan create -n appname -g rgname --islinux -l "South Central US" --sku S1 --number-of-workers 1
``` 

## Create a custom Docker container Web App

To create a web app and configuring it to run a custom Docker container, you can simply use the following command

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```
 
## Change the custom Docker container for an existing Web App on Linux App

To change a previously created app, from the current Docker image to a new image, you can simply use the following command

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
``` 

## Using Docker images from a private registry

To configure your app to use images from a private registry, you will need to provide the url for you registry, user name, and password, this can be done using the following command

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
``` 

## Enable continuous deployments for custom Docker images

with the following command you can enable the CD functionality, and get the webhook url that can be used to configure you DockerHub or Azure Container Registry repos.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
``` 

## Create a Web App on Linux App using one of our built-in runtime frameworks

To create a PHP 5.6 Web App on Linux App that, you can simply use the following command

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
``` 

## Change framework version for an existing Web App on Linux App

To change a previously created app, from the current framework version to Node Js 6.11, you can simply use the following command

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
``` 

## Setup Git deployments for your Web App

To setup Git deployments for your app, you can use the following command

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
``` 


## Next steps
* [What is Azure Web App on Linux?](app-service-linux-intro.md)
* [Instal Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
* [Azure Cloud Shell (Preview)](../cloud-shell/overview.md)
* [Creating web apps in Azure Web App on Linux](app-service-linux-how-to-create-web-app.md)
* [Set up staging environments in Azure App Service](./web-sites-staged-publishing.md)
* [Continuous Deployment with Azure Web App on Linux](./app-service-linux-ci-cd.md)
