---
title: Send an email from an Azure Automation runbook
description: Learn how to use SendGrid to send an email from within a runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
---
# Tutorial: Send an email from an Azure Automation runbook

You can send an email from a runbook with [SendGrid](https://sendgrid.com/solutions) using
PowerShell. This tutorial will show you how to create a reusable runbook that sends an email using
an API key stored in [Azure KeyVault](/azure/key-vault/).

In this tutorial, you learn how to:

> [!div class="checklist"]
>
> * Create an Azure KeyVault
> * Store your SendGrid API key in KeyVault
> * Create a runbook that retrieves your API key and sends an email

## Prerequisites

To complete this tutorial, the following are required:

* Azure subscription: If you don't have one yet, you can
  [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)
  or sign up for a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Create a SendGrid Account](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Automation account](automation-offering-get-started.md) and [Run As connection](automation-create-runas-account.md) to store and execute the runbook.

## Create an Azure KeyVault

You can create an Azure KeyVault using the following PowerShell script. Replace the variable
values with values specific to your environment. Use the embedded Azure Cloud Shell via the <kbd>Try It</kbd> button, located in the top right corner of the code block. You can also copy and run the code locally if you have the [Azure PowerShell Module](/powershell/azure/install-az-ps) installed on your local machine.

> [!NOTE]
> To retrieve your API key, use the steps found in [Find your SendGrid API key](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Login-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzureRmAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzureRmKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

For other ways to create an Azure KeyVault and store a secret, see [KeyVault Quickstarts](/azure/key-vault/).

## Import required modules to your Automation Account

To use Azure KeyVault within a runbook, you need to add the following two modules to your
Automation Account in the order listed below:

1. [AzureRM.Profile](https://www.powershellgallery.com/packages/AzureRM.profile).
2. [AzureRM.KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault).

Click <kbd>Deploy to Azure Automation</kbd> on the Azure Automation tab under Installation Options. This action opens up the Azure portal. On the Import page, select your Automation Account and click <kbd>OK</kbd>.

For additional methods for adding the required modules, see [Integration Modules](automation-integration-modules.md).

## Create the runbook to send an email

After you have created a KeyVault and stored your SendGrid API key, it's time to create the runbook
that will retrieve the API key and send an email.

This runbook uses the AzureRunAsConnection [Run As account](automation-create-runas-account.md) to
authenticate with Azure to retrieve the secret from Azure KeyVault.

Use this example to create a runbook called **Send-GridMailMessage**. You can modify the PowerShell script, and reuse
it for different scenarios.

1. Go to your Azure Automation account.
2. Under **Process Automation**, select **Runbooks**.
3. At the top of the list of runbooks, select **+ Create a runbook**.
4. On the **Add Runbook** page, enter **Send-GridMailMessage** for the runbook name. For the runbook type, select **PowerShell**. Then, select **Create**.
   ![Create Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. The runbook is created and the **Edit PowerShell Runbook** page opens.
   ![Edit the Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Copy the following PowerShell example into the **Edit** page. Ensure that the `$VaultName` is the name you specified when
   you created your KeyVault.

    > [!NOTE]
    > In the new **Az** modules, `Get-AzureKeyVaultSecret` is an alias for `Get-AzKeyVaultSecret`.
    > To enable aliases for **AzureRM** cmdlets, use [Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias?/view). You can also update your Automation Account with the newest modules by following the steps in
    > [How to update Azure PowerShell modules in Azure Automation](automation-update-azure-modules.md).

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    # If your automation account uses the Az modules, change this to Get-AzureKeyVaultSecret
    $SENDGRID_API_KEY = (Get-AzureKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Select **Publish** to save and publish the runbook.

To verify that the runbook executes successfully you can follow the steps under [Test a runbook](manage-runbooks.md#test-a-runbook) or [Start a runbook](start-runbooks.md).
If you do not initially see your test email, check your **Junk** and **Spam** folders.

## Clean Up

When no longer needed, delete the runbook. To do so, select the runbook in the runbook list, and click **Delete**.

Delete the key vault by using the `az keyvault delete` command:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## Next steps

* For issues creating or starting your runbook, see [Troubleshoot errors with runbooks](./troubleshoot/runbooks.md).
* For details about different ways to start a runbook, see [Starting a runbook](automation-starting-a-runbook.md).
* To monitor runbook execution, see [Forward job status and job streams from Automation to Azure Monitor logs](automation-manage-send-joblogs-log-analytics.md).
* To trigger a runbook using an alert, see [Use an alert to trigger an Azure Automation runbook](automation-create-alert-triggered-runbook.md).
