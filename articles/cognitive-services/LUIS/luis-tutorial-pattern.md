---
title: Use patterns to improve LUIS predictions - Azure | Microsoft Docs 
titleSuffix: Azure
description: Use pattern for intents to improve LUIS intent and entity predictions.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
---

# Use pattern templates on intents to improve predictions

This tutorial demonstrates how to use patterns to increase intent and entity prediction.  

In this tutorial, you learn how to:

> [!div class="checklist"]
* How to identify that a pattern would help your app
* How to create a pattern template for an intent
* How to use prebuilt and custom entities in a pattern template 
* How to verify pattern template prediction improvements
* Add a role to an entity to find contextually-based entities
* Add a Pattern.any to find free-form entities

## Prerequisites

> [!div class="checklist"]
> * For this article, you also need a [LUIS][LUIS] account in order to author your LUIS application.

> [!Tip]
> If you do not already have a subscription, you can register for a [free account](https://azure.microsoft.com/free/).

## Import HumanResources app
This tutorial imports a HumanResources app. The app has three intents: None, GetEmployeeOrgChart, GetEmployeeBenefits. The app has two entities: Prebuilt number and Employee. The Employee entity is a simple entity containing an employee's name. 

1. Create a new LUIS app file and name it `HumanResources.json`. 

2. Copy the following app definition into the file:
<!-- TBD: Post build release -- move to samples and link from there -->
    ```JSON
    {
      "luis_schema_version": "2.3.0",
      "versionId": "0.1",
      "name": "HumanResources",
      "desc": "",
      "culture": "en-us",
      "intents": [
        {
          "name": "GetEmployeeBenefits"
        },
        {
          "name": "GetEmployeeOrgChart"
        },
        {
          "name": "None"
        }
      ],
      "entities": [
        {
          "name": "Employee",
          "roles": []
        }
      ],
      "composites": [],
      "closedLists": [],
      "patternAnyEntities": [],
      "regex_entities": [],
      "prebuiltEntities": [
        {
          "name": "number",
          "roles": []
        }
      ],
      "model_features": [],
      "regex_features": [],
      "patterns": [],
      "utterances": [
        {
          "text": "did you watch the show last night?",
          "intent": "None",
          "entities": []
        },
        {
          "text": "does ilene chavez have any sl?",
          "intent": "GetEmployeeBenefits",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 5,
              "endPos": 16
            }
          ]
        },
        {
          "text": "does lottie change have 40 hours of pto?",
          "intent": "GetEmployeeBenefits",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 5,
              "endPos": 17
            }
          ]
        },
        {
          "text": "how many days of sick leave does santos mahaffey have?",
          "intent": "GetEmployeeBenefits",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 33,
              "endPos": 47
            }
          ]
        },
        {
          "text": "how many hours of paid time off does jody pardo have?",
          "intent": "GetEmployeeBenefits",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 37,
              "endPos": 46
            }
          ]
        },
        {
          "text": "is helen walton on maternity leave?",
          "intent": "GetEmployeeBenefits",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 3,
              "endPos": 14
            }
          ]
        },
        {
          "text": "is there blue in the color green?",
          "intent": "None",
          "entities": []
        },
        {
          "text": "that is too hot!",
          "intent": "None",
          "entities": []
        },
        {
          "text": "who are virgie benjamin's subordinates?",
          "intent": "GetEmployeeOrgChart",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 8,
              "endPos": 22
            }
          ]
        },
        {
          "text": "who does adrian garcia report to?",
          "intent": "GetEmployeeOrgChart",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 9,
              "endPos": 21
            }
          ]
        },
        {
          "text": "who is dina's manager?",
          "intent": "GetEmployeeOrgChart",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 7,
              "endPos": 10
            }
          ]
        },
        {
          "text": "who is mattie rivas manager?",
          "intent": "GetEmployeeOrgChart",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 7,
              "endPos": 18
            }
          ]
        },
        {
          "text": "who reports to magdalena joseph",
          "intent": "GetEmployeeOrgChart",
          "entities": [
            {
              "entity": "Employee",
              "startPos": 15,
              "endPos": 30
            }
          ]
        }
      ]
    }
    ```

3. On the LUIS **Apps** page, select **Import new app**. 

4. In the **Import new app** dialog, select the `HomeAutomation.json` file you created in step 1.

5. Select the **GetEmployeeOrgChart** intent, then change from **Entities view** to **Tokens view**. Several example utterances are listed. Each utterance contains a name, which is an Employee entity. Notice that each name is different and that the arrangement of the wording is different for each utterance. This diversity helps LUIS learn a wide range of utterances.

    ![Toggle Entities view](media/luis-tutorial-pattern/utterances-token-view.png)

6. Select **Train** in the top navigation bar to train the app. Wait for the green success bar.

7. Select **Test** in the top panel. Enter `Who does Patti Owens report to?` then select enter. Select **Inspect** under the utterance to see more information about the test.
    
    The employee name, Patti Owens, has not been used in an example utterance yet. This is a test to see how well LUIS learned this utterance is for the `GetEmployeeOrgChart` intent and the Employee entity should be `Patti Owens`. The result should be about 0.37 score for the `GetEmployeeOrgChart` intent. While the intent is correct, the score is low. The Employee entity is also correctly identified as `Patti Owens`.

    ![Toggle Entities view](media/luis-tutorial-pattern/original-test.png)

## Patterns teach LUIS common utterances with fewer examples
Because of the nature of the human resources domain, there are a few common ways of asking about employee relationships in organizations. For example:

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

These utterances are too close to determine the contextual uniqueness of each without providing many utterance examples. By adding a pattern to an intent, LUIS learns common utterance patterns for an intent without supplying many utterance examples. 

Example patterns for this intent include:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

The pattern is a combination of regular expression matching and machine learning. Provide around 10 pattern examples for LUIS to learn the pattern. The pattern examples, along with the intent utterances, give LUIS a better understanding of what utterances fit the intent and where, within the utterance, the entity exists. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## Add the patterns
1. Close the **Test** panel, if you haven't done so already.

2. In the left navigation, under **Improve app performance**, select **Patterns** from the left navigation.

3. Select the **GetEmployeeOrgChart** intent, then enter the following patterns, one at a time, selecting enter after each pattern:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    The `{Employee}` syntax marks the entity location within the patter as well as which entity it is. The syntax for an entity with a role is {Entity::role} such as `{Location::origin}`.

    ![Enter pattern for intent](./media/luis-tutorial-pattern/enter-pattern.png)

4. Select **Train** in the top navigation bar to train the app. Wait for the green success bar.

5. Select **Test** in the top panel. Enter `Who does Patti Owens report to?` then select Enter. This is the same utterance tested in the previous section. The result should be higher than 0.37 for the `GetEmployeeOrgChart` intent. 

    The score is now **0.99**, much better. LUIS learned the pattern relevant to the intent without providing many examples.

    ![Test with high score result](./media/luis-tutorial-pattern/high-score.png)

## Use an entity with a role in a pattern
The LUIS app is used to help move employees from one location to another. An example utterance is `Move Bob Jones from Seattle to Los Colinas`. Each location in the utterance has a different meaning. Seattle is the originating location and Los Colinas is the destination location for the move. In order to differentiate those locations in the pattern, create a location entity with two roles: origin and destination. 

### Create a simple entity with location and destination roles

### Add a pattern that uses location and destination roles

### Test the new pattern for role data extraction

## Use a Pattern.any entity to find free-form entities in a pattern
This HumanResources app also helps employees find company forms. Many of the forms have titles that are varying in length. Create a Pattern.any and use it in a pattern to extract the form name.

### Create a Pattern.any

### Add a pattern that uses the Pattern.any

### Test the new pattern for free-form data extraction

## Next steps

> [!div class="nextstepaction"]
> [Learn more about example utterances](Add-example-utterances.md)

[LUIS]: luis-reference-regions.md