---
title: Create and manage directories in Azure Storage by using Java
description: Use the Azure Storage libraries for Java to create and manage directories in Azure Blob storage accounts that have a hierarchical namespace.
services: storage
author: normesta
ms.service: storage
ms.date: 06/28/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
---

# Create and manage directories in Azure Storage by using Java

This article shows you how to use Java to manage directories in storage accounts that have a hierarchical namespace. 

## Connect to the storage account 

First, parse the connection string by calling the [parse](https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account.parse?view=azure-java-legacy) method of a [CloudStorageAccount](https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account?view=azure-java-legacy) object. 

Then, create an object that represents Blob storage in your storage account by calling the [createCloudBlobClient](https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account.createcloudblobclient?view=azure-java-legacy) method.

```java

CloudStorageAccount storageAccount = CloudStorageAccount.parse("<connection-string>");

cloudBlobClient = storageAccount.createCloudBlobClient();

```

Replace the `<connection-string>` placeholder value with the connection string of your storage account. 


## Create a directory

Create a directory reference by calling the **getDirectoryReference** method.

Create a directory by using the **create** method of a **CloudBlobDirectory** object. 

This example adds a directory named `my-directory` to a container and then adds a sub-directory named `my-subdirectory` to the directory named `my-directory`. 

```java
static void CreateDirectory(CloudBlobClient cloudBlobClient, String containerName)
throws URISyntaxException, StorageException{

    CloudBlobContainer cloudBlobContainer =
    cloudBlobClient.getContainerReference(containerName);

    if (cloudBlobContainer != null){

        CloudBlobDirectory cloudBlobDirectory =
        cloudBlobContainer.getDirectoryReference("my-directory");

        cloudBlobDirectory.create();

        cloudBlobDirectory.getDirectoryReference("my-subdirectory").create();
    }
}
```

## Rename a directory

Need explanation here.

```java
static void RenameDirectory(CloudBlobClient cloudBlobClient, String containerName)
    throws URISyntaxException, StorageException {

    CloudBlobContainer cloudBlobContainer =
        cloudBlobClient.getContainerReference(containerName);

    if (cloudBlobContainer != null){
        CloudBlobDirectory cloudBlobDirectory =
            cloudBlobContainer.getDirectoryReference("my-directory-2/my-directory");

        if (cloudBlobDirectory != null){
            // Need snippet here. Question pending. 

        }
    }

} 
```

## Move a directory

Need explanation here.

```java
static void MoveDirectory(CloudBlobClient cloudBlobClient, String containerName)
throws URISyntaxException, StorageException{

    CloudBlobContainer cloudBlobContainer =
        cloudBlobClient.getContainerReference(containerName);

    if (cloudBlobContainer != null){

        CloudBlobDirectory cloudBlobDirectory =
            cloudBlobContainer.getDirectoryReference("my-directory");

        if (cloudBlobDirectory != null){

            CloudBlobDirectory cloudBlobDestinationDirectory =
                cloudBlobContainer.getDirectoryReference("my-directory-2");

            if (cloudBlobDestinationDirectory != null){

            // Need snippet here. Question pending.

            }

        }
   }

}
```



## Delete a directory

Delete a directory by calling the **delete** method of a **CloudBlobDirectory** object.

This example deletes a directory named `my-directory`. 

```java
static void DeleteDirectory(CloudBlobClient cloudBlobClient, String containerName) 
throws URISyntaxException, StorageException{

    CloudBlobContainer cloudBlobContainer =
    cloudBlobClient.getContainerReference(containerName);

    if (cloudBlobContainer != null){

        CloudBlobDirectory cloudBlobDirectory =
            cloudBlobContainer.getDirectoryReference("my-directory");

        if (cloudBlobDirectory != null){

            cloudBlobDirectory.delete(true);    
        }
    }    
}
```

## Upload a file to a directory

First, create a blob reference in the target directory by calling the **getBlockBlobReference** method of a **CloudBlobDirectory** object. This returns a **CloudBlockBlob** object. Upload a file by calling the **uploadFromFile** method of a **CloudBlockBlob** object.

This example uploads a file to a directory named `my-directory`

```java
static void UploadFilesToDirectory(CloudBlobClient cloudBlobClient, 
String sourceFilePath, String containerName, String blobName)
throws URISyntaxException, StorageException, IOException{
    
    CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.getContainerReference(containerName);

    if (cloudBlobContainer != null){
       
        CloudBlobDirectory cloudBlobDirectory =
        cloudBlobContainer.getDirectoryReference("my-directory");

        if (cloudBlobDirectory != null){
            
            CloudBlockBlob cloudBlockBlob = 
            cloudBlobDirectory.getBlockBlobReference(blobName);

            if (cloudBlockBlob != null){
                
                cloudBlockBlob.uploadFromFile(sourceFilePath);
            }            
         }  
    }
}
```

## Download a file from a directory

First, create a blob reference in the source directory by calling the **getBlockBlobReference** method of a **CloudBlobDirectory** object. This returns a **CloudBlockBlob** object. Download that blob by calling the **downloadToFileAsync** method of a **CloudBlockBlob** object.

```java
static void GetFileFromDirectory(CloudBlobClient cloudBlobClient, 
String containerName, String blobName, String destinationFile)
throws URISyntaxException, StorageException, IOException{
    
    CloudBlobContainer cloudBlobContainer = 
         cloudBlobClient.getContainerReference(containerName);

    if (cloudBlobContainer != null){
        
        CloudBlobDirectory cloudBlobDirectory =
            cloudBlobContainer.getDirectoryReference("my-directory");

        if (cloudBlobDirectory != null){
            
            CloudBlockBlob cloudBlockBlob = 
            cloudBlobDirectory.getBlockBlobReference(blobName);

            if (cloudBlobDirectory != null){
                cloudBlockBlob.downloadToFile(destinationFile);
            }
        }
    }
}
```

## List the contents of a directory

To list containers in your storage account, call the **listBlobsSegmented** of a **CloudBlobDirectory** object.

This example asynchronously lists the contents of a directory by calling the **ListBlobsSegmentedAsync** method of a a **CloudBlobDirectory** object.

This example uses the continuation token to get the next segment of result.

```java
static void ListDirectoryContents(CloudBlobClient cloudBlobClient, String containerName)
throws URISyntaxException, StorageException{
    
    CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.getContainerReference(containerName);

    ResultContinuation blobContinuationToken = null;

    if (cloudBlobContainer != null){

        CloudBlobDirectory cloudBlobDirectory =
        cloudBlobContainer.getDirectoryReference("my-directory");

        if (cloudBlobDirectory != null){

            ResultSegment<ListBlobItem> resultSegment = null;

            do
            {
                resultSegment = cloudBlobDirectory.listBlobsSegmented();
    
                blobContinuationToken = resultSegment.getContinuationToken();
        
                for (ListBlobItem item :resultSegment.getResults()){
                    System.out.println(item.getUri());
                }
            } while (blobContinuationToken != null); 
        }     
    } 
}
```

## Next steps

Explore more APIs in the [com.microsoft.azure.storage.blob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-preview) namespace of the [Azure Storage libraries for Java](https://docs.microsoft.com/java/api/overview/azure/storage?view=azure-java-preview) docs.