---
title: Use dependency injection in .NET Azure Functions
description: Learn how to use dependency injection for registering and using services in .NET functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, functions, serverless architecture

ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
---
# Use dependency injection in .NET Azure Functions

Azure Functions supports the dependency injection (DI) software design pattern, which is a technique to achieve [Inversion of Control (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.

- Dependency injection in Azure Functions works in a similar fashion to the ASP.NET Core Dependency Injection features. Familiarity with [ASP.NET Core dependency injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)'s services, lifetimes, and patterns before using dependency injection in Azure Functions is recommended, but the two aren't identical.

- Support for dependency injection begins with Azure Functions 2.x.

## Prerequisites

Before you can use dependency injection, you must install the following NuGet packages:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions package](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) version 1.0.28 or later

- Optional: [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) Only required for registering HttpClient at startup

## Register services

To register services, create a method to configure and add components to an `IFunctionsHostBuilder` instance.  The Azure Functions host creates an instance of `IFunctionsHostBuilder` and passes it directly into your method.

To register the method, add the `FunctionsStartup` assembly attribute that specifies the type name used during startup.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### Caveats

There are a number of registration steps that happen before and after the runtime processes the startup class. Therefore, the keep in mind the following caveats:

- **The startup class is meant for only setup and registration.** Avoid using services registered at startup during the startup process. For instance, don't try to log a message in a logger that is being registered during startup. This point of the registration process is too early for your services to be available for use. After your app is set up, the Functions runtime continues to register additional dependencies, which can affect how your services operate.

- **Not all Functions types are available during setup**. For instance, `BindingContext` isn't available during the set-up process.

## Use injected dependencies

The dependency injection pipeline uses constructor injection to make your dependencies available to your function. The use of constructor injection requires that you not to use static functions.

The following sample demonstrates how the `IMyService` and `HttpClient` dependencies are injected into an HTTP-triggered function.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## Service lifetimes

Azure Functions apps provide the same service lifetimes as [ASP.NET Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): transient, scoped, and singleton.

For a Functions app, the different service lifetimes behave as follows:

- **Transient** services are created upon each request of the service
- **Scoped** service lifetime matches a function execution lifetime. Scoped services are created once per execution. Later requests for that service during the execution reuse the existing service instance.
- **Singleton** service lifetime matches the host lifetime and is reused across function executions on that instance. Singleton lifetime services are recommended for connections and clients, for example `SqlConnection`, `CloudBlobClient`, or `HttpClient` instances.

View or download a [sample of different service lifetimes](https://aka.ms/functions/di-sample) on GitHub.

## Logging services

If you need your own logging provider, the recommended way is to register an `ILoggerProvider` instance. Application Insights is added by Azure Functions automatically.

> [!WARNING]
> Do not add `AddApplicationInsightsTelemetry()` to the services collection as it registers services that conflict with services provided by the environment.

## Function app provided services

The function host registers many services. The following services are safe to take as a dependency in your application:

|Service Type|Lifetime|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Runtime configuration|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsible for providing the ID of the host instance|

If there are other services you want to take a dependency on, [create an issue and propose them on GitHub](https://github.com/azure/azure-functions-host).

### Overriding host services

Overriding services provided by the host is currently not supported.  If there are services you want to override, [create an issue and propose them on GitHub](https://github.com/azure/azure-functions-host).

## Reading configuration values

Provide configuration values to the setup class via [app settings](./functions-how-to-use-azure-function-app-settings.md#settings).

## Working with options

You can extract options from an `IConfiguration` instance into a custom type, which allows you to easily test your services.

Consider the following class that includes a property named consistent with configuration values.

```csharp
public class MyOptions
{
    public string AzureWebJobsStorage { get; set; }
}
```

From inside `Startup.Configure` you can extract the values from the `IConfiguration` instance into your custom type using the following code:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

Calling `Bind` copies matching values from the configuration into the custom instance. The options instance is now available in the IoC container to inject into a function.

The options object is injected into the function as an instance of the generic `IOptions` interface. Use the `Value` property to access the values found in your configuration.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        this._service = service;
        this._settings = options.Value;
    }
}
```

## Next steps

For more information, see the following resources:

- [How to monitor your function app](functions-monitoring.md)
- [Best practices for functions](functions-best-practices.md)
