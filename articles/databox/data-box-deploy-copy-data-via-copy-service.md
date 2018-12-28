---
title: Copy data to  your Microsoft Azure Data Box via SMB| Microsoft Docs
description: Learn how to copy data to your Azure Data Box via Data copy service
services: databox
author: alkohli

ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: alkohli
#Customer intent: As an IT admin, I need to be able to copy data to Data Box to upload on-premises data from my server onto Azure.
---
# Tutorial: Use Data copy service to copy data to Azure Data Box

This tutorial describes how to ingest data using the data copy service without the need of an intermediate host. The data copy service is useful in the network-attached storage (NAS) environments where the intermediate hosts may not be available. The data copy service runs locally on the Data Box, connects to your NAS device via SMB, and copies data to Data Box.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Copy data to Data Box
> * Prepare to ship Data Box.

## Prerequisites

Before you begin, make sure that:

1. You've completed the [Tutorial: Set up Azure Data Box](data-box-deploy-set-up.md).
2. You've received your Data Box and the order status in the portal is **Delivered**.
3. You have the credentials of the source NAS device that you connect to for data copy.
4. You are connected to a high-speed network. We strongly recommend that you have at least one 10-GbE connection. If a 10-GbE connection isn't available, use a 1-GbE data link but the copy speeds will be impacted.

## Copy data to Data Box

Once you're connected to the NAS, the next step is to copy data. Before you begin the data copy, review the following considerations:

- While copying data, make sure that the data size conforms to the size limits described in the [Azure storage and Data Box limits](data-box-limits.md).
- If data, which is being uploaded by Data Box, is concurrently uploaded by other applications outside of Data Box, then this could result in upload job failures and data corruption.

To copy data using data copy service, you need to create a job. Follow these steps to create a job to copy data.

1. In the local web UI of your Data Box, go to **Manage > Copy data**.
2. In the Copy data page, click **Configure and start**.

    ![Click configure and start on copy data page](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

3. In the **Configure and start** dialog box, provide the following inputs.
    
    |Field                          |Value    |
    |-------------------------------|---------|
    |Job name                       |A unique name for the job.         |
    |Source location                |Provide the SMB path to the data source in the format: `\\<DeviceIPAddress>\<ShareName>`.        |
    |Username                       |Username to access the data source. Source credentials are stored encrypted for the entire lifetime of the job.        |
    |Password                       |Password to access the data source. Source credentials are stored encrypted for the entire lifetime of the job.          |
    |Destination storage account    |Select the target storage account to upload data to from the dropdown list.         |
    |Destination storage type       |Select the target storage type from block blob, page blob, or Azure Files.        |
    |Destination container/share    |Enter the name of the container to upload data in your destination storage account. The name can be of form share name or container name. For example, `myshare` or `mycontainer`. You can also enter in the folrmat `sharename\virtual_directoryname` or `containername\virtual_dir_name` in cloud.        |
    |Copy files matching pattern    | Enter file name matching pattern in the following two ways.<br><li>**Use wildcard expressions** Only `“*”` and `“?”` are supported in wildcard expressions. For example, this expression ``“*.vhd”`` will match all the files that have .vhd extension. Similarly, `“*.dl?”` will match all the files whose extension is either `.dl` or `.dll`. Also, `“*foo”` will match all the files whose names end with `“foo”`.<br>You can directly enter wildcard expression enclosed within double quotes in the field. <br>By default, value entered in the field is treated as wildcard expression.</li><li>**Use regular expressions** - POSIX-based regular expressions are supported. For example, a regular expression `“.*\.vhd”` will match all the files that have `.vhd` extension.<br>Enter regular expression as `regex(<pattern>)` where `<pattern>` is a regular expression enclosed within double quotes. |
    |File optimization              |When enabled, the files are packed at the ingest. This speeds up the data copy for small files.        |
 
4. Click **Configure and start**. The inputs are validated and if the validation succeeds, then a job is started. It takes a few minutes for the job to start.

    ![Start a job as specified in Configure and start dialog box](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. A job with the specified settings is created. Select the checkbox and then you can pause and resume, cancel, or restart a job.

    ![Manage a job on copy data page](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - You can pause this job if it is impacting the NAS resources during the peak hours and resume it later during the off peak hours.

        ![Pause a job](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

          
    
    - You can cancel a job at any time. 
        ![Cancel a job](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        A confirmation is required when you cancel a job. 

        ![Confirm job cancellation](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        If you decide to cancel a job, the data that is already copied is not cleared. To wipe out any data that you have copied on your Data Box, reset the device.

    - You can restart a job if it has failed abruptly due to a transient error such as a network glitch. You can't restart a job if it has reached a terminal state such as completed successfully or completed with errors. The failures could be due to file naming or file size issues. These errors are logged but the job can't be restarted once it has completed.

        ![Restart a failed job](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)
    
    - In this release, you cannot delete a job.
    
    - You can create unlimited jobs but a maximum of 25 jobs can run in parallel at any given time.  
    
    >[!NOTE]
    > If you have any errors during the copy process, download the error logs for troubleshooting. These are available in the **Error log** column on the **Copy data** page.

To ensure data integrity, checksum is computed inline as the data is copied. Once the copy is complete, verify the used space and the free space on your device.
    
   ![Verify free and used space on dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## Prepare to ship

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]


## Next steps

In this tutorial, you learned about Azure Data Box topics such as:

> [!div class="checklist"]
> * Copy data to Data Box
> * Prepare to ship Data Box

Advance to the next tutorial to learn how to ship your Data Box back to Microsoft.

> [!div class="nextstepaction"]
> [Ship your Azure Data Box to Microsoft](./data-box-deploy-picked-up.md)

