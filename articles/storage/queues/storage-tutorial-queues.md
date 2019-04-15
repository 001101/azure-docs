---
title: Tutorial - Work with Azure storage queues
description: A tutorial on how to use the Azure Queue service to create queues, and insert, get, and delete messages.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/03/2019
#Customer intent: As a developer, I want to use queues in my app so that my service will scale automatically during high demand times without losing data.
---

# Tutorial: Work with Azure storage queues

Azure queue storage implements cloud-based queues to facilitate communication between components of a distributed application. Each queue maintains a list of messages that can be added by a sender component and processed by a receiver component. With a queue, your application can scale immediately to meet demand, improving resiliency. This article demonstrates the basic steps for working with an Azure storage queue.

In this tutorial, you learn how to:

> [!div class="checklist"]
>
> - Create an Azure storage account
> - Create the app
> - Programmatically access a queue
> - Insert messages into the queue
> - Dequeue messages
> - Add support for asynchronous code

## Prerequisites

- Get your free copy of the cross platform [Visual Studio Code](https://code.visualstudio.com/download) editor.
- Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).
- If you don’t have a current Azure subscription, create a [free account](https://azure.microsoft.com/free/) before you begin.

## Create an Azure storage account

First, create an Azure storage account. For a step-by-step guide to creating a storage account, see the [Create a storage account](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) quickstart.

## Create the app

Create a .NET Core application named **QueueApp**. For simplicity, this will be a single app that both sends and receives messages through the queue.

1. In a console window, use the `dotnet new` command to create a new console app with the name **QueueApp**. This command creates a simple app with a single source file: **Program.cs**.

```console
dotnet new console -n QueueApp
```

1. Switch to the newly created **QueueApp** folder and build the app to verify that all is well.

```console
cd QueueApp
```

```console
dotnet build
```

### Get your connection string

The client library uses a connection string to establish your connection. Your connection string is available in the **Settings** section of your storage account in the Azure portal.

1. In your browser, sign in to the [Azure portal](https://portal.azure.com/).

2. Select **Storage accounts** from either the **Azure services** icons at the top of the portal, or from the **FAVORITES** menu on the left of the portal page.

![Storage accounts icon or favorites menu item](media/storage-tutorial-queues/select-storage-accounts.png)

3. Select the storage account you created earlier.

![Storage account](media/storage-tutorial-queues/storage-account.png)

4. Select **Access keys**

![Access keys menu item](media/storage-tutorial-queues/select-access-keys.png)

5. Click the **Copy** button to the right of the **Connection string** field.

![Connection string](media/storage-tutorial-queues/get-connection-string.png)

The connection string is in this format:

```csharp
private const string connectionString = "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net";
```

### Add the connection string to the app

Add the connection string into the app so it can access the storage account.

1. From the command line in the project directory, type `code .` to open Visual Studio Code.

2. Open the **Program.cs** source file in the project.

3. In the **Program** class, add a `private const string connectionString = ` member to hold the connection string.

4. After the equal sign, paste the string value that you copied earlier in your Azure portal. This will initialize **connectionString**.

Your code should look similar to this. The **connectionString** value will be unique to your account.

```csharp
using System;

namespace QueueApp
{
    class Program
    {
        private const string connectionString = "DefaultEndpointsProtocol=https; ...";

        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

5. Save the file.

## Programmatically access a queue

1. Install the **WindowsAzure.Storage** package to the project with the `dotnet add package` command. Do this from a command line in the same folder as the project.

```console
dotnet add package WindowsAzure.Storage
```

2. At the top of the **Program.cs** file, add the following namespaces. We'll be using types from these namespaces to connect to Azure Storage and work with queues.

```csharp
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
```

3. Add the following method to your **Program** class to get a reference to a **CloudQueue**, which we're calling **mystoragequeue**. Getting a queue reference is a common task. This method will be called for both send and receive operations.

```csharp
static CloudQueue GetQueue()
{
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    return queueClient.GetQueueReference("mystoragequeue");
}
```

## Insert messages into the queue

Create a new method to asynchronously send a message into the queue. Add the following method to your **Program** class. This method gets a queue reference, then creates a new queue if it does not already exist by calling [**CreateIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.createifnotexistsasync?view=azure-dotnet). Then it adds the message to the queue by calling [**AddMessageAsync**](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.addmessageasync?view=azure-dotnet).

```csharp
static async Task SendMessageAsync(string newMessage)
{
    CloudQueue queue = GetQueue();
    bool createdQueue = await queue.CreateIfNotExistsAsync();

    if (createdQueue)
    {
        Console.WriteLine("The queue was created.");
    }

    CloudQueueMessage message = new CloudQueueMessage(newMessage);
    await queue.AddMessageAsync(message);
}
```

## Dequeue messages

Create a new method to asynchronously receive a message from the queue by calling [**GetMessageAsync**](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.getmessageasync?view=azure-dotnet). Once we've successfully received the message, it is safe to delete it so we don't process it more than once. After the message is received, delete it from the queue by calling [**DeleteMessageAsync**](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.deletemessageasync?view=azure-dotnet).

Add the following method to your **Program** class.

```csharp
static async Task<string> ReceiveMessageAsync()
{
    CloudQueue queue = GetQueue();
    bool exists = await queue.ExistsAsync();
    if (exists)
    {
        CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
        if (retrievedMessage != null)
        {
            string theMessage = retrievedMessage.AsString;
            await queue.DeleteMessageAsync(retrievedMessage);
            return theMessage;
        }
    }

    return "<queue empty or not created>";
}
```

## Update Main

Now, update the **Main** method to check for command line arguments. If there are any, assume they are the message and join them together to make a string. Then, add this string to our message queue by calling the **SendMessageAsync** method we added earlier.

If there are no command line arguments, the app will instead retrieve the first message in the queue and delete it by calling the **ReceiveMessageAsync** method.

Update **Main** to look like this:

```csharp
static async Task Main(string[] args)
{
    if (args.Length > 0)
    {
        string value = String.Join(" ", args);
        await SendMessageAsync(value);
        Console.WriteLine($"Sent: {value}");
    }
    else
    {
        string value = await ReceiveMessageAsync();
        Console.WriteLine($"Received {value}");
    }

    Console.ReadLine();
}
```

## Complete code

Here is the complete code listing for this project.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;

namespace QueueApp
{
    class Program
    {
        private const string connectionString = "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync();
                Console.WriteLine($"Received {value}");
            }
        }

        static CloudQueue GetQueue()
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            return queueClient.GetQueueReference("mystoragequeue");
        }

        static async Task SendMessageAsync(string newMessage)
        {
            CloudQueue queue = GetQueue();
            bool createdQueue = await queue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await queue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync()
        {
            CloudQueue queue = GetQueue();
            bool exists = await queue.ExistsAsync();
            if (exists)
            {
                CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await queue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
            }

            return "<queue empty or not created>";
        }
    }
}
```

## Add support for asynchronous code

Since our app uses cloud resources, we've created our code to run asynchronously. However, C#'s **async** and **await** keywords were not valid keywords in **Main** methods until C# 7.1. We can easily switch to that compiler through a flag in the .csproj file.

1. Open the **QueueApp.csproj** file in the editor.

2. Add `<LangVersion>7.1</LangVersion>` into the first **PropertyGroup** in the build file. It should look like the following when you are finished.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <LangVersion>7.1</LangVersion>
  </PropertyGroup>

...
```

3. Save the **QueueApp.csproj** file.

## Build and run the program

1. From the command line in the project directory, run the following to build the project.

```console
dotnet build
```

2. After the project builds successfully, run the following command to add our first message to the queue.

```console
dotnet run First queue message
```

You should see output similar to this:

```console
dotnet run First queue message
```

### Verify that the message was added to the queue

Now that our app has run and told us that it sent a message to the queue, we can verify that it worked by opening the contents of our queue in the Storage Explorer utility.

1. In Azure portal, navigate to the **Overview** page in your storage account.

![Overview menu item](media/storage-tutorial-queues/select-overview.png)

2. Select **Open in Explorer**.

![Open in Explorer icon](media/storage-tutorial-queues/select-open-in-explorer.png)

If you don't yet have Storage Explorer installed, you can follow the link to download your free copy of the utility.

3. In **Storage Explorer**, navigate to **mystoragequeue** and see the message we added.

![Queue message](media/storage-tutorial-queues/queue-message.png)

4. Run the app several more times with different strings to add more messages to the queue.

5. In **Storage Explorer**, select **Refresh** to see the new messages that were added.

![Select Refresh](media/storage-tutorial-queues/select-refresh.png)

6. Run the app with no command line arguments to receive and remove the first message in the queue.

```console
dotnet run
```

7. In **Storage Explorer**, select **Refresh** to see the that the first message was removed.

![Message removed](media/storage-tutorial-queues/message-removed.png)

8. If you continue to run the app until all the messages are removed, then run it one more time, you will get a message that the queue is empty.

```console
C:\Tutorials\QueueApp>dotnet run First queue message
Sent: First queue message

 C:\Tutorials\QueueApp>dotnet run Second queue message
Sent: Second queue message

 C:\Tutorials\QueueApp>dotnet run Third queue message
Sent: Third queue message

 C:\Tutorials\QueueApp>dotnet run
Received First queue message

 C:\Tutorials\QueueApp>dotnet run
Received Second queue message

 C:\Tutorials\QueueApp>dotnet run
Received Third queue message

 C:\Tutorials\QueueApp>dotnet run
Received <queue empty or not created>

 C:\Tutorials\QueueApp>_
```

## Clean up resources

When you're working in your own subscription, it's a best practice at the end of a project to identify whether you still need the resources you created. Resources left running can cost you money. You can delete resources one by one or just delete the resource group to get rid of the entire set.

## Next steps

Advance to the next article to learn how to create...
> [!div class="nextstepaction"]
> [Next steps](storage-quickstart-queues-portal.md)
