﻿## Sign in to Azure

Sign in to your Azure subscription with the `Connect-AzureRmAccount` command and follow the on-screen directions.

```powershell
Connect-AzureRmAccount
```

## Create a resource group

Create an Azure resource group with [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). A resource group is a logical container into which you can deploy and manage Azure resources. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

If you don't know which location you want to use, you can list the available locations. Display the list of locations by using the following code example and find the one you want to use. This example uses **eastus**. Store the location in a variable and use the variable so you can change it in one place.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## Create a storage account

Create a standard, general-purpose storage account with LRS replication by using [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Next, get the storage account context that defines the storage account you want to use. When acting on a storage account, reference the context instead of repeatedly passing in the credentials. Use the following example to create a storage account called *mystorageaccount* with locally redundant storage (LRS) and blob encryption (enabled by default).

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `
  -Kind Storage

$ctx = $storageAccount.Context
```
