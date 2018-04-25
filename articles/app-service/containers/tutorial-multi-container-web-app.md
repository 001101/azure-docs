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
#Customer intent: As an Azure customer, I want to show how to use the multi-container feature so customer can deploy their own containers into Web App for Containers.
---
#Create a multi-container (preview) app using Web App for Containers

> [!NOTE]
> The multi-container feature is current in preview. It expects a .yaml file similar to that of Docker Compose and Kubernetes. In this tutorial, you will learn how to take an existing Docker Compose .yaml and deploy to Web App for Containers using the Azure Cloud Shell. Alternatively, you can also work with a [Kubernetes sample](http://mangesh_to_provide_link).

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

## Working with a Docker Compose file

```yaml
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: examplepass

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    db_data:
```

> [!NOTE]
> If you have a multi-container app which requires a database, we strongly recommend you use [Azure Database services](https://azure.microsoft.com/en-us/product-categories/databases/) instead of a container. If you want to use a database in container for dev/testing purposes, please make sure to use the persisted share storage to store your database files, so the database can be persisted during app restarts. 

This sample is based on WordPress and MySQL from [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). It needs to be modified in order to work with the multi-container feature. Listed below are both supported and unsupported options.

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
Our sample states that WP is dependent on the MySQL container. However, this feature is not supported so it will be removed from the configuration.

### Persistent storage
If we want to persist the data, we need to map the container directory to our shared storage. We can utilize *WEBAPP_STORAGE_HOME* variable to access this shared directory. The volumes option maps the file system to directories within the container.  Once the app is enabled, we also need to enable an app setting to enable persistent storage which we will cover later.

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
      MYSQL_ROOT_PASSWORD: examplepass

  wordpress:
    container_name: wordpress
    image: wordpress
    volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
    restart: always
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_PASSWORD: example
```


## Create a multi-container app

In the Cloud Shell, create a [web app](app-service-linux-intro.md) in the `myAppServicePlan` App Service plan with the [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) command. Don't forget to replace _\<appname>_ with a unique app name, and _\<docker-ID>_ with your Docker ID.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file https://raw.githubusercontent.com/Azure-Samples/multi-container/master/docker-compose-wordpress.yml 

```

When the web app has been created, the Azure CLI shows output similar to the following example:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## Configure environment variables

Our sample is utilizing persistent storage, however we haven't yet enabled this on our app. To make this change, we'll need to update an app setting.

To set app settings, use the [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set)  command in the Cloud Shell. App settings are case-sensitive and space-separated.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```


### Test the web app

Verify that the web app works by browsing to it (`http://<app_name>.azurewebsites.net`). 


## Use a Kubernetes configuration (Optional)

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]


## Next steps

> [!div class="nextstepaction"]
> [Use a custom Docker image for Web App for Containers](tutorial-custom-docker-image)
