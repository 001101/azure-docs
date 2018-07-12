---
# Mandatory fields. See more on aka.ms/skyeye/meta.
#Intent: I want to use my existing containers as is and deploy them to Azure. (Windows or Linux) 
title: Quickstart - Deploy Hello World to Azure Service Fabric Mesh | Microsoft Docs
description: This quickstart shows you how to deploy a Service Fabric Mesh application to Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: Don’t add or edit keywords without consulting your SEO champ. 
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt 
---
# Quickstart: Deploy Hello World to Service Fabric Mesh

[Service Fabric Mesh](service-fabric-mesh-overview.md) makes it easy to create and manage microservices applications in Azure, without having to provision virtual machines. In this quickstart, you will create a Hello World application in Azure and expose it to the internet. This operation is completed in a single command. Within just a couple minutes, you'll see this in your browser:

![Hello world app in the browser][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

If you don't already have an Azure account, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Install Service Fabric Mesh CLI 
You can use the Azure Cloud Shell or a local installation of the Azure CLI to complete this quickstart. Install Azure Service Fabric Mesh CLI extension module by following these [instructions](service-fabric-mesh-howto-setup-cli.md).

## Create a resource group

Create a resource group to deploy the application to.

```azurecli-interactive
az account set --subscription "SubscriptionID"
az group create --name myResourceGroup --location eastus
```

## Deploy the application

Create your application using the `az mesh deployment create` command:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

In just over a minute, your command should return with `"provisioningState": "Succeeded"`. 

## Check application deployment status

At this point, your application has been deployed. You can check to see its status by using the `az mesh app show` command. This command provides useful information that you can follow up on.

The application name for this quickstart application is helloWorldApp, to gather the details on the application execute the following command:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## Browse to the application

Once the application status is returned as `"provisioningState": "Succeeded"`, you will need the ingress endpoint of the service. To retrieve the IP address query the network resource for the application where the service is deployed, and open it on a browser.

The network resource for this quickstart application is helloWorldNetwork, you can use the `az mesh network show` command to get the IP address:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldNetwork
```

The command will return with information like the json snippet below when running the command in [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

From the output, copy the IP address.

```json
    "publicIpAddress": "168.62.188.181",
    "qosLevel": "Bronze"
  },
  "location": "eastus",
  "name": "helloWorldNetwork",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/networks"
}
```

In the example above, the service end point IP is 168.62.188.181.  Take your corresponding IP address and open it in your favorite browser.

## See all the applications you have currently deployed to your subscription

You can use the `az mesh app list` command to get a list of applications you have deployed to your subscription.

```azurecli-interactive
az mesh app list --output table
```

## See the application logs

Examine the logs for the deployed application using the `az mesh code-package-log get` command:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## Clean up resources

When you are ready to delete the application and network resources, run the `az mesh app delete` and `az mesh network delete` commands.

```azurecli-interactive
az mesh app delete -g myResourceGroup -n helloWorldApp
az mesh network delete -g myResourceGroup -n helloWorldNetwork
```

If you no longer need any of the resources you created in this quickstart, you can execute the [az group delete][az-group-delete] command to remove the resource group and all the resources it contains.

```azurecli-interactive
az group delete --name myResourceGroup
```

## Next steps

To learn more about Service Fabric Mesh, take a look at this tutorial.
> [!div class="nextstepaction"]
> [Next steps](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
