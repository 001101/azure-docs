---
title: Troubleshoot VM and environment creation failures Azure DevTest Labs | Microsoft Docs
description: Learn how to troubleshoot virtual machine (VM) and environment creation failures in Azure DevTest Labs. 
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila

ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru

---

# Troubleshoot virtual machine (VM) and environment creation failures in Azure DevTest Labs
DevTest Labs gives you warnings if a machine name is invalid or if you are about to violate a lab policy. Sometimes, you see red `X` next to your lab VM or environment status that informs you that something went wrong.  This article provides a few tricks that you can use to find the underlying issue and, hopefully, avoid the issue in the future. 

## Portal notifications
If you are using the Azure portal, the first place to look at is the **notifications panel**.  The notifications panel, available on the main command bar by clicking the **bell icon**, will tell you whether the lab VM or environment creation was successful or not.  If there was a failure, you see the error message associated with the creation failure. The details often give further information to help you resolve the issue. In the following example, the virtual machine creation failed due to running out of cores. The detailed message tells you how to fix the issue and request a core quota increase.

![Azure portal notification](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## Activity logs 
Loook at activity logs if you are investigating a failure sometime after attempting the creation of your VM or environment. This section shows you how to find logs for VMs and environments. 

### Activity logs for virtual machines

- On the home page for your lab, select the VM to launch the **Virtual Machine** page.
- On the **Virtual Machine** page, in the **MONITORING** section of the left menu, select **Activity log** to see all logs associated with the VM.  
- In the activity log items, select the operation that failed. Typically, the failed operation is called `Write Virtualmachines`.   
- In the right pane, switch to the JSON tab. You see the details in the JSON view of the log.

    ![Activity log for a VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)

    Look through the JSON log until you find the `statusMessage` property. It gives you the main error message and further detail information, if applicable. The following JSON is an example for the core quoted exceeded error seen earlier in this article.

    ```json
    "properties": { 
        "statusCode": "Conflict", 
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at http://aka.ms/corequotaincrease.\"}]}}", 
    }, 
    ```

## Activity log for an environment

To see the activity log for an environment creation, follow these steps:

1. On the home page for your lab, select **Configuration and policies** on the left menu.
2. on the **Configuration and policies** page, select **Activity log** on the menu. 
3. Look for the failure in the activity list in the log and select it. 
4. In the right pane, switch tot he JSON tab, and look for the **statusMessage**. 

    ![Environment activity log](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## Resource Manager template deployment logs
If your environment or virtual machine was created through automation, there is one last place to look error information. That's the Azure Resource Manager template deployment log. When a lab resource is created through automation, it is often done through an Azure Resource Manager template deployment. See[https://github.com/Azure/azure-devtestlab/tree/master/Samples](https://github.com/Azure/azure-devtestlab/tree/master/Samples) for sample Azure Resource Manager templates that create DevTest Lab resources. 

To see the lab template deployment logs, follow these steps:

1. Launch the page for the resource group in which the lab resides.
2. Select **Deployments** menu item. 
3. Look for deployments with a failed status.  
4. Selecting **Error details** link will show a pop out with full error details. In the following example, the maximum allowed virtual machines per user policy prevented another lab VM from being created. 

![Templaete deployment error](./media/troubleshoot-vm-environment-creation-failures/template-deployment-error.png)  

## Next steps
See [Troubleshooting issues with using artifacts](troubleshoot-artifacts.md)