---
title: Find anomalies in your time series data using the Anomaly Finder REST API and C# | Microsoft Docs
description: Use the Anomaly Detector API to detect abnormalities in your data series as a batch.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/01/2019
ms.author: aahi
---

# Find anomalies in your time series data using the Anomaly Detector REST API and C# 

Use this quickstart to start using the Anomaly Detector API's two detection modes to find anomalies in your time series data. This C# application sends two API requests containing JSON-formatted time series data, and gets the response. 

| API request                                        | Application output                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detect anomalies as a batch                        | The JSON response containing the anomaly status (and other data) for each data point in the time series data, and the locations of any detected anomalies. |
| Detect the anomaly status of the latest data point | The JSON response containing the anomaly status (and other data) for the latest data point in the time series data.                                                                                                                                         |

 While this application is written in C#, the API is a RESTful Web service compatible with most programming languages.

## Prerequisites

- Any edition of [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Json.NET](https://www.newtonsoft.com/json)
    - To install Json.NET as a NuGet package in Visual studio:
        1. Right click in the **Solution Manager**
        2. Click **Manage NuGet Packages...**
        3. Search for `Json.NET` and install the package
- If you are using Linux/MacOS, this application can be run using [Mono](http://www.mono-project.com/).

- A batch of time series data points. The data for this quickstart can be found TBD

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## Create a new application

1. In Visual studio, create a new Console solution, and add the following packages. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Create variables for your subscription key, and your endpoint. Below are the URLs you can use for anomaly detection. These will later be appended to your endpoint to create the API request URLs.

    |Detection method  |URL  |
    |---------|---------|
    |Batch detection    | `/anomalyfinder/v2.0/timeseries/entire/detect`        |
    |Detection on the latest data point     | `/anomalyfinder/v2.0/timeseries/last/detect`        |
    
    ```csharp
        const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
        //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal.
        const string endpoint = "https://westus2.api.cognitive.microsoft.com/";
        const string latestPointDetectionUrl = "/anomalyfinder/v2.0/timeseries/last/detect";
        const string batchDetectionUrl = "/anomalyfinder/v2.0/timeseries/entire/detect";
        const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    ```

## Create a client to send requests

1. Create a new async function called `Request` that takes the variables created above.

2. Using a `HttpClient` object, set the client's security protocol, and header information. Be sure to add your subscription key to the `Ocp-Apim-Subscription-Key` header. Then create a `StringContent` object for the request.
 
3. Send the request with `PostAsync()`. If the request was successful, return the response.  

```csharp
static async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        if (res.IsSuccessStatusCode){
            return await res.Content.ReadAsStringAsync();
        }
        else{
            return $"ErrorCode: {res.StatusCode}";
        }
    }
}
```

## Detect anomalies as a batch

1. Create a new function called `detectAnomaliesBatch()`. Construct the request and send it by calling the `Request()` function with: your endpoint, subscription key, the URL for batch anomaly detection, and the time series data.

2. Deserialize the JSON object, and write it to the console. 

### Find the locations of detected data anomalies

3. The response's `IsAnomaly` field contains an array of boolean values relating to whether a given data point is an anomaly. Convert this to a string array with the response object's `ToObject<string[]>()` function.

4. Iterate through the array, and print the index of any `true` values. These values correspond to the index of anomalous data points, if any were found.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    string[] anomalies = jsonObj["IsAnomaly"].ToObject<string[]>();
    System.Console.WriteLine("\n Anomalies found in the following data positions:");
    for (var i = 0; i < anomalies.Length; i++){
        if (anomalies[i] == "True"){
            System.Console.Write(i + ", ");
        }
    }
}
```

## Get the anomaly status of the latest data point

1. Create a new function called `detectAnomaliesLatest()`. Construct the request and send it by calling the `Request()` function with: your endpoint, subscription key, the URL for latest point anomaly detection, and the time series data.

2. Deserialize the JSON object, and write it to the console. 

```csharp
static void detectAnomaliesLatest(string requestData){
    
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## load your time series data and send the request

1. In the main method of your application, load your JSON time series data with `File.ReadAllText()`. 

    [!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

2. Call the anomaly detection functions created above. Use `System.Console.ReadKey()` to keep the console window open after running the application.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

## Example response

A successful response is returned in JSON. Click TBD to see the response from this quickstart.

## Next steps

> [!div class="nextstepaction"]
> [Visualize anomalies using Python](../tutorials/visualize-anomalies-using-python.md)

* [REST API reference](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyFinderV2/operations/post-timeseries-entire-detect)
* [Find time series anomalies in a batch of data points](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyFinderV2/operations/post-timeseries-entire-detect)