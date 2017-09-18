---
title: Branching in Azure Data Factory pipeline | Microsoft Docs
description: 'Learn how to control flow of data in Azure Data Factory by branching and chaining activities.'
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: jhubbard
editor: spelluru

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo

---

# Branching and chaining activities in a Data Factory pipeline
In this tutorial, you create a Data Factory pipeline that showcases some of the control flow features. The pipeline does a simple copy from a container in Azure Blob Storage to another container in the same storage account. If the copy activity succeeds, you want to send the success of the copy details (such as the amount of data written) as a success email. If the copy activity fails, you want to send failure details (such as the error message) as a failure email. Throughout the tutorial, you see how to pass parameters.

A high-level overview of the scenario:
![Overview](media/tutorial-control-flow/overview.png)

You perform the following steps in this tutorial:

> [!div class="checklist"]
> * Create a data factory.
> * Create an Azure Storage linked service.
> * Create an Azure Blob dataset
> * Create a pipeline that contains a copy activity and web activity
> * Send outputs of activities to subsequent activities
> * Utilize parameter passing and system variables
> * Start a pipeline run.
> * Monitor the pipeline and activity runs.

This tutorial uses .NET SDK. You can use other mechanisms to interact with Azure Data Factory, refer to samples under "Quickstarts" in the table of contents.

## Prerequisites

* **Azure subscription**. If you don't have a subscription, you can create a [free trial](http://azure.microsoft.com/pricing/free-trial/) account.
* **Azure Storage account**. You use the blob storage as **source** data store. If you don't have an Azure storage account, see the [Create a storage account](../storage/common/storage-create-storage-account.md#create-a-storage-account) article for steps to create one.
* **Azure SQL Database**. You use the database as **sink** data store. If you don't have an Azure SQL Database, see the [Create an Azure SQL database](../sql-database/sql-database-get-started-portal.md) article for steps to create one.
* **Visual Studio** 2013, 2015, or 2017. The walkthrough in this article uses Visual Studio 2017.
* **Download and install [Azure .NET SDK](http://azure.microsoft.com/downloads/)**.
* **Create an application in Azure Active Directory** following [this instruction](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Make note of the following values that you use in later steps: **application ID**, **authentication key**, and **tenant ID**. Assign application to "**Contributor**" role by following instructions in the same article.

### Create blob table

1. Launch Notepad. Copy the following text and save it as **input.txt** file on your disk.

	```
    John|Doe
    Jane|Doe
	```
2. Use tools such as [Azure Storage Explorer](http://storageexplorer.com/) to create the **adfv2branch** container, and to upload the **input.txt** file to the container.

## Create Visual Studio project

Using Visual Studio 2015/2017, create a C# .NET console application.

1. Launch **Visual Studio**.
2. Click **File**, point to **New**, and click **Project**.
3. Select **Visual C#** -> **Console App (.NET Framework)** from the list of project types on the right.
4. Enter **ADFv2BranchTutorial** for the Name.
5. Click **OK** to create the project.

## Install NuGet packages

1. Click **Tools** -> **NuGet Package Manager** -> **Package Manager Console**.
2. In the **Package Manager Console**, run the following commands to install packages:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory 
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

2. Add the following code to the **Main** method that sets variables. Replace place-holders with your own values.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.
    string region = "East US";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2branch/";
    string inputBlobName = "input.txt";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string blobSourceDatasetName = "SourceStorageDataset";
    string blobSinkDatasetName = "SinkStorageDataset";
    string pipelineName = "Adfv2TutorialBranchCopy";
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
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## Create Azure Storage linked service

Add the following code to the **Main** method that creates an **Azure Storage linked service**. Learn more from [Azure Blob linked service properties](connector-azure-blob-storage.md#linked-service-properties) on supported properties and details.

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

## Create datasets

In this section, you create two datasets: one for the source and the other for the sink. 

### Create dataset for source Azure Blob

Add the following code to the **Main** method that creates an **Azure blob dataset**. Learn more from [Azure Blob dataset properties](connector-azure-blob-storage.md#dataset-properties) on supported properties and details.

You define a dataset that represents the source data in Azure Blob. This Blob dataset refers to the Azure Storage linked service you create in the previous step, and describes:

- The location of the blob to copy from: **FolderPath** and **FileName**;
- Notice the use of parameters for the FolderPath. “sourceBlobContainer” is the name of the parameter and the expression is replaced with the values passed in the pipeline run. The syntax to define parameters is @pipeline().parameters.<parameterName>


```csharp
// Create an Azure Blob source dataset
Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
DatasetResource sourceBlobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { 
            Value = "@pipeline().parameters.sourceBlobContainer" 
        },
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName
    }
);

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, sourceBlobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sourceBlobDataset, client.SerializationSettings));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### Create dataset for source Azure Blob

Add the following code to the **Main** method that creates an **Azure blob dataset**. Learn more from [Azure Blob dataset properties](connector-azure-blob-storage.md#dataset-properties) on supported properties and details.

You define a dataset that represents the source data in Azure Blob. This Blob dataset refers to the Azure Storage linked service you create in the previous step, and describes:

- The location of the blob to copy from: **FolderPath** and **FileName**;
- Notice the use of parameters for the FolderPath. “sinkBlobContainer” is the name of the parameter and the expression are replaced with the values passed in the pipeline run. The syntax to define parameters is @pipeline().parameters.<parameterName>
- No filename is included in this dataset. It retains the source dataset name that it is copying from.


```csharp
// Create an Azure Blob sink dataset
Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
DatasetResource sinkBlobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { 
            Value = "@pipeline().parameters.sinkBlobContainer" 
        }
    }
);

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, sinkBlobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sinkBlobDataset, client.SerializationSettings));
```

## Create Email class
In your C# project, create a class named **EmailRequest**. This defines what properties the pipeline sends in the body request when sending an email. In this tutorial, the pipeline sends four properties from the pipeline to the email:

- **Message**: body of the email. In the case of a successful copy, this property contains details of the run (number of data written). In the case of a failed copy, this property contains details of the error.
- **Data factory name**: name of the data factory
- **Pipeline name**: name of the pipeline
- **Receiver**: Parameter that is passed through. This property specifies the receiver of the email.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```
## Create email workflow endpoints
To trigger sending an email, you use [Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) to define the workflow. For details on creating a Logic App workflow, see [How to create a logic app](../logic-apps/logic-apps-create-a-logic-app.md). 

### Success email workflow 
Create a Logic App workflow named `CopySuccessEmail`. Define the workflow trigger as `When an HTTP request is received`, and add an action of `Office 365 Outlook – Send an email`.

![Success email workflow](media/tutorial-control-flow/success-email-workflow.png)

For your request trigger, fill in the `Request Body JSON Schema` with the following JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```
This aligns with the **EmailRequest** class you created in the previous section. 

Your Request should look like this in the Logic App Designer:

![Logic App designer - request](media/tutorial-control-flow/logic-app-designer-request.png)

For the **Send Email** action, customize how you wish to format the email, utilizing the properties passed in the request Body JSON schema. Here is an example:

![Logic App designer - send email action](media/tutorial-control-flow/send-email-action.png)

Make a note of your HTTP Post request URL for your success email workflow:

```
//Success Request Url
https://prodxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## Fail email workflow 
Clone your **CopySuccessEmail** and create another Logic Apps workflow of **CopyFailEmail**. In the request trigger, the `Request Body JSON schema` is the same. Simply change the format of your email like the `Subject` to tailor toward a failure email. Here is an example:

![Logic App designer - fail email workflow](media/tutorial-control-flow/fail-email-workflow.png)

Make a note of your HTTP Post request URL for your failure email workflow:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

You should now have two workflow URL’s:

```
//Success Request Url
https://prod-31.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prod-31.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## Create a pipeline
Add the following code to the Main method that creates a pipeline with a copy activity and dependsOn property. In this tutorial, the pipeline contains one activity: copy activity, which takes in the Blob dataset as a source and another Blob dataset as a sink. Upon the copy activity succeeding and failing, it calls different email tasks.

In this pipeline you use the following features:

- Parameters
- Web Activity
- Activity dependency
- Using output from an activity as an input to the subsequent activity

Let’s break down the following pipeline section by section:

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = copyBlobActivity,
            Inputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sourceBlobDataset
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sinkBlobDataset
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        },
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345 ",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        },
        new WebActivity
        {
            Name = sendFailEmailActivity,
            Method =WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/a2ee1dfc90144a3aa36c63e500000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=334555555xxxxxxI8yVXl9ligDASBAEFMreJQKZEOSzcpr8",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Failed" }
                }
            }
        }
    }   
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```
### Parameters
The first section of our pipeline defines parameters. 

- sourceBlobContainer - parameter in the pipeline consumed by the source blob dataset.
- sinkBlobContainer – parameter in the pipeline consumed by the sink blob dataset
- receiver – parameter in the pipeline consumed by the two web activities on which email address receives the email


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### Web Activity
The Web Activity allows a call to any REST endpoint. For more information about the activity, see [Web Activity](control-flow-web-activity.md). This pipeline uses a Web Activity to call the Logic Apps email workflow. You create two web activities: one that calls to the **CopySuccessEmail** workflow and one that calls the **CopyFailWorkFlow**.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prod-16.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```
In the “Url” property, paste the Request URL endpoints from your Logic Apps workflow accordingly. In the “Body” property, pass an instance of the “EmailRequest” class. The email request contains the following properties:

- Message – Passing value of `@{activity('CopyBlobtoBlob').output.dataWritten`. Accesses a property of the previous copy activity and passes the value of dataWritten. For the failure case, pass the error output instead of `@{activity('CopyBlobtoBlob').error.message`.
- Data Factory Name – Passing value of `@{pipeline().DataFactory}` This is a system variable, allowing you to access the corresponding data factory name. For a list of system variables, see [System Variables](control-flow-system-variables.md) article.
- Pipeline Name – Passing value of `@{pipeline().Pipeline}`. This is also a system variable, allowing you to access the corresponding pipeline name. 
- Receiver – Passing value of "@pipeline().parameters.receiver"). Accessing the pipeline parameters.
 
This code creates a new Activity Dependency, depending on the previous copy activity that it succeeds.

## Create pipeline run
Add the following code to the **Main** method that **triggers a pipeline run**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## Monitor pipeline run
1. Add the following code to the **Main** method to continuously check the status of the pipeline run until it finishes copying the data.

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
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## Run the code

Build and start the application, then verify the pipeline execution.

The console prints the progress of creating data factory, linked service, datasets, pipeline, and pipeline run. It then checks the pipeline run status. Wait until you see the copy activity run details with data read/written size. 


## Next steps
The pipeline in this sample copies data from one location to another location in an Azure blob storage. You learned how to: 

> [!div class="checklist"]
> * Create a data factory.
> * Create an Azure Storage linked service.
> * Create an Azure Blob dataset
> * Create a pipeline that contains a copy activity and web activity
> * Send outputs of activities to subsequent activities
> * Utilize parameter passing and system variables
> * Start a pipeline run.
> * Monitor the pipeline and activity runs.

Go through the other tutorials that implement different scenarios: 

Tutorial | Description
-------- | -----------
[Tutorial: copy data from an on-premises SQL Server to an Azure blob storage](tutorial-hybrid-copy-powershell.md) | Shows you how to copy data from an on-premises SQL Server database to an Azure blob storage. 
[Tutorial: transform data using Spark](tutorial-transform-data-spark-powershell.md) | Shows you how to transform data in the cloud by using a Spark cluster on Azure