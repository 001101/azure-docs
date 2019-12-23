---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
---

<a name="HOLTop"></a>

<!-- these links are for v2. Make sure to update them to the correct v3 content -->
[Reference documentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [Library source code](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Package (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [Samples](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/)
* The current version of [Node.js](https://nodejs.org/).

## Setting up

<!--
Add any extra steps preparing an environment for working with the client library. 
-->

### Create a Text Analytics Azure resource
<!-- NOTE
The below is an "include" file, which is a text file that will be referenced, and rendered on the docs site.
These files are used to display text across multiple articles at once. Consider keeping them in-place for consistency with other articles.
 -->
[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### Create a new Node.js application

In a console window (such as cmd, PowerShell, or Bash), create a new directory for your app, and navigate to it. 

```console
mkdir myapp && cd myapp
```

Run the `npm init` command to create a node application with a `package.json` file. 

```console
npm init
```
### Install the client library

Install the `@azure/ms-rest-js` and `@azure/cognitiveservices-textanalytics` NPM packages:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Your app's `package.json` file will be updated with the dependencies.

Create a file named `index.js` and add the following libraries:

```javascript
"use strict";

const os = require("os"); // Why do we need os ? If we are just doing console.log(os.EOL);, we should remove this dependency.
const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

<!-- QUESTION: Why does this titles "Create variables for your resource's Azure endpoint and subscription key" ?
The key and endpoint are generated as soon as the resource is provisioned. This step should be just after creating a new resource above and can be referenced here when creating index file.-->
Create variables for your resource's Azure endpoint and subscription key.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

<!-- Use the below example variable names and example strings, for consistency with the other quickstart variables -->
```javascript
const subscription_key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

The `subscription_key` and `endpoint` variables are generated when the text analytics resource is created and can be found in **Quick Start** section of the resource.

## Object model

<!-- 
    Briefly introduce and describe the functionality of the library's main classes. Include links to their reference pages. If needed, briefly explain the object hierarchy and how the classes work together to manipulate resources in the service.
-->

The Text Analytics client is a [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) object that authenticates to Azure using your key. The client provides several methods for analyzing text, as a single string, or a batch.

Text is sent to the API as a list of `documents`, which are `dictionary` objects containing a combination of `id`, `text`, and `language` attributes depending on the method used. The `text` attribute stores the text to be analyzed in the origin `language`, and the `id` can be any value. 

The response object is a list containing the analysis information for each document. 

## Code examples
<!-- If you add more code examples, add a link to them here-->
* [Client Authentication](#client-authentication)
* [Language Detection](#language-detection)
* [Sentiment Analysis](#sentiment-analysis)
* [Key Phrase Extraction](#key-phrase-extraction)
* [Named Entity Recognition](#named-entity-recognition)
* [Recognition of Personally Identifiable Information](#recognition-of-personally-identifiable-information)
* [Linked Entity Recognition](#linked-entity-recognition)

## Client Authentication

Create a new [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) object with `credentials` and `endpoint` as a parameter.

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(subscription_key));
```

## Language detection
<!-- TODO: Change description pointing to the latest methods and classes -->
Create an array of strings containing your documents. Call the client's [detectLanguages()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) method and get the returned [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Then iterate through the results, and print each document's ID, and language.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "This is a document written in English.",
        "Este es un document escrito en Español.",
        "这是一个用中文写的文件"
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`) // Q: Do we need to show both of the languages ? (Detected vs Primary)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
    console.log(os.EOL); // Do we need this, this is an unnecessary dependency to a developer.
}
languageDetection(textAnalyticsClient);
```

Run your code with `node index.js` in your console window.

### Output

```console
ID: 0
        Detected Language English
        Primary Language English
ID: 1
        Detected Language Spanish
        Primary Language Spanish
ID: 2
        Detected Language Chinese_Simplified
        Primary Language Chinese_Simplified
```

## Sentiment analysis

Create a list of dictionary objects, containing the documents you want to analyze. Call the client's [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) method and get the returned [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Iterate through the list of results, and print each document's ID and sentiment score. A score closer to 0 indicates a negative sentiment, while a score closer to 1 indicates a positive sentiment.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life.",
        "This was a waste of my time. The speaker put me to sleep.",
        "No tengo dinero ni nada que dar...",
        "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ]

    const sentimentResult = await client.analyseSentiment(sentimentInput);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);

        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Run your code with `node index.js` in your console window.

### Output

```console
ID: 0
        Document Sentiment: positive
        Sentences Sentiment(1):
                Sentence sentiment: positive
ID: 1
        Document Sentiment: negative
        Sentences Sentiment(2):
                Sentence sentiment: negative
                Sentence sentiment: neutral
ID: 2
        Document Sentiment: negative
        Sentences Sentiment(1):
                Sentence sentiment: negative
ID: 3
        Document Sentiment: neutral
        Sentences Sentiment(2):
                Sentence sentiment: neutral
                Sentence sentiment: neutral
```


## Key phrase extraction
<!-- TODO: Update language and check for updated links  -->
Create a list of objects, containing your documents. Call the client's [extractKeyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) method and get the returned     [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) object. Iterate through the results and print each document's ID, and any detected key phrases.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "猫は幸せ",
        "Fahrt nach Stuttgart und dann zum Hotel zu Fu.",
        "My cat might need to see a veterinarian.",
        "A mi me encanta el fútbol!"
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Run your code with `node index.js` in your console window.

### Output
<!-- Q- When compared with V2 the extracted key phrases are different. Is this a good example ?
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
] -->

```console
ID: 0
        Document Key Phrases: 猫は幸せ
ID: 1
        Document Key Phrases: Fahrt nach Stuttgart und dann zum Hotel zu Fu
ID: 2
        Document Key Phrases: cat,veterinarian
ID: 3
        Document Key Phrases: mi,encanta,fútbol
```

## Named Entity Recognition

Create a list of objects, containing your documents. Call the client's [recognizeEntities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) method and get the [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) object. Iterate through the list of results, and print each document's ID. For each detected entity, print its wikipedia name, the type and sub-types (if exists) as well as the locations in the original text.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];

    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} Type: ${entity.type} Sub Type: ${entity.subtype ? entity.subtype : "N/A"}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Run your code with `node index.js` in your console window.

### Output

```console
Document ID: 0
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## Run the application

Run the application with the `node` command on your quickstart file.

```console
node index.js
```
