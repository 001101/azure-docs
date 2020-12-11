---
title: RabbitMQ output bindings for Azure Functions
description: Learn to send RabbitMQ messages from Azure Functions.
author: carychai

ms.assetid: 
ms.topic: reference
ms.date: 02/19/2020
ms.author: cachai
ms.custom: 

---

# RabbitMQ output binding for Azure Functions overview

> [!NOTE]
> The RabbitMQ bindings are only fully supported on **Windows Premium** plans. Linux support will be released early in the 2021 calendar year. Consumption is not supported.

Use the RabbitMQ output binding to send messages to a RabbitMQ queue.

For information on setup and configuration details, see the [overview](functions-bindings-service-bus-output.md).

## Example

# [C#](#tab/csharp)

The following example shows a [C# function](functions-dotnet-class-library.md) that sends a RabbitMQ message:

```cs
[FunctionName("RabbitMQOutputCSharp")]
public static void BindToClient(
    [RabbitMQ(ConnectionStringSetting = "rabbitMQ")] IModel client,
    ILogger logger)
{
    QueueDeclareOk queue = client.QueueDeclare("hello", false, false, false, null);
    logger.LogInformation("Opening connection and creating queue!");
}
```

# [C# Script](#tab/csharp-script)

The following example shows a RabbitMQ output binding in a *function.json* file and a [C# script function](functions-reference-csharp.md) that uses the binding. The function uses a timer trigger to send a queue message every 15 seconds.

Here's the binding data in the *function.json* file:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Here's C# script code that creates a single message:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Here's C# script code that creates multiple messages:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# [JavaScript](#tab/javascript)

The following example shows a RabbitMQ output binding in a *function.json* file and a [JavaScript function](functions-reference-node.md) that uses the binding. The function uses a timer trigger to send an object message to the specified queue every minute.

Here's the binding data in the *function.json* file:

```json
{
    "scriptFile": "index.js",
    "entryPoint": "",
    "dataType": "string",
    "bindings": [
        {
            "type": "timerTrigger",
            "schedule": "0 */1 * * * *",
            "name": "myTimer",
            "direction": "in"
        },
        {
            "dataType": "string",
            "type": "rabbitMQ",
            "direction": "out",
            "name": "$return",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ],
    "disabled": false
}
```

Here's JavaScript code:

```javascript
module.exports = async function(context) {
    context.log("Output function firing...");
    context.done(null, "Hello, world!");
}
```

# [Python](#tab/python)

The following example shows a RabbitMQ output binding in a *function.json* file and a Python function that uses the binding. The function uses a timer trigger to send an object message to the specified queue every minute.

Here's the binding data in the *function.json* file:

```json
{
    "scriptFile": "__init__.py",
    "dataType": "string",
    "bindings": [
        {
            "type": "timerTrigger",
            "schedule": "0 */1 * * * *",
            "name": "myTimer",
            "direction": "in"
        },
        {
            "dataType": "string",
            "type": "rabbitMQ",
            "direction": "out",
            "name": "$return",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ],
    "disabled": false
}
```

In *_\_init_\_.py*, you can write out a message to the queue by passing a value to the `set` method.

```python
import logging

def main(myTimer):
    logging.info("Output function firing...");
    message = "hi"
    return message
```

# [Java](#tab/java)

The following example shows a Java function that sends a message to RabbitMQ queue when triggered by a TimerTrigger every 5 minutes.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
            @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
            @RabbitMQOutput(connectionStringSetting = "rabbitMQ", queueName = "hello") OutputBinding<String> output,
            final ExecutionContext context) {
        output.setValue("Some string");
}
```

 In the [Java functions runtime library](/java/api/overview/azure/functions/runtime), use the `@QueueOutput` annotation on function parameters whose value would be written to a RabbitMQ queue. The parameter type should be `OutputBinding<T>`, where T is any native Java type of a POJO.

---

## Attributes and annotations

# [C#](#tab/csharp)

In [C# class libraries](functions-dotnet-class-library.md), use the [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs).

Here's a `RabbitMQAttribute` attribute in a method signature:

```csharp
[FunctionName("RabbitMQTest")]
public static void BindToClient([RabbitMQ(ConnectionStringSetting = "rabbitMQ")] IModel client,
    ILogger logger)
{
    ...
}
```

For a complete example, see C# example.

# [C# Script](#tab/csharp-script)

Attributes are not supported by C# Script.

# [JavaScript](#tab/javascript)

Attributes are not supported by JavaScript.

# [Python](#tab/python)

Attributes are not supported by Python.

# [Java](#tab/java)

The `ServiceBusQueueOutput` and `ServiceBusTopicOutput` annotations are available to write a message as a function output. The parameter decorated with these annotations must be declared as an `OutputBinding<T>` where `T` is the type corresponding to the message's type.

---

## Configuration

The following table explains the binding configuration properties that you set in the *function.json* file and the `ServiceBus` attribute.

|function.json property | Attribute property |Description|
|---------|---------|----------------------|
|**type** | n/a | Must be set to "RabbitMQTrigger". This property is set automatically when you create the trigger in the Azure portal.|
|**direction** | n/a | Must be set to "in". This property is set automatically when you create the trigger in the Azure portal. |
|**name** | n/a | The name of the variable that represents the queue in function code. |
|**queueName**|**QueueName**|Name of the queue to receive messages from.  Set only if monitoring a queue, not for a topic.|
|**hostName**|**HostName**|(optional if using ConnectStringSetting) Hostname of the queue (Ex: 10.26.45.210)|
|**userName**|**UserName**|(optional if using ConnectionStringSetting) Name to access the queue |
|**password**|**Password**|(optional if using ConnectionStringSetting) Password to access the queue|
|**connectionStringSetting**|**ConnectionStringSetting**|The name of the app setting that contains the RabbitMQ message queue connection string. (Example: amqp://user:password@url:port)|
|**preFetchCount**|**PreFetchCount**| Gets the prefetch count while creating the RabbitMQ QoS. This setting controls how many values are cached.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

# Usage

# [C#](#tab/csharp)

Use the following parameter types for the output binding:

* `byte[]` - If the parameter value is null when the function exits, Functions does not create a message.
* `string` - If the parameter value is null when the function exits, Functions does not create a message.
* `C# POCO` - If the message is properly formatted as a C# object

When working with C# functions:

* Async functions need a return value or `IAsyncCollector` instead of an `out` parameter.

* To access the session ID, bind to a [`Message`](/dotnet/api/microsoft.azure.servicebus.message) type and use the `sessionId` property.

# [C# Script](#tab/csharp-script)

Use the following parameter types for the output binding:


# [JavaScript](#tab/javascript)

Access the queue or topic by using `context.bindings.<name from function.json>`. You can assign a string, a byte array, or a JavaScript object (deserialized into JSON) to `context.binding.<name>`.

# [Python](#tab/python)

Use the [Azure Service Bus SDK](../service-bus-messaging/index.yml) rather than the built-in output binding.

# [Java](#tab/java)

Use the [Azure Service Bus SDK](../service-bus-messaging/index.yml) rather than the built-in output binding.

---

## Exceptions and return codes

| Binding | Reference |
|---|---|
| Service Bus | [Service Bus Error Codes](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Service Bus Limits](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## host.json settings

This section describes the global configuration settings available for this binding in versions 2.x and higher. The example host.json file below contains only the settings for this binding. For more information about global configuration settings, see [host.json reference for Azure Functions version](functions-host-json.md).

> [!NOTE]
> For a reference of host.json in Functions 1.x, see [host.json reference for Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

If you have `isSessionsEnabled` set to `true`, the `sessionHandlerOptions` will be honored.  If you have `isSessionsEnabled` set to `false`, the `messageHandlerOptions` will be honored.

|Property  |Default | Description |
|---------|---------|---------|
|prefetchCount|0|Gets or sets the number of messages that the message receiver can simultaneously request.|
|maxAutoRenewDuration|00:05:00|The maximum duration within which the message lock will be renewed automatically.|
|autoComplete|true|Whether the trigger should automatically call complete after processing, or if the function code will manually call complete.<br><br>Setting to `false` is only supported in C#.<br><br>If set to `true`, the trigger completes the message automatically if the function execution completes successfully, and abandons the message otherwise.<br><br>When set to `false`, you are responsible for calling [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet) methods to complete, abandon, or deadletter the message. If an exception is thrown (and none of the `MessageReceiver` methods are called), then the lock remains. Once the lock expires, the message is re-queued with the `DeliveryCount` incremented and the lock is automatically renewed.<br><br>In non-C# functions, exceptions in the function results in the runtime calls `abandonAsync` in the background. If no exception occurs, then `completeAsync` is called in the background. |
|maxConcurrentCalls|16|The maximum number of concurrent calls to the callback that the message pump should initiate per scaled instance. By default, the Functions runtime processes multiple messages concurrently.|
|maxConcurrentSessions|2000|The maximum number of sessions that can be handled concurrently per scaled instance.|

## Next steps

- [Run a function when a Service Bus queue or topic message is created (Trigger)](./functions-bindings-service-bus-trigger.md)
