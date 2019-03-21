---
title: Onboard a customer to Azure Delegated Resource Management
description: Learn how to onboard a customer to Azure Delegated Resource Management, allowing their resources to be accessed and managed through your own tenant. 
author: JnHs
ms.author: jenhayes
ms.service: service-provider-toolkit
ms.date: 04/03/2019
ms.topic: overview
manager: carmonm
---
# Onboard a customer to Azure Delegated Resource Management

This article explains how you, as a service provider, can onboard a customer to Azure Delegated Resource Management, allowing their resources to be accessed and managed through your own Azure AD tenant. While we’ll refer to service providers and customers here, enterprises managing multiple tenants can use the same process to consolidate their management experience.

You can repeat this process if you are managing resources for multiple customers. Then, when an authorized user signs in to your tenant, that user can be authorized across customer tenancy scopes to perform management operations without having to log in to every individual customer tenant.

> [!NOTE]
> Customers can be onboarded automatically when they purchase a managed services offer that you published to Azure Marketplace. For more info, see [Publish Managed Services offers to Azure Marketplace](publish-managed-services-offers.md).

The onboarding process requires actions to be taken from within both the service provider’s tenant and from the customer’s tenant. All of these steps are described in this article.

> [!IMPORTANT]
> If a customer has deployed any Azure managed applications to a subscription, that subscription can't be onboarded for Azure Delegated Resource Management at this time.

## Gather tenant and subscription details

To onboard a customer’s tenant, it must have an active Azure subscription. You’ll need to know the following:
- The tenant ID of the service provider’s tenant (where you will be managing the customer’s resources)
- The tenant ID of the customer’s tenant (which will have resources managed by the service provider)
- The subscription IDs for each specific subscription in the customer’s tenant that will be managed by the service provider

If you don’t have this info already, you can retrieve it in one of the following ways.

### PowerShell

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -subscriptionId <subscriptionId>
```

### Azure CLI

```azurecli
az login -u <username> -p <password>
az account set --subscription <subscriptionId/name>
az account show 
```

## Confirm that the customer's subscription is ready for onboarding

During the limited preview period, each subscription must be authorized for onboarding. When a subscription is authorized, the **Microsoft.ManagedServices** resource provider is registered for that subscription. You can confirm that this is the case (from within the customer's tenant) in one of the following ways.

### Azure portal

1. In the Azure portal, select the subscription.
2. Select **Resource providers**.
3. Confirm that **Microsoft.ManagedServices** shows as **Registered**.

### PowerShell

```powershell
Select-AzureRmSubscription -SubscriptionId <subscriptionId>
Get-AzureRmResourceProvider -ProviderNameSpace "Microsoft.ManagedServices"
```
This should return results similar to the following:

```powershell
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### Azure CLI

```azurecli
az account set –subscription <subscriptionId>
az provider show –namespace "Microsoft.ManagedServices" –-output table
```

This should return results similar to the following:

```azurecli
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## Define roles and permissions

As a service provider, you may have multiple offers with a single customer, requiring different access for different customer scopes. 

To make management easier, we recommend using Azure AD user groups for each role, allowing you to add or remove individual users to the group rather than assigning permissions directly to that user. You may also want to assign roles to a service principal. Be sure to follow the principle of least privilege so that users only have the permissions needed to complete their job, helping to reduce the chance of inadvertent errors.

For example, you may want to use a structure like this:


|Group name  |Type  |objectId/appId  |Role definition  |Role definition ID  |
|---------|---------|---------|---------|---------|
|Architects     |User group         |\<objectId\>         |Contributor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Assessment     |User group         |\<objectId\>         |Reader         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM Specialists     |User group         |\<objectId\>         |VM Contributor         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Service principal name (SPN)         |\<objectId\>         |Contributor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

You’ll need to have these ID values ready in order to define authorizations. If you don’t have them already, you can retrieve them in one of the following ways.

### PowerShell

```powershell
# To retrieve the objectId for an Azure AD group
(Get-AzureRmADGroup -DisplayName "<yourGroupName> ").id

# To retrieve the objectId for an Azure AD user
(Get-AzureRmADUser -UserPrincipalName "<yourUPN> ").id

# To retrieve the objectId for an SPN
(Get-AzureRmADAppliaction -DisplayName "<appDisplayName>").objectId

# To retrieve role definition IDs
(Get-AzureRmRoleDefinition -Name <roleName>).id
```

### Azure CLI

```azurecli
# To retrieve the objectId for an Azure AD group
az ad group list –-query "[?displayName == '<yourGroupName>'].objectId" –-output tsv

# To retrieve the objectId for an Azure AD user
az ad user show –-upn-or-object-id "<yourUPN>" –-query "objectId " –-output tsv

# To retrieve the objectId for an SPN
az ad sp list –-query "[?displayName == '<spDisplayName>'].objectId" –-output tsv

# To retrieve role definition IDs
az role definition list –-name "<roleName>" | grep name
```

## Create an Azure Resource Manager Template

To onboard your customer, you’ll need to create an [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) template that includes the following:


|Field  |Definition  |
|---------|---------|
|**mspName**     |Service provider name         |
|**description**     |A brief description of your offer (for example, “Contoso VM management offer”)      |
|**managedByTenantId**     |Your tenant ID         |
|**authorizations**     |The **principalId** values for the users/groups/SPNs from your tenant, each mapped to a built-in **roleDefinitionId** value to specify the level of access         |

Use the Azure Resource Manager template that we provide, along with a parameter file that you modify to match your configuration and define your authorizations.

The following example shows a modified parameter file.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
            "value": "Contoso MSP"
        },
        "mspOfferDescription": {
            "value": "Contoso VM management offer"
        },
        "managedByTenantId": {
            "value": "b2a0bb8e-3f26-47f8-9040-209289b412a8"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "9ecfa873-0557-4d13-a6e2-92bbb9ac8dc7",
                    "roleDefinitionId": "e7975a8b-bc78-4af0-a2ed-e42e1ce0a888"
                },
                {
                    "principalId": "9d56d1a0-e255-4e75-be6b-271b5342c099",
                    "roleDefinitionId": "5fdebb7d-8ac0-4e7c-9b07-92da805136a9"
                }
            ]
        }
    }
}
```

## Deploy the Azure Resource Manager templates

Once you have updated your parameter file, you must deploy the Resource Management template in the customer’s tenant. This must be done by a user with authorization to change role assignments for the subscriptions you’re onboarding, and the template must be deployed as a subscription-level deployment.

> [!IMPORTANT]
> A separate deployment is needed for each subscription that you want to onboard to Azure Delegated Resource Management.

```powershell
# Deploy ARM template using template and parameter file locally
New-AzureRmDeployment -Name <deploymentName> `
                      -Location <AzureRegion> `
                      -TemplateFile <pathToTemplateFile> `
                      -TemplateParameterFile <pathToParameterFile> `
                      -Verbose

# Deploy ARM template that is located externally
New-AzureRmDeployment -Name <deploymentName> `
                      -Location <AzureRegion> `
                      -TemplateUri <templateUri> `
                      -TemplateParameterUri <parameterUri> `
                      -Verbose
```

### Azure CLI

```azurecli
# Deploy ARM template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external ARM template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## Confirm successful onboarding

When a customer subscription has successfully been onboarded to Azure Delegated Resource Management, users in the service provider’s tenant will be able to see the subscription and its resources (if they have the appropriate permissions). To confirm this, check to make sure the subscription appears in one of the following ways.

### Azure portal

In the service provider’s tenant:
1. Open the **My customers** blade.
2. Select **Customers**.
3. Confirm that you can see the subscription(s) with the offer name you provided in the Resource Manager template.

In the customer’s tenant:
1. Open the **Service providers** blade.
2. Select **Providers**.
3. Confirm that you can see the subscription(s) with the offer name you provided in the Resource Manager template.

### PowerShell

```powershell
Get-AzureRmSubscription
```
### Azure CLI

```azurecli
az account list