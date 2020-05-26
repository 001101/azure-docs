---
title: Deployment history deletions
description: Describes how Azure Resource Manager automatically deletes deployments from the deployment history. Deployments are deleted when the history is close to exceeding the limit of 800.
ms.topic: conceptual
ms.date: 05/26/2020
---
# Automatic deletions from deployment history

Every time you deploy a template, information about the deployment is written to the deployment history. Each resource group is limited to 800 deployments in its deployment history.

Starting in June 2020, Azure Resource Manager automatically deletes deployments from your history as you near the limit. Automatic deletion is a change from past behavior. Previously, you had to manually delete deployments from the deployment history to avoid getting an error.

Deleting a deployment from the history doesn't affect any of the resources that were deployed.

## When deployments are deleted

Deployments are deleted from your deployment history only when you near the limit of 800. Azure Resource Manager deletes a small set of the oldest deployments to clear space for future deployments. Most of your deployment history remains unchanged. The oldest deployments are always deleted first.

:::image type="content" source="./media/deployment-history-deletions/deployment-history.png" alt-text="Deletions from deployment history":::

You also trigger the deletion process when you run the [what-if operation](template-deploy-what-if.md) or validate a deployment.

## Opt out of automatic deletions

You can opt out of automatic deletions from the history. **Use this option only when you want to manage the deployment history yourself.** The limit of 800 deployments in the history is still enforced. If you exceed 800 deployments, you'll receive an error and your deployment will fail.

To disable automatic deletions, register the `Microsoft.Resources/DisableDeploymentGrooming` feature flag. You apply the flag to each subscription that you want to opt out of automatic deletions.

# [PowerShell](#tab/azure-powershell)

For PowerShell, use [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

To see the current status of your subscription, use:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

# [Azure CLI](#tab/azure-cli)

For Azure CLI, use [az feature register](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

To see the current status of your subscription, use:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# [REST](#tab/rest)

For REST API, use [Features - Register](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

To see the current status of your subscription, use:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

---

## Next steps

* To learn about viewing the deployment history, see [View deployment history with Azure Resource Manager](deployment-history.md).
