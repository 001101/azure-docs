---
title: Azure Quickstart - Deep learning training with Batch AI - Azure CLI | Microsoft Docs
description: Quickly learn to run a deep learning training job on a single GPU with Batch AI using the Azure CLI
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn

ms.assetid:
ms.custom:
ms.service: batch-ai
ms.workload:
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 06/08/2017
ms.author: Alexander.Yukhanov
---

# Quickstart: Run your first deep learning training job using the Azure CLI

The Azure CLI is used to create and manage Azure resources from the command line or in scripts. This quickstart shows how to use the Azure CLI to create the smallest Batch AI cluster, consisting of one GPU node. Then, run an example deep learning training job on the cluster using Horovod, which is a distributed TensorFlow framework. The job runs in a Docker container to train a convolutional neural network on the MNIST database of handwritten
digits. After completing this quickstart, you will understand the key concepts of using Batch AI for training a deep learning model, and be ready to try training jobs at larger scale.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this quickstart requires that you are running the Azure CLI version 2.0.20 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0](/cli/azure/install-azure-cli). 

## Create a resource group

Create a resource group with the [az group create](/cli/azure/group#az-group-create) command. An Azure resource group is a logical container into which Azure resources are deployed and managed. 

The following example creates a resource group named *myResourceGroup* in the *eastus* location. Be sure to choose a location such as East US in which the Batch AI service is available.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus
```

# Create a single GPU cluster

First, use the [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) command to create a Batch AI *workspace*. You need a workspace to organize your Batch AI clusters and other resources.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup 
```

As a basic example, the following [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) command creates a single-node GPU cluster named *mycluster* using the NC6 virtual machine size, which contains one NVIDIA Tesla K80 GPU. This cluster runs a default Ubuntu Server image designed to host container-based applications. This example includes the `--use-auto-storage` option to create and configure a storage account, and mount a file share and storage container in that account on each node. This command adds a user account named *azureuser*, and generates SSH keys if they don't already exist in the default key location (*~/.ssh*). 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --use-auto-storage \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

The cluster creation command runs quickly, and returns command output showing the cluster settings. It takes a few minutes to allocate and start the node. To see the status of the cluster, run the [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) command. 

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

Initially, output is similar to the following:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  steady        0          0            1          0           0

```
Continue the following steps to create a training job while the pool state is changing. The cluster is ready to run the job when the state is `steady` and the node is `idle`.

## Configure storage for script and output files

When Batch AI automatically created the storage account, it also created an SMB file share named `batchaishare`. The rest of this quickstart uses this file share to store both the training script and the output of the traning job.

To make it easier to set up the auto-storage account using the Azure CLI commands, first set environment variables to contain the storage account credentials. Note that Batch AI creates the storage account automatically in the *batchaiautostorage* resource group, which is used in the command to export the storage key.

```bash
export AZURE_STORAGE_ACCOUNT=$(az batchai cluster show --name mycluster --workspace myworkspace --resource-group myResourceGroup --query 'nodeSetup.mountVolumes.azureFileShares[0].accountName' | sed s/\"//g)

export AZURE_STORAGE_KEY=$(az storage account keys list --account-name $AZURE_STORAGE_ACCOUNT --resource-group batchaiautostorage --query [0].value)
```

Run the [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) command to reate a folder named `horovod_samples` in the share, and a folder named `logs` for the job output:

```azurecli-interactive
az storage directory create \
    --share-name batchaishare \
    --name horovod_samples

az storage directory create \
    --share-name batchaishare \
    --name logs
```

### Deploy training script to storage

Create a local working folder, and download the [tensorflow_mnist.py](https://raw.githubusercontent.com/uber/horovod/v0.9.10/examples/tensorflow_mnist.py) sample script into the folder.

```bash
wget https://raw.githubusercontent.com/uber/horovod/v0.9.10/examples/tensorflow_mnist.py
```

Upload the script to your storage account using the [az storage file upload](/cli/azure/storage/file#az-storage-file-upload) command.

```azurecli-interactive
az storage file upload \
    --share-name batchaishare \
    --path horovod_samples \
    --source tensorflow_mnist.py
```


## Submit training job

An experiment is a logical container for related Batch AI jobs. First, create an experiment in your workspace by using the [az batchai experiment create](/cli/azure/batchai/experiment#az-batchai-experiment-create) command:


```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

In your working directory, create a training job configuration file `job.json` with the following content. You will use pass this configuration file when you submit the training job. Update with the name of the storage account (value of the $AZURE_STORAGE_ACCOUNT variable).

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/horovod_samples/tensorflow_mnist.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ]
        },
        "jobPreparation": {
          "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

This configuration file specifies:

* `nodeCount` - number of nodes required by the job (1 for this quickstart);
* `cntkSettings` - specifies the path of the training script and command-line arguments. Command-line arguments include
path to training data and the destination path for storing generated models. `AZ_BATCHAI_OUTPUT_MODEL`
is an environment variable set by Batch AI based on output directory configuration (see below);
* `stdOutErrPathPrefix` - path where Batch AI will create directories containing job's output and logs;
* `outputDirectories` - collection of output directories to be created by Batch AI. For each directory,
Batch AI creates an environment variable with name `AZ_BATCHAI_OUTPUT_<id>`, where `<id>` is the directory
identifier;
* `mountVolumes` - list of filesystems to be mounted during the job execution. The filesystems are mounted under
`AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` is an environment variable set by Batch AI;
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` tells that the storage account name will be specified during the job submission
via --storage-account-name parameter or `AZURE_BATCHAI_STORAGE_ACCOUNT` environment variable on your computer.

## Submit the Job

Use the following command to submit the job on the cluster:

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

Example output:
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# Monitor Job Execution

The training script is reporting the training progress in `stderr.txt` file inside of the standard output directory. You
can monitor the progress using the following command:

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

Example output:
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

		Built time: Jan 31 2018 15:03:41
		Last modified date: Tue Jan 30 03:26:13 2018
		Build type: release
		Build target: GPU
		With 1bit-SGD: no
		With ASGD: yes
		Math lib: mkl
		CUDA version: 9.0.0
		CUDNN version: 7.0.4
		Build Branch: HEAD
		Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
		MPI distribution: Open MPI
		MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

The streaming is stopped when the job is completed (succeeded or failed).

# Inspect Generated Model Files

The job stores the generated model files in the output directory with `id` attribute equals to `MODEL`, you can list
model files and get download URLs using the following command:

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
```

Example output:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

Alternatively, you can use the Portal or Azure Storage Explorer to inspect the generated files. To distinguish output
from the different jobs, Batch AI creates a unique folder structure for each of them. You can find the path to the
folder containing the output using `jobOutputDirectoryPathSegment` attribute of the submitted job:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

Example output:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# Delete Resources

Delete the resource group and all allocated resources with the following command:

```azurecli
az group delete -n batchai.quickstart -y
```
