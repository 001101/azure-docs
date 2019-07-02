---
title: Samples - CAF governance blueprint - Deploy steps
description: Deploy steps of the CAF governance blueprint sample.
author: absheik
ms.author: absheik
ms.date: 06/27/2019
ms.topic: sample
ms.service: blueprints
manager: anushar
ms.custom: fasttrack-edit
---
# Deploy the CAF governance blueprint blueprint sample

To deploy the CAF governance blueprint blueprint sample, the following steps must be taken:

> [!div class="checklist"]
> - Deploy the [CAF governance](../caf-governance/index.md) blueprint sample
> - Create a new blueprint from the sample
> - Mark your copy of the sample as **Published**
> - Assign your copy of the blueprint to an existing subscription

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free)
before you begin.

## Create blueprint from sample

First, implement the blueprint sample by creating a new blueprint in your environment using the
sample as a starter.

1. Select **All services** and search for and select **Policy** in the left pane. On the **Policy**
   page, select **Blueprints**.

1. From the **Getting started** page on the left, select the **Create** button under _Create a
   blueprint_.

1. Find the **CAF governance** blueprint sample under _Other Samples_ and select **Use
   this sample**.

1. Enter the _Basics_ of the blueprint sample:

   - **Blueprint name**: Provide a name for your copy of the CAF governance blueprint
     sample.
   - **Definition location**: Use the ellipsis and select the management group to save your copy of
     the sample to.

1. Select the _Artifacts_ tab at the top of the page or **Next: Artifacts** at the bottom of the
   page.

1. Review the list of artifacts that make up the blueprint sample. Many of the artifacts have
   parameters that we'll define later. Select **Save Draft** when you've finished reviewing the
   blueprint sample.

## Publish the sample copy

Your copy of the blueprint sample has now been created in your environment. It's created in
**Draft** mode and must be **Published** before it can be assigned and deployed. The copy of the
blueprint sample can be customized to your environment and needs, but that modification may move it
away from the CAF governance blueprint.

1. Select **All services** and search for and select **Policy** in the left pane. On the **Policy**
   page, select **Blueprints**.

1. Select the **Blueprint definitions** page on the left. Use the filters to find your copy of the blueprint sample and then select it.

1. Select **Publish blueprint** at the top of the page. In the new page on the right, provide a  **Version** for your copy of the blueprint sample. This property is useful for if you make a
   modification later. Provide **Change notes** such as "First version published from the CAF governance blueprint sample." Then select **Publish** at the bottom of the page.

## Assign the sample copy

Once the copy of the blueprint sample has been successfully **Published**, it can be assigned to a subscription within the management group it was saved to. This step is where parameters are provided to make each deployment of the copy of the blueprint sample unique.

1. Select **All services** and search for and select **Policy** in the left pane. On the **Policy** page, select **Blueprints**.

1. Select the **Blueprint definitions** page on the left. Use the filters to find your copy of the blueprint sample and then select it.

1. Select **Assign blueprint** at the top of the blueprint definition page.

1. Provide the parameter values for the blueprint assignment:

   - Basics

     - **Subscriptions**: Select one or more of the subscriptions that are in the management group
       you saved your copy of the blueprint sample to. If you select more than one subscription, an
       assignment will be created for each using the parameters entered.
     - **Assignment name**: The name is pre-populated for you based on the name of the blueprint.
       Change as needed or leave as is.
     - **Location**: Select a region for the managed identity to be created in. Azure Blueprint uses
       this managed identity to deploy all artifacts in the assigned blueprint. To learn more, see
       [managed identities for Azure resources](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Blueprint definition version**: Pick a **Published** version of your copy of the blueprint
       sample.

   - Lock Assignment

     Select the blueprint lock setting for your environment. For more information, see [blueprints resource locking](../../concepts/resource-locking.md).

   - Managed Identity

     Leave the default _system assigned_ managed identity option.

   - Blueprint parameters

     The parameters defined in this section are used by many of the artifacts in the blueprint
     definition to provide consistency.
        
       /*Abdul to verify below*/

     - **Organization name**: Enter a short-name for your organization. This property is primarily
       used for naming resources.
     - **Shared Service Subscription ID**: Subscription ID where the [ISO 27001 Shared Services](../iso27001-shared/index.md)
       blueprint sample is assigned.
     - **Default subnet address prefix**: The CIDR notation for the virtual network default subnet.
       Default value is _10.1.0.0/16_.
     - **Workload location**: Determines what location the artifacts are deployed to. Not all
       services are available in all locations. Artifacts deploying such services provide a
       parameter option for the location to deploy that artifact to.

   - Artifact parameters

     The parameters defined in this section apply to the artifact under which it's defined. These
     parameters are [dynamic parameters](../../concepts/parameters.md#dynamic-parameters) since
     they're defined during the assignment of the blueprint. For a full list or artifact parameters
     and their descriptions, see [Artifact parameters table](#artifact-parameters-table).

1. Once all parameters have been entered, select **Assign** at the bottom of the page. The blueprint
   assignment is created and artifact deployment begins. Deployment takes roughly an hour. To check
   on the status of deployment, open the blueprint assignment.

> [!WARNING]
> The Azure Blueprints service and the built-in blueprint samples are **free of cost**. Azure
> resources are [priced by product](https://azure.microsoft.com/pricing/). Use the [pricing calculator](https://azure.microsoft.com/pricing/calculator/)
> to estimate the cost of running resources deployed by this blueprint sample.

## Artifact parameters table

The following table provides a list of the blueprint artifact parameters:

|Artifact name|Artifact type|Parameter name|Description|
|-|-|-|-|
|Append CostCenter TAG to Resource Groups|Policy|Policy_CostCenter_Tag|**UnLocked** - Append CostCenter TAG & its value from the Resource Group.|
|n/a|n/a|Organization|**UnLocked** - Enter your organization name (e.g. Contoso), must be unique|
|n/a|n/a|HUB-RG-Location|**UnLocked** - Select 1 Azure Region for Deployment|
|n/a|n/a|Policy_Allowed-Locations|**UnLocked** - Which Azure Regions will you allow resources to be built in?|
|n/a|n/a|Policy_Allowed-VM-SKUs|**UnLocked** - Allowed virtual machine SKUs|
|n/a|n/a|Policy_Allowed-StorageAccount-SKUs|**UnLocked** - SKU used in Diagnostic Log storage accounts|
|n/a|n/a|Policy_Allowed-Resource-Types|**UnLocked** - Which Azure Resources you want to allow in your environment|
|n/a|n/a|Policy_CostCenter_Tag|**UnLocked** - Append CostCenter TAG & its value from the Resource Group|
|n/a|n/a|LocalAdmin-Username|**UnLocked** - KeyVault-Secret LocalAdmin Username|
|n/a|n/a|Local-Admin-Password|**UnLocked** - KeyVault-Secret LocalAdmin Password|
|n/a|n/a|KeyVault-user-id|**UnLocked** - AAD object ID of the user that requires access to Key Vault.|
|n/a|n/a|LogAnalytics_DataRetention|**UnLocked** - Number of days data will be retained in Log Analytics|
|n/a|n/a|LogAnalytics_Location|**UnLocked** - Region to use when establishing the workspace|

## Next steps

Now that you've reviewed the steps to deploy the CAF Governance blueprint sample, visit the following articles to learn about the architecture:

> [!div class="nextstepaction"]
> [CAF Governance blueprint - Overview](./index.md)

Addition articles about blueprints and how to use them:

- Learn about the [blueprint life-cycle](../../concepts/lifecycle.md).
- Understand how to use [static and dynamic parameters](../../concepts/parameters.md).
- Learn to customize the [blueprint sequencing order](../../concepts/sequencing-order.md).
- Find out how to make use of [blueprint resource locking](../../concepts/resource-locking.md).
- Learn how to [update existing assignments](../../how-to/update-existing-assignments.md).