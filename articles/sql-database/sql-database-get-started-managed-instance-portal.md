---
title: 'Azure portal: Create SQL Database Managed Instance | Microsoft Docs'
description: Create an Azure SQL Database Managed Instance in a VNET and use SSMS to restore the Wide World Importers database backup.
keywords: sql database tutorial, create a sql database managed instance
author: CarlRabeler
ms.service: sql-database
ms.custom: managed instance
ms.workload: "Active"
ms.tgt_pltfrm: portal
ms.devlang: 
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: carlrab
manager: Craig.Guyer

---
# Create an Azure SQL Database Managed Instance in the Azure portal

This quick start tutorial walks through creating an Azure SQL Database Managed Instance using the Azure portal in a dedicated subnet of a virtual network (VNET), connecting to the Managed Instance using SQL Server Management Studio on a virtual machine in the same VNET, and then restoring a backup of the Wide World Importers database into the Managed Instance.

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

## Log in to the Azure portal

Log in to the [Azure portal](https://portal.azure.com/).

## Whitelist your subscription

Managed Instance is being released initially as a limited public preview that requires your subscription to be whitelisted. If your subscription is not already whitelisted, use the following steps to be offered and accept preview terms and send a request for whitelisting.

1. Click **Create a resource** in the upper left-hand corner of the Azure portal.
2. Locate **Managed Instance** and then select **Azure SQL Database Managed Instance (preview)**.
3. Click **Create**.

   ![managed instance create](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Select your subscription, click **Preview terms**, and then provide the requested information.

   ![managed instance preview terms](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. Click **OK** when completed.

   ![managed instance preview terms](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

> [!NOTE]
> While awaiting preview approval, you can continue and complete the next few sections of this tutorial.

## Configure a virtual network (VNET)

The following steps show you how to create a new [Azure Resource Manager (ARM)](../azure-resource-manager/resource-manager-deployment-model.md) virtual network (VNET) for use by your Managed Instance.

1. Click **Create a resource** in the upper left-hand corner of the Azure portal.
2. Locate and then click **Virtual Network**, verify the **Resource Manager** is selected as the deployment mode, and then click **Create**.

   ![virtual network create](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Fill out the virtual network form with the requested information, using the information in the following table.

   | Setting| Suggested value | Description |
   | ------ | --------------- | ----------- |
   |**Name**|Any valid name|For valid virtual network names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Address space**|Any valid address range, such as 10.2.0.0/24|The virtual network's address name in CIDR notation.|
   |**Subscription**|Your subscription|For details about your subscriptions, see [Subscriptions](https://account.windowsazure.com/Subscriptions).|
   |**Resource Group**|Any valid resource group (new or existing)|For valid resource group names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Location**|Any valid location| For information about regions, see [Azure Regions](https://azure.microsoft.com/regions/).|
   |**Subnet name**|Any valid subnet name|or valid virtual network names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Subnet address range**|Any valid subnet address|The subnet's address range in CIDR notation. It must be contained by the address space of the virtual network|
   |**Service endpoints**|Disabled|Enable one or more service endpoints for this subnet|
   ||||

   ![virtual network create form](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Click **Create**.

## Create new route table and set route table on Managed Instance subnet

The following steps show you how to create a 0.0.0.0/0 Next Hop Internet route and apply it to the Managed Instance subnet.

1. Click **Create a resource** in the upper left-hand corner of the Azure portal.
2. Locate and then click **Route table**, and then click **Create** on the Route table page. 

   ![route table create](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. Fill out the route table form with the requested information, using the information in the following table.

   | Setting| Suggested value | Description |
   | ------ | --------------- | ----------- |
   |**Name**|Any valid name|For valid route table names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Subscription**|Your subscription|For details about your subscriptions, see [Subscriptions](https://account.windowsazure.com/Subscriptions).|
   |**Resource Group**|Select the resource group you created in the previous procedure|For valid resource group names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Location**|Select the location you specified in the previous procedure| For information about regions, see [Azure Regions](https://azure.microsoft.com/regions/).|
   |**Disable BCP route propogation**|Disabled||
   ||||

   ![route table create form](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. Click **Create**.
5. After the route table has been created, open the newly created route table.

   ![route table](./media/sql-database-managed-instance-tutorial/route-table.png)

6. Click **Routes** and then click **Add**.

7.  Add **0.0.0.0/0 Next Hop Internet route** as the **only** route, using the information in the following table.

    | Setting| Suggested value | Description |
    | ------ | --------------- | ----------- |
    |**Route name**|Any valid name|For valid route names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
    |**Address prefix**|0.0.0.0/0|The destination IP address in CIDR notation that this route applies to.|
    |**Next hop type**|Internet|The next hop handles the matching packets for this route|
    |||

    ![route](./media/sql-database-managed-instance-tutorial/route.png)

8. Click **OK**.
9. To set this route table on the subnet where Managed Instance is to be deployed, open the virtual network that you created earlier.
10. Click **Subnets** and then click the subnet that you created earlier.

    ![subnet](./media/sql-database-managed-instance-tutorial/subnet.png)

11. Click **Route table** and then select the **myMI_route_table**.

    ![set route table](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. Click **Save**

    ![set route table-save](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## Create a Managed Instance

The following steps show you how to create your Managed Instance after your preview has been approved.

1. Click **Create a resource** in the upper left-hand corner of the Azure portal.
2. Locate **Managed Instance** and then select **Azure SQL Database Managed Instance (preview)**.
3. Click **Create**.

   ![managed instance create](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Select your subscription and verify that the preview terms show **Accepted**.

   ![managed instance preview accepted](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. Fill out the Managed Instance form with the requested information, using the information in the following table.

   | Setting| Suggested value | Description |
   | ------ | --------------- | ----------- |
   |**Managed instance name**|Any valid name|For valid instance names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Mnaged instance admin login**|Any valid user name|For valid user names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Password**|Any valid password|The password must be at least 12 characters long and meet the [defined complexity requirements](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Resource Group**|The resource group that you created earlier||
   |**Location**|The location that you previously selected|For information about regions, see [Azure Regions](https://azure.microsoft.com/regions/).|
   |**Virtual network**|The virtual network that you created earlier|

   ![managed instance create form](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. Click **Pricing tier** to review the pricing tier options.
6. Use the sliders or text boxes to specify the amount of storage and the number of virtual cores. When complete, click **Apply** to save your selection.  
   ![managed instance create form](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. Click **Create** to deploy the Managed Instance.
8. Click the **Notifications** icon to view the status of deployment.
 
   ![deployment progress](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. Click **Deployment in progress** to open the Managed Instance window to further monitor the deployment progress.
 
   ![managed instance create form](./media/sql-database-managed-instance-tutorial/managed-instance.png)

10. Record the host name that you specified, as you will need it later in this tutorial to connect to your Managed Instance using SQL Server Management Studio.

While deployment occurs, continue to the next procedure.
 
## Create a virtual machine in the same VNET

The following steps show you how to create a virtual machine in the same VNET in which the Managed Instance is being created. 

1. Click **Create a resource** in the upper left-hand corner of the Azure portal.
2. Select **Compute**, and then select **Windows Server 2016 Datacenter**. 

   ![compute](./media/sql-database-managed-instance-tutorial/compute.png)

3. Fill out the virtual machine form with the requested information, using the information in the following table.

   | Setting| Suggested value | Description |
   | ------ | --------------- | ----------- |
   |**Name**|Any valid name|For valid virtual machine names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **VM disk type**|SSD|SSDs provide the best balance between price and performance.|   
   |**User name**|Any valid user name|For valid user names, see [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Password**|Any valid password|The password must be at least 12 characters long and meet the [defined complexity requirements](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).| 
   |**Subscription**|Your subscription|For details about your subscriptions, see [Subscriptions](https://account.windowsazure.com/Subscriptions).|
   |**Resource Group**|The resource group that you created earlier||
   |**Location**|The location that you previously selected||
   |**Already have a Windows license**|No|If you own Windows licenses with active Software Assurance (SA), use Azure Hybrid Benefit to save compute cost|
   ||||

   ![virutal machine create form](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

3. Click **OK**.
4. Select a size for the VM. To see more sizes, select **View all** or change the **Supported disk type** filter. For this tutorial, you only need a small virtual machine.

    ![VM sizes](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. Click **Select**.
6. Under **Settings**, review but keep the default settings. 

    ![VM settings](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  
7. Click **OK**.
8. On the summary page, review the offer details and then click **Create** to start the virtual machine deployment.
 
## Connect to virtual machine

The following steps show you how to connect to your newly created virtual machine using a remote desktop connection.

1. After deployment completes, go to the virtual machine resource.

    ![VM](./media/sql-database-managed-instance-tutorial/vm.png)  

2. Click the **Connect** button on the virtual machine properties. A Remote Desktop Protocol file (.rdp file) is created and downloaded.
3. To connect to your VM, open the downloaded RDP file. 
4. When prompted, click **Connect**. On a Mac, you need an RDP client such as this [Remote Desktop Client](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) from the Mac App Store.

5. Enter the user name and password you specified when creating the virtual machine, then click **Ok**.

6. You may receive a certificate warning during the sign-in process. Click **Yes** or **Continue** to proceed with the connection.

You are connected to your virtual machine in the Server Manager dashboard.

## Install SQL Server Management Studio (SSMS) and connect to the Managed Instance

The following steps show you how to download and install SSMS, and then connect to your Managed Instance.

1. In Server Manager, click **Local Server** in the left-hand pane.

    ![server manager properties](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. In the **Properties** pane, click **On** to modify the IE Enhanced Security Configuration.
3. In the **Internet Explorer Enhanced Security Configuration** dialog box, click **Off** in the Administrators section of the dialog box and then click **OK**.

    ![internet explorer enhanced security configuration](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  

4. open **Internet Explorer** from the task bar.
5. Click **OK** to complete the setup of Internet Explorer 11.
6. Enter https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms in the URL address box and click **Enter**. 
7. Download the most recent version of SQL Server Management Studio and click **Run** when prompted.
8. When prompted, click **Install** to begin.
9. When the installation completes, click **Close**.
10. Open SSMS.
11. In the **Connect to Server** dialog box, enter the **host name* for your Managed Instance in the **Server name** box and then click **Connect**.

    ![ssms connect](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  


## Download Wide World Importers backup file and restore to Managed Instance.

Use the following steps to download the Wide World Importers backup file and restore it to your Managed Instance using SSMS.

1. Using Internet Explorer, enter https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak in the URL address box and then, when prompted, click **Save** to save this file in the Downloads folder.
2. 

## Next steps

