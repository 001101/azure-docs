---
# Mandatory fields.
title: Set up an Azure function for processing data
titleSuffix: Azure Digital Twins
description: See how to create an Azure function that can access and be triggered by digital twins.
author: cschormann
ms.author: cschorm # Microsoft employees only
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Connect Azure Functions apps for processing data

During preview, updating digital twins based on data is handled using [**event routes**](concepts-route-events.md) through compute resources, such as [Azure Functions](../azure-functions/functions-overview.md). An Azure function can be used to update a digital twin in response to:
* device telemetry data coming from IoT Hub
* property change or other data coming from another digital twin within the twin graph

This article walks you through creating an Azure function for use with Azure Digital Twins. 

Here is a overview of the steps it contains:

1. Create an Azure Functions app in Visual Studio
2. Write an Azure function with an [Event Grid](../event-grid/overview.md) trigger
3. Add authentication code to the function (to be able to access Azure Digital Twins)
4. Publish the function app to Azure
5. Set up [security](concepts-security.md) access. In order for the Azure function to receive an access token at runtime to access the service, you'll need to configure managed service identity for the function app.

## Create an Azure Functions app in Visual Studio

In Visual Studio 2019, select _File > New > Project_ and search for the _Azure Functions_ template, select "Next".

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: new project dialog":::

Specify a name for the function app and select "Create".

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: new project dialog":::

Select the *Event Grid trigger* and select "Create".

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: Azure function project trigger dialog":::

Once your function app is created, your visual studio will have auto populated code sample in **function.cs** file in your project folder. This short Azure function is used to log events.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: Project window with sample code":::

## Write an Azure function with an Event Grid trigger

You can write an Azure function by adding SDK to your function app. The function app interacts with Azure Digital Twins using the [Azure IoT Digital Twin client library for .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 

In order to use the SDK, you'll need to include the following packages into your project. You can either install the packages using visual studio nutget package manager or add the packages using `dotnet` command line tool. . Choose either of these methods: 

**1. Add packages using Visual Studio package manager:**
    
You can do this by right clicking on your project and select _Manage Nuget Packages..._ from the list. Then, in the window that opens, select _Browse_ tab and search for the following packages. Select _Install_ and _accept_ the License agreement to install the packages.

* `Azure.DigitalTwins.Core` (version `1.0.0-preview.2`)
* `Azure.Identity` (version `1.1.1`)

For configuration of the Azure SDK pipeline to set up properly for Azure Functions, you will also need the following packages. Repeat the same process as above to install all the packages.

* `System.Net.Http`
* `Azure.Core.Pipeline`

:::image type="content" source="media/how-to-create-azure-function/adding-packages.png" alt-text="Visual Studio: open nuget package manager":::

**2. Add packages using `dotnet` command line tool:**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.1.1
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
Next, in your Visual Studio Solution Explorer, open _function.cs_ file where you have sample code and add the following _using_ statements to your Azure function. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## Add authentication code to the Azure function

You will now add authentication code that will allow the function to access Azure Digital Twins.

Add variables to your function class for these values: 
* The Azure Digital Twins app ID
* The URL of your Azure Digital Twins instance. It is a good practice to read the service URL from an environment variable, rather than hard-coding it in the function.
* A static variable to hold an HttpClient instance. HttpClient is relatively expensive to create, and we want to avoid having to do this for every function invocation.

Also add a local variable inside of your function to hold your Azure Digital Twins client instance to the function project. Do *not* make this a static variable inside your class.

After these changes, your function code should be similar to the following:

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## Publish the Azure function app

To publish the function app to Azure, right-select the function project (not the solution) in Solution Explorer, and choose **Publish..**.

> [!IMPORTANT] 
> Publishing an Azure function will incur additional charges on your subscription, independent of Azure Digital Twins.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: publish azure function ":::

Select **Azure** as the publishing target and select **Next**

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: publish azure function dialog, page 1 ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: publish function dialog, page 2":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: publish function dialog, page 3":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: publish function dialog, page 4":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: publish function dialog, page 5":::

On the following page, enter the desired name for the new function app, a resource group, and other details.
For your Functions app to be able to access Azure Digital Twins, it needs to have a system-managed identity and have permissions to access your Azure Digital Twins instance.

## Set up security access for the Azure function app using CLI

The Azure function skeleton from earlier examples requires that a bearer token be passed to it, in order to be able to authenticate with Azure Digital Twins. To make sure that this bearer token is passed, you'll need to set up [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) for the function app. This only needs to be done once for each function app.

You can create system-managed identity and assign the function app's identity to the _Azure Digital Twins Owner (Preview)_ role for your Azure Digital Twins instance. This will give the function app permission in the instance to perform data plane activities. Then, make the URL of your Azure Digital Twins instance accessible to your function by setting an environment variable.

 Use [Azure Cloud Shell](https://shell.azure.com) to run the following commands:

Use the following command to create the system-managed identity. Take note of the principalId field in the output.

```azurecli	
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>	
```
Use the *principalId* value in the following command to assign the function app's identity to the *owner* role for your Azure Digital Twins instance.

```azurecli	
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```
Lastly, you can make the URL of your Azure Digital Twins instance accessible to your function by setting an environment variable. For more information on this, see [*Environment variables*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> The Azure Digital Twins instance's URL is made by adding *https://* to the beginning of your Azure Digital Twins instance's *hostName*. To see the hostName, along with all the properties of your instance, you can run `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

```azurecli	
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

### Assign access roles using CLI

Because Azure Digital Twins uses role-based access control to manage access (see [*Concepts: Security for Azure Digital Twins solutions*](concepts-security.md) for more information on this), you also need to add a role for each function app that you want to allow to access Azure Digital Twins.

To assign a role, you need the **resource ID** of the Azure Digital Twins instance you have created. If you did not record it earlier when you created your instance, you can retrieve it using this command:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
The resource ID will be part of the output, as a long string named "id" that begins with the letters "/subscriptions/…".

Use the resource ID along with the Azure function's object ID from earlier in the command below:

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```
## Set up security access for the Azure function app using Azure portal

A system assigned managed identity enables Azure resources to authenticate to cloud services (e.g. Azure Key Vault) without storing credentials in code. Once enabled, all necessary permissions can be granted via Azure role-based-access-control. The lifecycle of this type of managed identity is tied to the lifecycle of this resource. Additionally, each resource (e.g. Virtual Machine) can only have one system assigned managed identity.

In the [Azure portal](https://portal.azure.com/), search for _function app_ in the search bar with the function app name that you created earlier. Select the *Function App* from the list. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Azure portal: Search function app":::

On the function app window, select _Identity_ in the navigation bar on the left to enable managed identity.
Under _System assigned_ tab, toggle the _Status_ to On and _save_ it. You will see a pop-up to _Enable system assigned managed identity_.
Select _Yes_ button. 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure portal: Function app deployment":::

You can verify in the notifications that your function is successfully registered with Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure portal: Search function app":::

Also note the **object ID** shown on this page, as it will be used in the next section.


### Assign access roles using Azure portal

select _Azure role assignments_ button which opens up to Azure role assignments page. Then, select _+Add role assignment(Preview)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure portal: Search function app":::

On the _Add role assignment (Preview)_ window that opens up, select

* _Scope_: Resource group
* _Subscription_: select your Azure subscription
* _Resource group_: select your resource group from the dropdown
* _Role_: select _Azure Digital Twins Owner (Preview)_ from the dropdown

Save your details by selecting _Save_ button.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure portal: Add role assignment for Azure function":::

## Next steps

In this article, you followed the steps to set up an Azure function for use with Azure Digital Twins. Next, you can subscribe your Azure function to Event Grid, to listen on an endpoint. This endpoint could be:
* An Event Grid endpoint attached to Azure Digital Twins to process messages coming from Azure Digital Twins itself (such as property change messages, telemetry messages generated by [digital twins](concepts-twins-graph.md) in the twin graph, or life-cycle messages)
* The IoT system topics used by IoT Hub to send telemetry and other device events
* An Event Grid endpoint receiving messages from other services

Next, see how to build on your basic Azure function to ingest IoT Hub data into Azure Digital Twins:
* [*How-to: Ingest telemetry from IoT Hub*](how-to-ingest-iot-hub-data.md)