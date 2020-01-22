---
title: include file
description: include file
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/21/2020
ms.author: diberry
---


Use the Language Understanding (LUIS) prediction client library for Python to:

* Get prediction by slot

[Reference documentation]() | [Library source code]() | [Prediction runtime Package (NuGet)]() | [C# Samples]()

## Prerequisites

* Language Understanding (LUIS) portal account - [Create one for free](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## Setting up

### Create an environment variable

Using your key, and the name of your resource, create two environment variables for authentication:

* LUIS_RUNTIME_KEY
* LUIS_RUNTIME_ENDPOINT
* LUIS_APP_ID - The public LUIS IoT app ID is `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* LUIS_APP_SLOT_NAME - `production` or `staging`

Use the instructions for your operating system.

#### [Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

After you add the environment variables, restart the console window.

#### [Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

After you add the environment variables, run `source ~/.bashrc` from your console window to make the changes effective.

#### [macOS](#tab/unix)

Edit your `.bash_profile`, and add the environment variable:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

After you add the environment variables, run `source .bash_profile` from your console window to make the changes effective.
***

### Create a new python file

Create a new python file in your preferred editor or IDE, named `prediction_quickstart.py`.

### Install the SDK

Within the application directory, install the Language Understanding (LUIS) prediction runtime client library for Python with the following command:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## Object model

The Language Understanding (LUIS) prediction runtime client is a [LUISRuntimeClient]() object that authenticates to Azure, which contains your resource key.

Once the client is created, use this client to access functionality including:

* Prediction by [staging or product slot]()
* Prediction by [version]()

## Code examples

These code snippets show you how to do the following with the Language Understanding (LUIS) prediction runtime client library for Python:

* [Prediction by slot](#get-prediction-from-runtime)

## Add the dependencies

From the project directory, open the *prediction_quickstart.py* file in your preferred editor or IDE. Add the following dependencies:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## Authenticate the client

1. Create variables for your own required LUIS information:

    Add variables to manage your prediction key pulled from an environment variable named `LUIS_RUNTIME_KEY`. If you created the environment variable after the application is launched, the editor, IDE, or shell running it will need to be closed and reloaded to access the variable. The methods will be created later.

    Create a variable to hold your resource name `LUIS_RUNTIME_ENDPOINT`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Create a variable for the app ID as an environment variable named `LUIS_APP_ID`. Set the environment variable to the public IoT app, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Create a variable to set the `production` published slot.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Create an [CognitiveServicesCredentials]() object with your key, and use it with your endpoint to create an [LUISRuntimeClient]() object.

        [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## Get prediction from runtime

Add the following method to create the request to the prediction runtime.

The user utterance is part of the [prediction_request]() object.

The **get_slot_prediction** method needs several parameters such as the app ID, the slot name, and the prediction request object to fulfill the request. The other options such as verbose, show all intents, and log are optional.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## Main code for the prediction

Use the following main method to tie the variables and methods together to get the prediction.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=main)]

## Run the application

Run the application with the `python prediction_quickstart.py` command from your application directory.

```python
python prediction_quickstart.py
```

## Clean up resources

When you are done with your predictions, clean up the work from this quickstart by deleting the program.cs file and its subdirectories.
