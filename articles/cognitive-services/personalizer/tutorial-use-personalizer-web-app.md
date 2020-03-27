---
title: Use web app - Personalizer
description: Customize a C# .NET web app with a Personalizer loop to provide the correct content to a user based on actions (with features) and context features.
ms.topic: troubleshooting
ms.date: 03/09/2020
ms.author: diberry
---
# Add Personalizer to a .NET web app

Customize a C# .NET web app with a Personalizer loop to provide the correct content to a user based on actions (with features) and context features.

**In this tutorial, you learn how to:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Set up Personalizer key and endpoint
> * Collect features
> * Call Rank API
> * Display top action, designated as _rewardActionId_

## Select the best content for a web app

A web app should use Personalizer when there is an _action_ (some type of content) on the web page that needs to be personalized to a single top item (rewardActionId) to display. Examples of action lists include news articles, button placement locations, and word choices for product names.

You send the list to the Personalizer loop, Personalizer selects the single best content, then your web app displays that content.

In this tutorial, the actions are types of food:

* pasta
* ice cream
* juice
* salad

To help Personalizer learn about your actions, send both action features and context features with each Rank API request.

A **feature** is information about the action or context that can be aggregated (grouped) across members of your web app user base. A feature _isn't_ individually specific (such as a user ID) or highly specific (such as an exact time of day).

### Actions with features

Each action (content item) has features to help distinguish the food item:

* taste - such as salty or sweet
* spiceLevel - none, low, medium
* nutritionLevel - a number within a range such as 1-10

The features aren't configured as part of the loop configuration in the Azure portal. Instead they are sent as a JSON object with each Rank API call. This allows flexibility for the actions and their features to grow, change, and shrink over time, which allows Personalizer to follow trends.

```json
"actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
```

## Context features

Context features help Personalizer understand the context of the actions. The context for this sample application includes:

* time of day - morning, afternoon, evening, night
* user's preference for taste - salty or sweet
* browser's context - user agent, geographical location, referrer

```json
"contextFeatures": [
    {
      "time": "morning"
    },
    {
      "taste": "sweet"
    },
    {
      "httpRequestFeatures": {
        "_synthetic": false,
        "OUserAgent": {
          "_ua": "",
          "_DeviceBrand": "",
          "_DeviceFamily": "Other",
          "_DeviceIsSpider": false,
          "_DeviceModel": "",
          "_OSFamily": "Windows",
          "_OSMajor": "10",
          "DeviceType": "Desktop"
        }
      }
    }
  ]
```

## How does the web app use Personalizer?

The web app uses Personalizer to select the best action from the list of pasta, ice cream, juice, and salad. It does this by sending the following information with each Rank API call:
* **actions** with their features such as `taste` and `spiceLevel`
* **context** features such as `time` of day, user's `taste` preference, and the browser's user agent information, and context features
* **actions to exclude** such as juice
* **eventid** which is different for each call to Rank API.

## Features in a web app

A web app, using Personalizer, needs features about the actions (content) and the context. Features should be selected with the same planning and design that you would apply to any schema or model in your technical architecture. The feature values can be determined with business logic or 3rd party values should not be so highly-specific that they don't apply across a group or class.

This app uses `time` as a feature but groups time into categories of `morning`, `afternoon`, `evening`, and `night`. That is an example of using the information of time but not in a highly specific way, such as `10:05:01 UTC+2`.

## Download the working sample

In this tutorial, the complete sample web app is provided for you.

1. Download the [GitHub samples repository](git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git) for Personalizer.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Use Visual Studio 2019 to open the solution, `HttpRequestFeatures.sln`. This sample is found within the `/samples/HttpRequestFeatures` folder.

    The solution includes two projects:
    * **HttpRequestFeaturesExample**, a .NET web app which manages both the web page and the Rank API call.
        * **HomeController.cs** manages the interaction with Personalizer and passes the data to the Index.cshtml page to display.
        * **Index.cshtml** displays the values passed from HomeController.cs
    * **HttpRequestFeatures**, a class library which create the context features for the user agent.

    > [!div class="mx-imgBorder"]
    > ![Use Visual Studio 2019 to open the solution, `HttpRequestFeatures.sln`. This sample is found within the `/samples/HttpRequestFeatures` folder.](./media/tutorial-web-app/solution-explorer-files.png)

## Create a Personalizer resource in the Azure portal

Use either the [Azure portal](how-to-create-resource.md#create-a-resource-in-the-azure-portal) or the [Azure CLI](how-to-create-resource.md#create-a-resource-with-the-azure-cli) to create a Personalizer resource.

The resource has 2 values you need in order to use the loop:

* **Endpoint** - an example is: `https://your-resource-name.cognitiveservices.azure.com/`
* **Key** - a 32 character string

## Configure project with your Personalizer endpoint

Using best security practices, the endpoint is stored in the `appsetings.json` file, as the property **ServiceEndpoint**. Change `your-resource-end-goes-here` to your own endpoint URL.

```JSON
{
  "PersonalizerConfiguration": {
    "ServiceEndpoint": "replace-with-your-resource-endpoint"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## Configure project with your Personalizer key

Using best security practices, use the Visual Studio solution feature to manage user secrets.

1. In Visual Studio, on the **Solution Explorer**, right-click the **HTTPRequestFeaturesExample** project, then select **Manage User Secrets**.

1. In the secrets.json file, enter the value for the **PersonalizerApiKey**.

    ```json
    {
      "PersonalizerApiKey": "replace-with-your-resource-key"
    }
    ```

## Run the sample

1. Build and run the **HTTPRequestFeaturesExample** project. A browser window opens to `https://localhost:44332/` displaying the single page application.

    > [!div class="mx-imgBorder"]
    > ![Build and run the HTTPRequestFeaturesExample project. A browser window opens to `https://localhost:44332/` displaying the single page application.](./media/tutorial-web-app/web-app-single-page.png)

## Step through code execution to understand the Rank API



