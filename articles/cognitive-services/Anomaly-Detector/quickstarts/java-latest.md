---
title: Detect latest point anomaly status  - Java - Anomaly Finder - Microsoft Cognitive Services | Microsoft Docs
description: Get information and code samples to help you quickly get started using Java and the Anomaly Finder in Cognitive Services.
services: cognitive-services
author: siji
manager: bix

ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: kefre
---

# Detect latest point anomaly status  - Java - Anomaly Finder

This article provides information and code samples to help you quickly get started using the Anomaly Finder API. With the [Detect latest point anomaly status](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyFinderV2/operations/post-timeseries-last-detect) method, you can accomplish task of detecting latest point anomaly status.

## Prerequisites

- You must have [Java&trade; Platform, Standard Edition Development Kit 7 or 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (JDK 7 or 8) installed.

- You must have a subscription key, refer to [Obtaining Subscription Keys](../How-to/get-subscription-key.md).


## Detect latest point anomaly status

### Example of time series data

To see the time series data used in the example click [here](../includes/request.md).

### Detect latest point anomaly status example

To run the sample, perform the following steps:
1. Create a new Command-Line App.
2. Replace the Main class with the following code (keep any `package` statements).
3. Replace the `[YOUR_SUBSCRIPTION_KEY]` value with your valid subscription key.
4. Replace the `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` with the example or your own data points.
5. Download these global libraries from the Maven Repository to the `lib` directory in your project:
   * `org.apache.httpcomponents:httpclient:4.5.2`
6. Run 'Main'.

```java

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    public static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

    // Replace the endpoint string value with your endpoint.
    public static final String uriBase = "https://[REPLACE_WITH_ENDPOINT]/anomalyfinder/v2.0/timeseries/last/detect";

    public static void main(String[] args) {
        final String content = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";

        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost request = new HttpPost(uriBase);

        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        try {
            StringEntity params = new StringEntity(content);
            request.setEntity(params);

            CloseableHttpResponse response = client.execute(request);
            try {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    System.out.println("----------");
                    System.out.println(response.getStatusLine());
                    System.out.println("Response content is :\n");
                    System.out.println(EntityUtils.toString(respEntity, "utf-8"));
                    System.out.println("----------");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            } finally {
                response.close();
            }

        } catch (Exception ex) {
            System.err.println("Exception on Anomaly Finder: " + ex.getMessage());
            ex.printStackTrace();
        } finally {
            try {
                client.close();
            } catch (Exception e) {
                System.err.println("Exception on closing HttpClient: " + e.getMessage());
                e.printStackTrace();
            }
        }
    }
}

```

### Example response

A successful response is returned in JSON. Click [here](../includes/response-latest.md) to see the response from the example.

## Next steps

> [Java app](../tutorials/java-tutorial.md)
