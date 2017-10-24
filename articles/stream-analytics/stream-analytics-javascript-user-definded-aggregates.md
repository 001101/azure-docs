---
title: Azure Stream Analytics JavaScript user-defined aggregates | Microsoft Docs
description: Perform advanced query mechanics with JavaScript user-defined aggregates
keywords: javascript, user defined aggregates, uda
services: stream-analytics
author: minhe
manager: santoshb
editor: cgronlun

ms.assetid:
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/28/2017
ms.author: minhe
---
# Azure Stream Analytics JavaScript User-Defined Aggregates (Preview)

Azure Stream Analytics supports user-defined aggregates (UDA) written in JavaScript. With JavaScript UDA you can implement state-ful business logics by defining the state data structure, how state is accumulated and decumulated, and how aggregate result is calculated. The article introduces the two different JavaScript UDA interfaces, steps to create a UDA, and how to use UDA with window-based operations in Stream Analytics query.

## JavaScript user-defined aggregates

A user-defined aggregate is used on top of a time window specification to aggregate over the events in that window and produce a single result value. There are two types of aggregate, AccumulateOnly and AccumulateDeaccumulate. Both types of UDA can be used by TumblingWindow, HoppingWindow, and SlidingWindow; but AccumulateDeaccumulate UDA performs better than AccumulateOnly UDA when using with HoppingWindow and SlidingWindow. You choose one of the two types based on the algorithm you use.

### AccumulateOnly aggregates

AccumulateOnly aggregates can only accumulate new events to its state, the algorithm does not allow deaccumulation of values. You should choose this aggregate type when deaccumulate that information from the state value is imppossible or hard to implement. Below is the JavaScript template for AccumulatOnly aggregates:

````JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

### AccumulateDeaccumulate aggregates

AccumulateDeaccumulate aggregates can perform both actions, the algorithm allow deaccumulation of a previous accumulated value. State is for example a list of event values, or a sum, in which deaccumulation is a simple remove or subtraction. Below is the JavaScript template for AccumulateDeaccumulate aggregates:

````JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

## UDA - JavaScript function declaration

Each JavaScript UDA is defined by a Function object declaration. Below are the major elements in a UDA definition.

### Function name
The name of this Function object. The function name should literally match the UDA alias (preview behavior, we are considering support anonymous function when GA). When called in Stream Analytics query, UDA alias with a “uda.” prefix will be used as the UDA identifier.

### Method - init()
The init() method initializes state of the aggregate. This method is called when window starts.

### Method – accumulate()
The accumulate() method calculate the UDA state based on the previous state and the current event values. This method is called when a event enters a time window (TUMBLINGWINDOW, HOPPINGWINDOW, or SLIDINGWINDOW).

### Method – deaccumulate()
The deaccumulate() method recalculate state based on the previous state and the current event values. This method is called when a event leaves a SLIDINGWINDOW.

### Method – deaccumulateState()
The deaccumulateState() method recalculate state based on the previous state and the state of a hop. This method is called when a set of events leave a HOPPINGWINDOW.

### Method – computeResult()
The computeResult() method returns aggregate result based on the current state. This method is called at the end of a time window (TUMBLINGWINDOW, HOPPINGWINDOW, and SLIDINGWINDOW).

## Adding a JavaScript UDF from Azure Portal
You can implement UDAs that are more complex in their functionality, than the simpler aggregates similar to count, sum, and average provided by Stream Analytics. One such example, computing time weighted averages, is discussed in this section.

Now let’s create a simple JavaScript UDF under an existing ASA job by following below steps.
1.	Logon to Azure Portal with below feature preview URL.
https://portal.azure.com/?microsoft_azure_streamanalytics_udafunctions=true

2.	Locate your Stream Analytics job, then click on functions  link under “JOB TOPOLOGY”.

3.	Click on the “Add” icon to add a new function.

4.	On the “New Function” blade, select “JavaScript UDA” as the Function Type, then you will see a default UDA template show up in the editor.

5.	Fill in “TWA” as the UDF alias and change the function implementation as below:

````JavaScript
// Sample UDA which calculate Time-Weighted Average of incoming values.
function main() {
    this.init = function () {
        this.totalValue = 0.0;
        this.totalWeight = 0.0;
    }

    this.accumulate = function (value, timestamp) {
        this.totalValue += value.level * value.weight;
        this.totalWeight += value.weight;

    }

    // Uncomment below for AccumulateDeaccumulate implementation
    /*
    this.deaccumulate = function (value, timestamp) {
        this.totalValue -= value.level * value.weight;
        this.totalWeight -= value.weight;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
        this.totalValue -= otherState.totalValue;
        this.totalWeight -= otherState.totalWeight;
    }
    */

    this.computeResult = function () {
        if(this.totalValue == 0) {
            result = 0;
        }
        else {
            result = this.totalValue/this.totalWeight;
        }
        return result;
    }
}
````

6.	Once you click the “Save” button, your UDA will show up on the function list.

7.	Click on the new function “TWA”, you can check the function definition.

8.	Please note, the UDA alias and the JavaScript function name should be exactly the same. For our example, both the alias and the JavaScription function name are “TWA”.

## Calling JavaScript UDF in ASA query
In Azure Portal and open your job, edit your query and call TWA() function with a mandate prefix “uda.”. For example:

````SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
````

## Testing Query with UDA
Create a local JSON file with below content, uploade the file to Stream Analytics job, test above query.

````JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
````

