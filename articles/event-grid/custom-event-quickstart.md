---
title: Custom events for Azure Event Grid | Microsoft Docs
description: Use Azure Event Grid to publish a topic, and subscribe to that event. 
services: event-grid 
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 08/10/2017
ms.topic: hero-article
ms.service: event-grid
---

# Create and route custom events with Azure Event Grid

Azure Event Grid is an eventing service for the cloud. In this article, you use the Azure CLI to create a custom topic, subscribe to the topic, and trigger the event to view the result. Typically, you send events to an endpoint that responds to the event, such as, a webhook or Azure Function. However, to simplify this article, you send the events to a URL that merely collects the messages.

When you are finished, you see that the event data has been sent to an endpoint.

![Event data](./media/custom-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this topic requires that you are running the Azure CLI version 2.0 or later. To find the version, run `az --version`. If you need to install or upgrade, see [Install Azure CLI 2.0](/cli/azure/install-azure-cli).
 
## Create a resource group

Event Grid topics are Azure resources, and must be placed in an Azure resource group. The resource group is a logical collection into which Azure resources are deployed and managed.

Create a resource group with the [az group create](/cli/azure/group#create) command. 

The following example creates a resource group named *gridResourceGroup* in the *westus2* location.

```azurecli-interactive 
az group create --name gridResourceGroup --location westus2
```

## Create a custom topic

A topic provides a user-defined endpoint that you post your events to. You create the topic in your resource group. The topic name must be unique.

```azurecli-interactive
az eventgrid topic create --topic-name {unique-topic-name} -l westus2 -g gridResourceGroup
```

## Create a message endpoint

Before subscribing to the topic, let's create the endpoint for the event message. Rather than write code to respond to the event, let's create an endpoint that collects the messages so you can view them. Go to [RequestBin](https://requestb.in/), and click **Create a RequestBin**.  Copy the bin URL, because you need it when subscribing to the topic.

## Subscribe to a topic

You subscribe to a topic to tell Event Grid which events you want to track. The following example subscribes to the topic you created. It passes the URL from RequestBin as the endpoint for event notification.

```azurecli-interactive
az eventgrid topic event-subscription create --name {unique-event-subscription-name} \
  --endpoint {your-URL-from-RequestBin} \
  -g gridResourceGroup 
  --topic-name {your-topic-name}
```
 
## Send an event to your topic

Now, let's trigger an event to see how Event Grid distributes the message to your endpoint. First, let's get the URL and key for the topic.

```azurecli-interactive
endpoint=$(az eventgrid topic show --topic-name {your-topic-name} -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic show-keys --topic-name {your-topic-name} -g gridResourceGroup --query "key1" --output tsv)
```

To simplify this article, we have set up sample event data to send to the topic. Typically, an application or Azure service would send the event data. The following example gets the event data:

```azurecli-interactive
body=$(eval echo "'$(curl https://tempstor1ga23s.blob.core.windows.net/template/customevent.json)'")
```

If you `echo "$body"` you can see the full event. The `data` element of the JSON is the payload of your event. Any well-formed JSON can go in this field. You can also use the subject field for advanced routing and filtering. 
 
CURL is a utility that performs HTTP requests. In this article, we use CURL to send the event to our topic. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

You have triggered the event, and Event Grid sent the message to the endpoint you configured when subscribing. Browse to the RequestBin URL that you created earlier. Or, click refresh in your open RequestBin browser. You see the event you just sent. 

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## Clean up resources
If you plan to continue working with this event, do not clean up the resources created in this article. If you do not plan to continue, use the following command to delete the resources you created in this article.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## Next steps

Now that you know how to create topics and event subscriptions, learn more about what Event Grid can help you do:

- [About Event Grid](overview.md)