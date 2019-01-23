---
title: Triggers and bindings in Azure Functions
description: Learn to use triggers and bindings to connect your Azure Function to online events and cloud-based services.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc

ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
---

# Azure Functions triggers and bindings concepts

Triggers are the "cause" for the "effect" of a running function. A trigger defines how a function is invoked and a function must have exactly one trigger. Triggers have associated data, which is often provided as the payload of the function. 

Binding to a function is a way of declaratively connecting another resource to the function; bindings may be connected as *input bindings*, *output bindings*, or both. Data from bindings is provided to the function as parameters.

You can mix and match different bindings to suit your needs. Bindings are optional and a function might have one or multiple input and/or output bindings.

Triggers and bindings let you avoid hardcoding access to other services. Your function receives data (for example, the content of a queue message) in function parameters. You send data (for example, to create a queue message) by using the return value of the function. 

In C# and C# script, alternative ways to send data are `out` parameters and [collector objects](functions-reference-csharp.md#writing-multiple-output-values).

Consider the following examples of how you could implement different functions.

| Example Scenario | Trigger | Input Binding | Output Binding |
|-------------|---------|---------------|----------------|
| A new queue message arrives which runs a function to write to another queue. | Queue | Queue | Queue |
|A scheduled job reads Blob Storage contents and creates a new Cosmos DB document. | Timer | Blob Storage | Cosmos DB |
|The Event Grid is used to read an image from Blob Storage and a document from Cosmos DB to send an email. | Event Grid | Blob Storage and  Cosmos DB | SendGrid |
| A webhook that uses Microsoft Graph to update an Excel sheet. | HTTP | *None* | Microsoft Graph |

These examples are not meant to be exhaustive, but are provided to illustrate how you can use triggers and bindings together.

###  Trigger and binding definitions

Triggers and bindings are defined differently depending on the development approach.

| Approach | Triggers and bindings are configured by... |
|-------------|--------------------------------------------|
| C# class library | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorating methods and parameters with C# attributes |
| All others (including Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;updating [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

The portal provides a UI for this configuration, but you can edit the file directly by opening the **Advanced editor**.

In .NET, the parameter type defines the data type for input data. For instance, use `string` to bind to the text of a queue trigger, a byte array to read as binary and a custom type to de-serialize to a POCO object.

For languages that are dynamically typed such as JavaScript, use the `dataType` property in the *function.json* file. For example, to read the content of an HTTP request in binary format, set `dataType` to `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Other options for `dataType` are `stream` and `string`.

## Binding direction

All triggers and bindings have a `direction` property in the [function.json](./functions-reference.md) file:

- For triggers, the direction is always `in`
- Input and output bindings use `in` and `out`
- Some bindings support a special direction `inout`. If you use `inout`, only the **Advanced editor** is available in the **Integrate** tab.

When you use [attributes in a class library](functions-dotnet-class-library.md) to configure triggers and bindings, the direction is provided in an attribute constructor or inferred from the parameter type.

## Supported bindings

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

For information about which bindings are in preview or are approved for production use, see [Supported languages](supported-languages.md).

## Resources
- [Binding expressions and patterns](./functions-bindings-expressions-patterns.md)
- [Using the Azure Function return value](./functions-bindings-return-value.md)
- [How to register a binding expression](./functions-bindings-register.md)
- Testing:
  - [Strategies for testing your code in Azure Functions](functions-test-a-function.md)
  - [Manually run a non HTTP-triggered function](functions-manually-run-non-http.md)
- [Handling binding errors](./functions-bindings-errors.md)
 
## Next steps
> [!div class="nextstepaction"]
> [Register Azure Functions binding extensions](./functions-bindings-register.md)
