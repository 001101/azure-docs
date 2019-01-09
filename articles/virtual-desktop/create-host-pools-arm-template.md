---
title: Create a Windows Virtual Desktop host pool with an ARM template (test) - Azure
description: How to create a host pool in Windows Virtual Desktop with an ARM template.
services: virtual-desktop
author: Heidilohr

ms.service: virtual-desktop
ms.topic: how-to
ms.date: 11/21/2018
ms.author: helohr
---
# Create a host pool with an ARM template (Preview)

Host pools are a collection of one or more identical virtual machines within Windows Virtual Desktop tenant environments. Each host pool can contain an app group that users can interact with as they would on a physical desktop.

Follow this section's instructions to create a host pool for a Windows Virtual Desktop tenant with an Azure Resource Manager (ARM) template provided by Microsoft. This article will tell you how to create a host pool in Windows Virtual Desktop, create a resource group with VMs in an Azure subscription, join those VMs to the AD domain, and register the VMs with Windows Virtual Desktop.

## Create a VM image

See Copy Windows 10 Enterprise multi-session images to your storage account to use a Windows 10 Enterprise multi-session image or see Create a VM image for automated deployment to create your own custom image.

## Run the ARM template for provisioning a new host pool

This section's instructions are the same as [Create a host pool with Azure Marketplace](create-host-pools-azure-marketplace.md), only this time you’ll find the template in GitHub.

To start, go to [this GitHub URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### Deploy the template to Azure

If you're deploying in an Enterprise subscription, scroll down and select **Deploy to Azure**, then skip ahead fill out the parameters based on your image source.

If you're deploying in a Cloud Solution Provider subscription, follow these steps to deploy to Azure:

1. Scroll down and right-click **Deploy to Azure**, then select **Copy Link Location**.
2. Open a text editor like Notepad and paste the link there.
3. Right after “https://portal.azure.com/” and before the hashtag (#) enter an at sign (@) followed by the tenant domain name. Here's an example of the format you should use: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Sign in to the Azure portal as a user with Admin/Contributor permissions to the Cloud Solution Provider subscription.
5. Paste the link you copied to the text editor into the address bar.

### Set up image source parameters

Once you've deployed to Azure, you need to set up the source of the image you want to use. There are three options for preexisting images:

- You can use an image from Azure Gallery
- You can [create an image from a VHD from blob storage you uploaded to Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/prepare-for-upload-vhd-image).
- You can [create an Azure-managed VM image from a generalized OS VHD](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/upload-generalized-managed#create-a-managed-image-from-the-uploaded-vhd).

Fill out the following parameters based on the source of the image you want to use for the host pool's virtual machines:

If the image source is Azure Gallery:

1. For **Rdsh Image Source**, select **Gallery**.
2. For **Rdsh Gallery Image SKU**, select the appropriate image.
3. For **Rdsh is Windows Server**, select the appropriate value based on the image. If you're using Windows 10 Enterprise multi-session, select **Windows client**.
4. For **Rdsh is 1809 or Later**, select the appropriate value based on the image. Windows Server 2019 is an 1809 release.
5. You can then leave the following parameters empty:
    - **Vm Image Vhd Uri**
    - **Rdsh Custom Image Source Name**
    - **Rdsh Custom Image Source Resource Group**
    - **Rdsh Use Managed Disks**
    - **Storage Account Resource Group Name**

If the image source is a VHD from blob storage:

1. For **Rdsh Image Source**, select **CustomVHD**.
2. For **Vm Image Vhd Uri**, enter the full URL of the VHD in blob storage.
3. For **Rdsh is Windows Server**, select the appropriate value based on the image. If you're using Windows 10 Enterprise multi-session, select **Windows client**.
4. For **Rdsh is 1809 or Later**, select the appropriate value based on the image. Windows Server 2019 is an 1809 release.
5. For **Rdsh Use Managed Disks**, select the desired value. If you select **false**, the VHD files for each virtual machine will be stored in the same storage account as the image you provided in the **Vm Image Vhd Uri** parameter.
6. If you selected **false** for **Rdsh Use Managed Disks**, enter the name of the resource group containing the storage account and image for the **Storage Account Resource Group Name** parameter. Otherwise, leave this parameter empty.
7. You can then leave the following parameters empty or unchanged:
    - **Rdsh Gallery Image SKU**
    - **Rdsh Custom Image Source Name**
    - **Rdsh Custom Image Source Resource Group**

If the image source is an Azure managed VM image:

1. For **Rdsh Image Source**, select **CustomImage**.
2. For **Rdsh Custom Image Source Name**, enter the name of the Azure Image resource you want to use as the image source.
3. For **Rdsh Custom Image Source Resource Group**, enter the name of the resource group containing the Azure Image resource.
4. For **Rdsh is Windows Server**, select the appropriate value based on the image. If you're using Windows 10 Enterprise multi-session, select **Windows client**.
5. For **Rdsh is 1809 or Later**, select the appropriate value based on the image. Windows Server 2019 is an 1809 release.
6. You can then leave the following parameters empty or unchanged:
    - **Vm Image Vhd Uri**
    - **Rdsh Gallery Image SKU**
    - **Rdsh Use Managed Disks**
    - **Storage Account Resource Group Name**

### Set up your virtual machine properties

After defining your desired image source, enter or change additional information for the virtual machines:

1. For **Rdsh Name Prefix**, you can enter your own prefix into the text box. Otherwise, the virtual machine names will begin with the first 10 characters of the resource group name.
2. For **Rdsh Number of Instances**, enter the number of virtual machines you want to create.
3. For **Rdsh VM Disk Type**, select the disk type you want to use for the virtual machines. If you selected **CustomVHD** as the **Rdsh Image Source** and **false** for **Rdsh Use Managed Disks**, make sure this parameter matches the storage account type where the image is located.

### Set up your domain and network properties

After filling in the virtual machine information, enter the domain and network properties:

1. For **Domain to Join**, enter the name of the domain you'd like the virtual machines to join (for example, “contoso.com”).
2. For **Existing Domain UPN**, enter the UPN of a user with permission join the virtual machines to the domain and organization unit (for example, “vmadmin@contoso.com”).
3. For **Existing Domain Password**, enter the defined Existing Domain UPN's domain password.
4. For **Ou Path**, enter the name of the organizational unit to place the virtual machines during the domain join process. If you do not have a pre-defined organizational unit for these virtual machines, you can leave this parameter empty.
5. For **Existing Vnet Name**, enter the name of the virtual network Azure resource you want to use for the virtual machines.
6. For **Existing Subnet Name**, enter the name of the subnet of the virtual network you want to use for the virtual machines.
7. For **Virtual Network Resource Group Name**, enter the name of the resource group containing the virtual network Azure resource.

### Set up your connection and authentication properties

Finally, set up your connection and authentication properties for Windows Virtual Desktop by entering the following information:

1. For **Rd Broker URL**, leave this value as “https://rdbroker.wvd.microsoft.com” unless provided a different URL.
2. For **Existing Tenant Group Name**, enter the name of the tenant group that contains your Windows Virtual Desktop tenant. If you were not given a specific tenant group name, leave this value as “Default Tenant Group”.
3. For **Existing Tenant Name**, enter the name of your Windows Virtual Desktop tenant.
4. For **Host Pool Name**, enter the name of the Windows Virtual Desktop host pool you want to register your newly created virtual machines to. You can enter the name of an existing host pool or a new host pool you're creating with this template.
5. For **Tenant Admin Upn or Application Id**, enter the appropriate UPN or Application ID that will authenticate to Windows Virtual Desktop. When you're creating a new host pool, this principal must be assigned either the **RDS Owner** or **RDS Contributor** role at the Windows Virtual Desktop tenant scope. If you're adding these virtual machines to an existing host pool, this principal must be assigned either the **RDS Owner** or **RDS Contributor** role at the Windows Virtual Desktop host pool scope. Don't enter a UPN that requires multi-factor authentication. If you do, the virtual machines this template creates won't be able to connect to a Windows Virtual Desktop host pool.
6. For **Tenant Admin Password**, enter the password corresponding to the UPN or service principal (identified by the Application ID).
7. For **Is Service Principal**, select the appropriate value for the **Tenant Admin Upn or Application Id** principal.
8. If you selected **true** for **Is Service Principal**, enter your Azure AD Tenant ID for the **Aad Tenant Id** parameter to successfully authenticate to Windows Virtual Desktop as a service principal. Otherwise, leave this parameter empty.

## Assign users to the desktop application group

After the GitHub ARM template completes, assign user access before you start testing the full session desktops on your virtual machines.

To assign users to the desktop application group:

1. Copy the RDPowerShell.zip file and extract it to a folder on your local computer.
2. Unzip RDPowerShell.zip.
3. Open a PowerShell prompt as Administrator.
4. At the PowerShell command shell prompt, enter the following cmdlet:

    ```powershell
    cd <path-to-folder-where-RDPowerShell.zip-was-extracted>
    Import-module .\Microsoft.RdInfra.RdPowershell.dll
    ```

5. Run the following cmdlet to sign in to the Windows Virtual Desktop environment.

    ```powershell
    Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
    ```

6. Run the following cmdlet to set the context to the tenant group specified in the ARM template.

    ```powershell
    Set-RdsContext -TenantGroupName <Tenant Group name>
    ```

7. Run the following cmdlet to add users to the desktop application group.

    ```powershell
    Add-RdsAppGroupUser <tenantname> <hostpoolname> “Desktop Application Group” -UserPrincipalName <userupn>
    ```

The user’s UPN should match the user’s identity in Azure Active Directory (for example, user1@contoso.com). If you want to add multiple users, you must run this cmdlet for each user.

After you've completed these steps, users added to the desktop application group can sign in to Windows Virtual Desktop with supported Remote Desktop clients and see a resource for a session desktop.