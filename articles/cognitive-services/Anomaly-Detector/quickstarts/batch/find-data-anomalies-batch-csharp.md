---
title: Find anomalies as a batch using the Anomaly Finder REST API and C# | Microsoft Docs
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

# Find anomalies throughout time series data using the Anomaly Detector REST API and C# 

Use this quickstart to begin using the Anomaly Detector API to find anomalies in your time series data as a batch of data points. This C# application sends a batch JSON-formatted data points to the API, and gets the response. The API will generate and apply a statistical model to the data set, and each point is analyzed with the same model. While this application is written in C#, the API is a RESTful Web service compatible with most programming languages.

## Prerequisites

- Any edition of [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Json.NET](https://www.newtonsoft.com/json)
    - To install Json.NET as a NuGet package in Visual studio:
        1. Right click in the **Solution Manager**
        2. Click **Manage NuGet Packages...**
        3. Search for `Json.NET` and install the package
- If you are using Linux/MacOS, this application can be run using [Mono](http://www.mono-project.com/).
- A batch of time series data points. The data for this quickstart can be found TBD

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

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

2. Create variables for your subscription key, and your endpoint. To detect anomalies in a batch of data points, use the URL `anomalyfinder/v2.0/timeseries/entire/detect`. Then create a string with a path to the JSON formatted time series data.
    
    ```csharp
        const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
        const string endpoint = "https://westus2.api.cognitive.microsoft.com/";
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

## Send the API request and get a response

1. In the main function of your application, perform the following steps. Read in the file containing your JSON time series data.

    ```csharp
    var requestData = File.ReadAllText(dataPath);
    ```

2. Construct the request and send it by calling the `Request()` function with: your endpoint, subscription key, the URL for batch anomaly detection, and time series data.

    ```csharp
    //construct the request
    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;
    ```

### Display the response and find anomaly locations

3. Deserialize the JSON object, and write it to the console. 
    ```csharp
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
    ```

4. The response's `IsAnomaly` field contains an array of boolean values relating to whether a given data point is an anomaly. Convert this to a string array.

    ```csharp
    string[] anomalies = jsonObj["IsAnomaly"].ToObject<string[]>();
    ```

5. Iterate through the array, and print the index of any `true` values. These values correspond to the index of anomalous data points, if any were found.
    
    ```csharp
    System.Console.WriteLine("Anomalies found in the following data positions:");
    for (var i = 0; i < anomalies.Length; i++){
        if (anomalies[i] == "True"){
            System.Console.Write(i+", ");
        }
    }
    System.Console.WriteLine("\nPress any key to exit ");
    System.Console.ReadKey();
    ```

### Example response

A successful response is returned in JSON. Click TBD to see the response from this quickstart.

## Next steps

> [!div class="nextstepaction"]
> [Visualize anomalies using Python](../tutorials/visualize-anomalies-using-python.md)

> [REST API reference](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyFinderV2/operations/post-timeseries-entire-detect)

> [Find time series anomalies in a batch of data points](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyFinderV2/operations/post-timeseries-entire-detect)