---
title: Azure Functions Table storage bindings
description: Understand how to use Azure Table storage bindings in Azure Functions.
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, functions, event processing, dynamic compute, serverless architecture

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2017
ms.author: chrande
---
# Azure Functions Table storage bindings

This article explains how to work with Azure Table storage bindings in Azure Functions. Azure Functions supports input and output bindings for Azure Table storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## Table storage input binding

Use the Azure Table storage input binding to read a table in an Azure Storage account.

## Input - example

See the language-specific example:

* [Precompiled C# read one entity](#input---c-example-1)
* [Precompiled C# read multiple entities](#input---c-example-2)
* [C# script - read one entity](#input---c-script-example-1)
* [C# script - read multiple entities](#input---c-script-example-2)
* [JavaScript](#input---javascript-example)

### Input - C# example 1

The following example shows [precompiled C#](functions-dotnet-class-library.md) code that reads a single table row. 

The row key value "{queueTrigger}" indicates that the row key comes from the queue message string.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
    }
}
```

### Input - C# example 2

The following example shows [precompiled C#](functions-dotnet-class-library.md) code that reads multiple table rows. Note that the `MyPoco` class derives from `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
        }
    }
}
```

### Input - C# script example 1

The following example shows a table input binding in a *function.json* file and [C# script](functions-reference-csharp.md) code that uses the binding. The function uses a queue trigger to read a single table row. 

The *function.json* file specifies a `partitionKey` and a `rowKey`. The `rowKey` value "{queueTrigger}" indicates that the row key comes from the queue message string.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Here's the C# script code:

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### Input - C# script example 2

The following example shows a table input binding in a *function.json* file and [C# script](functions-reference-csharp.md) code that uses the binding. The function reads entities for a partition key that is specified in a queue message.

Here's the *function.json* file:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

The C# script code adds a reference to the Azure Storage SDK so that the entity type can derive from `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### Input - F# example

The following example shows a table input binding in a *function.json* file and [F# script](functions-reference-fsharp.md) code that uses the binding. The function uses a queue trigger to read a single table row. 

The *function.json* file specifies a `partitionKey` and a `rowKey`. The `rowKey` value "{queueTrigger}" indicates that the row key comes from the queue message string.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Here's the F# code:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

### Input - JavaScript example

The following example shows a  table input binding in a *function.json* file and [JavaScript code] (functions-reference-node.md) that uses the binding. The function uses a queue trigger to read a single table row. 

The *function.json* file specifies a `partitionKey` and a `rowKey`. The `rowKey` value "{queueTrigger}" indicates that the row key comes from the queue message string.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Here's the JavaScript code:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## Input - C# attributes
 
For [precompiled C#](functions-dotnet-class-library.md) functions, use the following attributes to configure a table input binding:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), which is defined in NuGet package [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  The attribute's constructor takes the table name, partition key, and row key. It can be used on an out parameter or on the return value of the function, as shown in the following example:

  ```csharp
  [FunctionName("TableInput")]
  public static void TableInput(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)  in package [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Enables you to specify a different storage account than the function app's default storage account. This attribute can be applied at the parameter, method, or class level. Parameter level overrides method and class level, and method level overrides class level. Here's an example:

  ```csharp
  [StorageAccount("TableFunctionsStorage")]
  public static class TableFunctions
  {
    [FunctionName("TableInput")]
    [StorageAccount("TableInputStorage")]
    public static void TableInput([
        QueueTrigger("table-items")] string input, 
        [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco,
        TraceWriter log)
  ```

## Input - settings

The following settings appear only in the *function.json* file:

|Property|Description|
|--------|-----------|
|**type** | Must be set to `table`. This property is set automatically when you create the binding in the Azure portal.|
|**direction** | Must be set to `in`. This property is set automatically when you create the binding in the Azure portal. |
|**name** | The name of the variable that represents the table or entity in function code. | 

The following settings are configured in the *function.json* file or the `Table` attribute constructor:

|Property|Description|
|--------|-----------|
|**tableName** | The name of the table.| 
|**partitionKey** | Optional. The partition key of the table entity to read. See the [usage](#input---usage) section for guidance on how to use this property.| 
|**rowKey** | Optional. The row key of the table entity to read. See the [usage](#input---usage) section for guidance on how to use this property.| 
|**take** | Optional. The maximum number of entities to read in JavaScript. See the [usage](#input---usage) section for guidance on how to use this property.| 
|**filter** | Optional. An OData filter expression for table input in JavaScript. See the [usage](#input---usage) section for guidance on how to use this property.| 
|**connection** | The name of an app setting that contains the connection string used to connect to the storage account. If not set, the default connection string for the function app is used. This property is set automatically when you create the trigger in the Azure portal.|

## Input - usage

The Table storage input binding supports the following scenarios:

* **Read one row in C# or C# script**

  Set `partitionKey` and `rowKey`. Access the table data by using a method parameter `T <paramName>`. In C# script, `paramName` is the value specified in the `name` property of *function.json*. `T` is typically a type that implements `ITableEntity` or derives from `TableEntity`. The `filter` and `take` properties are not used in this scenario. 

* **Read one or more rows in C# or C# script**

  Access the table data by using a method parameter `IQueryable<T> <paramName>`. In C# script, `paramName` is the value specified in the `name` property of *function.json*. `T` must be a type that implements `ITableEntity` or derives from `TableEntity`. You can use `IQueryable` methods to do any filtering required. The `partitionKey`, `rowKey`, `filter`, and `take` properties are not used in this scenario.  

  An alternative is to use a `CloudTable paramName` method parameter to read the table by using the Azure Storage SDK.

* **Read one or more rows in JavaScript**

  Set the `filter` and `take` properties. Don't set `partitionKey` or `rowKey`. Access the input table entity (or entities) using `context.bindings.<name>`. The deserialized objects have `RowKey` and `PartitionKey` properties.

## Table storage output binding

Use an Azure Table storage output binding to write entities to a table in an Azure Storage account.

## Output - example

See the language-specific example that creates table entities.


* [Precompiled C#](#output---c-example)
* [C# script](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### Output - C# example

The following example shows [precompiled C#](functions-dotnet-class-library.md) code that uses an HTTP trigger to write a single table row. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### Output - C# script example

The following example shows a table output binding in a *function.json* file and [C# script](functions-reference-csharp.md) code that uses the binding. The function writes multiple table entities.

Here's the *function.json* file:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Here's the C# script code:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

### Output - F# example

The following example shows a table output binding in a *function.json* file and [F# script](functions-reference-fsharp.md) code that uses the binding. The function writes multiple table entities.

Here's the *function.json* file:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Here's the F# code:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### Output - JavaScript example

The following example shows a  table output binding in a *function.json* file and a [JavaScript function](functions-reference-node.md) that uses the binding. The function writes multiple table entities.

Here's the *function.json* file:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Here's the JavaScript code:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

## Output - C# attributes

 For [precompiled C#](functions-dotnet-class-library.md) functions, use the following attributes to configure a table output binding:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), which is defined in NuGet package [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  The attribute's constructor takes the table name. It can be used on an `out` parameter or on the return value of the function, as shown in the following example:

  ```csharp
  [FunctionName("TableOutput")]
  [return: Table("MyTable")]
  public static MyPoco TableOutput(
      [HttpTrigger] dynamic input, 
      TraceWriter log)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), which is defined in NuGet package [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Enables you to specify a different storage account than the function app's default storage account. This attribute can be applied at the parameter, method, or class level. Parameter level overrides method and class level, and method level overrides class level. Here's an example:

  ```csharp
  [StorageAccount("TableFunctionsStorage")]
  public static class TableFunctions
  {
    [FunctionName("TableOutput")]
    [StorageAccount("TableOutputStorage")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
  }
  ```

## Output - settings

The following settings appear only in the *function.json* file:

|Property|Description|
|--------|-----------|
|**type** | Must be set to `table`. This property is set automatically when you create the binding in the Azure portal.|
|**direction** | Must be set to `out`. This property is set automatically when you create the binding in the Azure portal. |
|**name** | The variable name used in function code that represents the table or entity. Set to `$return` to reference the function return value.| 

The following settings are configured in the *function.json* file or the `Table` attribute constructor:

|Property|Description|
|--------|-----------|
|**tableName** | The name of the table.| 
|**partitionKey** | The partition key of the table entity to write. See the [usage section](#output---usage) for guidance on how to use this property.| 
|**rowKey** | The row key of the table entity to write. See the [usage section](#output---usage) for guidance on how to use this property.| 
|**connection** | The name of an app setting that contains the connection string used to connect to the storage account. If not set, the default connection string for the function app is used. This setting is configured automatically when you create the trigger in the Azure portal.|

## Output - usage

The Table storage output binding supports the following scenarios:

* **Write one row in any language**

  In C# and C# script, access the output table entity by using a method parameter such as `out T paramName` or the function return value. In C# script, `paramName` is the value specified in the `name` property of *function.json*. `T` can be any serializable type if the partition key and row key are provided by the *function.json* file or the `Table` attribute. Otherwise, `T` must be a type that includes `PartitionKey` and `RowKey` properties. In this scenario, `T` typically implements `ITableEntity` or derives from `TableEntity`, but it doesn't have to.

* **Write one or more rows in C# or C#**

  In C# and C# script, access the output table entity by using a method parameter `ICollector<T> paramName` or `ICollectorAsync<T> paramName`. In C# script, `paramName` is the value specified in the `name` property of *function.json*. `T` specifies the schema of the entities you want to add. Typically, `T` derives from `TableEntity` or implements `ITableEntity`, but it doesn't have to. The partition key and row key values in *function.json* or the `Table` attribute constructor are not used in this scenario.

  An alternative is to use a `CloudTable paramName` method parameter to write to the table by using the Azure Storage SDK.

* **Write one or more rows in JavaScript**

  In JavaScript functions, access the table output using `context.bindings.<name>`.

## Next steps

> [!div class="nextstepaction"]
> [Learn more about Azure functions triggers and bindings](functions-triggers-bindings.md)
