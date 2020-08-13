---
title: "Quickstart: Building your first static web app with Azure Static Web Apps using the Azure CLI"
description: Learn to build an Azure Static Web Apps instance with the Azure portal.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic:  quickstart
ms.date: 08/13/2020
ms.author: cshoe
---

# Quickstart: Building your first static web app using the Azure CLI

Azure Static Web Apps publishes websites to a production environment by building apps from a GitHub repository. In this quickstart, you build a web application using your preferred front-end framework.

If you don't have an Azure subscription, [create a free trial account](https://azure.microsoft.com/free).

## Prerequisites

- [GitHub](https://github.com) account
- [GitHub personal access token](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com) account
- [Azure CLI](../../cli/azure/install-azure-cli?view=azure-cli-latest) installed (version 2.10 and higher)

[!INCLUDE [create repository from template](../../includes/static-web-apps-getting-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-getting-started-clone-repo.md)]

Next, switch to the new folder using the following command.

```bash
cd my-first-static-web-app
```

## Create a static web app

Now that the repository is created, you can create a static web app from the Azure CLI.

> [!IMPORTANT]
> Make sure you are in the _my-first-static-web-app_ folder in your terminal.

Begin by signing in to the Azure CLI by using the following command.

```bash
az login
```

Once signed in, you can create a new static web app from your repository.

# [No Framework](#tab/vanilla-javascript)

```bash
az staticwebapp create -n my-first-static-web-app -g <RESOURCE_GROUP_NAME> -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app -l <LOCATION> -b master --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# [Angular](#tab/angular)

```bash
az staticwebapp create -n my-first-static-web-app -g <RESOURCE_GROUP_NAME> -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app -l <LOCATION> -b master --app-artifact-location "dist/angular-basic" --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# [React](#tab/react)

```bash
az staticwebapp create -n my-first-static-web-app -g <RESOURCE_GROUP_NAME> -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app -l <LOCATION> -b master --app-artifact-location "build" --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# [Vue](#tab/vue)

```bash
az staticwebapp create -n my-first-static-web-app -g <RESOURCE_GROUP_NAME> -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app -l <LOCATION> -b master --app-artifact-location "dist" --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

- `<RESOURCE_GROUP_NAME>`: Replace this value with an Azure resource group name.

- `<YOUR_GITHUB_ACCOUNT_NAME>`: Replace this value with your GitHub username.

- `<LOCATION>`: Replace this value with the location nearest you. Options include: `CentralUS`, `EastAsia`, `EastUS2`, `WestEurope`, and `WestUS2`.

- `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Replace this  value with the [GitHub personal access token](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) you previously generated.

You can now view the created app in Azure.

1. Open the [Azure portal](https://portal.azure.com)
1. Search for **my-first-web-static-app** from the top search bar
1. Select on the app name

[!INCLUDE [view website](../../includes/static-web-apps-getting-started-view-website.md)]

## Clean up resources

If you're not going to continue to use this application, you can delete the Azure Static Web Apps instance through the following steps:

1. Open the [Azure portal](https://portal.azure.com)
1. Search for **my-first-web-static-app** from the top search bar
1. Select the app name
1. Select the **Delete** button
1. Select **Yes** to confirm the delete action

## Next steps

> [!div class="nextstepaction"]
> [Add an API](add-api.md)
