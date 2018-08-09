---
title: Copy data to  your Microsoft Azure Data Box| Microsoft Docs
description: Learn how to copy data to your Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''

ms.assetid: 
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/07/2018
ms.author: alkohli
---
# Tutorial: Copy data to Azure Data Box 

This tutorial describes how to copy data from your host computer using the local web UI and then prepare to ship Data Box.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Copy data to Data Box
> * Prepare to ship Data Box.

## Prerequisites

Before you begin, make sure that:

1. You have completed the [Tutorial: Set up Azure Data Box](data-box-deploy-set-up.md).
2. You have received your Data Box and the order status in the portal is **Delivered**.
3. You have a host computer that has the data that you want to copy over to Data Box. Your host computer must
    - Run a [Supported operating system](data-box-system-requirements.md).
    - Be connected to high speed network. We strongly recommend that you have at least one 10 GbE connection. If a 10 GbE connection is not available, a 1 GbE data link can be used but the copy speeds will be impacted. 

## Copy data

Your Data Box creates three shares for each associated storage account. The three shares store data from block blobs, page blobs, and Azure Files. For each share on the device you want to copy the data to, you need to get the corresponding share access credentials. 

Under block blob and page blob shares, first level entities are containers, and second level entities are blobs. Under shares for Azure Files, first level entities are shares.

Consider the following example. 

- Storage account: *Mystoracct*
- Share for block blob: *Mystoracct_Block/my-container/blob*
- Share for page blob: *Mystoracct_Page/my-container/blob*
- Share for file: *Mystoracct_AzFile/my-share*

> [!IMPORTANT]
> - Ensure that you copy the data to shares that correspond to the appropriate data format. For instance, copy the block blob data to the share for block blobs. If the data format does not match the appropriate share type, then at a later step, the data upload to Azure will fail.
> -  While copying data, ensure that the data size conforms to the size limits described in the [Azure storage and Data Box limits](data-box-limits.md). 
> - If data, which is being uploaded by Data Box, is concurrently uploaded by other applications outside of Data Box, then this could result in upload job failures and data corruption.

Depending on whether your Data Box is connected to a Windows Server host computer or to a Linux host, the steps to copy can be different.

### Using Windows Server host computer

#### Connect to an SMB share

Perform the following steps to connect and copy data from your computer to the Data Box.

1. The first step is to authenticate and start a copy session. Go to **Connect and copy**. Click **Get credentials** to get the access credentials for the shares associated with your storage account. 

    ![Get share credentials 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. In the Access share and copy data dialog box, copy the **Username** and the **Password** corresponding to the share. Click **OK**.<br>
    
    ![Get share credentials 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Access the shares associated with your storage account (Mystoracct in the following example). Use the `\\<IP of the device>\ShareName` path to access the shares. Depending upon your data format, connect to the shares (use the share name) at the following address: 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    To connect to the shares from your host computer, open a command window. At the command prompt, type:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Enter the password for the share when prompted. The following sample shows connecting to a share via the preceding command.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Press  Windows + R. In the **Run** window, specify the `\\<device IP address>`. Click **OK**. This opens File Explorer.
    
    ![Connect to share via File Explorer 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5. You should now be able to see the shares as folders. Create a folder for the files that you intend to copy (in this case templates). In this preview release, the folders may show the gray cross, this is not an error condition. The folders are flagged by the application to track the status.
    
    ![Connect to share via File Explorer 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

#### Copy data to an SMB share

 After you have connected to the SMB share, initiate a data copy. You can use any SMB compatible file copy tool such as Robocopy to copy your data. Multiple copy jobs can be initiated using Robocopy. Use the following command: 
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
    ```
 The attributes are described in the following table.
    
|Attribute  |Description  |
|---------|---------|
|/e     |Copies subdirectories including empty directories.         |
|/r:     |Specifies the number of retries on failed copies.         |
|/w:     |Specifies the wait time between retries, in seconds.         |
|/is     |Includes the same files.         |
|/nfl     |Specifies that file names are not to be logged.         |
|/ndl    |Specifies that directory names are not to be logged.        |
|/np     |Specifies that the progress of the copying operation (the number of files or directories copied so far) will not be displayed. Displaying the progress significantly lowers the performance.         |
|/MT     | Use multithreading, recommended 32 or 64 threads. This option not used with encrypted files. You may need to separate encrypted and unencrypted files. However, single threaded copy significantly lowers the performance.           |
|/fft     | Use to reduce the time stamp granularity for any file system.        |
|/b     | Copies files in Backup mode.        |
|/z    | Copies files in Restart mode, use this if the environment is unstable.       |
| /zb     | Uses Restart mode. If access is denied, this option uses Backup mode.         |
|/efsraw     | Copies all encrypted files in EFS raw mode. Use only with encrypted files.         |
|log+:<LogFile>| Appends the output to the existing log file.|    
 
 The following sample shows the output of the robocopy command to copy files to the Data Box.

        ```
        C:\Users>robocopy
            -------------------------------------------------------------------------------
           ROBOCOPY     ::     Robust File Copy for Windows
        -------------------------------------------------------------------------------
        
          Started : Thursday, March 8, 2018 2:34:53 PM
               Simple Usage :: ROBOCOPY source destination /MIR
        
                     source :: Source Directory (drive:\path or \\server\share\path).
                destination :: Destination Dir  (drive:\path or \\server\share\path).
                       /MIR :: Mirror a complete directory tree.
        
            For more usage information run ROBOCOPY /?    
        
        ****  /MIR can DELETE files as well as copy them !
        
        C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
        -------------------------------------------------------------------------------
           ROBOCOPY     ::     Robust File Copy for Windows
        -------------------------------------------------------------------------------
        
          Started : Thursday, March 8, 2018 2:34:58 PM
           Source : C:\Git\azure-docs-pr\contributor-guide\
             Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
        
            Files : *.*
        
          Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
        
        ------------------------------------------------------------------------------
        
        100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
        100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
        100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
        100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                    New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                    New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
        100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
        100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
        100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
        100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
        100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
        100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
        100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
        100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
        100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
        100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
        100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
        ------------------------------------------------------------------------------
        
                       Total    Copied   Skipped  Mismatch    FAILED    Extras
            Dirs :         1         1         1         0         0         0
           Files :        17        17         0         0         0         0
           Bytes :     3.9 k     3.9 k         0         0         0         0          
        C:\Users>
        ```
<br>

    For more information on Robocopy command, go to [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

7. Open the target folder to view and verify the copied files. If you have any errors during the copy process, download the error files for troubleshooting.
8. To ensure data integrity, checksum is computed inline as the data is copied. Once the copy is complete, verify the used space and the free space on your device. 
    
    ![Verify free and used space on dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

### Connect to and copy data from a Linux host computer

To copy data from a Linux host computer, you will need to configure the device to allow access to the NFS client. Perform the following steps to configure Data Box to allow access to NFS clients.

#### Connect to an NFS share

1. You need to supply the IP addresses of the allowed clients that can access the share.

2. Ensure that the Linux host has NFSv4 client installed. To install NFS client, use the following command:

    `sudo apt-get install nfs-common` 

    For more information, go to [Install NFSv4 client]().

3. Once the NFS client is installed, use the following command to mount the NFS share you created on your Edge device:

    `sudo mount <Edge device IP>:/<NFS share on Edge device> /home/username/<Folder on local Linux computer>`

Prior to setting up the mounts, make sure the directories that will act as mountpoints on your local computer are already created and also do not contain any files or sub-folders.

The following example shows how to connect via NFS to a Data Box share. The Data Box device IP is 10.161.23.130, the share mylinuxshare2 is mounted on the ubuntuVM, mount point being /home/databoxubuntuhost/databox.

    `sudo mount -t nfs 10.161.23.130:/mylinuxshare2 /home/databoxubuntuhost/databox`


#### Copy data to an NFS share

If you are using a Linux host computer, follow these guidelines:

- Use a copy utility similar to Robocopy. Some of the alternatives available in Linux are [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/), or [Ultracopier](https://ultracopier.first-world.info/).  
- Ensure that you have a multithreaded option with atleast 32 or 64 threads. 


### Connect to and copy data from 3rd party software

If you are configuring your Data Box to copy data from other hosts that 
## Prepare to ship

Final step is to prepare the device to ship. In this step, all the device shares are taken offline. The shares cannot be accessed once you start preparing the device to ship.
1. Go to **Prepare to ship** and click **Start preparation**. 
   
    ![Prepare to ship 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. If the checksum was not enabled, then you are presented the option to enable checksum. We recommend that for the integrity of your data, you perform the checksum validation. Selecting **Enable checksum** will then trigger checksum computation and may take some time depending upon the size of your data. Click **Start preparation**.
    1. The device shares go offline and the device is locked when we prepare to ship.
        
        ![Prepare to ship 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. The device status updates to *Ready to ship* once the device preparation is complete. 
        
        ![Prepare to ship 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. You can download the list of files that were copied in this process.
        
        ![Prepare to ship 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. Shut down the device. Go to **Shut down or restart** page and click **Shut down**. When prompted for confirmation, click **OK** to continue.
4. Remove the cables and return to the device case. The next step is to ship the device to Microsoft.


In this tutorial, you learned about Azure Data Box topics such as:

> [!div class="checklist"]
> * Copy data to Data Box
> * Prepare to ship Data Box

Advance to the next tutorial to learn how to set up and copy data on your Data Box.

> [!div class="nextstepaction"]
> [Ship your Azure Data Box to Microsoft](./data-box-deploy-picked-up.md)

