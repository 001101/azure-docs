---
title: Connect to Azure Resource Manager on your Azure Stack Edge device
description: Describes how to connect to the Azure Resource Manager running on your Azure Stack Edge using Azure PowerShell.
services: databox
author: alkohli

ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 12/04/2019
ms.author: alkohli
#Customer intent: As an IT admin, I need to understand how to connect to Azure Resource Manager on my Azure Stack Edge device so that I can manage resources.
---

# Connect to Azure Resource Manager on your Azure Stack Edge device

Azure Resource Manager provides a management layer that enables you to create, update, and delete resources in your Azure subscription. For example, you can use the Azure Resource Manager APIs to create, update, and delete VMs in a local subscription that are hosted on your Azure Stage Edge device. This tutorial describes how to connect to the Azure Resource Manager running on your Azure Stack Edge using Azure PowerShell.

## Endpoints on Azure Stack Edge device

The following table summarizes the various endpoints exposed on your device, the supported protocols, and the ports to access those endpoints. Through out the article, you will find references to these endpoints.

| # | Endpoint | Supported protocols | Port used | Used for |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 30005 | To connect to Azure Resource Manager for automation |
| 2. | Security token service | https | 443 | To authenticate via access and refresh tokens |
| 3. | Blob | https | 443 | To connect to Blob storage via REST |
| 4. | Appliance | https | 443 | To connect to the local web UI on the appliance |

## Connecting to Azure Resource Manager workflow

The process of connecting to local APIs of the device using Azure Resource Manager requires the following steps:

| Step # | You'll do this step ... | .. on this location. |
| --- | --- | --- |
| 1. | [Configure your Azure Stack Edge device](#step-1-configure-azure-stack-edge-device-local-web-ui) | Local web UI |
| 2. | [Create and install certificates](#step-2-create-and-install-certificates) | Windows client/local web UI |
| 3. | [Review and configure the prerequisites](#step-3-review-and-configure-the-prerequisites-client) | Windows client |
| 4. | [Set up Azure PowerShell on the client](#step-4-set-up-the-development-environment-client) | Windows client |
| 5. | [Modify host file for endpoint name resolution](#step-5-modify-host-file-for-endpoint-name-resolution-client-or-dns-server) | Windows client or DNS server |
| 6. | [Check that the endpoint name is resolved](#step-6-verify-endpoint-name-resolution-client) | Windows client |
| 7. | [Use Azure PowerShell cmdlets to verify connection to Azure Resource Manager](#step-7-use-azure-powershell-cmdlets-to-create-a-vm-client) | Windows client |

The following sections detail each of the above steps in connecting to Azure Resource Manager.

## Step 1: Configure Azure Stack Edge device (local web UI) 

1. Complete the network settings, web proxy settings (optional), and time settings (optional). In this example, **Port 6** network interface settings are configured.

    ![Data Box Edge network settings](media/azure-stack-edge-r-series-connect-resource-manager/network-settings.png)

    Select the network interface name, in this case **Port 6** to view the network settings for this interface.

    ![Network settings (Port 6)](media/azure-stack-edge-r-series-connect-resource-manager/network-settings-port6.png)

    In this example, the network interface is set to **DHCP**.

    When defining device name and DNS domain via the local UI, you will select:

    a. The Azure consistent network from the list of available cluster networks.

    b. The Azure consistent services VIP from the Azure consistent network that you chose.

    - If the interfaces associated with the Azure consistent services network used is set to DHCP, the Azure consistent VIP will be automatically set for you once you set and apply the DNS domain and the Azure consistent services network.

    - If you used a static IP address for the network interface associated with the Azure consistent services network, then you will have an option to choose the Azure consistent services VIP. For reference, see the screenshot below.

    In this following example, the Azure consistent services VIP was automatically populated and is -- 5.5.34.37.

    ![Data Box Edge device](media/azure-stack-edge-r-series-connect-resource-manager/device.png)

    c. Copy and save the Azure consistent services VIP. You will use this information later.

    > [!IMPORTANT]
    > The device name, DNS domain will be used to form the endpoints that are exposed.

1. Go to the **Compute settings**. Select the network interface that you will use to connect to Azure Resource Manager.

    ![Data Box Edge compute settings](media/azure-stack-edge-r-series-connect-resource-manager/compute-settings.png)

1. Enable compute on a network interface that you will use to connect to Azure Resource Manager. Azure Stack Edge will create and manage a virtual switch corresponding to that network interface.

    ![Network settings (Port 6) enable](media/azure-stack-edge-r-series-connect-resource-manager/network-settings-port6-enable.png)

## Step 2: Create and install certificates

Certificates ensure that your communication is trusted. On your Azure Stack Edge device, self-signed appliance, blob, and Azure Resource Manager certificates are automatically generated. Optionally, you can bring in your own signed local UI, blob, and Azure Resource Manager certificates as well.

When you bring in a signed certificate of your own, you also need the corresponding signing chain of the certificate. For the signing chain, local UI, Azure Resource Manager, and the blob certificates on the device, you will need the corresponding certificates on the client machine also to authenticate and communicate with the device.

The following section describes the procedure to create signing chain and endpoint certificates, import these certificates on your Windows client, and finally upload these certificates on the device.

### Create certificates (Optional)

For test and development use only, you can use Windows PowerShell to create certificates on your local system. While creating the certificates for the client, follow these guidelines:

1. You first need to create a root certificate for the signing chain. See steps to [Create signing chain certificates]().

2. You can next create the endpoint certificates for the local UI, blob, and Azure Resource Manager. See the steps to [Create endpoint certificates]().


### Upload certificates on the device

The certificates that you created in the previous step will be in the Personal store on your client. These certificates need to be exported on your client into appropriate format files that can then be uploaded to your device.

1. The root certificate must be exported as a .cer format file. For detailed steps, see [Export certificates as a .cer format file](#export-certificate-to-.cer-format).

2. The endpoint certificates must be exported as *.pfx* files with private keys. For detailed steps, see [Export certificates as .pfx file with private keys](#export-certificate-to-.pfx-format-with-a-private-key).

3. The root and endpoint certificates are then uploaded on the device using the **+Add certificate** option on the **Certificates** page in the local web UI. To upload the certificates, follow the steps in [Upload certificates]().

If you experience any errors, go to [Errors with certificates]().

### Import certificates on the client running Azure PowerShell

The certificates that you created in the previous step must be imported on your Windows client into the appropriate certificate store.

1. The root certificate that you exported as the *.cer* should now be imported in the Trusted Root Certificate Authorities on your client system. For detailed steps, see [Import certificates into the Trusted Root Certificate Authorities store.](#import-certificates-into-the-personal-store)

2. The endpoint certificates that you exported as the *.pfx* must be exported as *.cer*. This *.cer* is then imported in the **Personal** certificate store on your system. For detailed steps, see [Import certificates into the Personal certificate store](#export-certificate-to-.cer-format).

## Step 3: Review and configure the prerequisites on the client 

Your Windows client must meet the following prerequisites:

1. Run PowerShell Version 5.0. You must have PowerShell version 5.0 or higher. To check the version of PowerShell on your system, run the following cmdlet:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Compare the **Major** version and ensure that it is 5.0 or later.

    If you have an outdated version, see [Upgrading existing Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

    If you don\'t have PowerShell 5.0, follow [Installing Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6).

    A sample output is shown below.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. You can access the PowerShell Gallery.

    Run PowerShell as administrator. Verify if the PSGallery is registered as a repository.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    A sample output is shown below.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
For more information, see [Validate the PowerShell Gallery accessibility](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?view=azs-1908#2-validate-the-powershell-gallery-accessibility).

## Step 4: Set up the Azure PowerShell on the client 

1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).

2. Define the environment as Azure Stack Edge and the port to be used for Azure Resource Manager calls -- 30005. You define the environment in two ways:

    a. Set the environment. Type the following command:

    ```powershell
    Set-AzureRMEnvironment -Name <String>
    ```
    
    For more information, go to [Set-AzureRMEnvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0).

    b. Define the environment inline for every cmdlet that you execute. This ensures that all the API calls are going through the correct environment. By default, the calls would go through the Azure public.

3. Install Azure PowerShell modules on your client.

    a. Run PowerShell as an administrator. You need access to PowerShell gallery. In this example, you are running Azure Stack 1904 or later based on your API profile.

    b. To install the required Azure PowerShell modules from the PowerShell Gallery, run the following command:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.7.2
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    Get-Module -Name "Azs*" -ListAvailable
    ```
    
    For more information, see [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0#install-the-azure-powershell-module).

A sample output is shown below that indicates the AzureRM modules were installed successfully.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Install-Module -Name AzureStack -RequiredVersion 1.7.2
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...
Script     1.7.2      AzureStack

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
 
PS C:\windows\system32> Get-Module -Name "Azs*" -ListAvailable

    Directory: C:\Program Files\WindowsPowerShell\Modules 
 
ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Script     0.2.2      Azs.Azurebridge.Admin               {Get-AzsAzureBridgeProduct, Invoke-AzsAzureBridgeProductDo...
Script     0.3.1      Azs.Backup.Admin                    {Get-AzsBackupConfiguration, Get-AzsBackup, Restore-AzsBac...
Script     0.2.2      Azs.Commerce.Admin                  Get-AzsSubscriberUsage
Script     0.2.3      Azs.Compute.Admin                   {Get-AzsVMExtension, Remove-AzsComputeQuota, New-AzsComput...
Script     0.4.1      Azs.Fabric.Admin                    {Restart-AzsInfrastructureRoleInstance, Get-AzsScaleUnitNo...
Script     0.2.2      Azs.Gallery.Admin                   {Add-AzsGalleryItem, Remove-AzsGalleryItem, Get-AzsGallery...
Script     0.3.2      Azs.Infrastructureinsights.Admin    {Close-AzsAlert, Get-AzsRegionHealth, Get-AzsAlert, Get-Az...
Script     0.2.2      Azs.Keyvault.Admin                  Get-AzsKeyVaultQuota
Script     0.2.2      Azs.Network.Admin                   {Get-AzsNetworkQuota, New-AzsNetworkQuota, Get-AzsNetworkA...
Script     0.2.3      Azs.Storage.Admin                   {Restore-AzsStorageAccount, New-AzsStorageQuota, Get-AzsSt...
Script     0.2.2      Azs.Subscriptions                   {Get-AzsDelegatedProviderOffer, Remove-AzsSubscription, Ge...
Script     0.3.3      Azs.Subscriptions.Admin             {Add-AzsPlanToOffer, Get-AzsSubscriptionsQuota, Get-AzsDel...
Script     0.2.3      Azs.Update.Admin                    {Resume-AzsUpdateRun, Get-AzsUpdateLocation, Get-AzsUpdate...
  
PS C:\windows\system32>
```

## Step 5: Modify host file for endpoint name resolution 

You will now add the Azure consistent VIP that you defined on the local web UI of device to:

- The host file on the client, OR
- The host file on the DNS server

> [!IMPORTANT]
> We recommend that you modify the host file on the DNS server for endpoint name resolution.

On your Windows client that you are using to connect to the device, take the following steps:

1. Start **Notepad** as an administrator, and then open the **hosts** file located at C:\Windows\System32\Drivers\etc.

    ![Windows Explorer hosts file](media/azure-stack-edge-r-series-connect-resource-manager/hosts-file.png)

2. Add the following entries to your **hosts** file replacing with appropriate values for your device: 

    ```
    <Azure consistent services VIP> login.<appliance name>.<DNS domain>
    <Azure consistent services VIP> management.<appliance name>.<DNS domain>
    <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > The entry in the hosts file should match exactly that provided to connect to Azure Resource Manager at a later step. Make sure that the DNS Domain entry here is all in the lowercase.

    You saved the Azure consistent services VIP from the local web UI in an earlier step.

    The login.\<appliance name\>.\<DNS domain\> entry is the endpoint for Security Token Service (STS). STS is responsible for creation, validation, renewal, and cancellation of security tokens. The security token service is used to create the access token and refresh token that are used for continuous communication between the device and the client.

3. For reference, use the following image. Save the **hosts** file.

    ![hosts file in Notepad](media/azure-stack-edge-r-series-connect-resource-manager/hosts-file-notepad.png)

## Step 6: Verify endpoint name resolution on the client

Check if the endpoint name is resolved on the client that you are using to connect to the Azure consistent VIP.

1. You can use the ping.exe command-line utility to check that the endpoint name is resolved. Given an IP address, the ping command will return the TCP/IP host name of the computer you\'re tracing.

    Add the -a switch to the command line as shown in the example below. If the host name is returnable, it will also return this potentially valuable information in the reply.

    ![Ping in command prompt](media/azure-stack-edge-r-series-connect-resource-manager/ping-command-prompt.png)

2. You can use the Add-AzureRmEnvironment cmdlet to further ensure that the communication via Azure Resource Manager is working properly and the API calls are going through the port dedicated for Azure Resource Manager, 30005.

    The Add-AzureRmEnvironment cmdlet adds endpoints and metadata to enable Azure Resource Manager cmdlets to connect with a new instance of Azure Resource Manager. A sample output is shown below:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ResourceManagerEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com:30005 -ActiveDirectoryEndpoint https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/ -ActiveDirectoryServiceEndpointResourceId https://management.dbe-n6hugc2ra.microsoftdatabox.com:30005/ -EnableAdfsAuthentication
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https:// management.dbe-n6hugc2ra.microsoftdatabox.com:30005 https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```
    
## Step 7: Use Azure PowerShell cmdlets to create a VM (client)

After you have verified that your device to client communication via Azure Resource Manager is working fine, take the following steps to create and deploy a VM on your device:


1. Use the Add-AzureRmEnvironment cmdlet to further ensure that the communication via Azure Resource Manager is working properly and the API calls are going through the port dedicated for Azure Resource Manager - 30005.

The Add-AzureRmEnvironment cmdlet adds endpoints and metadata to enable Azure Resource Manager cmdlets to connect with a new instance of Azure Resource Manager. 

> [!IMPORTANT]
> The Azure Resource Manager endpoint URL that you provide in the following cmdlet is case-sensitive. Make sure the endpoint URL is all in lowercase and matches what you provided in the hosts file. If the case doesn't match, then you will see an error.

```powershell
Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>:30005/
```

A sample output is shown below:

```powershell
PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com:30005/

Name  Resource Manager Url                    ActiveDirectory Authority
----  --------------------                   -------------------------
AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com:30005 https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
```

2. Call local device APIs to authenticate the connections to Azure Resource Mananger. 

1. These credentials are for a local machine account and are solely used for API access.

2. You can connect via login-AzureRMAccount or via Connect-AzureRMAccount command. Provide the following information when you see a login window:

    a. Username -- *EdgeARMuser*

    b. Password - *Password1*

To sign in, type the following command:

```powershell
login-AzureRMAccount -EnvironmentName <Environment Name>

PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE

Account         SubscriptionName  TenantId              Environment
-------         ----------------  --------              -------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
PS C:\Users\Administrator>
```

An alternative way to log in is to use the Connect-AzureRmAccount cmdlet. Here is a sample output of the command. The tenant ID in this instance is hard coded - c0257de7-538f-415c-993a-1b87a031879d.

```powershell
PS C:\windows\system32> $pass = ConvertTo-SecureString "Password1" -AsPlainText -Force;
PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred

Account       SubscriptionName   TenantId            Environment
-------       ----------------   --------            -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE

PS C:\windows\system32>
```

> [!IMPORTANT]
> You are logged out of this connection to Azure Resource Manager every couple of hours. If this happens, any cmdlets that you execute, will return error messages to the effect that you are not connected to Azure anymore. You will need to sign in again.

## Next steps

Deploy VMs on your Azure Stack Edge device