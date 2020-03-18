---
title: Transformation with Azure Databricks
description:  Learn how to use a solution template to transform data by using a Databricks notebook in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
---

# Transformation with Azure Databricks

In this tutorial, you create an end-to-end pipeline containing **Validation**, **Copy**, and **Notebook** activities in Data Factory.

- **Validation** ensures that your source dataset is ready for downstream consumption before you trigger the copy and analytics job.

- **Copy** duplicates the source dataset to the sink storage which is mounted as DBFS in the Databricks notebook. In this way, the dataset can be directly consumed by Spark.

- **Notebook** triggers the Databricks notebook that transforms the dataset. It also adds the dataset to a processed folder or SQL Data Warehouse.

For simplicity, the template in this tutorial doesn't create a scheduled trigger. You can add one if necessary.

![1](media/solution-template-Databricks-notebook/pipeline-example.png)

## Prerequisites

- A **blob storage account** with a container called `sinkdata` for use as **sink**

  Make note of the **storage account name**, **container name**, and **access key**. You'll need these values later in the template.

- An **Azure Databricks workspace**

## Import a notebook for Transformation

To import a **Transformation** notebook to your Databricks workspace:

1. Sign in to your Azure Databricks workspace, and then select **Import**.
       ![2](media/solution-template-Databricks-notebook/import-notebook.png)
   Your workspace path can be different from the one shown, but remember it for later.
1. Select **Import from: URL**. In the textbox, enter `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`

   ![3](media/solution-template-Databricks-notebook/import-from-url.png)

1. Now let's update the **Transformation** notebook with your storage connection information.

   In the imported notebook, go to **command 5** as shown in the following code snippet.

   - Replace `<storage name>`and `<access key>` with your own storage connection information.
   - Use the storage account with the `sinkdata` container.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Generate a **Databricks access token** for Data Factory to access Databricks.
   1. In your Databricks workspace, select your user profile icon in the upper right.
   1. Select **User Settings**.
    ![4](media/solution-template-Databricks-notebook/user-setting.png)
   1. Select **Generate New Token** under the **Access Tokens** tab.
   1. Select **Generate**.

    ![5](media/solution-template-Databricks-notebook/generate-new-token.png)

   **Save the access token** for later use in creating a Databricks linked service. The access token looks something like 'dapi32db32cbb4w6eee18b7d87e45exxxxxx'.

## How to use this template

1. Go to the **Transformation with Azure Databricks** template and create new linked services for following connections.

   ![Connections setting](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Source Blob Connection** – to access the source data.

       For this exercise, you can use the public blob storage that contains the source files. Reference following screenshot for configuration. Use the following **SAS URL** to connect to source storage (read-only access):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Destination Blob Connection** – to store the copied data.

       In the linked service, select your sink storage blob.

       ![7](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** – to connect to the Databricks cluster.

        Create a Databricks-linked service using the access key you generated earier. You may opt to select an *interactive cluster* if you have one. This example uses the *New job cluster* option.

        ![8](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Select **Use this template**. You'll see a pipeline created.

    ![Create a pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## Pipeline introduction and configuration

In the new pipeline, most settings are  configured automatically with default values. Review the configurations of your pipeline and make any necessary changes:

- A _Validation_ activity **Availability flag** is created for checking the source. The Dataset value should be set to   *SourceAvailabilityDataset* which was created earlier.

   ![12](media/solution-template-Databricks-notebook/validation-settings.png)

- A _Copy data_ activity **file-to-blob** is created for copying the dataset from the source to the sink. Check the source and sink tabs to change these settings.

  - Source tab
   ![13](media/solution-template-Databricks-notebook/copy-source-settings.png)

  - Sink tab
   ![14](media/solution-template-Databricks-notebook/copy-sink-settings.png)

\***
- A _Notebook_ activity **Transformation** is created with the linked service from earlier.
    ![16](media/solution-template-Databricks-notebook/notebook-activity.png)

   1. Select **Settings** tab. For *Notebook path*, the template defines a path by default. You may need to browse and select the correct notebook path uploaded in **Prerequisite** 2.

       ![17](media/solution-template-Databricks-notebook/notebook-settings.png)
    
     1. Check out the *Base Parameters* created as shown in the screenshot. They are to be passed to the Databricks notebook from Data Factory. 

         ![Base parameters](media/solution-template-Databricks-notebook/base-parameters.png)

1. **Pipeline Parameters** is defined as below.

    ![15](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Setting up datasets.
    1. **SourceAvailabilityDataset** is created to check if source data is available.

        ![9](media/solution-template-Databricks-notebook/source-availability-dataset.png)

    1. **SourceFilesDataset** - for copying the source data.

        ![10](media/solution-template-Databricks-notebook/source-file-dataset.png)

    1. **DestinationFilesDataset** – for copying into the sink/destination location.

        1. Linked service - *sinkBlob_LS* created in previous step.

        2. File path - *sinkdata/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Select **Debug** to run the pipeline. You can find link to Databricks logs for more detailed Spark logs.

    ![18](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    You can also verify the data file using storage explorer. (For correlating with Data Factory pipeline runs, this example appends the pipeline run ID from data factory to the output folder. This way you can track back the files generated via each run.)

    ![19](media/solution-template-Databricks-notebook/verify-data-files.png)

## Next steps

- [Introduction to Azure Data Factory](introduction.md)
