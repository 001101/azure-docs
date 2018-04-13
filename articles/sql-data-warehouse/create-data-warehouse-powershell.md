---
title: 'Azure PowerShell: Create a SQL database | Microsoft Docs'
description: Learn how to create a SQL Database logical server, server-level firewall rule, and databases in the Azure portal.
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: implement
ms.date: 04/11/2018
ms.author: elbutter
ms.reviewer: jrj
---

# Create a single Azure SQL database using PowerShell

PowerShell is used to create and manage Azure resources from the command line or in scripts. This guide details using PowerShell to deploy an Azure SQL Data Warehouse in an [Azure resource group](../azure-resource-manager/resource-group-overview.md) in an [Azure SQL logical server](../sql-database/sql-database-features.md).

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

This tutorial requires the Azure PowerShell module version 4.0 or later. Run ` Get-Module -ListAvailable AzureRM` to find the version. If you need to install or upgrade, see [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps). 

## Log in to Azure

Log in to your Azure subscription using the [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) command and follow the on-screen directions.

```powershell
Add-AzureRmAccount
```

## Create variables

Define variables for use in the scripts in this quickstart.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## Create a resource group

Create an [Azure resource group](../azure-resource-manager/resource-group-overview.md) using the [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) command. A resource group is a logical container into which Azure resources are deployed and managed as a group. The following example creates a resource group named `myResourceGroup` in the `westeurope` location.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## Create a logical server

Create an [Azure SQL Database logical server](../sql-database/sql-database-features.md) using the [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) command. A logical server contains a group of databases managed as a group. The following example creates a randomly named server in your resource group with an admin login named `ServerAdmin` and a password of `ChangeYourAdminPassword1`. Replace these pre-defined values as desired.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## Configure a server firewall rule

Create an [Azure SQL Database server-level firewall rule](../sql-database/sql-database-firewall-configure.md) using the [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) command. A server-level firewall rule allows an external application, such as SQL Server Management Studio or the SQLCMD utility to connect to a SQL database through the SQL Database service firewall. In the following example, the firewall is only opened for other Azure resources. To enable external connectivity, change the IP address to an appropriate address for your environment. To open all IP addresses, use 0.0.0.0 as the starting IP address and 255.255.255.255 as the ending address.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database and SQL Data Warehouse communicate over port 1433. If you are trying to connect from within a corporate network, outbound traffic over port 1433 may not be allowed by your network's firewall. If so, you will not be able to connect to your Azure SQL server unless your IT department opens port 1433.
>

## Create a data warehouse in the server with sample data

Create a data warehouse with a [DW400 performance level](sql-database-service-tiers.md) in the server using the [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) command. The following example creates a database called `mySampleDataWarehouse` and loads the AdventureWorksDW sample data into this database. Replace these predefined values as desired (other quickstart tutorials in this collection build upon the values in this quickstart).

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -SampleName "AdventureWorksDW" `
    -RequestedServiceObjectiveName "DW400"
```

## Clean up resources

Other quickstart tutorials in this collection build upon this quickstart. 

> [!TIP]
> If you plan to continue on to work with subsequent quickstart tutorials, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart in the Azure portal.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## Next steps

You have now created a data warehouse, created a firewall rule, connected to your data warehouse, and run a few queries. To learn more about Azure SQL Data Warehouse, continue to the tutorial for loading data.
> [!div class="nextstepaction"]
>[Load data into a SQL data warehouse](load-data-from-azure-blob-storage-using-polybase.md)
