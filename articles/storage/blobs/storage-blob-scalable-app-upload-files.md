---
title: Upload large amounts of random data in parallel to Azure Storage  | Microsoft Docs 
description: Build a scalable application using Azure blob storage
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: ''

ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/13/2017
ms.author: gwallace
ms.custom: mvc
---

# Upload large amounts of random data in parallel to Azure storage

This tutorial is part two of a series. This tutorial shows you deploy an application that uploads large amount of random data and download data with an Azure storage account. When you're finished, you have a console application running on a virtual machine that you upload and download large amounts of data to a storage account.

In part two of the series, you learn how to:

> [!div class="checklist"]
> * Configure the connection string
> * Build the application
> * Run the application
> * Validate the number of connections

Azure blob storage provides a scalable service for storing your data. To ensure your application is as performant as possible, an understanding of how blob storage works is recommended. Knowledge of the limits for Azure blobs is important, to learn more about these limits visit: [blob storage scalability targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets). [Partition naming](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47) is another important factor when designing a highly performing application using blobs. Azure storage uses a range-based partitioning scheme to scale and load balance. This configuration means that files with similar naming conventions or prefixes go to the same partition. This logic includes the name of the container that the files are being uploaded to. In this tutorial, you use files that have GUIDs for names as well as randomly generated content. They are then uploaded to five different containers with random names.

## Prerequisites

To complete this tutorial, you must have completed the previous Storage tutorial: [Create a virtual machine and storage account for a scalable application][previous-tutorial].

## Remote into your virtual machine

Use the following command on your local machine to create a remote desktop session with the virtual machine. Replace the IP address with the publicIPAddress of your virtual machine. When prompted, enter the credentials used when creating the virtual machine.

```
mstsc /v:<publicIpAddress>
```

## Configure the connection string

In the Azure portal, navigate to your storage account. Select **Access keys** under **Settings** in your storage account. Copy the **connection string** from the primary or secondary key. Log in to the virtual machine you created in the previous tutorial. Open a **Command Prompt** as an administrator and run the `setx` command with the `/m` switch, this saves a machine setting environment variable. The environment variable is not available until to reload the **Command Prompt**. Replace **\<storageConnectionString\>** in the following sample:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

When finished, open another **Command Prompt**, navigate to `D:\git\storage-dotnet-perf-scale-app` and type `dotnet build` to rebuild the application.

## Run the application

Open a `Command Prompt` and navigate to `D:\git\storage-dotnet-perf-scale-app`.

Type `dotnet run` to run the application. A command runs initially to populate your local package cache, to improve restore speed and enable offline access. This command takes up to a minute to complete and only happens once.

```
dotnet run
```

The application creates five random named containers and begins uploading the files in the staging directory to the storage account. The application sets the minimum threads to 100 and the [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) to 100 to ensure that a large number of concurrent connections are allowed when running the application.

In addition to setting the threading and connection limit settings, the [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) for the [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) method are configured to configure parallelism and disabling MD5 hash validation. The files are uploaded in 100-mb blocks, this provides better performance but can be costly if using a poorly performing network as the entire 100-mb block is retried.

|Property|Value|Description|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| The setting breaks the blob into blocks when uploading. For highest performance, this value should be 8 times the number of cores. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| This property disables checking the MD5 hash of the content uploaded. Disabling MD5 validation produces a faster transfer. |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| This property determines if an MD5 hash is calculated and stored   |
| [RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| 2-second backoff with 10 max retry |Determines the retry policy of requests. Connection failures are retried, in this example `ExponentialRetry` is configured with a 2-second backoff and a maximum retry count of 10 |

The `UploadFilesAsync` task is shown in the following example:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directiory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the semaphore class to define the number of threads to use in the application.
        Semaphore sem = new Semaphore(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string fileName in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            Random r = new Random((int)DateTime.Now.Ticks);
            string s = (r.Next() % 10000).ToString("X5");
            Console.WriteLine("Starting upload of {0} as {1} to container {2}.", fileName, s, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(s);
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            sem.WaitOne();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  Defined the BlobRequestionOptions on the upload.
            // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
            // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
            // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled, this imroves the upload speed.
            tasks.Add(blockBlob.UploadFromFileAsync(fileName, null, new BlobRequestOptions() { ParallelOperationThreadCount = 8, DisableContentMD5Validation = true, StoreBlobContentMD5 = false }, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchonous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

    time.Stop();

    Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

    Console.ReadLine();
}
```

The following example is a truncated application output running on a Windows system.

```
Created container https://mystorageaccount.blob.core.windows.net/jjahy
Created container https://mystorageaccount.blob.core.windows.net/aboan
Created container https://mystorageaccount.blob.core.windows.net/scdpz
Created container https://mystorageaccount.blob.core.windows.net/abbim
Created container https://mystorageaccount.blob.core.windows.net/vefmk
Iterating in directiory: D:\git\storage-dotnet-perf-scale-app\upload
Found 36 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt as 0187A to container jjahy.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt as 00E13 to container aboan.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt as 02121 to container scdpz.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt as 00589 to container abbim.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt as 01922 to container vefmk.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### Validate the connections

While the files are being uploaded, you can verify the number of concurrent connections to your storage account. Open a **Command Prompt** and type `netstat -a | find /c "blob:https"`. This shows the number of connections that are currently opened using `netstat`. The following example shows a similar output to what you see when running the tutorial yourself. As you can see from the example, 800 connections were open when uploading the random files to the storage account. By uploading in parallel block chunks, the amount of time required to transfer the contents is greatly reduced.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## Next steps

In part two of the series, you learned about uploading large amounts of random data to a storage account in parallel, such as how to:

> [!div class="checklist"]
> * Configure the connection string
> * Build the application
> * Run the application
> * Validate the number of connections

Advance to part three of the series to upload large amounts of data to a storage account using exponential retry and parallelism.

> [!div class="nextstepaction"]
> [Upload large amounts of large files in parallel to a storage account](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md