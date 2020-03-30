*Note: This is a temporary page.  **This will not be published.***

-- 

### Intro

We'll be working with Robert Outlaw's (routlaw) team to generate reference docs. 

The reference documentation types we need for Spool are:

- **REST Management** docs to support resource management (portal actions) via REST APIs. This is similar to the [Mixed Reality REST API doc set](https://docs.microsoft.com/en-us/rest/api/mixedreality/), the [Azure Media Services OpenAPI Swagger spec](https://docs.microsoft.com/en-us/azure/media-services/latest/), and the [Azure Media Services REST API spec](https://docs.microsoft.com/en-us/azure/media-services/latest/).
- **Azure CLI** docs to support resource management (portal actions) via the Azure CLI. This is similar to the [Azure Monitor CLI doc set](https://docs.microsoft.com/en-us/cli/azure/monitor?view=azure-cli-latest) and the [Azure Media Services CLI doc set](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest).
- **C# Reference** docs to support the server and client C# SDKs. This is similar to the [Azure Notification Hubs .NET API docs](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.notificationhubs?view=azure-dotnet).
- **JS Reference** docs to support the server and client JS SDKs. This is similar to the [Azure Blob Storage API reference docs](https://docs.microsoft.com/en-us/javascript/api/@azure/storage-blob/?view=azure-node-latest)
- **Swift Reference** docs to support the server and client Swift SDKs. This is similar to the [Spatial Anchors Objective C SDK docs](https://docs.microsoft.com/en-us/objectivec/api/spatial-anchors/).
- **Java Reference** docs to support the server and client Java SDKs. This is similar to the [Azure Functions Java API docs](https://docs.microsoft.com/en-us/azure/azure-functions/)

The status and ownership of these reference docs are outlined in [this spreadsheet](https://microsoft.sharepoint-df.com/:x:/t/IC3SDK/EasbZy5MyMBLq2S0NyTNBVABhKiR6r8bq8Ld8clQQkgOeA?e=jxpgWn). This spreadsheet is being updated regularly as we approach public preview and GA.


#### Generating REST Management API Docs

Customer intent: Manage Azure resources via REST APIs

Golden standard: TODO

Preparation instructions: TODO


#### Generating Azure CLI Docs

Customer intent: Manage Azure resources via Azure CLI

Golden standard: TODO

Preparation instructions: TODO


#### Generating C# SDK Reference Docs

**Customer intent**: Utilize Spool Client and Server C# SDKs.

**Golden standard**: [Microsoft.ML](https://docs.microsoft.com/en-us/dotnet/api/microsoft.ml?view=ml-dotnet)

**Preparation instructions**: Instructions are detailed [here](https://review.docs.microsoft.com/en-us/help/onboard/admin/reference/dotnet/documenting-api?branch=master). In summary:

- In your project's build settings, enable the generation of an XML comment file with the `-doc` compiler option. Our nuget packages must include this file.
- Annotate your code with [XML documentation comments](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/xmldoc/). Within these comments, use the [recommended documentation tags](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/xmldoc/recommended-tags-for-documentation-comments). .NET API documentation writing guidelines are located [here](https://github.com/dotnet/dotnet-api-docs/wiki).
- You can include markdown in your annotations by following the instructions [here](https://review.docs.microsoft.com/en-us/help/onboard/admin/reference/dotnet/documenting-api?branch=master). An example of this is [here](https://docs.microsoft.com/en-us/dotnet/api/microsoft.ml.transforms.valuemappingestimator?view=ml-dotnet).
- Publish your package to Nuget, and then let Mick know you're ready. He'll then [open an onboarding request with the docs team](https://review.docs.microsoft.com/en-us/help/onboard/?branch=master).




#### Generating JS SDK Reference Docs

Customer intent: Utilize Spool Client and Server JS SDKs

Golden standard: [Azure Storage](https://docs.microsoft.com/en-us/javascript/api/@azure/storage-blob/?view=azure-node-latest)

Preparation instructions: Instructions are detailed [here](https://review.docs.microsoft.com/en-us/help/onboard/admin/reference/js-ts/documenting-api?branch=master). In summary:
- For Javascript, [JSDoc conventions](https://jsdoc.app/) are used.
- For TypeScript, [TypeDoc conventions](http://typedoc.org/guides/doccomments/) are used.
- Once your package is ready, let Mick know and he'll [open an onboarding request with the docs team](https://review.docs.microsoft.com/en-us/help/onboard/?branch=master).


#### Generating Swift SDK Reference Docs

Customer intent: TODO

Golden standard: TODO

Preparation instructions: TODO


#### Generating Java SDK Reference Docs

Customer intent: TODO

Golden standard: TODO

Preparation instructions: TODO



### FAQ

**Q:** How do we deliver SDKs to Robert's team?

**A:** For non-released SDKs, we suggest using an unauthenticated (very important) MyGet feed for .NET and a NPM feed for JS/TS.

~

**Q:** Would it be possible to send you partially-completed SDKs to "flush the pipes" – whereby partial reference docs are generated and dropped into Spool's private repository?

**A:** Yes, we can start working with incomplete content, as long as its available in a feed. This is actually a great way to train your team on how to trigger new doc builds and manage the configuration yourself.