---
title: Distributed training using Horovod - Azure Batch AI | Microsoft Docs
description: Tutorial - How to train a distributed model with Horovod using the Azure Batch AI service and Azure CLI.
services: batch-ai
author: johnwu10
manager: jeconnoc

ms.service: batch-ai
ms.topic: tutorial
ms.date: 08/07/2018
ms.author: danlep
ms.custom: mvc
---

# Tutorial: Train a distributed model with Horovod 

Batch AI is a managed service for training machine learning models at scale on clusters of Azure GPUs. This tutorial demonstrates how to train a distributed model by running it in parallel across multiple nodes in a Batch AI cluster. A common Batch AI workflow is introduced along with how to interact with Batch AI resources through the Azure CLI. Topics covered include:

> [!div class="checklist"]
> * Set up a Batch AI workspace, experiment, and cluster
> * Set up an Azure file share for input and output
> * Parallelize a model using Horovod
> * Submit a training job
> * Monitor the job
> * Retrieve the training results

[Horovod](https://github.com/uber/horovod) is a distributed training framework for Tensorflow, Keras, and PyTorch, and is used for this tutorial. Horovod was chosen because it enables you to convert a training script designed to run on a single GPU to one that can run efficiently on a distributed system using just a few lines of code. 

An example Keras object detection model is modified to run in parallel with Horovod. The model trains on the [CIFAR-10 dataset](https://www.cs.toronto.edu/~kriz/cifar.html)of images. The training job runs on a cluster containing 24 cores and 4 GPUs, and takes approximately 60 minutes to complete.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
before you begin.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this tutorial requires that you are running the Azure CLI version 2.0.38 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli). 

## Create a resource group

A resource group must be created in order to deploy resources in Batch AI. In this example, create a resource group named `batchai.horovod` in the `eastus` region. The available regions can be found [here](https://azure.microsoft.com/global-infrastructure/services/).

Use the [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) command to create the resource:

```azurecli-interactive
az group create --name batchai.horovod --location eastus 
```

This resource group is used for the remainder of the tutorial to create the different Batch AI resources: workspace, experiment, cluster, and job. For an explanation of these resources, see [Overview of resources in Batch AI](resource-concepts.md) 

## Create a workspace

Create a single development workspace for this example using the [az batchai workspace create](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) command. The following command creates a workspace called `batchaidev` under the resource group created earlier.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

## Create an experiment

For this tutorial, create a single experiment to run the distributed job. The following [az batchai experiment create](https://docs.microsoft.com/en-us/cli/azure/batchai/experiment?view=azure-cli-latest#az-batchai-experiment-create) command creates an experiment called `cifar` under the workspace and resource group.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

The next few sections will consist of instructions for creating all the necessary resources in order to run the experiment.

## Provision a GPU cluster

The next step is to provision a GPU cluster that can be used to run the experiment. Batch AI provides a flexible range of options for customizing clusters towards specific needs. The full documentation for the different options can be found [here](https://docs.microsoft.com/en-us/cli/azure/batchai/cluster?view=azure-cli-latest). Here are some important options to consider both for functional and budgeting purposes when choosing configurations for the cluster:

* **VM size** - Azure contains many options for [GPU-enabled VMs](../virtual-machines/linux/sizes-gpu.md) which can be used for Batch AI clusters. For this experiment, a `Standard_NC6` machine is used, which contains one NVIDIA Tesla K80 GPU. This VM size is chosen because the NC-series machines are optimized for compute-intensive algorithms that are ideal for deep learning jobs like this one.

* **Priority** - Azure offers dedicated VMs in addition to a [low-priority VM](../batch/batch-low-pri-vms.md) option that allocates unutilized capacity of other VMs at significant cost savings in exchange for the possibility of VMs being pre-empted and interrupting your jobs. For this experiment, a `dedicated` option is selected.

* **Target** - The number of nodes that should be allocated can be defined at the creation of a cluster or an [auto-scale](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-auto-scale) option can be selected. For this experiment, the target cluster size is manually set to 4 nodes in order to demonstrate the distributed training performance.

* **VM image** - The VMs in the cluster can be provisioned with a default Ubuntu Server image or with a preconfigured Azure image, such as a [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md). For this experiment, a default Ubuntu Server image is used as it is designed to host container-based applications, such as Docker.

* **Storage** - Batch AI provides many flexible storage options for data depending on the specific needs. Azure Batch AI clusters offer an auto-storage option that automatically creates a file share and storage container in a designated storage account. It mounts this storage to each node of the created cluster, allowing files and data to be stored in a central location that can be accessed by all VMs. This tutorial utilizes this auto-storage option. For those who do not wish to use auto-storage, see the following [article](use-azure-storage.md) for other Azure storage options.

The following [az batchai cluster create](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) command creates a new cluster called `nc6cluster` with the above configurations under the workspace and resource group created earlier.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --name nc6cluster --vm-priority dedicated --workspace batchaidev --vm-size Standard_NC6 --target 4 --use-auto-storage --generate-ssh-keys
```
The `--use-auto-storage` option creates a storage account in a new or existing resource group named **batchaiautostorage**.
It also creates an Azure file share and blob storage container with the names **batchaishare** and **batchaicontainer**, respectively. They are mounted on each cluster node at $AZ_BATCHAI_MOUNT_ROOT/autoafs and $AZ_BATCHAI_MOUNT_ROOT/autobfs. 

The `--generate-ssh-keys` option generates private and public SSH keys if they don't already exist in the default key location. 

Documentation for further Batch AI cluster configurations can be found in the Azure CLI documentation link above. If other experiments have common compute requirements, the same cluster can be used for jobs across the whole workspace.

The [az batchai cluster show](https://docs.microsoft.com/en-us/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show) command can be used to confirm the successful creation of a cluster. It usually takes a few minutes for the cluster to be fully provisioned.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

## Access the auto-storage

During creation, a unique storage account name was generated which must be retrieved in order to access and modify the storage. The [az batchai cluster show](https://docs.microsoft.com/en-us/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show) command can be used again to perform this action. The following command queries the storage account name from the cluster.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

The output should be similar to the following. Substitute the name of the storage account for `<STORAGE ACCOUNT NAME>` in later commands in this tutorial. 

```json
{
  "storageAccountName": "baixxxxxxxxx"
}
```

The storage account name can be used to access the file share named `batchaishare`, which was automatically created as mentioned earlier. In practice, this same storage can be used across multiple jobs and experiments. Therefore, create a directory within the file share to store files related to this specific experiment in order to keep things organized. The following [az storage directory create](/cli/azure/storage/directory?view=azure-cli-latest#az-storage-directory-create) command creates a directory called `cifar`.

```azurecli-interactive
az storage directory create --name cifar --share-name batchaishare --account-name <STORAGE ACCOUNT NAME>
```

The next step is to prepare the actual training script, which you then upload to the newly created directory.

## Create the training script

For this experiment, the following [Python script](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py) will be modified with a few small changes in order to parallelize the model. Create a file named `cifar_cnn_distributed.py` with the content below. All changes that were made to the original source code are commented with a `HOROVOD` prefix.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

As shown in this example, it is easy to modify a model to enable distributed training using the Horovod framework. More information can be found at the [Horovod repo](https://github.com/uber/horovod) on how to use Horovod with other machine learning libraries.

Keep in mind that this script uses a relatively small model and dataset for demo purposes, so a distributed model will not necessarily show a substantial performance improvement. In order to truly see the power of distributed training, a much larger model, and dataset must be used.

## Upload the training script

Once the script is ready, the next step is to upload it to the file share directory that was created earlier. The following [az storage file upload](/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-upload) command uploads it to the proper location.

```azurecli-interactive
az storage file upload --path cifar --share-name batchaishare --source cifar_cnn_distributed.py --account-name <STORAGE ACCOUNT NAME>
```

## Submit the training job

After completing the previous steps, create a training job can then be created. In Batch AI, a `job.json` file ([see schema](https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json) is used to define the parameters on how to run a job. For the experiment, create a job configuration file called `job.json` with the following content.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_MOUNT_ROOT/autoafs/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_MOUNT_ROOT/autoafs/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/autoafs/cifar",
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Going through each of the properties:

* **nodeCount** - The number of nodes to dedicate for the job. In this experiment, the job will be run in parallel on `4` different nodes. 

* **horovodSettings** - The `pythonScriptFilePath` field defines the path to the Horovod script, which is located in the `cifar` directory created previously. The `commandLineArgs` field is the command-line arguments for running the script. For this experiment, the directory of where to save the model is the only required argument. `$AZ_BATCHAI_MOUNT_ROOT/autoafs` is the path where the auto-storage was mounted. 

* **stdOutErrPathPrefix** - The path to store the job outputs and logs, which for this experiment will be the same `cifar` directory. 

* **jobPreparation** - Any special instructions for preparing the environment for running the job. This experiment requires installation of the indicated MPI and Horovod packages.

* **containerSettings** - The settings on the type of container that the job should run on. This experiment uses a Docker container built with `tensorflow`.

Using the configuration, create the job using the [az batchai job create](/cli/azure/batchai/job?view=azure-cli-latest#az-batchai-job-create) command. The following command queues a new job called `cifar_distributed` using all the resources that have been set up to this point.

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name <STORAGE ACCOUNT NAME>
```

If the nodes are currently busy, the job may take a while before it actually starts running. Use the [az batchai job show](/cli/azure/batchai/job?view=azure-cli-latest#az-batchai-job-show) command to view the execution state of the job.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

## Visualize the distributed training

Once the job starts running, use the [az batchai cluster show](https://docs.microsoft.com/en-us/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show) command once again to query the status of the cluster nodes. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

The output should be similar to the following example, which shows all four in a running state. This result shows that all four nodes are currently being utilized in the distributed training.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## Monitor the job

While the job is running, use the [az batchai job file list](https://docs.microsoft.com/en-us/cli/azure/batchai/job/file?view=azure-cli-latest#az-batchai-job-file-list) command to list the output files for the job.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

For this specific experiment, the output should be similar to what is shown below. The overall output for the job is logged to `stdout.txt` while `stderr.txt` outputs any errors that occur in the main execution. The other files are `output, error, and job preparation` logs corresponding to each individual node.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

Use the [az batchai job file stream](https://docs.microsoft.com/en-us/cli/azure/batchai/job/file?view=azure-cli-latest#az-batchai-job-file-stream) command to stream the contents of the files. The following example streams the main output log.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

## Retrieve the results

The script that was executed performed 25 epochs through the dataset, which takes some time. If everything went well, the validation accuracy should be about 70-75% and the trained model should be saved to the file share storage at `cifar/saved_models/keras_cifar10_trained_model.h5`. This model can be downloaded locally using the [az storage file download](/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) command.

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name batchaishare --account-name <STORAGE ACCOUNT NAME> 
```

## Delete the resources

Once jobs are finished running, a best practice for saving compute costs is to downscale all clusters to `0 nodes` in order to not be charged for idle time. This action can be performed using the [az batchai cluster resize](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-resize) command. Use the same command with a different target to reallocate the nodes in the future.

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

If you don't plan to use the workspace in the future, delete the resource group  using the [az group delete](https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest#az-group-delete) command. Deleting a resource group deletes all resources that are part of that group.

```azurecli-interactive
az group delete --name batchai.horovod
```

Note, this command will not delete the auto-storage that was created with the cluster as that was created using a different resource group. To delete the auto-storage, use the same command to delete the `batchaiautostorage` resource group. If not deleted, future auto-storage options will use this existing storage account instead of creating a new one.

```azurecli-interactive
az group delete --name batchaiautostorage
```

## Next steps

In this tutorial, you learned about how to:

> [!div class="checklist"]
> * Set up a Batch AI workspace, experiment, and cluster
> * Set up an Azure file share for input and output
> * Parallelize a model using Horovod
> * Submit a training job
> * Monitor the job
> * Retrieve the training results

For more examples of using Batch AI, see the recipes on GitHub.

> [!div class="nextstepaction"]
> [Batch AI recipes](https://github.com/Azure/BatchAI/tree/master/recipes)
