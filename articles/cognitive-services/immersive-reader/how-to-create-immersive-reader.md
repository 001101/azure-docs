---
title: "Create an Immersive Reader Resource"
titleSuffix: Azure Cognitive Services
description: This article will show you how to create a new Immersive Reader resource with a custom subdomain and then configure Azure AD in your Azure tenant.
services: cognitive-services
author: rwaller
manager: guillasi

ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
---

# Create an Immersive Reader resource and configure Azure Active Directory authentication

This article shows how to create an Immersive Reader resource and configure it with Azure Active Directory authentication.

## Set up Powershell environment

1. Start by opening the [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Ensure that the cloud shell is set to PowerShell.

1. Copy and paste the following code snippet into the shell.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true)] [String] $subscriptionName,
        [Parameter(Mandatory=$true)] [String] $resourceGroupName,
        [Parameter(Mandatory=$false)] [String] $resourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $resourceName,
        [Parameter(Mandatory=$true)] [String] $resourceLocation,
        [Parameter(Mandatory=$true)] [String] $resourceSku,
        [Parameter(Mandatory=$true)] [String] $subdomain,
        [Parameter(Mandatory=$true)] [String] $clientSecret
    )
    {
        Write-Host "Setting the active subscription to '$subscriptionName'"
        az account set --subscription $subscriptionName

        Write-Host "Checking if a resource with the name '$resourceName' already exists"
        $resourceId = az cognitiveservices account show --name $resourceName --resource-group $resourceGroupName --query "id" -o tsv
        if ($resourceId) {
            throw "Error: Resource with the name '$resourceName' already exists"
        }

        Write-Host "Checking if the resource group '$resourceGroupName' already exists"
        $resourceGroupExists = az group exists --name $resourceGroupName
        if ($resourceGroupExists -eq "true") {
            Write-Host "Resource group exists"
        } else {
            Write-Host "Resource group does not exist. Creating resource group"
            az group create --name $resourceGroupName --location $resourceGroupLocation | Out-Null
        }

        Write-Host "Creating the new Immersive Reader resource '$resourceName' (SKU '$resourceSku') in '$resourceLocation' with subdomain '$subdomain'"
        $resourceId = az cognitiveservices account create `
                        --name $resourceName `
                        --resource-group $resourceGroupName `
                        --kind ImmersiveReader `
                        --sku $resourceSku `
                        --location $resourceLocation `
                        --custom-domain $subdomain `
                        --query "id" `
                        -o tsv

        if (-not $resourceId) {
            throw "Error: Failed to create Immersive Reader resource"
        }
        Write-Host "Immersive Reader resource created successfully"

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id "http://ImmersiveReaderAAD" --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $secureClientSecret = ConvertTo-SecureString -String $clientSecret -AsPlainText -Force
            $adApp = az ad app create --password $secureClientSecret --display-name "ImmersiveReaderAAD" --identifier-uris "http://ImmersiveReaderAAD"
            $clientId = $adApp.ApplicationId
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id "http://ImmersiveReaderAAD" --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            $principal = az ad sp create --id $clientId
            $principalId = $principal.Id
        }

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User" | Out-Null

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $clientSecret
        $result.Subdomain = $subdomain

        Write-Host "All set! Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Run the function `Create-ImmersiveReaderResource`, supplying the parameters as appropriate.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource <SUBSCRIPTION_NAME> <RESOURCE_GROUP_NAME> <RESOURCE_GROUP_LOCATION> <RESOURCE_NAME> <RESOURCE_LOCATION> <RESOURCE_SKU> <CUSTOM_SUBDOMAIN> <CLIENT_SECRET>
    ```

    >[!NOTE]
    > Use [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) to get your subscription name.
    >
    > If the specified resource group does not exist, it will be created. If the specified resource group already exists, the resource group location parameter will be ignored.
    >
    > The resource location can be any of the following: `eastus`, `eastus2`, `southcentralus`, `westus`, `westus2`, `australiaeast`, `southeastasia`, `centralindia`, `japaneast`, `northeurope`, `uksouth`, `westeurope`.
    >
    > The resource SKU can be `S0` or `S1`.
    >
    > The custom subdomain needs to be globally unique and cannot include special characters, such as: `.`, `!`, `,`.

1. Copy the JSON output into a text file for later use. The output should look like the following.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## Next steps

* View the [Node.js tutorial](./tutorial-nodejs.md) to see what else you can do with the Immersive Reader SDK using Node.js
* View the [Python tutorial](./tutorial-python.md) to see what else you can do with the Immersive Reader SDK using Python
* View the [Swift tutorial](./tutorial-ios-picture-immersive-reader.md) to see what else you can do with the Immersive Reader SDK using Swift
* Explore the [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) and the [Immersive Reader SDK Reference](./reference.md)




