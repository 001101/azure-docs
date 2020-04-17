---
title: What is App Service Static Apps?
description: The key features and functionality of App Service Static Apps.
services: azure-functions
author: craigshoemaker
ms.service: azure-functions
ms.topic:  overview
ms.date: 05/08/2020
ms.author: cshoe
---

# What is App Service Static Apps (Preview)?

App Service Static Apps is a service that automatically builds and deploys web apps to Azure from a GitHub repository.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Static Apps overview":::

Applying commits and pull requests to a designated branch triggers a GitHub Action workflow. The workflow builds the web application for production and globally deploys the web app and API.

Back-end API functionality is powered by Azure Functions. Hosted on the Linux consumption plan, the app's API automatically scales up and down based on demand to the API.

## Key features

- **Free web hosting** for static content like HTML, CSS, JavaScript, and images
- **Free API** support provided by Azure Functions
- **Streamlined GitHub integration** where repository changes trigger builds and deployments
- **Globally distributed** static content, putting content closer to your users
- **Free SSL certificates**, which are automatically renewed
- **Custom domains**\* to provide branded customizations to your app
- **Seamless security model** with a reverse-proxy when calling APIs, which requires no CORS configuration
- **Authentication provider integrations** with Azure Active Directory, Facebook, Google, GitHub, and Twitter
- **Customizable authorization role definition** and assignments
- **Back-end routing rules** enabling full control over the content and routes you serve
- **Generated staging versions** powered by pull requests enabling preview versions of your site before publishing

## What you can do with Static Apps

- **Build modern JavaScript applications** with frameworks and libraries like [Angular](https://angular.io/), [React](https://reactjs.org/), [Svelete](https://svelte.dev/), [Vue](https://vuejs.org/) with an [Azure Functions](https://azure.microsoft.com/services/functions/) back-end
- **Publish static sites** with frameworks like [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md)
- **Deploy web applications** with frameworks like [Next.js](deploy-nextjs.md) and [Nuxt.js](deploy-nuxtjs.md)

\* Apex domain registrations are not supported during preview.

## Next steps

> [!div class="nextstepaction"]
> [Build your first static app](getting-started.md)
