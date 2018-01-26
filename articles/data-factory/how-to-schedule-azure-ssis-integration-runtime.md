---
title: How to schedule Azure SSIS integration runtime | Microsoft Docs
description: This article describes how to schedule starting and stopping of an Azure SSIS integration runtime by using Azure Automation and Data Factory.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: 

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 01/25/2018
ms.author: spelluru

---
# How to schedule starting and stopping of an Azure SSIS integration runtime 
If you run SSIS packages in Azure only between certain times of a day or a certain day of a week etc., you may also want to run the Azure SSIS integration runtime only for that period of time. This action saves money as the Azure SSIS IR is charged as long as it's in the **Started** state. Using the Data Factory UI or Azure PowerShell, you can manually start or stop an Azure SSIS integration runtime as described in [Manage an Azure SSIS integration runtime](manage-azure-ssis-integration-runtime.md). This article describes how to schedule starting and stopping of an Azure SSIS integration runtime (IR) by using Azure Automation and Azure Data Factory. Here are the high level steps described in this article:

1. **Create and test an Azure Automation runbook.** In this step, you create a PowerShell runbook with the script that starts or stops an Azure SSIS IR based on the arguments passed to its parameters (resource group name, data factory name, Azure SSIS IR name, start/stop command). Then, you test the runbook in both START and STOP scenarios and confirm that IR starts or stops. 
2. **Create two schedules for the runbook.**. For the first schedule, configure the runbook with START as the operation. For the second schedule, configure the runbook with STOP as the operation. For both the schedules, you specify the cadence at which the runbook is run. For example, you may want to schedule the first one to run at 8 AM every day and the second one to run at 11 PM everyday. When the first runbook runs, it starts the Azure SSIS IR. When the second runbook runs, it stops the Azure SSIS IR. 
3. **Create two webhooks for your Azure Automation runbook**, one for the START operation and the other for the STOP operation. You create a Data Factory pipeline with two web activities that use URIs for these webhooks. For example, you start IR in the first web activity, wait for 30 minutes for the Azure SSIS IR to start, run an SSIS package by using a stored procedure activity, and then stop the Azure SSIS IR. Then, you create a schedule trigger to schedule the pipeline to run at the cadence you specify. 
4. **Create a Data Factory pipeline** with two web activities that invoke the webhooks to start and stop the Azure SSIS IR. 

> [!NOTE]
> This article applies to version 2 of Data Factory, which is currently in preview. If you are using version 1 of the Data Factory service, which is generally available (GA), see [Invoke SSIS packages using stored procedure activity in version 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

 
## Prerequisites
If you haven't provisioned an Azure SSIS integration runtime already, provision it by following instructions in the [tutorial](tutorial-deploy-ssis-packages-azure.md) or [Create Azure SSIS integration runtime](create-azure-integration-runtime.md). 

## Create and test an Azure Automation runbook
In this section, you perform the following steps: 

1. Create an Azure Automation account.
2. Create a PowerShell runbook in the Azure Automation account. The PowerShell script associated with the runbook either starts or stops an Azure SSIS IR based on the command you specify for the OPERATION parameter. 
3. Test the runbook in both start and stop scenarios to confirm that it works. 

### Create an Azure Automation account
If you don't have an Azure Automation account, create by following the instructions in this step. For detailed steps, see [Create an Azure Automation account](../automation/automation-quickstart-create-account.md). As part of this step, you create an **Azure Run As** account (a service principal in your Azure Active Directory), and add it to the **Contributor** role of your Azure subscription. Ensure that it's same as the subscription that contains the data factory that has the Azure SSIS IR. Azure Automation uses this account to authenticate to Azure Resource Manager and operate on your resources. 

1. Log in to the [Azure portal](https://portal.azure.com/).    
2. Select **New** on the left menu, select **Monitoring + Management**, and select **Automation**. 

    ![New -> Monitoring + Management -> Atuomation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. In the **Add Automation Account** window, take the following steps: 

    1. Specify a **name** for the automation account. 
    2. Select the **subscription** that has the data factory with Azure SSIS IR. 
    3. For **Resource group**, select **Create new** to create a new resource group, or select **Use existing** to select an existing resource group. 
    4. Select a **location** for the automation account. 
    5. Confirm that **Create Run As account** is set to **Yes**. A service principal is created in your Azure Active Directory. It's added to the **Contributor** role of your Azure subscription
    6. Select **Pin to dashboard** so that you see it on the dashboard of the portal. 
    7. Select **Create**. 

        ![New -> Monitoring + Management -> Atuomation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. You see the **deployment status** on the dashboard and in the notifications. 
    
    ![Deploying automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. You see the home page for the automation account after it's created successfully. 

    ![Automation home page](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### Import Data Factory modules

1. Select **Modules** in the **SHARED RESOURCES** section on the left menu, and verify whether you have **AzureRM.DataFactoryV2** in the list of modules. If it's not there, select **Browse gallery** on the toolbar.

    ![Automation home page](./media/how-to-schedule-azure-ssis-integration-runtime/automation-modules.png)
2. In the **Browse Gallery** window, type **AzureRM.Profile** in the search window, and press **ENTER**. Select **AzureRM.Profile** in the list. Then, click **Import** on the toolbar. 

    ![Select AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/select-azurerm-profile.png)
1. In the **Import** window, select **I agree to update all of the Azure modules** option, and click **OK**.  

    ![Import AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/import-azurerm-profile.png)
4. Close windows to get back to the **Modules** window. You should see the status of the import in the list. Select **Refresh** to refresh the list. Wait until you see the **STATUS** as **Available**.

    ![Import status](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-profile.png)
1. Repeat the steps to import **AzureRM.DataFactoryV2** module. Confirm that the status of this module is set to **Avaiable** before proceeding further. 

    ![Final import status](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-datafactoryv2.png)

### Create a PowerShell runbook
The following procedure provides steps for creating a PowerShell runbook. The script associated with the runbook either starts/stops an Azure SSIS IR based on the command you specify for the **OPERATION** parameter. Note that this section does not provide all the details for creating a runbook. For more information, see [Create a runbook](../automation/automation-quickstart-create-runbook.md) article.

1. Switch to the **Runbooks** tab, and select **+ Add a runbook** from the toolbar. 

    ![Add a runbook button](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Select **Create a new runbook**, and perform the following steps: 

    1. For **Name**, type **StartStopAzureSsisRuntime**.
    2. For **Runbook type**, select **PowerShell**.
    3. Select **Create**.
    
        ![Add a runbook button](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Copy/paste the following script to the runbook script window. Save and then publish the runbook by using the **Save** and **Publish** buttons on the toolbar. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Add-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![Edit PowerShell runbook](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. Test the runbook by selecting **Start** button on the toolbar. 

    ![Start runbook button](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. In the **Start Runbook** window, perform the following steps: 

    1. For **RESOURCE GROUP NAME**, enter the name of the resource group with the data factory that has the Azure SSIS IR. 
    2. For **DATA FACTORY NAME**, enter the name of the data factory that has the Azure SSIS IR. 
    3. For **AZURESSISNAME**, enter the name of the Azure SSIS IR. 
    4. For **OPERATION**, enter **START**. 
    5. Select **OK**.  

        ![Start runbook window](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. In the job window, select **Output** tile. In the output window of the job, wait until you see the message **##### Completed #####** after you see **##### Starting #####**. Starting an Azure SSIS IR takes approximately 20 minutes. Close the **Job** window, and get back to the **Runbook** window.

    ![Azure SSIS IR - started](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Repeat the previous two steps, but by using **STOP** as the value for the **OPERATION**. Start the runbook again by selecting the **Start** button on the toolbar. Specify the resource group name, data factory name, Azure SSIS IR name, and STOP for OPERATION. In the output window of the job, wait until you see message **##### Completed #####** after you see **##### Stopping #####**. Stopping an Azure SSIS IR does not take as long as starting the Azure SSIS IR. Close the **Job** window, and get back to the **Runbook** window. 

## Create schedules for the runbook to start/stop the IR
In the previous section, you created an Azure Automation runbook that can either start or stop an Azure SSIS IR. In this section, you create two schedules for the runbook. When configuring the first schedule, you specify START for the OPERATION parameter. Similarly, when configuring the second schedule, you specify STOP for the OPERATION. For detailed steps for creating schedules, see [Create a schedule](../automation/automation-schedules.md#creating-a-schedule).

1. In the **Runbook** window, select **Schedules**, and select **+ Add a schedule** on the toolbar. 

    ![Azure SSIS IR - started](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. In the **Schedule Runbook** window, perform the following steps: 

    1. Select **Link a schedule to your runbook**. 
    2. Select **Create a new schedule**.
    3. In the **New Schedule** window, type **Start IR daily** for **Name**. 
    4. In the **Starts section**, for the time, specify a time a few minutes past the current time. 
    5. For **Recurrence**, select **Recurring**. 
    6. In the **Recur every** section, select **Day**. 
    7. Select **Create**. 

        ![Schedule for Azure SSIS IR start](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Switch to the **Parameters and run settings** tab. Specify the resource group name, data factory name, and Azure SSIS IR name. For **OPERATION**, enter **START**. Select **OK**. Select **OK** again to see the schedule on the **Schedules** page of the runbook. 

    ![Schedule for staring the Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Repeat the previous two steps to create a scedule named **Stop IR daily**. This time, specify time at least 30 minutes after the time you specified for the **Start IR daily** schedule. For **OPERATION**, specify **STOP**. 
5. In the **Runbook** window, select **Jobs** on the left menu. You should see the jobs created by the schedules at the specified times and their statuses. You can see details about the job such as its output similar to what you have seen when you tested the runbook. 
6. After you are done testing, disable the schedules by editing them and selecting **NO** for **Enabled**.  

## Create webhooks to start and stop IR
Follow instructions in [Create a webhook](../automation/automation-webhooks.md#creating-a-webhook) to create two webhooks for the runbook. For the first one, specify START as the OPERATION, and for the second one, specify STOP as the OPERATION. Note down the URLs for both the webhooks. You use these URLs when configuring Web activities in the Data Factory pipeline. The following image shown an example of creating a webhook that starts the Azure SSIS IR:

1. In the **Runbook** window, select **Webhooks** from the left menu, and select **+ Add Webhook** on the toolbar. 

    ![Webhooks -> Add Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. In the **Add Webhook** window, select **Create new webhook**, and do the following actions: 

    1. For **Name**, enter **StartAzureSsisIR**. 
    2. Confirm that **Enabled** is set to **Yes**. 
    3. Copy the **URL** and save it somewhere. This step is very important. You do not see the URL later. 
    4. Select **OK**. 

        ![New Webhook window](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Switch to the **Parameters and run settings** tab. Specify the resource group name, the data factory name, and the Azure SSIS IR name. For **OPERATION**, enter **START**. Click **OK**. Then, click **Create**. 

    ![Webhook - parameters and run settings]](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Repeat the previous three steps to create another webhook named **StopAzureSsisIR**. Don't forget to copy the URL. When specifying the parameters and run settings, enter **STOP** for **OPERATION**. 

You should have two URLs, one for the StartAzureSsisIR webhook and the other for the StopAzureSsisIR webhook. You can send an HTTP POST request to these URLs to start/stop your Azure SSIS IR. 

## Create and schedule a Data Factory pipeline that starts/stops the IR
This section shows how to use a Web activity to invoke the webhooks you created in the previous section.

The pipeline you create consists of four activities. 

1. The first **Web** activity invokes the first webhook to start the Azure SSIS IR. 
2. The **Wait** activity waits for 30 minutes (1800 seconds) for the Azure SSIS IR to start. 
3. The **Stored Procedure** activity runs a SQL script that runs the SSIS package. The second **Web** activity stops the Azure SSIS IR. For more information about invoking an SSIS package from a Data Factory pipeline by using the Stored Procedure activity, see [Invoke an SSIS package](how-to-invoke-ssis-package-stored-procedure-activity.md). 
4. The second **Web** activity invokes the webhook to stop the Azure SSIS IR. 

After you create and test the pipeline, you create a schedule trigger and associate with the pipeline. The schedule trigger defines a schedule for the pipeline. Suppose, you create a trigger that is scheduled to run daily at 11 PM. The trigger runs the pipeline at 11 PM every day. The pipeline starts the Azure SSIS IR, executes the SSIS package, and then stops the Azure SSIS IR. 

### Create a data factory

1. Log in to the [Azure portal](https://portal.azure.com/).    
2. Click **New** on the left menu, click **Data + Analytics**, and click **Data Factory**. 
   
   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. In the **New data factory** page, enter **MyAzureSsisDataFactory** for the **name**. 
      
     ![New data factory page](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   The name of the Azure data factory must be **globally unique**. If you receive the following error, change the name of the data factory (for example, yournameMyAzureSsisDataFactory) and try creating again. See [Data Factory - Naming Rules](naming-rules.md) article for naming rules for Data Factory artifacts.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Select your Azure **subscription** in which you want to create the data factory. 
4. For the **Resource Group**, do one of the following steps:
     
      - Select **Use existing**, and select an existing resource group from the drop-down list. 
      - Select **Create new**, and enter the name of a resource group.   
         
      To learn about resource groups, see [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md).  
4. Select **V2 (Preview)** for the **version**.
5. Select the **location** for the data factory. Only locations that are supported for creation of data factories are shown in the list.
6. Select **Pin to dashboard**.     
7. Click **Create**.
8. On the dashboard, you see the following tile with status: **Deploying data factory**. 

	![deploying data factory tile](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. After the creation is complete, you see the **Data Factory** page as shown in the image.
   
   ![Data factory home page](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Click **Author & Monitor** to launch the Data Factory User Interface (UI) in a separate tab.

### Create a pipeline

2. Create a data factory with a pipeline as shown in the following image: In the following example, the first **Web** activity uses the URL for the webhook that starts the Azure SSIS IR. The **Wait** activity waits for 30 minutes (1800 seconds) for the Azure SSIS IR to start. The **Stored Procedure** activity runs a SQL script that runs the SSIS package. The second **Web** activity stops the Azure SSIS IR. For more information about invoking an SSIS package from a Data Factory pipeline by using the Stored Procedure activity, see [Invoke an SSIS package](how-to-invoke-ssis-package-stored-procedure-activity.md). 

    ![Data Factory pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/data-factory-pipeline.png)

    For detailed instructions on creating a data factory with a pipeline, see [Create a data factory](quickstart-create-data-factory-portal.md).

### Trigger the pipeline

### Schedule the pipeline 
You can create a trigger to run this pipeline at a specified cadence. For details about associating a schedule trigger with a pipeline, see [Trigger the pipeline on a schedule](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## Next steps
See the following articles from SSIS documentation: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)