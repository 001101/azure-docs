---
# Mandatory fields.
title: Use the Azure Digital Twins APIs and SDKs
titleSuffix: Azure Digital Twins
description: See how to work with the Azure Digital Twins APIs, including via SDK.
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 05/08/2020
ms.topic: how-to
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Use the Azure Digital Twins APIs and SDKs

The Azure Digital Twins REST APIs are used to manage the major elements of your Azure Digital Twins solution. The API surface can be broadly divided into the following categories: 

* **DigitalTwinsModels** - The DigitalTwinsModels category contains APIs to manage the [models](concepts-models.md) in an Azure Digital Twins instance. Management activities include upload, validation, retrieval, and deletion of models authored in DTDL.
* **DigitalTwins** - The DigitalTwins category contains the APIs that let developers create, modify, and delete [digital twins](concepts-twins-graph.md) and their relationships in an Azure Digital Twins instance.
* **Query** - The Query category lets developers [find sets of digital twins in the twin graph](how-to-query-graph.md) across relationships.
* **EventRoutes** - The EventRoutes category contains APIs to [route data](concepts-route-events.md), through the system and to downstream services.

You can either use the REST APIs directly, or through an SDK. Currently, the only published SDK for interacting with these APIs is in C#. If you are working in another language, you can [generate your own SDK using AutoRest](how-to-create-custom-sdks.md).

## REST API usage examples

Use the [Azure Digital Twins OpenAPI (Swagger) file](https://github.com/Azure/azure-digital-twins/blob/private-preview/OpenApiSpec/digitaltwins.json) as a resource for calling the APIs directly.

You can also view example call bodies in the Swagger's accompanying [examples folder](https://github.com/Azure/azure-digital-twins/tree/private-preview/OpenApiSpec/examples).

## Azure Digital Twins .NET (C#) SDK

The Azure Digital Twins .NET (C#) SDK is part of the Azure SDK for .NET. It is located here: [Azure IoT Digital Twin client library for .NET](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/digitaltwins/sdk/digitaltwins/Azure.DigitalTwins.Core).

> [!NOTE]
> For in-depth information on SDK design, see the general [design principles for Azure SDKs](https://azure.github.io/azure-sdk/general_introduction.html) and the specific [.NET design guidelines](https://azure.github.io/azure-sdk/dotnet_introduction.html).

To use the SDK, include the NuGet package **Azure.DigitalTwins.Core** with your project. You will also need the **Azure.Identity** package.

* In Visual Studio, you can add packages with the NuGet Package Manager (Tools>NuGet Package Manager>Manage NuGet Packages for Solution). 
* Using the .NET command-line tool, you can run:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core
    dotnet add package Azure.identity
    ```

For a detailed walk-through of using the APIs in practice, see the [Tutorial: Code a client app](tutorial-code.md). 

### .NET SDK usage examples

Here are a few code samples.

Authenticate against the service:

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

Upload a model and list models:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Create and query twins:

```csharp
// Initialize twin metadata
var meta = new Dictionary<string, object>
{
    { "$model", "dtmi:com:contoso:SampleModel;1" },
};
// Initialize the twin properties
var initData = new Dictionary<string, object>
{
    { "$metadata", meta },
    { "data", "Hello World!" }
};
try {
    await client.CreateDigitalTwinAsync($"firstTwin", JsonSerializer.Serialize(initData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}

// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
}
```

See the [Tutorial: Code a client app](tutorial-code.md) for a walk-through of this sample app code. 

### General API usage

This section contains general information about and guidelines for using the APIs and SDKs.

* To use the SDK, instantiate the **DigitalTwinsClient** class. The constructor requires credentials that can be obtained with a variety of authentication methods in the [Azure.Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) package. 
* You may find the [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) useful while getting started, but there are several other options, including credentials for [managed identity](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet), which you will likely use to authenticate [Azure Functions set up with MSI](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) against Azure Digital Twins.
* All service API calls are exposed as member functions on the DigitalTwinsClient class.
* All service functions exist in synchronous and asynchronous versions
* All service functions throw an exception for any return status >= 400. Make sure you wrap calls into a try section appropriately and catch at least [**RequestFailedExceptions**](https://docs.microsoft.com/dotnet/api/azure.requestfailedexception?view=azure-dotnet).
* Most service methods return **Response<T>** or (**Task<Response<T>>** for the asynchronous calls), where `T` is the class of return object for the service call. The [**Response**](https://docs.microsoft.com/dotnet/api/azure.response-1?view=azure-dotnet) class encapsulates the service return and presents return values in its Value field.  
* Service methods with paged results return [**Pageable<T>**](https://docs.microsoft.com/dotnet/api/azure.pageable-1?view=azure-dotnet-preview) or [**AsyncPageable<T>**](https://docs.microsoft.com/dotnet/api/azure.asyncpageable-1?view=azure-dotnet-preview) as results.
* You can iterate over paged results using an [**await foreach** loop](https://docs.microsoft.com/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* See [Azure.Core](https://docs.microsoft.com/dotnet/api/azure?view=azure-dotnet-preview) for reference on the underlying SDK infrastructure and types.

Service methods return strongly-typed objects wherever possible. However, because Azure Digital Twins mostly is based on models custom-configured by the user at run time (via DTDL models uploaded to the service), many service APIs take and return twin data in form of JSON data.

### Serialization Helpers
The SDK also contains a number of helper classes for serialization. As described in the previous section, the core SDK methods return twin data as JSON, but the helper functions let you quickly create or deserialize twin data for access to basic information.

The helper classes available are:
* `BasicDigitalTwin`: Represents the core data of a digital twin
* `BasicRelationship`: Represents the core data of a relationship
* `UpdateOperationUtility`: Represents JSON Patch information used in update calls
* `WriteableProperty`: Represents property metadata

#### Deserializing a Digital Twin
You can always deserialize twin data using the JSON library of your choice, for example `System.Test.Json` or `Newtonsoft.Json`. For basic access to a twin, the helper classes make things a bit more convenient.

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

The `BasicDigitalTwin` helper class also gives you access to properties defined on the twin, via a `Dictionary<string, object>`. To list properties you could write:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

#### Creating a Digital Twin
Using the `BasicDigitalTwin` class you can also prepare data for creating a twin instance:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomId", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

This code is equivalent to the "manual" variant:
```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example: Room; 1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomId", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### Deserializing a relationship
You can always deserialize relationship data using the JSON library of your choice, for example `System.Test.Json` or `Newtonsoft.Json`. For basic access to a relationship, the helper classes make things a bit more convenient.

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

The `BasicRelationship` helper class also gives you access to properties defined on the relationship, via a `Dictionary<string, object>`. To list properties you could write:

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

#### Creating a Relationship

Using the `BasicDigitalTwin` class you can also prepare data for creating a twin instance:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
                                    // Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

#### Creating a Patch for Update
Update calls for twins and relationships use JSON Patch structure. To create JSON patch operations lists, you can use the `UpdateOperationsUtility` class.

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// unset a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## Monitor API metrics

API metrics such as requests, latency, and failure rate can be viewed in the [Azure portal](https://portal.azure.com/). 

From the portal homepage, search for your Azure Digital Twins instance to pull up its details. Select the **Metrics** option from the Azure Digital Twins instance's menu to bring up the *Metrics* page.

:::image type="content" source="media/how-to-use-apis/metrics.png" alt-text="Metrics page of an Azure Digital Twins instance in the Azure portal":::

From here, you can view the metrics for your instance and create custom views.

## Next steps

See how to use the APIs to create an Azure Digital Twins instance:
* [How-to: Create an Azure Digital Twins instance](how-to-set-up-instance.md)

Or, walk through the steps to create a client app like the one used in this how-to:
* [Tutorial: Code a client app](tutorial-code.md)
