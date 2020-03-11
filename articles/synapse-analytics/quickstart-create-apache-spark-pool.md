---
title: Quickstart create a Spark pool 
description: Create a new Spark pool for an Azure Synapse Analytics workspace by following the steps in this guide. 
services: synapse-analytics 
author: malvenko 
ms.service: synapse-analytics 
ms.topic: quickstart 
ms.subservice:
ms.date: 03/15/2020 
ms.author: josels 
ms.reviewer: jrasnick, carlrab
---

# Quickstart: Create a new Spark pool

Synapse Analytics offers various analytics engines to help you ingest, transform, model, analyze, and serve your data. A Spark pool offers open-source big data compute capabilities. After creating a Spark pool in your Synapse workspace, data can be loaded, modeled, processed, and served to obtain insights.

In this quickstart, you learn how to use the Azure portal to create a Spark pool in a Synapse workspace.

> [!IMPORTANT]
> Billing for Spark instances is prorated per minute, whether you are using them or not. Be sure to shutdown your Spark instance after you have finished using it, or set a short timeout. For more information, see the **Clean up resources** section of this article.

If you don't have an Azure subscription, [create a free account before you begin](https:/azure.microsoft.com/free/).

## Prerequisites

- Azure subscription - [create one for free](https:/azure.microsoft.com/free/)
- [Synapse Analytics workspace](quickstart-create-workspace.md)

## Sign in to the Azure portal

Sign in to the [Azure portal](https:/portal.azure.com/)

## Create new Spark pool

1. In the Synapse workspace where you want to create the Spark pool, click **New Spark pool**.
![Overview of Synapse workspace with a red box around the command to create a new Spark pool](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Enter the following details in the **Basics** tab:

    |Setting | Suggested value | Description |
    | :------ | :-------------- | :---------- |
    | **Spark pool name** | A valid pool name | This is the name that the Spark pool will have. |
    | **Node size** | Small (4 vCPU / 32 GB) | Set this to the smallest size to reduce costs for this quickstart |
    | **Autoscale** | Enabled | Leave this default setting |
    | **Number of nodes** | 3 - 40 | Leave this default setting |
    ||||

    ![Spark pool create flow - basics tab.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Note that there are specific limitations for the names that Spark pools can use. Names must contain letters or numbers only, must be 15 or less characters, must start with a letter, not contain reserved words, and be unique in the workspace.

3. Click **Next: additional settings** and review the default settings. Do not modify any default settings.
![Spark pool create flow - additional settings tab.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Click **Next: tags**. Do not add any tags.
![Spark pool create flow - additional settings tab.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Click **Review + create**.

6. Make sure that the details look correct based on what was previously entered, and click **Create**.
![Spark pool create flow - review settings tab.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. At this point, the resource provisioning flow will start, indicating once it's complete
 ![Spark pool create flow - resource provisioning.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. After the provisioning completes, navigating back to the workspace will show a new entry for the newly created Spark pool.
 ![Spark pool create flow - resource provisioning.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. At this point, there are no resources running, no charges for Spark, you have created metadata about the Spark instances you want to create.

## Clean up resources

Follow the steps below to delete the Spark pool from the workspace.
> [!WARNING]
> Deleting a Spark pool will remove the analytics engine from the workspace. It will no longer be possible to connect to the pool, and all queries, pipelines, and notebooks that use this Spark pool will no longer work.

If you want to delete the Spark pool, do the following:

1. Navigate to the Spark pools blade in the workspace.
2. Select the Apache pool to be deleted (in this case, **contosospark**)
3. Press **delete**.
 ![Listing of Spark pools, with the recently created pool selected.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Confirm the deletion, and press **Delete** button.
 ![Confirmation dialog to delete the selected Spark pool.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. When the process completes successfully, the Spark pool will no longer be listed in the workspace resources.

Once the SQL pool is created, it is available in the workspace for loading data, processing streams, reading from the lake, and so.

## Next steps

- See [Quickstart: Create a Spark pool in Synapse Studio using web tools](spark/apache-spark-notebook-create-spark-use-sql.md).
- See [Quickstart: Create a Synapse SQL pool using the Azure portal](quickstart-create-sql-pool.md).
