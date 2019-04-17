---
title: Reference bindings in non-.NET functions using extension bundles
description: Extension bundles make all bindings published by the Azure Functions team available to non-.NET functions through a setting in the host.json file
author: craigshoemaker
ms.author: cshoe
ms.date: 4/17/2019
ms.topic: article
ms.service: functions
ms.prod: azure
manager: jeconnoc
---

# Reference bindings in non-.NET functions using extension bundles

Extension bundles make all bindings published by the Azure Functions team available to non-.NET functions through a setting in the *host.json* file.

As of Azure Functions version 2.x, bindings are available as separate packages from the functions runtime. While .NET functions access bindings through NuGet packages, extension bundles allow non-.NET functions access to all bindings through a configuration setting.

> [!TIP]
> Now that bindings are available via configuration non-.NET functions no longer need to install .NET Core in order to use bindings.

## Reference function bindings

To make bindings available to your non-.NET function, ensure you have the latest version of [Azure Functions Core Tools](./functions-run-local.md#install-the-azure-functions-core-tools) add update the *host.json* file the following entry for `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- The `id` property references the namespace for Microsoft Azure Functions extension bundles.
- The `version` property uses the [interval notation for specifying version ranges](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards).

Once you reference the extension bundles in your project, then all default bindings are available to your functions.

## Next Steps

- [Manually install or update Azure Functions binding extensions from the portal](./install-update-binding-extensions-manual.md)