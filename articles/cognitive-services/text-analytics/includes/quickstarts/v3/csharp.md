---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 12/26/2019
ms.author: assafi
---

<a name="HOLTop"></a>

<!-- these links are for v2. Make sure to update them to the correct v3 content on the same site where appropriate.-->
[Reference documentation]() | [Library source code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Package (NuGet)]() | [Samples]()

> [!NOTE]
> The code in this article uses the synchronous methods of the Text Analytics .NET SDK as well as un-secured credentials use for simplicity reasons. For production scenarios, we recommend using the batched asynchronous methods for performance and scalability. For example, calling [AnalyzeSentimentAsync()]() instead of [AnalyzeSentiment()](). For secured use of credentials we recommend using [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-overview) to store all access keys and the use of [AAD authentication](https://docs.microsoft.com/en-us/azure/cognitive-services/authentication#authenticate-with-azure-active-directory) for all role based access controls. **Remember to never store access keys in code.**

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/)
* The [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)

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

### Create a new .NET Core application

Using the Visual Studio IDE, create a new .NET Core console app. This will create a simple "Hello World" project with a single C# source file: *program.cs*.

Install the client library by right-clicking on the solution in the **Solution Explorer** and selecting **Manage NuGet Packages**. In the package manager that opens select **Browse**, check **Include prerelease**, and search for `Azure.AI.TextAnalytics`. Click on it, and then **Install**. You can also use the [Package Manager Console](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Open the *program.cs* file and add the following `using` directives:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

In the application's `Program` class, create variables for your resource's key and endpoint.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

<!-- Use the below example variable names and example strings, for consistency with the other quickstart variables -->
```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Replace the application's `Main` method. You will define the methods called here later.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);

    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    KeyPhraseExtractionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    EntityPIIExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

## Object model

<!-- 
    Briefly introduce and describe the functionality of the library's main classes. Include links to their reference pages. If needed, briefly explain the object hierarchy and how the classes work together to manipulate resources in the service.
-->

<!-- TODO: Update client docs link -->
The Text Analytics client is a [TextAnalyticsClient]() object that authenticates to Azure using your key, and provides functions to accept text as single strings or as a batch. You can send text to the API synchronously, or asynchronously. The response object will contain the analysis information for each document you send. An optional, `TextAnalyticsClientOptions` instance can be used to initialize the client with various default settings (e.g. default language or country hint).

<!-- TODO: Update samples link to deeper v3 link -->
Additional examples, including AAD authentication and the use of client default settings can be found [here](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples).

## Code examples
<!-- If you add more code examples, add a link to them here-->
* [Sentiment Analysis](#sentiment-analysis)
* [Language detection](#language-detection)
* [Entity recognition](#entity-recognition)
* [Entity recognition - PII](#entity-pii)
* [Key phrase extraction](#key-phrase-extraction)
* [Entity linking](#entity-linking)

In your program's `main()` method, call the authentication method to instantiate the client.

## Sentiment analysis

<!-- TODO: Update client docs links -->
Create a new function called `SentimentAnalysisExample()` that takes the client that you created earlier, and call its [AnalyzeSentiment()]() function. The returned [Response\<AnalyzeSentimentResult\>]() object will contain the sentiment label and score of the entire input document, as well as a sentiment analysis for each sentence.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"Sentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"Sentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"Positive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"Negative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"Neutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### Output

```console
Document sentiment: Positive

Sentence [offset 0, length 30]
Sentence sentiment: Positive
Positive score: 1.00
Negative score: 0.00
Neutral score: 0.00

Sentence [offset 31, length 30]
Sentence sentiment: Neutral
Positive score: 0.21
Negative score: 0.02
Neutral score: 0.77
```

## Language detection

<!-- TODO: Update client docs links -->
Create a new function called `LanguageDetectionExample()` that takes the client that you created earlier, and call its  [DetectLanguage()]() function. The returned [Response\<DetectLanguageResult>]() object will contain the detected language in `Value.PrimaryLanguage` if successful, and an `Value.ErrorMessage` if not.

> [!Tip]
> In some cases it may be hard to disambiguate languages based on the input. You can use the `countryHint` parameter to specify a 2-letter country code. By default the API is using the "US" as the default countryHint, to remove this behavior you can reset this parameter by setting this value to empty string `countryHint = ""`. To set a different default, set the `TextAnalyticsClientOptions.DefaultCountryHint` property and pass it during the client's initialization.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine($"Language: {response.Value.PrimaryLanguage.Name}\n");
}
```

### Output

```console
Language: French
```

## Entity recognition

<!-- TODO: Update client docs link -->
Create a new function called `EntityRecognitionExample()` that takes the client that you created earlier, call its [RecognizeEntities()]() function and iterate through the results. The returned [Response\<RecognizeEntitiesResult\>]() object will contain the list of detected entities in `Value.NamedEntities` if successful, and an `Value.ErrorMessage` if not. For each detected entity, print its Type and Sub-Type if exists.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### Output

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## Personal Identifiable Information (PII) Entity recognition

<!-- TODO: Update client docs link -->
Create a new function called `EntityPIIExample()` that takes the client that you created earlier, call its [RecognizePiiEntities()]() function and iterate through the results. Similar to the previous function the returned [Response\<RecognizeEntitiesResult\>]() object will contain the list of detected entities in `Value.NamedEntities` if successful, and an `Value.ErrorMessage` if not.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### Output

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```

## Entity Linking

<!-- TODO: Update client docs link -->
Create a new function called `EntityLinkingExample()` that takes the client that you created earlier, call its [RecognizeLinkedEntities()]() function and iterate through the results. The returned [Response\<RecognizeLinkedEntitiesResult\>]() object will contain the list of detected entities in `Value.LinkedEntities` if successful, and an `Value.ErrorMessage` if not. Since linked entities are uniquely identified, occurrences of the same entity are grouped under a `LinkedEntity` object as a list of `LinkedEntityMatch` objects.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
        }
    }
}
```

### Output

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

## Key phrase extraction

<!-- TODO: Update client docs link -->
Create a new function called `KeyPhraseExtractionExample()` that takes the client that you created earlier and call its [ExtractKeyPhrases()]() function. The result will contain the list of detected key phrases in `KeyPhrases` if successful, and an `ErrorMessage` if not. Print any detected key phrases.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### Output

```console
Key phrases:
    cat
    veterinarian
```
