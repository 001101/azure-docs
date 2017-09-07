---
title: Create an Azure data factory using .NET | Microsoft Docs
description: Create an Azure data factory to copy data from one location in in an Azure Blob Storage to another location in the same Blob Storage. 
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: spelluru

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/06/2017
ms.author: jingwang

---

# Create a data factory and pipeline using .NET SDK

This quickstart describes how to use .NET SDK to create an Azure data factory. The pipeline in this data factory copies data from one location to another location in an Azure blob storage.

## Prerequisite

* **Azure subscription**. If you don't have a subscription, you can create a [free trial](http://azure.microsoft.com/pricing/free-trial/) account.
* **Azure Storage account**. You use the blob storage as **source** and **sink** data store. If you don't have an Azure storage account, see the [Create a storage account](../storage/common/storage-create-storage-account.md#create-a-storage-account) article for steps to create one. Prepare blob(s) under any path in the storage as source.
* **Visual Studio** 2013, 2015, or 2017. The walkthrough in this article uses Visual Studio 2017.
* **Download and install [Azure .NET SDK](http://azure.microsoft.com/downloads/)**.
* **Create an application in Azure Active Directory** following [this instruction](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Make note of the following values that you use in later steps: **application ID**, **authentication key**, and **tenant ID**. Assign application to "**Contributor**" role.

## Create Visual Studio project

Using Visual Studio 2013/2015/2017, create a C# .NET console application.

1. Launch **Visual Studio**.
2. Click **File**, point to **New**, and click **Project**.
3. Select **Visual C#** -> **Console App (.NET Framework)** from the list of project types on the right.
4. Enter **ADFv2QuickStart** for the Name.
5. Click **OK** to create the project.

## Install NuGet packages

1. Click **Tools** -> **NuGet Package Manager** -> **Package Manager Console**.
2. In the **Package Manager Console**, run the following commands to install packages:

    ```
    Install-Package Microsoft.Azure.Management.DataFactories
    Install-Package Microsoft.Azure.Management.ResourceManager
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## Create Data Factory client

1. Open **Program.cs**, include the following statements to add references to namespaces.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Add the following code to the **Main** method that sets variables. Replace below place-holders with your own values.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";
    string region = "<choose the region to create the factory, e.g. East US>";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/path>";
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/path>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. Add the following code to the **Main** method that creates an instance of **DataFactoryManagementClient** class. You use this object to create data factory, linked service, datasets, and pipeline. You also use this object to monitor the pipeline run details.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## Create data factory

Add the following code to the **Main** method that creates a **data factory**.

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    LoggingStorageAccountName = storageAccount, // Storage account for logging
    LoggingStorageAccountKey = storageKey       // Storage account key for logging
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## Create linked service

Add the following code to the **Main** method that creates an **Azure Storage linked service**.

You create linked services in a data factory to link your data stores and compute services to the data factory. In this quickstart, you only need create one Azure Storage linked service as both copy source and sink store, named "AzureStorageLinkedService" in the sample.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

## Create dataset

Add the following code to the **Main** method that creates an **Azure blob dataset**.

You define dataset that represents the data to copy from/to. In this example, this Blob dataset refers to the Azure Storage linked service you create in above step. It takes a parameter whole value is set when this dataset is consumed in an activity. The parameter is used to construct the "folderPath" pointing to where the data resides.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## Create pipeline

Add the following code to the **Main** method that creates a **pipeline with one blob-to-blob copy activity**.

In this example, this pipeline contains one activity and takes two parameters - input blob path and output blob path. The values for these parameters are set when the pipeline is triggered. The copy activity refers the same Blob dataset created in above step as input and output, while it takes the two different parameters as source path and sink path respectively.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## Create pipeline run

Add the following code to the **Main** method that **triggers a pipeline run**.

This code also sets values of **inputPath** and **outputPath** parameters specified in pipeline with the actual values of source and sink blob paths.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## Monitor pipeline run

1. Add the following code to the **Main** method to continuously check the pipeline run status until it finishes copying the data.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Add the following code to the **Main** method that retrieves copy activity run details, for example, size of the data read/written.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10), pipelineName).ToList();
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## Run the code

Build and start the application, then verify the pipeline execution.

The console prints the progress of creating data factory, linked service, datasets, pipeline, and pipeline run. It then checks the pipeline run status. Wait until you see the copy activity run details with data read/written size. Then, use Azure Storage explorer to check the blob(s) is copied to "outputBlobPath" from "inputBlobPath" as you specified in variables.

## Clean up resources
You can clean up the resources that you created in the Quickstart in two ways. You can delete the [Azure resource group]((../azure-resource-manager/resource-group-overview.md)), which includes all the resources in the resource group. If you want to keep the other resources intact, delete only the data factory you created in this tutorial.

### To delete the entire resource group including the newly created data factory:
1.	Locate your resource group in the Azure portal. From the left-hand menu in the Azure portal, click **Resource groups** and then click the name of your resource group, such as our example **myresourcegroup**.
2.	On your resource group page, click **Delete**. Then type the name of your resource group, such as our example **myresourcegroup**, in the text box to confirm deletion, and then click **Delete**.

### To delete the newly created data factory:
1. Locate your data factory in the Azure portal. From the left-hand menu in Azure portal, click **More services**, search for the **data factories** category, and select it.
2. In the **Data factories** page, search for the data factory you created. Select the data factory you want to delete. 
3. In the **Data factory** page, click **Delete** on the toolbar to delete the data factory.

## Next steps
The pipeline in this sample copies data from one location to another location in an Azure blob storage. Go through the following tutorials to learn about using Data Factory in slightly complex scenarios. 

Tutorial | Description
-------- | -----------
[Tutorial: copy data from Azure Blob Storage to Azure SQL Database](tutorial-copy-data-dot-net.md) | Shows you how to copy data from a blob storage to a SQL database. For a list of data stores supported as sources and sinks in a copy operation by data factory, see [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats). 
[Tutorial: copy data from an on-premises SQL Server to an Azure blob storage](tutorial-copy-onprem-data-to-cloud-dot-net.md) | Shows you how to copy data from an on-premises SQL Server database to an Azure blob storage. 
[Tutorial: transform data using Spark](tutorial-transform-data-dot-net.md) | Shows you how to transform data in the cloud by using a Spark cluster on Azure
