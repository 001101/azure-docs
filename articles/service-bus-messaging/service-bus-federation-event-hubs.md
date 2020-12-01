---
title: Message replication to Event Hubs - Azure Service Bus | Microsoft Docs
description: This article provides an overview of event replication and cross-region federation between Azure Service Bus and Azure Event Hubs. 
ms.topic: article
ms.date: 09/15/2020
---

# Message replication to Event Hubs

This article explains how to replicate data from Service Bus entities to Event Hubs, covering several of the patterns explained in the [federation overview](service-bus-federation-overview.md) article. 

Replication applications and tasks use the Azure Functions runtime and all the fundamentals for how to create such tasks are covered in the [Event replication tasks and applications](service-bus-federation-replicator-functions.md) article.

In this article we lean on the concept of [configured tasks](service-bus-federation-replicator-functions.md#replication-tasks), which do not require you to write code, but rather leverage code that is already available. "Custom tasks" are explained in the sample application repositories linked below in [next steps](#next-steps) 

### Start a new replication project 

Once you've [created a replication application in Azure Functions](service-bus-federation-replicator-functions.md#configuring-a-replication-application), the easiest way to start new replication project is to clone the sample repository and use it as a baseline for developing your own tasks. You'll do this with `git` and providing a target directory, or you can clone the repository directly on Github.

``` powershell 
git clone "https://github.com/Azure-Samples/azure-messaging-replication-dotnet" {project-dir}
```

In the `functions/config` folder of the repository you will find the [ConfigBaseApp](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/main/config/ConfigBaseApp) template project. Copy that entire project into a new, parallel directory. 

### Creating replication tasks 

To create a new replication task, first create a new folder underneath the project folder. The name of the new folder is the name of the function, for instance `QueueAToQueueB`. The name has no real functional correlation with the messaging entities being used and serves only for you to identify them. You can create dozens of functions in the same project.

Next, create a `function.json` file in the folder. The file configures the function. Start with the following content:

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "disabled": false,
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub"
}
```

In that file, you need to complete three configuration steps that depend on which entities you want to connect:

1. [Configure the input direction](#configure-the-input-direction)
2. [Configure the output direction](#configure-the-output-direction)


### Configure the input direction

#### Service Bus Queue input

If you want to receive messages from a Service Bus queue, add configuration information to the top section within "bindings" that sets

* **type** - the "serviceBusTrigger" type.
* **connection** - the name of the app configuration value for the Service Bus connection string. This value must be `{FunctionName}-source-connection` if you want to use the provided scripts.
* **queueName** - the name of the Service Bus Queue within the namespace identified by the connection string.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "QueueAToEventHubB-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

##### Service Bus Topic input

If you want to receive messages from a Service Bus topic, add configuration information to the top section within "bindings" that sets

* **type** - the "serviceBusTrigger" type.
* **connection** - the name of the app configuration value for the Service Bus connection string. This value must be `{FunctionName}-source-connection` if you want to use the provided scripts.
* **topicName** - the name of the Service Bus Topic within the namespace identified by the connection string.
* **subscriptionName** - the name of the Service Bus Subscription on the given topic within the namespace identified by the connection string.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "TopicXSubYToEventHubB-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### Configure the output direction

To forward events to an Event Hub, add configuration information to the bottom section within "bindings" that sets

* **type** - the "eventHub" type.
* **connection** - the name of the app configuration value for the Event Hub connection string. This value must be `{FunctionName}-target-connection` if you want to use the provided scripts.
* **eventHubName** - the name of the Event Hub within the namespace identified by the connection string.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "QueueAToEventHubB-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

### Build, Configure, Deploy

Once you've created the tasks you need, you need to build the project, configure
the (existing) application, and deploy the tasks.

#### Build

The `Build-FunctionApp.ps1` PowerShell script will build the project and put all
required files into the `./bin` folder immediately underneath the project root.
This needs to be run after every change. 

#### Configure

The `Configure-Function.ps1` PowerShell script calls the shared `Update-PairingConfiguration.ps1` PowerShell script and needs to be run once for each task in an existing Function
app, for the configured pairing.

For instance, assume a task `QueueAToQueueB` that is configured like this:

```JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "QueueAToEventHubB-source-connection",
            "queueName": "queueA",
            "name": "input" 
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "QueueAToEventHubB-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ],
    "disabled": false,
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub"
}
```
For this task, you would configure the Function application and the permissions
on the messaging resources like this:

```powershell
Configure-Function.ps1 -ResourceGroupName "myreplicationapp" 
                          -FunctionAppName "myreplicationapp" 
                          -TaskName "QueueAToEventHubB"
                          -SourceNamespaceName "my1stnamespace"
                          -SourceQueueName "queueA" 
                          -TargetNamespaceName "my2ndnamespace"
                          -TargetEventHubName "eventHubB"
```

The script assumes that the messaging resources already exist. The configuration script will add the required configuration entries to the application configuration.

#### Deploy

Once the build and Configure tasks are complete, the directory can be deployed into the Azure Functions app as-is. The `Deploy-FunctionApp.ps1` script simply calls the publish task of the Azure Functions tools:

```Powershell
func azure functionapp publish $FunctionAppName
```

Replication applications are regular Azure Function applications and you can therefore use any of the [available deployment options](../azure-functions/functions-deployment-technologies.md). For testing, you can also run the [application locally](../azure-functions/functions-develop-local.md), but with the messaging services in the cloud.

In CI/CD environments, you simply need to integrate the steps described above into a build script.

### Next Steps

* [Custom task project examples](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/main/code/README.md)

* [Replicating event data between Service Bus entities](service-bus-federation-service-bus.md)
