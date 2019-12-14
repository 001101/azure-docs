---
title: Deploy a management tool for Windows Virtual Desktop using service principal - Azure
description: How to deploy the management tool for Windows Virtual Desktop using PowerShell.
services: virtual-desktop
author: Heidilohr

ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: helohr
---

# Deploy a management tool using PowerShell

The management tool provides a user interface (UI) for managing Microsoft Virtual Desktop resources. In these instructions, you'll learn how to deploy the management tool using PowerShell and connect to the management tool.

## Important considerations

Each Azure Active Directory (Azure AD) tenant's subscription will need its own separate deployment of the management tool. This tool doesn't support Azure AD Business-to-Business (B2B) scenarios. 

This management tool is a sample. Microsoft will provide important security and quality updates. The [source code is available in GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Customers and partners are encouraged to customize the tool to fit their business needs.

To following browsers are compatible with the management tool:
- Google Chrome 68 or later
- Microsoft Edge 40.15063 or later
- Mozilla Firefox 52.0 or later
- Safari 10 or later (macOS only)

## Pre-requisites

Before deploying the management tool, you'll need an Azure Active Directory user to create an app registration and deploy the management UI. This user must:

- Have permission to create resources in your Azure subscription
- Have permission to create an Azure AD application. Follow these steps to check if your user has the [required permissions](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

To complete the deployment and configuration steps in this article, download the following PowerShell scripts from the [RDS-Templates GitHub repo](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) and place them in the same folder on your local machine:
  - **createWvdMgmtUxAppRegistration.ps1**
  - **updateWvdMgmtUxApiUrl.ps1** 

After deploying and configuring the management tool, you'll want to launch the management UI to validate. This user must:
- Have a role assignment to view or edit your Windows Virtual Desktop tenant

## Setting up PowerShell

To login to both the Az and AzureAD PowerShell modules in preparation for the remaining PowerShell steps:

1. Open PowerShell as an Administrator and navigate to the directory where you saved the PowerShell scripts.
2. Sign in to Azure with an account that has Owner or Contributor permissions on the Azure subscription you would like to use for the diagnostics tool:
    ```powershell
    Login-AzAccount
    ```
3. Sign in to Azure AD with the same account:
    ```powershell
    Connect-AzureAD
    ```
4. Navigate to the folder where you saved the two PowerShell scripts from the RDS-Templates GitHub repo.

Keep this PowerShell window open to run all of the PowerShell commands.

## Create an Azure Active Directory app registration

Run the following PowerShell commands to create the app registration with the required API permissions:
```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration"
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool"

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```
Now that you've completed the Azure AD app registration, you can deploy the management tool.

## Deploy the management tool

Run the following PowerShell commands to deploy the management tool and associate it with the service principal you just created:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

Select-AzSubscription -SubscriptionId $subscriptionId
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -isServicePrincipal $true `
    -azureAdminUserPrincipalNameOrApplicationId $servicePrincipalCredentials.UserName `
    -azureAdminPassword $servicePrincipalCredentials.Password `
    -applicationName $appName
```
After you've created the web app, you must add a redirect URI to the Azure AD application to successfully login users.

## Set the Redirect URI

Run the following PowerShell commands to retrieve the web app URL and set it as the authentication redirect URI (also called a reply URL):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
$azureADApplication = Get-AzureADApplication | where { $_.AppId -eq $servicePrincipalCredentials.UserName } 
Get-AzureADApplication | where { $_.AppId -eq $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```
Now that you've added a redirect URI for user login, you must update the API URL so the management tool UI can interact with the API backend service.

## Update the API URL for the web application

Run the following commands to update the API URL configuration in the web application front end:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```
Now that you've fully configured the management tool web app, it's time to verify the Azure AD application and provide consent.

## Verify the Azure AD application and provide consent

To verify the Azure AD application configuration and provide consent:

1. Open your internet browser and sign in to the [Azure portal](https://portal.azure.com/) with your administrative account.
2. From the search bar at the top of the Azure portal, search for **App registrations** and select the item under **Services**.
3. Select **All applications** and search for it using the unique app name you provided for the PowerShell script.
4. In the left panel, select **Authentication** and verify that the redirect URI is the same as the web app URL for the management tool.
   ![The authentication page with the entered redirect URI](media/management-ui-redirect-uri.png)
5. In the left panel, select **API permissions** to confirm that permissions were added. If you're a global admin, click the button and follow the dialog prompts to provide admin consent for your organization.
    ![The API permissions page](media/management-ui-permissions.png)

You can now start using the management tool.

## Use the management tool

You can now access the management tool at any time. Follow these instructions to launch the tool:

1. In a web browser, enter the URL of the web app, for example <https://wvdmgmt20200101.azurewebsites.net>. If you don't remember it, sign in to Azure, find the app service you deployed, then click on the URL.
2. Sign in using your Windows Virtual Desktop credentials.
   > [!NOTE]
   > If you were unable to grant admin consent earlier in these steps, each user who logs in will be need to provide their own user consent to use the tool.
3. When prompted to choose a Tenant Group, select **Default Tenant Group** from the drop-down list.
4. When you select Default Tenant Group, a menu should appear on the left side of your window. In this menu, find the name of your tenant group and select it.
   > [!NOTE]
   > If you have a custom Tenant Group, enter the name manually instead of choosing from the drop-down list.

## Report issues

If you come across any issues with the management tool or other Windows Virtual Desktop tools, follow the directions in [ARM Templates for Remote Desktop Services](https://github.com/Azure/RDS-Templates/blob/master/README.md) to report them on GitHub.

## Next steps

Now that you've learned how to deploy and connect to the management tool, you can learn how to use Azure Service Health to monitor service issues and health advisories.

* [Set up service alerts tutorial](./set-up-service-alerts.md)