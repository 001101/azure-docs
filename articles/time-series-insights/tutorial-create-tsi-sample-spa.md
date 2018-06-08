﻿---
title: Create an Azure Time Series Insights single-page web app
description: Learn how to create a single-page web application that queries and renders data from a TSI environment.
author: BryanLa
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/10/2018
ms.author: bryanla
# Customer intent: As a developer, I want learn how to create a Time Series Insights single-page web application (SPA), so I can apply the principles to building my own SPA.
---

# Tutorial: Create an Azure Time Series Insights single-page web app

This tutorial will guide you through the process of creating a single-page web app (SPA), modeled after the [Time Series Insights (TSI) sample application](https://insights.timeseries.azure.com/clientsample). In this tutorial, you'll about:

> [!div class="checklist"]
> * The application design
> * How to register the application with Azure Active Directory (AD)
> * How to build, publish, and test the web application 

## Prerequisites

If you haven't established your own TSI environment yet, complete the [Create an Azure Time Series Insights environment](tutorial-create-populate-tsi-environment.md) tutorial first. You'll use the TSI environment as a data source for this tutorial. You'll also create a [free Azure subscription](https://azure.microsoft.com/en-us/free/), if you don't already have one. 

You'll also need to install Visual Studio if you haven't already. For this tutorial, you can [download/install the free Community version, or a free trial](https://www.visualstudio.com/downloads/).

## Application design overview

As mentioned, the TSI sample application is the basis for the design and code of this tutorial. The sample uses the TSI Client JavaScript library to query and visualize data from a TSI environment. 

The TSI Client JavaScript library provides an abstraction for two important API categories:
- **Wrapper methods for calling the TSI Query APIs**: REST APIs that allow you to query for TSI data by using JSON-based expressions. The methods are organized under the `TsiClient.server` namespace of the library.
- **Methods for creating and populating several types of charting controls**: Methods that are used for visualizing the TSI data in a web page. The methods are organized under the `TsiClient.ux` namespace of the library.

For an overview of the structure of the TSI sample app its use of the TSI Client library, refer to the [Explore the Azure Time Series Insights JavaScript client library](tutorial-explore-js-client-lib.md) tutorial.

## Register the application with Azure AD 

Before building the application, you register it with Azure AD. Registration serves as the identity configuration for an application, enabling it to use Azure AD's OAuth support for single sign-on. OAuth requires SPA applications to use the "implicit" authorization grant, so you also use the manifest editor to update the corresponding property. An application manifest is a JSON representation of the application's identity configuration. 

1. Sign in to the [Azure portal](https://portal.azure.com) using your Azure subscription account.  
2. Select the **Azure Active Directory** resource in the left pane, then **App registrations**, then **+ New application registration**:  
   
   ![Azure portal Azure AD application registration](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

3. On the **Create** page, fill in the required parameters:
   
   Parameter|Description
   ---|---
   **Name** | Provide a meaningful registration name.  
   **Application type** | Since you're building an SPA web application, leave as "Web app/API."
   **Sign-on URL** | Enter the URL for the home/sign-in page of the application. Because you host the application in Azure App Service (later), you must use a URL within the "https://azurewebsites.net" domain. In this example, the name is based on the registration name.

   When finished, click **Create** to create the new application registration.

   ![Azure portal Azure AD application registration - creation](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

4. Resource applications provide REST APIs for use by other applications, and are also registered with Azure AD. APIs provide granular/secured access to client applications, by exposing "scopes." Because your application will call the "Azure Time Series Insights" API, you need to specify the API and scope, for which permission will be requested at runtime. Select **Settings**, then **Required permissions**, then **+ Add**:

   ![Azure portal Azure AD add permissions](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

5. From the **Add API access** page, click **1 Select an API** to specify the TSI API. On the **Select an API** page, enter "azure time" in the search field. Then select the "Azure Time Series Insights" API in the results list, and click **Select**: 

   ![Azure portal Azure AD add permissions - API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

6. Now you specify a scope on the API. On the **Add API access** page again, click **2 Select permissions**. On the **Enable Access** page, select the "Access Azure Time Series Insights service" scope. Then click **Select**, then click **Done** back on the **Add API access** page:

   ![Azure portal Azure AD add permissions - scope](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

7. As mentioned previously, you also need to update the application manifest. Click on the application name in the breadcrumb to go back to the **Registered app** page. Select **Manifest**, change the `oauth2AllowImplicitFlow` property to `true`, then click **Save**:

   ![Azure portal Azure AD update manifest](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

8. Finally, click on the breadcrumb to go back to the **Registered app** page again, and copy the **Home page** URL and **Application ID** properties for your application. You'll use these properties in a later step:

   ![Azure portal Azure AD properties](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## Build and publish the web application

1. Create a directory to store your application project files. Then browse to each of the following URLs, right-click on the "Raw" link in the upper right area of the page, and "Save as" into your project directory:

   - **index.html** HTML and JavaScript for the page https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css:** CSS style sheet: https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
2. Start and sign in to Visual Studio, to create a project for the web application. On the **File** menu, select the **Open**, **Web site** option. Select the working directory where you stored the HTML and CSS files, then click **Open**:

   ![VS - File open web site](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

3. Open **Solution Explorer** from the Visual Studio **View** menu. You should see your new solution, containing the web site project (globe icon), which contains the HTML and CSS files:

   ![VS - Solution explorer new solution](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

4. Before you can publish the application, you need to update the application's code to associate it with your new Azure AD application registration, and your TSI environment:

   a. Open the **index.html** file in your Visual Studio solution, and scroll down to following lines of JavaScript code.

      > [!div class="mx-imgBorder"]
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

   b. Change the `clientID` and `postLogoutRedirectUri` variables respectively, to use the Application ID and Home Page URL you copied previously during step #8 in [Register the application with Azure AD](#register-the-application-with-azure-ad) section. 

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. Scroll down to following lines of JavaScript code.

      > [!div class="mx-imgBorder"]
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=207-224&highlight=12)]

   d. Change the second argument of the `getAggregates` call, to use your TSI environment's full qualified domain name (FQDN). The FQDN is shown next to the **Data Access FQDN** property on the TSI environment **Overview** page (for an example, see step #2 of the [Verify the telemetry data section](tutorial-create-populate-tsi-environment#verify-the-telemetry-data), in the "Create an Azure Time Series Insights environment" tutorial):

      ```javascript
      tsiClient.server.getAggregates(token, '10000000-0000-0000-0000-100000000108.env.timeseries.azure.com', aggregateExpressions3.map(function(ae){return ae.toTsx()})).then(function(result){
      ``` 
   e. Save the **index.html** when you've finished editting.

5. Now publish the web application to your Azure subscription as an Azure App Service:

   >[!NOTE]
   >Several of the fields on the following dialog boxes are populated with data from your Azure subscription. As such, it may take a few seconds for each dialog to load completely, before you're able to continue.

   a. Right click on the web site project node in **Solution Explorer**, and select **Publish Web App**: 

       > [!div class="mx-imgBorder"]
       ![VS - Solution explorer publish web app](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. Select **Microsoft Azure App Service** to create a publish target: 

       > [!div class="mx-imgBorder"]
       ![VS - Publish profile](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)

   c. If the account used for Visual Studio sign-in has access to multiple subscriptions, select the subscription you'd like to use. Then click “New”: 

       > [!div class="mx-imgBorder"]
       ![VS - Publish profile - app service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)

   d. Give the **Create App Service** dialog a few seconds to load all fiels, then modify the following fields:
   
      Field|Description
      ---|---
      **App Name** | Change to the Azure AD app registration name you used in step #3, in [Register the application with Azure AD](#register-the-application-with-azure-ad). 
      **Resource Group** | Change to match the **App Name** field.
      **App Service Plans** | Change to match the **App Name** field.

       > [!div class="mx-imgBorder"]
       ![VS - Publish profile - add new app service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)

      When finished, click **Create**. You notice the **Export** button in the lower left, is replaced with a "Deploying:" message. When the App Service publish target has been added to the publishing profile, you'll be returned to the previous dialog.

   e. Once you return to the **Publish** dialog, make sure **Publish method** is set to "Web deploy", then click “Publish”:

       > [!div class="mx-imgBorder"]
       ![VS - Publish web app - publish the app service](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)

   f. You should see a successful publish log in the Visual Studio **Output** window. You may also notice a browser tab opened, taking you to the new web application.

       > [!div class="mx-imgBorder"]
       ![VS - Publish web app - publish log output](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)

6. next...

## Test the web application

## Clean up resources

This tutorial creates several running Azure services. If you plan to abandon or delay completion of this tutorial series, we recommend deleting all resources to avoid incurring unnecessary costs. 

From the left-hand menu in the Azure portal:

1. Click the **Resource groups** icon, then select the resource group you created for the TSI Environment. At the top of the page, click **Delete resource group**, enter the name of the resource group, then click **Delete**. 
2. Click the **Resource groups** icon, then select the resource group that was created by the device simulation solution accelerator. At the top of the page, click **Delete resource group**, enter the name of the resource group, then click **Delete**. 

## Next steps

In this tutorial, you learned how to:

> [!div class="checklist"]
> * The application design
> * How to register the application with Azure Active Directory (AD)
> * How to build, publish, and test the web application 

Now that you know how to create your own TSI SPA web application, learn more about shaping your JSON to get the best possible query performance:

> [!div class="nextstepaction"]
> [How to shape JSON to maximize query performance](how-to-shape-query-json.md)


