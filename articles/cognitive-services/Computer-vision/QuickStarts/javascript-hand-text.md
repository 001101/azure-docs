---
title: "Quickstart: Computer Vision 2.0 and 2.1 - Extract printed and handwritten text - REST, JavaScript"
titleSuffix: "Azure Cognitive Services"
description: In this quickstart, you extract printed and handwritten text from an image using the Computer Vision API with JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme

ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: seodec18
---
# Quickstart: Extract printed and handwritten text using the Computer Vision REST API and JavaScript

In this quickstart, you'll extract printed and handwritten text from an image using the Computer Vision REST API. With the [Batch](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) and [Get Read Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) methods, you can detect text in an image and extract recognized characters into a machine-readable character stream. 



> [!IMPORTANT]
> The [Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) method runs asynchronously. This method does not return any information in the body of a successful response. Instead, the Batch Read method returns a URI in the value of the `Operation-Location` response header field. You can then call this URI, which represents the [Get Read Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) API, to both check the status and return the results of the Read method call.

## Prerequisites

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) before you begin.

- You must have a subscription key for Computer Vision. You can get a free trial key from [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Or, follow the instructions in [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) to subscribe to Computer Vision and get your key. 


## Create and run the sample


To create and run the sample, do the following steps:

1. Copy the following code into a text editor.
1. Optionally, replace the value of the `value` attribute for the `inputImage` control with the URL of a different image from which you want to extract text.
1. Save the code as a file with an `.html` extension. For example, `get-text.html`.
1. Open a browser window.
1. When the webpage is displayed in the browser, fill the required parameters, and choose the **Read image** button.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        let subscriptionKey = document.getElementById("key").value;
        let endpoint = document.getElementById("endpoint").value;
        if (!subscriptionKey) { throw new Error('Please enter your subscription key and endpoint.'); }
        
        var uriBase = endpoint + "/vision/v3.0/read/analyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;


        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase,

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: JSON.stringify({url: sourceImageUrl}),
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Endpoint: 
<input type="text" name="endpoint" id="endpoint" value="" style="width: 300px;"/>
<div style="margin: 20px;">Example: https://westus2.api.cognitive.microsoft.com</div>
Subscription Key:    
<input type="text" name="key" id="key" value="" style="width: 300px;"/>
<br><br>

Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
```



## Examine the response

A successful response is returned in JSON. The sample webpage parses and displays a successful response in the browser window, similar to the following example:


```json
{
  "status": "succeeded",
  "createdDateTime": "2020-02-11T20:56:33Z",
  "lastUpdatedDateTime": "2020-02-11T20:56:34Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8206,
        "width": 800,
        "height": 154,
        "unit": "pixel",
        "lines": [
          {
            "language": "en",
            "boundingBox": [
              6,
              4,
              774,
              14,
              773,
              61,
              5,
              49
            ],
            "text": "The quick brown fox jumps over the lazy",
            "words": [
              {
                "boundingBox": [
                  14,
                  5,
                  76,
                  6,
                  74,
                  49,
                  12,
                  48
                ],
                "text": "The",
                "confidence": 0.83
              },
              {
                "boundingBox": [
                  84,
                  6,
                  182,
                  7,
                  180,
                  51,
                  82,
                  49
                ],
                "text": "quick",
                "confidence": 0.762
              },
              {
                "boundingBox": [
                  191,
                  7,
                  312,
                  9,
                  309,
                  54,
                  189,
                  51
                ],
                "text": "brown",
                "confidence": 0.67
              },
              {
                "boundingBox": [
                  320,
                  9,
                  382,
                  10,
                  379,
                  55,
                  317,
                  54
                ],
                "text": "fox",
                "confidence": 0.849
              },
              {
                "boundingBox": [
                  390,
                  10,
                  497,
                  11,
                  493,
                  57,
                  387,
                  55
                ],
                "text": "jumps",
                "confidence": 0.703
              },
              {
                "boundingBox": [
                  506,
                  11,
                  596,
                  12,
                  591,
                  59,
                  502,
                  57
                ],
                "text": "over",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  604,
                  12,
                  666,
                  13,
                  661,
                  60,
                  600,
                  59
                ],
                "text": "the",
                "confidence": 0.923
              },
              {
                "boundingBox": [
                  674,
                  13,
                  773,
                  14,
                  768,
                  62,
                  670,
                  60
                ],
                "text": "lazy",
                "confidence": 0.863
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              5,
              53,
              79,
              56,
              77,
              95,
              4,
              92
            ],
            "text": "dog",
            "words": [
              {
                "boundingBox": [
                  6,
                  53,
                  74,
                  56,
                  72,
                  95,
                  5,
                  92
                ],
                "text": "dog",
                "confidence": 0.418
              }
            ]
          },
          {
            "language": "en",
            "boundingBox": [
              0,
              90,
              787,
              95,
              787,
              145,
              0,
              136
            ],
            "text": "Pack my box with five dozen liquor jugs",
            "words": [
              {
                "boundingBox": [
                  1,
                  96,
                  79,
                  93,
                  79,
                  135,
                  0,
                  136
                ],
                "text": "Pack",
                "confidence": 0.835
              },
              {
                "boundingBox": [
                  87,
                  93,
                  151,
                  92,
                  151,
                  135,
                  87,
                  135
                ],
                "text": "my",
                "confidence": 0.88
              },
              {
                "boundingBox": [
                  162,
                  92,
                  226,
                  91,
                  225,
                  135,
                  161,
                  135
                ],
                "text": "box",
                "confidence": 0.301
              },
              {
                "boundingBox": [
                  234,
                  91,
                  335,
                  90,
                  335,
                  135,
                  233,
                  135
                ],
                "text": "with",
                "confidence": 0.959
              },
              {
                "boundingBox": [
                  346,
                  91,
                  418,
                  91,
                  417,
                  136,
                  345,
                  135
                ],
                "text": "five",
                "confidence": 0.489
              },
              {
                "boundingBox": [
                  426,
                  91,
                  527,
                  93,
                  527,
                  138,
                  425,
                  136
                ],
                "text": "dozen",
                "confidence": 0.727
              },
              {
                "boundingBox": [
                  554,
                  94,
                  687,
                  98,
                  687,
                  143,
                  553,
                  139
                ],
                "text": "liquor",
                "confidence": 0.377
              },
              {
                "boundingBox": [
                  701,
                  99,
                  787,
                  103,
                  787,
                  146,
                  700,
                  143
                ],
                "text": "jugs",
                "confidence": 0.693
              }
            ]
          }
        ]
      }
    ]
  }
}
```


## Next steps

Explore a JavaScript application that uses Computer Vision to perform optical character recognition (OCR); create smart-cropped thumbnails; and detect, categorize, tag, and describe visual features in images. 

> [!div class="nextstepaction"]
> [Computer Vision API JavaScript Tutorial](../Tutorials/javascript-tutorial.md)

* To rapidly experiment with the Computer Vision API, try the [Open API testing console](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
