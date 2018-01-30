---
title:  Azure Quickstart - Transfer objects to/from Azure Blob storage using PHP | Microsoft Docs 
description: Quickly learn to transfer objects to/from Azure Blob storage using PHP
services: storage
author: roygara
manager: jeconnoc

ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: quickstart
ms.date: 1/10/2018
ms.author: v-rogara
---

#  Transfer objects to/from Azure Blob storage using PHP
In this quickstart, you learn how to use PHP to upload, download, and list block blobs in a container in Azure Blob storage. 

## Prerequisites

To complete this quickstart: 
* Install [PHP](http://php.net/downloads.php)
* Install [The Azure SDK for PHP](../../php-download-sdk.md)


If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## Download the sample application
The [sample application](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) used in this quickstart is a basic PHP application.  

Use [git](https://git-scm.com/) to download a copy of the application to your development environment. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

This command clones the repository to your local git folder. To open the PHP sample application, look for the storage-blobs-php-quickstart folder, and open the example file.  

## Configure your storage connection string
In the application, you must provide the connection string for your storage account. It is recommended to store this connection string within an environment variable on the local machine running the application. Follow one of the examples below depending on your Operating System to create the environment variable.  Replace \<yourconnectionstring\> with your actual connection string.

### Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

### Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## Run the sample
This sample creates a test file in the 'Documents' folder. The sample program uploads the test file to Blob storage, lists the blobs in the container, and downloads the file with a new name. 

Run the sample. The following output is an example of the output returned when running the application:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
When you press the button displayed, the sample program deletes the storage container and the files. Before you continue, check your server's folder for the two files. You can open them and see they are identical.

You can also use a tool such as the [Azure Storage Explorer](http://storageexplorer.com) to view the files in Blob storage. Azure Storage Explorer is a free cross-platform tool that allows you to access your storage account information. 

After you've verified the files, hit any key to finish the demo and delete the test files. Now that you know what the sample does, open the example.rb file to look at the code. 

## Understand the sample code

Next, we walk through the sample code so that you can understand how it works.

### Get references to the storage objects
The first thing to do is create the references to the objects used to access and manage Blob storage. These objects build on each other, and each is used by the next one in the list.

* Create an instance of the Azure storage **Client** object to set up connection credentials. 
* Create the **BlobService** object that points to the Blob service in your storage account. 
* Create the **Container** object, which represents the container you are accessing. Containers are used to organize your blobs like you use folders on your computer to organize your files.

Once you have the Cloud Blob container, you can create the **Block** blob object that points to the specific blob in which you are interested, and perform operations such as upload, download, and copy.

> [!IMPORTANT]
> Container names must be lowercase. See [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) for more information about container and blob names.

In this section, you set up an instance of Azure storage client, instantiate the blob service object, create a new container, and then set permissions on the container so the blobs are public. The container is called **quickstartblobs**. 

```PHP
# Setup a specific instance of an Azure::Storage::Client
$connectionString = getenv('storageconnectionstring');

// Create blob client.
$blobClient = BlobRestProxy::createBlobService($connectionString);

# Create the BlobService that represents the Blob service for the storage account
$createContainerOptions = new CreateContainerOptions();

$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### Upload blobs to the container

Blob storage supports block blobs, append blobs, and page blobs. Block blobs are the most commonly used, and that is what is used in this quickstart.  

To upload a file to a blob, get the full path of the file by joining the directory name and the file name on your local drive. You can then upload the file to the specified path using the **create\_block\_blob()** method. 

The sample code creates a local file to be used for the upload and download, storing the file to be uploaded as **file\_path\_to\_file** and the name of the blob as **local\_file\_name**. The following example uploads the file to your container called **quickstartblobs**.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    $txt = "Hello Azure!";
    fwrite($myfile, $txt);
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

To perform a partial update of the content of a block blob, use the **create\_block\_list()** method. Block blobs can be as large as 4.7 TB, and can be anything from Excel spreadsheets to large video files. Page blobs are primarily used for the VHD files used to back IaaS VMs. Append blobs are used for logging, such as when you want to write to a file and then keep adding more information. Append blob should be used in a single writer model. Most objects stored in Blob storage are block blobs.

### List the blobs in a container

You can get a list of files in the container using the **list\_blobs()** method. The following code retrieves the list of blobs, then loops through them, showing the names of the blobs found in a container.  

```PHP
$blob_list = $blobClient->listBlobs($containerName);
    $blobs = $blob_list->getBlobs();
    echo "These are the blobs present in the container: ";
    foreach($blobs as $blob)
    {
        echo $blob->getName().": ".$blob->getUrl()."<br />";
    }
    echo "<br />";
```

### Download the blobs

Download blobs to your local disk using the **getBlob()** method. The following code downloads the blob uploaded in a previous section. "_DOWNLOADED" is added as a suffix to the blob name so you can see both files on local disk. 

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### Clean up resources
If you no longer need the blobs uploaded in this quickstart, you can delete the entire container using the **deleteContainer()** method. If the files created are no longer needed, you use the **deleteBlob()** method to delete the files.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## Next steps
 
In this quickstart, you learned how to transfer files between a local disk and Azure blob storage using Ruby. To learn more about working with blob storage, continue to the Blob storage How-to.

> [!div class="nextstepaction"]
> [Blob Storage Operations How-To](./storage-php-how-to-use-blobs.md)


For more information about the Storage Explorer and Blobs, see [Manage Azure Blob storage resources with Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
