---
title: Local Search API Node quickstart | Microsoft Docs
description: How to start using the Bing Local Search API in Node.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: bing-local-search
ms.topic: article
ms.date: 08/02/2018
ms.author: rosh, v-gedod
---

# Local Search Node quickstart
 
The following Node example gets local response data from the query for a *hotel in Bellevue*.

## Prerequisites
You must have a [Cognitive Services API account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) with Bing APIs. The [free trial](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) is sufficient for this quickstart. Use the access key provided by the free trial.

##Code scenario
The following code gets defines and sends the request. It is implemented in the following steps:

1. Declare variables to specify the endpoint by host and path.
2. Specify the query, and add the query parameter. 
3. Create a handler function for the response.
4. Define the Search function that creates the request and adds the Ocp-Apim-Subscription-Key header.
5. Run the Search function. 

The complete code for this demo follows:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'www.bingapis.com';
let path = '/api/v7/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&appid=" + accessKey + "&traffictype=Internal_monitor&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

````

## Next steps
- [Local Search endpoint](local-search-endpoint.md)
- [Local Search quickstart](local-quickstart.md)
- [Local Search Java quickstart](local-search-java-quickstart.md)
- [Local Search Python quickstart](local-search-python-quickstart.md)
