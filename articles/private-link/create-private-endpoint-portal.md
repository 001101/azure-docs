---
title: 'Create an Azure Private Endpoint using Azure Portal| Microsoft Docs'
description: Learn about Azure Private Endpoint
services: virtual-network
author: KumudD
# Customer intent: As someone with a basic network background, but is new to Azure, I want to create an Azure Private Endpoint
ms.service: virtual-network
ms.topic: article
ms.date: 09/06/2019
ms.author: kumud

---
# Create Azure Private Endpoint using Azure Portal
A private endpoint is the fundamental building block for private link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with private link resources. 

In this Quickstart, you will learn how to create a VM on an Azure virtual network, a storage account with an Azure Private Endpoint using the Azure portal. Then, you can securely access the private link resource (the storage account) from the VM.


## Sign in to Azure

Sign in to the Azure portal at https://portal.azure.com.

## Create the virtual network

Create a virtual network and the subnet to host the VM.

1. On the upper-left side of the screen, select **Create a resource** > **Networking** > **Virtual network**.
1. In **Create virtual network**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Name | Enter *myVNet*. |
    | Address space | Enter *10.1.0.0/16*. |
    | Subscription | Select your subscription.|
    | Resource group | Select **Create new**, enter *myResourceGroup*, then select **OK**. |
    | Location | Select **East US**.|
    | Subnet - Name | Enter *mySubnet*. |
    | Subnet - Address range | Enter *10.1.0.0/24*. |
1. Leave the rest as default and select **Create**.


## Create virtual machine

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual Machine**.

1. In **Create a virtual machine - Basics**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. You created this in the previous section.  |
    | **INSTANCE DETAILS** |  |
    | Virtual machine name | Enter *myVm*. |
    | Region | Select **West US**. |
    | Availability options | Leave the default **No infrastructure redundancy required**. |
    | Image | Select **Windows Server 2019 Datacenter**. |
    | Size | Leave the default **Standard DS1 v2**. |
    | **ADMINISTRATOR ACCOUNT** |  |
    | Username | Enter a username of your choosing. |
    | Password | Enter a password of your choosing. The password must be at least 12 characters long and meet the [defined complexity requirements](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reenter password. |
    | **INBOUND PORT RULES** |  |
    | Public inbound ports | Leave the default **None**. |
    | **SAVE MONEY** |  |
    | Already have a Windows license? | Leave the default **No**. |

1. Select **Next : Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next : Networking**.

1. In **Create a virtual machine - Networking**, select this information:

    | Setting | Value |
    | ------- | ----- |
    | Virtual network | Leave the default **myVNet**.  |
    | Address space | Leave the default **10.1.0.0/24**.|
    | Subnet | Leave the default **mySubnet (10.1.0.0/24)**.|
    | Public IP | Leave the default **(new) myVm-ip**. |
    | Public inbound ports | Select **Allow selected ports**. |
    | Select inbound ports | Select **HTTP** and **RDP**.|

1. Select **Review + create**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## Create a private storage account
Create a private storage account in your virtual network as follows:

1. 1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Storage** > **Storage acount**.

1. In **Create storage account - Basics**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. You created this in the previous section.|
    | **INSTANCE DETAILS** |  |
    | Storage account name  | Enter *myvmstorageaccount*. If this name is taken, create a unique name. |
    | Region | Select **West US**. |
    | Performance| Leave the default **Standard**. |
    | Account kind | Leave the default **Storage (general purpose v2)**. |
    | Replication | Leave the default **Locally-redundant storage (LRS).**. |
  
3. Select **Next : Networking**.
4. In **Create a storage account - Networking**, connectivity method, select **Private endpoint**.
5. In **Create a storage account - Networking**, select **Add private endpoint**. 
6. In **Create private endpoint**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. You created this in the previous section.|
    |Location|Select **West US**.|
    |Name|Enter *myPrivateEndpoint*.  |
    |target sub-resource|Leave the default **Blob primary**. |
    | **NETWORKING** |  |
    | Virtual network  | Select *MyVirtualNetwork* from resource group *myResourceGroup*. |
    | Subnet | Leave the default **default**. |
    | **PRIVATE DNS INTEGRATION**|  |
    | Integrate with private DNS zone  | Leave the default **Yes**. |
    | Private DNS zone  | Leave the default ** (New) privatelink.blob.core.windows.net**. |
7. Select **OK**. 
8. Select **Review + create**. You're taken to the **Review + create** page where Azure validates your configuration. 
9. When you see the Validation passed message, select **Create**. 
10. Browse to the storage account resource that was just created.S
11. Select **Keys** from the left content menu.
12. Select **Copy** on the connection string for key1.
 
## Connect to a VM from the internet

Connect to the VM *myVm1* from the internet as follows:

1. In the portal's search bar, enter *myVm*.

1. Select the **Connect** button. After selecting the **Connect** button, **Connect to virtual machine** opens.

1. Select **Download RDP File**. Azure creates a Remote Desktop Protocol (*.rdp*) file and downloads it to your computer.

1. Open the downloaded *.rdp* file.

    1. If prompted, select **Connect**.

    1. Enter the username and password you specified when creating the VM.

        > [!NOTE]
        > You may need to select **More choices** > **Use a different account**, to specify the credentials you entered when you created the VM.

1. Select **OK**.

1. You may receive a certificate warning during the sign in process. If you receive a certificate warning, select **Yes** or **Continue**.

1. Once the VM desktop appears, minimize it to go back to your local desktop.  

## Access storage account privately from the VM

1. In the Remote Desktop of *myVM*, open PowerShell.
2. Enter `nslookup mystorageaccount.blob.core.windows.net`
    You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 


Additional options to access the Storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure Storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the private endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a private endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about private endpoint settings, see Manage private endpoints.