---
title: Using multiple containers (preview) on Web App for Containers - Azure | Microsoft Docs
description: How to use a multi-container app for Web App for Containers.
keywords: azure app service, web app, linux, docker, compose, kubernetes, multi-container, container
services: app-service
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2018
ms.author: msangapu
ms.custom: mvc
#Customer intent: As an Azure customer, I want to show how to use the multiple containers so customer can deploy their own containers into Web App for Containers.
---
#Create a multi-container (preview) app using Web App for Containers

> [!NOTE]
> Deployment to multiple containers is currently in preview. In this tutorial, you will learn how to take an existing .yaml configuration (Docker Compose or Kubernetes) and deploy to Web App for Containers using the Azure Cloud Shell.

[Web App for Containers](app-service-linux-intro.md) provides built-in Docker images on Linux with support for specific versions, such as PHP 7.0 and Node.js 4.5. Web App for Containers uses the Docker container technology to host both built-in images and custom images as a platform as a service. In this tutorial, you learn how to build a custom Docker image and deploy it to Web App for Containers. This pattern is useful when the built-in images don't include your language of choice, or when your application requires a specific configuration that isn't provided within the built-in images.

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

To complete this tutorial, you need:

* An active [Azure subscription](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Experience with Docker Compose or Kubernetes

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)] 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)] 

## Using a Docker Compose configuration

In this section, you will learn how to use a Docker Compose configuration in Web App for Containers. To use a Kuberentes configuration, follow the steps at [Use a Kubernetes configuration](#using-a-kubernetes-configuration-optional) instead for the rest of the tutorial.

```yaml
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: mysqldbpass

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_PASSWORD: mysqldbpass
volumes:
    db_data:
```

> [!NOTE]
> If your app requires a database, [Azure Database services](https://azure.microsoft.com/en-us/product-categories/databases/) is strongly recommended for production environments, instead of a container. If you want to use a database in container for development or testing purposes, make sure to use the persisted share storage to store your database files, so the database can be persisted during app restarts. 

This sample is based on WordPress and MySQL from [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). It needs to be modified in order to work with multiple containers. Listed below are both supported and unsupported options.

### Supported compose options for multi-container 
- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

### Unsupported compose options for multi-container
- build
- depends_on
- networks
- secrets

## Multi-container configuration

### Removing dependencies
This sample states that WP is dependent on the MySQL container. However, `depends_on` is not supported so it will be removed from the configuration.

### Persistent storage
If you want to persist the data, you need to map the container directory to  shared storage.You can utilize *WEBAPP_STORAGE_HOME* variable to access this shared directory. The volumes option maps the file system to directories within the container.  Once the app is enabled, you also need to enable an app setting to enable persistent storage which you will do later.

```yaml
version: '3.3'

services:

  mysql:
    container_name: mysql57
    image: mysql:5.7
    volumes:
      - ${WEBAPP_STORAGE_HOME}/site/data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: mysqldbpass

  wordpress:
    container_name: wordpress
    image: wordpress
    volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
    restart: always
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_PASSWORD: mysqldbpass
```


## Create a multi-container app (Docker)

<!--Change this to a create command 

-->

In the Cloud Shell, create a multi-container [web app](app-service-linux-intro.md) in the `myAppServicePlan` App Service plan with the [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) command. Don't forget to replace _\<app_name>_ with a unique app name.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file https://raw.githubusercontent.com/Azure-Samples/multi-container/master/docker-compose-wordpress.yml 

```

When the web app has been created, the Azure CLI shows output similar to the following example:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgoKICBteXNxbDoKICAgIGNvbnRhaW5lcl9uYW1lOiBteXNxbDU3CiAgICBpbWFnZTogbXlzcWw6NS43CiAgICB2b2x1bWVzOgogICAgICAtICR7V0VCQVBQX1NUT1JBR0VfSE9NRX0vc2l0ZS9kYXRhOi92YXIvbGliL215c3FsCiAgICByZXN0YXJ0OiBhbHdheXMKICAgIGVudmlyb25tZW50OgogICAgICBNWVNRTF9ST09UX1BBU1NXT1JEOiBleGFtcGxlcGFzcwoKICB3b3JkcHJlc3M6CiAgICBjb250YWluZXJfbmFtZTogd29yZHByZXNzCiAgICBpbWFnZTogd29yZHByZXNzCiAgICB2b2x1bWVzOgogICAgICAtICR7V0VCQVBQX1NUT1JBR0VfSE9NRX0vc2l0ZS93d3dyb290Oi92YXIvd3d3L2h0bWwKICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgcG9ydHM6CiAgICAgIC0gODA4MDo4MAogICAgZW52aXJvbm1lbnQ6CiAgICAgIFdPUkRQUkVTU19EQl9QQVNTV09SRDogZXhhbXBsZQo="
  }
]
```

## Configure environment variables

This sample is utilizing persistent storage, however it isn't yet enabled. To make this change, you need to update an app setting.

To set app settings, use the [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set)  command in the Cloud Shell. App settings are case-sensitive and space-separated.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### Browse to the app

Browse to the deployed app using your web browser.(`http://<app_name>.azurewebsites.net`). 


The multi-container sample is running in Web App for Containers.

![Sample multi-container app on Web App for Containers](media/tutorial-multi-container-app/AzureMultiContainerWordPressInstall.png)

**Congratulations**, you've created a multi-container app on Web App for Containers. If you're only looking for Docker Compose instructions, you're done. See [next steps](#next-steps).

## Use a Kubernetes configuration (optional)

In this section, you will learn how to use a Kubernetes configuration to deploy to multiple containers. Make sure you have a [resource group](#create-a-resource-group) and an [App Service plan](#create-an-azure-app-service-plan) already.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.7.9
    ports:
    - containerPort: 80
  - name: redis
    image: redis
  - name: ubuntu
    image: ubuntu:latest
    command: ["/bin/sh"]
    args: ["-c", "while true; do echo hello; sleep 10; done"]

```

### Supported Kubernetes options for multi-container 
- args
- command
- containers
- image
- name
- ports
- spec

> [!NOTE]
>Any other Kubernetes syntax not explicitly called out aren't supported in Public Preview.
>

## Create a multi-container app (Kubernetes)

In the Cloud Shell, create a multi-container [web app](app-service-linux-intro.md) in the `myAppServicePlan` App Service plan with the [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) command. Don't forget to replace _\<app_name>_ with a unique app name.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type kube --multicontainer-config-file https://raw.githubusercontent.com/Azure-Samples/multi-container/master/k8.yaml 

```


```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "KUBE|YXBpVmVyc2lvbjogdjEKa2luZDogUG9kCm1ldGFkYXRhOgogIG5hbWU6IHNvbWUtd29yZHByZXNzCiAgbGFiZWxzOgogICAgbmFtZTogc29tZS13b3JkcHJlc3MKc3BlYzoKICBjb250YWluZXJzOgogICAgLSBpbWFnZTogd29yZHByZXNzCiAgICAgIG5hbWU6IHdvcmRwcmVzcwogICAgICBlbnY6CiAgICAgICAgLSBuYW1lOiAiV09SRFBSRVNTX0RCX1BBU1NXT1JEIgogICAgICAgICAgdmFsdWU6ICJteXNxbGRicGFzcyIKICAgIC0gaW1hZ2U6IG15c3FsNTcKICAgICAgbmFtZTogbXlzcWwKICAgICAgZW52OgogICAgICAgIC0gbmFtZTogIk1ZU1FMX1JPT1RfUEFTU1dPUkQiCiAgICAgICAgICB2YWx1ZTogIm15c3FsZGJwYXNzIgo="
  }
]
```
[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]


## Next steps

> [!div class="nextstepaction"]
> [Use a custom Docker image for Web App for Containers](tutorial-custom-docker-image.md)
