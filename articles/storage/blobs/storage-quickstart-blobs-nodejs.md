---
title: Azure Quickstart - Transfer objects to/from Azure Blob storage using Node.js| Microsoft Docs
description: Quickly learn to transfer objects to/from Azure Blob storage using Node.js
services: storage
author: craigshoemaker
manager: jeconnoc

ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/21/2018
ms.author: cshoe
---

# Transfer objects to/from Azure Blob storage using Node.js

In this quickstart, you learn how to use Node.js to upload, download, and list block blobs in a container using Azure Blob storage.

## Prerequisites

To complete this quickstart, you need an [Azure subscription](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## Download the sample application

The [sample application](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)  in this quickstart is a simple Node.js console application. To begin, clone the repository to your machine using the following command:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

To open the application, look for the `storage-blobs-node-quickstart` folder and open it in your favorite code editing environment.

## Configure your storage connection string

Before running the application, you must provide the connection string for your storage account. The sample repository includes a file named `.env.example`. You can rename this file by removing the `.example` extension, which results in a file named `.env`. Inside the `.env` file, add your connection string value after the `AZURE_STORAGE_CONNECTION_STRING` key.

## Install required packages

In the application directory run `npm install` to install the required packages for the application.

```bash
npm install
```

## Run the sample
Now that the dependencies are installed, you can run the sample by passing commands to the script. For instance, to create a blob container you run the following command:

```bash
node index.js --command createContainer
```

Commands available include:

| Command | Description |
|---------|---------|
|`createContainer` | Creates a container named `test-container` (succeeds even if container already exists) |
|`upload`          | Uploads the `example.txt` file to the `test-container` container |
|`download`        | Downloads the contents of the `example` blob to `example.downloaded.txt` |
|`delete`          | Deletes the `example` blob |
|`list`            | Lists the contents of `test-container` container to the console |


## Understanding the sample code
This code sample uses a few modules to interface with the file system and the command line. 

```javascript
require('dotenv').config();
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

The purpose of the modules is as follows: 

- `dotenv` loads environment variables defined in a file named `.env` into the current execution context
- `path` is required in order to determine the absolute file path of the file to upload to blob storage
- `yargs` exposes a simple interface to access command-line arguments
- `azure-storage` is the [Azure Storage SDK](/nodejs/api/azure-storage) module for Node.js

Next, a series of variables are initialized:

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

The variables are set to the following values:

- `blobService` is set to a new instance of the Azure Blob service
- `containerName` is set the name of the container
- `sourceFilePath` is set to the absolute path of the file to upload
- `blobName` is created by taking the file name and removing the file extension

In the following implementation, each of the `blobService` functions is wrapped in a `Promise`, which allows access to JavaScript's `async` function and `await` operator to streamline the callback nature of the [Azure Storage API](/nodejs/api/azure-storage/blobservice). When a successful response returns for each function, the `Promise` resolves with relevant data along with a message specific to the action.

### Create a blob container

The `createContainer` function calls [`createContainerIfNotExists`](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) and sets the appropriate access level for the blob.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

The second parameter (`options`) for `createContainerIfNotExists` accepts a value for [`publicAccessLevel`](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists). The value `blob` for `publicAccessLevel` specifies that specific blob data is exposed to the public. This setting is in contrast to `container` level access, which grants the ability to list the contents of the container.

The use of `createContainerIfNotExists` allows the application to run the `createContainer` command multiple times without returning errors when the container already exists. In a production environment, you often only call `createContainerIfNotExists` once as the same container is used throughout the application. In these cases, you can create the container ahead of time through the portal or via the Azure CLI.

### Upload a blob to the container

The `upload` function uses the [`createBlockBlobFromLocalFile`](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) function to upload and write or overwrite a file from the file system into blob storage. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
In context of the sample application, the file named `example.txt` is uploaded to a blob named `example` inside a container named `test-container`. Other approaches available to upload content into blobs include working with [text](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) and [streams](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream).

To verify the file is uploaded to your blob storage, you can use the [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) to view the data in your account.

### List the blobs in a container

The `list` function calls the [`listBlobsSegmented`](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) method to return a list of blob metadata in a container. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

Calling `listBlobsSegmented` returns blob metadata as an array of [`BlobResult`](/nodejs/api/azure-storage/blobresult) instances. Results are returned in 5,000 increment batches (segments). If there are more than 5,000 blobs in a container, then the results include a value for `continuationToken`. To list subsequent segments from the blob container, you can pass the continuation token back into `listBlobSegmented` as the second argument.

### Download a blob from the container

The `download` function uses [`getBlobToLocalFile`](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) to download the contents of the blob to the given absolute file path.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
The implementation shown here changes the source file path to append `.downloaded.txt` to the file name. In real-world contexts, you can change the location as well as the file name when selecting a download destination.

### Delete blobs in the container

The `deleteBlock` function (aliased as the `delete` console command) calls the `deleteBlobIfExists`(/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) function. As the name implies, this function does not error out if the blob is already deleted when the command is dispatched.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### Calling code

To expose the functions implemented to the command line, each of the functions is mapped to an object literal.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list
};
```

With `_module` now in place, it is available to be interrogated for known commands.

```javascript
const commandExists = () => {
    const cmd = args.command;
    const exists = !!_module[cmd];

    if(!exists) {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }

    return exists;
};
```

If a given command does not exist, then `_module`'s properties are rendered to the console to as help text to the user. 

The function `executeCommand` is an `await` function, which calls the given command using the `await` operator and logs any messages to data to the console.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if(response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Finally, the executing code first calls `commandExists` to verify a known command is passed into the script. If an existing command is selected, then the command is run and any errors are logged to the console.

```javascript
try {
    console.log(`Executing '${args.command}'...`);

    if(commandExists()){
        executeCommand();
    }
} catch(e) {
    console.log(e);
}
```

## Next steps

This quickstart demonstrates how to transfer a file between a local disk and Azure Blob storage using Node.js. To learn more about working with Blob storage, continue to the Blob storage How-to.

> [!div class="nextstepaction"]
> [Blob Storage Operations How-To](storage-nodejs-how-to-use-blob-storage.md)

For the Node.js reference for Azure Storage, see [azure-storage package](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).