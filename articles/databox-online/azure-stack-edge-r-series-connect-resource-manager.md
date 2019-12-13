---
title: Connect to Azure Resource Manager on your Azure Stack Edge device
description: Describes how to connect to the Azure Resource Manager running on your Azure Stack Edge using Azure PowerShell.
services: databox
author: alkohli

ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 12/11/2019
ms.author: alkohli
#Customer intent: As an IT admin, I need to understand how to connect to Azure Resource Manager on my Azure Stack Edge device so that I can manage resources.
---

# Connect to Azure Resource Manager on your Azure Stack Edge device

Azure Resource Manager provides a management layer that enables you to create, update, and delete resources in your Azure subscription. The Azure Stack Edge device supports the same Azure Resource Manager APIs to create, update, and delete VMs in a local subscription. This support lets you manage the device in a manner consistent with the cloud. 

This tutorial describes how to connect to the local APIs on your Azure Stack Edge device via Azure Resource Manager using Azure PowerShell.

## Endpoints on Azure Stack Edge device

The following table summarizes the various endpoints exposed on your device, the supported protocols, and the ports to access those endpoints. Throughout the article, you will find references to these endpoints.

| # | Endpoint | Supported protocols | Port used | Used for |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 30005 | To connect to Azure Resource Manager for automation |
| 2. | Security token service | https | 443 | To authenticate via access and refresh tokens |
| 3. | Blob | https | 443 | To connect to Blob storage via REST |


## Connecting to Azure Resource Manager workflow

The process of connecting to local APIs of the device using Azure Resource Manager requires the following steps:

| Step # | You'll do this step ... | .. on this location. |
| --- | --- | --- |
| 1. | [Configure your Azure Stack Edge device](#step-1-configure-azure-stack-edge-device) | Local web UI |
| 2. | [Create and install certificates](#step-2-create-and-install-certificates) | Windows client/local web UI |
| 3. | [Review and configure the prerequisites](#step-3-install-powershell-on-the-client) | Windows client |
| 4. | [Set up Azure PowerShell on the client](#step-4-set-up-azure-powershell-on-the-client) | Windows client |
| 5. | [Modify host file for endpoint name resolution](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows client or DNS server |
| 6. | [Check that the endpoint name is resolved](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows client |
| 7. | [Set Azure Resource Manager password](#step-7-set-azure-resource-manager-password) | Windows client |
| 8. | [Use Azure PowerShell cmdlets to verify connection to Azure Resource Manager](#step-8-set-azure-resource-manager-environment) | Windows client |

The following sections detail each of the above steps in connecting to Azure Resource Manager.

## Step 1: Configure Azure Stack Edge device 

Take the following steps in the local web UI of your Azure Stack Edge device.

1. Complete the network settings, web proxy settings (optional), and time settings (optional). In this example, **Port 6** network interface settings are configured.

    ![Azure Stack Edge network settings page](media/azure-stack-edge-r-series-connect-resource-manager/network-settings.png)

    Select the network interface name, in this case **Port 6** to view the network settings for this interface.

    ![Azure Stack Edge network settings page](media/azure-stack-edge-r-series-connect-resource-manager/network-settings-port6.png)

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


## Step 2: Create and install certificates

Certificates ensure that your communication is trusted. On your Azure Stack Edge device, self-signed appliance, blob, and Azure Resource Manager certificates are automatically generated. Optionally, you can bring in your own signed blob and Azure Resource Manager certificates as well.

When you bring in a signed certificate of your own, you also need the corresponding signing chain of the certificate. For the signing chain, Azure Resource Manager, and the blob certificates on the device, you will need the corresponding certificates on the client machine also to authenticate and communicate with the device.

To connect to Azure Resource Manager, you will need to create or get signing chain and endpoint certificates, import these certificates on your Windows client, and finally upload these certificates on the device.

### Create certificates (Optional)

For test and development use only, you can use Windows PowerShell to create certificates on your local system. While creating the certificates for the client, follow these guidelines:

1. You first need to create a root certificate for the signing chain. For more information, see See steps to [Create signing chain certificates](azure-stack-edge-r-series-manage-certificates.md#create-signing-chain-certificate).

2. You can next create the endpoint certificates for the blob and Azure Resource Manager. See the steps to [Create endpoint certificates](azure-stack-edge-r-series-manage-certificates.md#create-signed-endpoint-certificates).

3. For all these certificates, make sure that the subject name and subject alternate name conform to the following guidelines:

    |Type |Subject name (SN)  |Subject alternative name (SAN)  |Subject name example |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Multi-SAN single certificate for both endpoints|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

For more information on certificates, go to how to [Manage certificates](azure-stack-edge-r-series-manage-certificates.md).


### Upload certificates on the device

The certificates that you created in the previous step will be in the Personal store on your client. These certificates need to be exported on your client into appropriate format files that can then be uploaded to your device.

1. The root certificate must be exported as a DER format file with *.cer* file extension. For detailed steps, see [Export certificates as a .cer format file](azure-stack-edge-r-series-manage-certificates.md#export-certificates-as-der-format).

2. The endpoint certificates must be exported as *.pfx* files with private keys. For detailed steps, see [Export certificates as .pfx file with private keys](azure-stack-edge-r-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

3. The root and endpoint certificates are then uploaded on the device using the **+Add certificate** option on the **Certificates** page in the local web UI. To upload the certificates, follow the steps in [Upload certificates](azure-stack-edge-r-series-manage-certificates.md#upload-certificates).


### Import certificates on the client running Azure PowerShell

The Windows client where you will invoke the Azure Resource Manager APIs needs to establish trust with the device. To this end, the certificates that you created in the previous step must be imported on your Windows client into the appropriate certificate store.

1. The root certificate that you exported as the DER format with*.cer* extension should now be imported in the Trusted Root Certificate Authorities on your client system. For detailed steps, see [Import certificates into the Trusted Root Certificate Authorities store.](azure-stack-edge-r-series-manage-certificates.md#import-certificates-as-der-format)

2. The endpoint certificates that you exported as the *.pfx* must be exported as *.cer*. This *.cer* is then imported in the **Personal** certificate store on your system. For detailed steps, see [Import certificates into personal store](azure-stack-edge-r-series-manage-certificates.md#import-certificates-as-der-format).

## Step 3: Install PowerShell on the client 

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

## Step 4: Set up Azure PowerShell on the client 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. You will install Azure PowerShell modules on your client that will work with your device.

    a. Run PowerShell as an administrator. You need access to PowerShell gallery. 


    b. To install the required Azure PowerShell modules from the PowerShell Gallery, run the following command:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Make sure that you have Azure-RM module version 2.5.0 running at the end of the installation. 
    If you have an existing version of Azure-RM module that does not match the required version, uninstall using the following command:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    You will now need to install the required version again.
   

A sample output is shown below that indicates the AzureRM version 2.5.0 modules were installed successfully.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
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

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## Step 5: Modify host file for endpoint name resolution 

You will now add the Azure consistent VIP that you defined on the local web UI of device to:

- The host file on the client, OR,
- The DNS server configuration

> [!IMPORTANT]
> We recommend that you modify the the DNS server configuration for endpoint name resolution.

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

    Add the `-a` switch to the command line as shown in the example below. If the host name is returnable, it will also return this potentially valuable information in the reply.

    ![Ping in command prompt](media/azure-stack-edge-r-series-connect-resource-manager/ping-command-prompt.png)


## Step 7: Set Azure Resource Manager password

Take the following steps to set a new password for the user trying to log into Azure Resource Manager. 

Use the PowerShell option in Azure CLI to run the following cmdlets. <!--need to add the version of Azure CLI>

1. Set the tenant, subscription, and environment for cmdlets to use in the current session using Set-AzContext. 

    `Set-AzureContext`

    Here is the sample output.

    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
       
    Name    Account             SubscriptionName    Environment     TenantId
    ----    -------             ----------------    -----------     -------
    Work    test@outlook.com    Subscription1       AzureCloud      xxxxxxxx
    ```


2. Get the SDK encryption key from the Azure portal. 

    1. In the Azure portal, go to your Azure Stack Edge resource and then go to **Device properties**. 

    2. Copy the Encryption Key. You will use this key later.
 

3. Set the password to connect to the device local APIs via Azure Resource Manager. Type:

    `Execute Set-AzDataBoxEdgeUser`

    The password and encryption key parameters must be passed as secure strings. Use the following cmdlets to convert the password and encryption key to secure strings. 

    
    ```azurepowershell
    $pass = ConvertTo-SecureString “Password1” -AsPlainText -Force
    $key = ConvertTo-SecureString “xxxxxxxxxxxxxxxxxx” -AsPlainText -Force
    ```

    Use the above generated secure strings as parameters in the Execute Set-AzDataBoxEdgeUser cmdlet to reset the password. Here is the sample output.

    
    ```azurepowershell
    PS C:\> Set-AzDataBoxEdgeUser -ResourceGroupName sampleRGName -DeviceName SampleDeviceName -Name EdgeARMUser  -Password $pass -EncryptionKey $key
       
        User name   Type ResourceGroupName DeviceName
        ---------   ---- ----------------- ----------
        EdgeARMUser ARM   sampleRGName     SampleDeviceName
    ```


## Step 8: Set Azure Resource Manager environment

Set the Azure Resource Manager environment and verify that your device to client communication via Azure Resource Manager is working fine. Take the following steps for this verification:


1. Use the `Add-AzureRmEnvironment` cmdlet to further ensure that the communication via Azure Resource Manager is working properly and the API calls are going through the port dedicated for Azure Resource Manager - 30005.

    The `Add-AzureRmEnvironment` cmdlet adds endpoints and metadata to enable Azure Resource Manager cmdlets to connect with a new instance of Azure Resource Manager. 


    > [!IMPORTANT]
    > The Azure Resource Manager endpoint URL that you provide in the following cmdlet is case-sensitive. Make sure the endpoint URL is all in lowercase and matches what you provided in the hosts file. If the case doesn't match, then you will see an error.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>:30005/"
    ```

    A sample output is shown below:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com:30005/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com:30005 https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Set the environment as Azure Stack Edge and the port to be used for Azure Resource Manager calls as 30005. You define the environment in two ways:

    - Set the environment. Type the following command:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
        For more information, go to [Set-AzureRMEnvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0).

    - Define the environment inline for every cmdlet that you execute. This ensures that all the API calls are going through the correct environment. By default, the calls would go through the Azure public but you want these to go through the environment that you set for Azure Stack Edge device.

2. Call local device APIs to authenticate the connections to Azure Resource Manager. 

    1. These credentials are for a local machine account and are solely used for API access.

    2. You can connect via `login-AzureRMAccount` or via `Connect-AzureRMAccount` command. 

        1. To sign in, type the following command. The tenant ID in this instance is hard coded - c0257de7-538f-415c-993a-1b87a031879d. Use the following username and password.

            - **Username** - *EdgeArmUser*

            - **Password** - You set the password for Azure Resource Manager in an earlier step. Use that password to sign in. The default password is *Password1*. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            An alternative way to log in is to use the `login-AzureRmAccount` cmdlet. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>`

            Here is a sample output of the command. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> The connection to Azure Resource Manager expires every 1.5 hours or if your Azure Stack Edge device restarts. If this happens, any cmdlets that you execute, will return error messages to the effect that you are not connected to Azure anymore. You will need to sign in again.

  


## Next steps

[Deploy VMs on your Azure Stack Edge device](azure-stack-edge-r-series-deploy-vm-powershell.md).