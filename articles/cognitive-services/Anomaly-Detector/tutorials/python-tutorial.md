---
title: Anomaly Finder Python app - Microsoft Cognitive Services | Microsoft Docs
description: Explore a Python notebook that uses the Anomaly Finder API in Microsoft Cognitive Services. Send original data points to API and get the expected value and anomaly points.
services: cognitive-services
author: chliang
manager: bix

ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author:  chliang
---

# Anomaly Finder Python application

The tutorial shows how to use the Anomaly Finder API in Python and how to visualize your results using popular libraries. Using Jupyter to run the tutorial and trying your own data with your subscription key. To learn how to get started
with interactive Jupyter notebooks, refer to [Jupyter Documentation](http://jupyter.readthedocs.io/en/latest/index.html).

## Prerequisites

### Subscribe to Anomaly Finder and get a subscription key 

- You must have a subscription key, refer to [Obtaining Subscription Keys](../How-to/get-subscription-key.md).

## Download the example code

1. Navigate to the [tutorial notebook in Github](https://github.com/MicrosoftAnomalyDetection/python-sample-v2).
2. Click on the green button to clone or download the tutorial. 

## Opening the tutorial notebook in Jupyter

1. Open a command prompt and go to the folder python-sample.
2. Run the command Jupyter notebook from the command prompt, which will start Jupyter.
3. In the Jupyter window, click on <em>Anomaly Finder API Example.ipynb</em> to open the tutorial notebook.


## Running the tutorial

To use this notebook, you would need a subscription key for the Anomaly Finder API. Please refer to [Obtaining Subscription Keys](../How-to/get-subscription-key.md). After completing the process, paste your key into the variables section of the notebook as follows. Either the primary or the secondary key works.
Make sure to enclose the key in quotes to make it a string.

```Python
            # Variables
            # Depending on what anomaly detection API you want to use, detect last point or all points from entire series you posted, you should use the right endpoint
            endpoint = 'https://westus2.api.cognitive.microsoft.com/anomalyfinder/v2.0/timeseries/entire/detect'
            # endpoint = 'https://westus2.api.cognitive.microsoft.com/anomalyfinder/v2.0/timeseries/last/detect'
            subscription_key = '' #Here you should paste your primary key between the quote ''

```

## Next steps

> [REST API reference](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyFinderV2/operations/post-timeseries-entire-detect)
