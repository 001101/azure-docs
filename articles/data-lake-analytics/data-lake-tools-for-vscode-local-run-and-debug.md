
---
title: 'Azure Data Lake tools: U-SQL local run and local debug with Visual Studio Code | Microsoft Docs'
description: Learn how to use the Azure Data Lake Tools for Visual Studio Code to local run and local debug.
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview storage file,upload to storage path
services: data-lake-analytics
documentationcenter: ''
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal

ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
---

# U-SQL local run and local debug with Visual Studio Code

## Prerequisites
Make sure you have the prerequisites in place before starting these procedures:
- Azure Data Lake Tool for Visual Studio Code. For instructions, see [Use the Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- C# for Visual Studio Code (if you want to perform a U-SQL local debug).

   ![Install C# in Data Lake Tools for Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > The U-SQL local run and debug features currently only support Windows users. 


## Set up the U-SQL local run environment

1. Open the command palette by selecting Ctrl+Shift+P, and then enter **ADL: Download LocalRun Dependency** to download the packages.  

   ![Download the ADL LocalRun Dependency packages](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Locate the dependency packages from the path shown in the **Output** pane, and then install **BuildTools** and **Win10SDK 10240**. For example:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
<<<<<<< HEAD
  ![Locate the dependency packages](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)
=======

  ![LocateDependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)
>>>>>>> 9e01f42df1ad851ec19f6d83515d7e9309e92c84

- To install **BuildTools**, follow the wizard instructions.   

  ![Instal lBuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

- To install **Win10SDK 10240**, follow the wizard instructions.  

  ![Install Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Set up the*environment variable. Set the **SCOPE_CPP_SDK** environment variable to:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
Restart the OS to make sure that the environment variable settings take effect.  

   ![Ensure the SCOPE_CPP_SDK environment variable is installed](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## Start the local run service and submit the U-SQL job to a local account 
For the first-time user, you are prompted to download the ADL: Download Localrun Dependency packages if they are not already installed.
1. Select Ctrl+Shift+P to open the command palette, and then enter **ADL: Start Local Run Service**.
2. Select **Accept** to accept the EULA for the first time. 

<<<<<<< HEAD
   ![Accept the EULA](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. The cmd console opens. For first time users, you need to enter **3**, and then locate the local folder path for your data input and output. For other options, you can simply use the default values. 
=======
   ![Accept the EULA ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. The **cmd console** opens. For first-time users, you need to enter **3**, and then input the local folder path for your data input and output. For other options, you can use the default values. 
>>>>>>> 9e01f42df1ad851ec19f6d83515d7e9309e92c84

   ![Data Lake Tools for Visual Studio Code local run cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Select Ctrl+Shift+P to open the command palette, enter **ADL: Submit Job**, and then select **Local** to submit the job to your local account.

   ![Data Lake Tools for Visual Studio Code select local](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. After submitting the job, you can see the submission details by selecting **jobUrl** in the **Output** window. You can also view the job submission status from the cmd console. Enter **7** in the cmd console if you want to know more job details.

   ![Data Lake Tools for Visual Studio Code local run output](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools for Visual Studio Code local run cmd status](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## Start a local debug for the U-SQL job  
For the first-time user, you are prompted to download the ADL: Download Localrun Dependency packages if they are not already installed.
  
1. Select Ctrl+Shift+P to open the command palette, and then enter **ADL:Start Local Run Service**. The cmd console opens. Make sure that the **DataRoot** is set.
3. Set a breakpoint in your C# code-behind.
4. Back in the script editor, select Ctrl+Shift+P to open the command console, and then enter **Local Debug** to start your local debug service.

![Data Lake Tools for Visual Studio Code local debug result](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## Next steps
- For using the Azure Data Lake Tools for Visual Studio Code, see [Use the Azure Data Lake tools for Visual Studio code](data-lake-analytics-data-lake-tools-for-vscode.md).
- For getting started information on Data Lake Analytics, see [Tutorial: Get started with Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
<<<<<<< HEAD
- For information on using Data Lake Tools for Visual Studio, see [Tutorial: Develop U-SQL scripts using the Data Lake tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- For the information on developing assemblies, see [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md).
=======
- For information on using Data Lake tools for Visual Studio, see [Tutorial: Develop U-SQL scripts using the Data Lake tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- For the information on developing assemblies, see [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md).
>>>>>>> 9e01f42df1ad851ec19f6d83515d7e9309e92c84
