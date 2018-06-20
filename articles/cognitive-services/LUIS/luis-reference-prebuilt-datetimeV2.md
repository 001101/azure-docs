---
title: LUIS Prebuilt entities datetimeV2 reference | Microsoft Docs
description: This article has datetimeV2 prebuilt entity information in Language Understanding (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2017
ms.author: v-geberr
---

## Prebuilt datetime entity resolution

The **builtin.datetimeV2** prebuilt entity extracts date and time values. These values resolve in a standardized format for client programs to consume. When an utterance has a date or time that isn't complete, LUIS includes _both past and future values_ in the endpoint response. 

## Example JSON 
The following example JSON response has a `builtin.datetimeV2` entity with a subtype of `datetime`. For examples of other types of datetimeV2 entities, see [Subtypes of datetimeV2](#subtypes-of-datetimev2)</a>.

```JSON
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 15,
    "endIndex": 30,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## JSON property descriptions

|Property name |Property type and description|
|---|---|
|Entity|**string** - Text extracted from the utterance with type of date, time, date range, or time range.|
|type|**string** - One of the [subtypes of datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** - The index in the utterance at which the entity begins.|
|endIndex|**int** - The index in the utterance at which the entity ends.|
|resolution|Has a `values` array that has one, two, or four [values of resolution](#values-of-resolution).|
|end|The end value of a time, or date range, in the same format as `value`. Only used if `type` is `daterange`, `timerange`, or `datetimerange`|

## Subtypes of datetimeV2

The **builtin.datetimeV2** prebuilt entity has the following subtypes, and examples of each are provided in the table that follows:
* builtin.datetimeV2.date
* builtin.datetimeV2.time
* builtin.datetimeV2.daterange
* builtin.datetimeV2.timerange
* builtin.datetimeV2.datetimerange
* builtin.datetimeV2.duration
* builtin.datetimeV2.set

## Values of resolution
* The array has one element if the date or time in the utterance is fully specified and unambiguous.
* The array has two elements if the datetimeV2 value is ambiguous. Ambiguity includes lack of specific year, time, or time range. See [Ambiguous dates](#ambiguous-dates) for examples. When the time is ambiguous for A.M. or P.M., both values are included.
* The array has four elements if the utterance has two elements with ambiguity. This ambiguity includes elements that have:
  * A date or date range that is ambiguous as to year
  * A time or time range that is ambiguous as to A.M. or P.M. For example, 3:00 April 3rd.

Each element of the `values` array may have the following fields: 

|Property name|Property description|
|--|--|
|timex|time, date, or date range expressed in TIMEX format that follows the [ISO 8601 standard](https://en.wikipedia.org/wiki/ISO_8601) and the TIMEX3 attributes for annotation using the TimeML language. This annotation is described in the [TIMEX guidelines](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|The subtype, which can be one of the following items: datetime, date, time, daterange, timerange, datetimerange, duration, set.|
|value|**Optional.** A datetime object in the Format yyyy:MM:dd  (date), HH:mm:ss (time) yyyy:MM:dd HH:mm:ss (datetime). If `type` is `duration`, the value is the number of seconds (duration) <br/> Only used if `type` is `datetime` or `date`, `time`, or `duration.|

## Valid date values

The **builtin.datetimeV2** supports dates between the following ranges:

| Min | Max |
|----------|-------------|
| 1st January 1900   | 31st December 2099 |

## Ambiguous dates

When it is unclear if the date is in the past or future, LUIS provides both values. One case of this occurrence is an utterance that includes the month and date, but not the year.  

For example, given the utterance "May 2nd":
* If today's date is May 3rd 2017, LUIS provides both "2017-05-02" and "2018-05-02" as values. 
* When today's date is May 1st 2017, LUIS provides both "2016-05-02" and "2017-05-02" as values.

The following example shows the resolution of the entity "may 2nd". This resolution assumes that today's date is a date between May 2nd 2017 and May 1st 2018.
Fields with `X` in the `timex` field are parts of the date that aren't explicitly specified in the utterance.

```
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## Date range resolution examples for numeric date

The `datetimeV2` entity extracts date and time ranges. The `start` and `end` fields specify the beginning and end of the range. For the utterance "May 2nd to May 5th", LUIS provides **daterange** values for both the current year and the next year. In the `timex` field, the `XXXX` values indicate the ambiguity of the year. `P3D` indicates the time period is three days long.

```
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## Date range resolution examples for day of week

The following example shows how LUIS uses **datetimeV2** to resolve the utterance "Tuesday to Thursday". In this example, the current date is June 19th. LUIS includes **daterange** values for both of the date ranges that precede and follow the current date.

```
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## Ambiguous time
The values array has two elements if the time, or time range is ambiguous as to AM or PM. When there's an ambiguous time, values have both the AM and PM times.

## Time range resolution example

The following example shows how LUIS uses **datetimeV2** to resolve the utterance "6pm to 7pm".

```
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

## Example JSON responses for entities

Prebuilt entity   |   Example utterance   |   JSON
------|------|------|
```builtin.datetimeV2.date```    |   ```tomorrow```   |```{ "type": "builtin.datetimeV2.date", "entity": "tomorrow", "resolution": {"values": [{"timex": "2017-06-21","type": "date", "value": "2017-06-21"}]} }``` |
```builtin.datetimeV2.date```    |   ```january 10 2009```   |```{ "type": "builtin.datetimeV2.date", "entity": "january 10 2009", "resolution": { "values": [ {"timex": "2009-01-10", "type": "date", "value": "2009-01-10" }] } }```|
```builtin.datetimeV2.date```    |   ```monday```    |```{ "entity": "monday", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "XXXX-WXX-1", "type": "date", "value": "2017-06-19" },{"timex": "XXXX-WXX-1","type": "date", "value": "2017-06-26"}]} }```|
```builtin.datetimeV2.daterange```    |   ```next week```   |```{ "entity": "next week", "type": "builtin.datetime.dateV2.daterange", "resolution": { "values": [{ "timex": "2017-W27", "type": "daterange", "start": "2017-06-26", "end": "2017-07-03"}] } }```|
```builtin.datetimeV2.date```    |   ```next monday```   |```{ "entity": "next monday", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "2017-06-26", "type": "date", "value": "2017-06-26" }] } }```|
```builtin.datetimeV2.time```      |   ```3 : 00```   |```{ "type": "builtin.datetimeV2.time", "entity": "3 : 00", "resolution": { "values": [{ "timex": "T03:00", "type": "time", "value": "03:00:00" }, { "timex": "T15:00", "type": "time", "value": "15:00:00" }]}	}```|
```builtin.datetimeV2.time```      |   ```4 pm```     |```{ "type": "builtin.datetimeV2.time", "entity": "4 pm", "resolution": { "values": [{"timex": "T16",  "type": "time", "value": "16:00:00"}] }	}```|
```builtin.datetimeV2.timerange```    |   ```6pm to 7pm```   |```{ "entity": "6pm to 7pm", "type": "builtin.datetime.dateV2.timerange", "resolution": { "values": [{ "timex": "(T18,T19,PT1H)", "type": "timerange", "start": "18:00:00", "end": "19:00:00"}] } }```|
```builtin.datetimeV2.datetimerange```      |   ```tomorrow morning```   |```{ "entity": "tomorrow morning", "type": "builtin.datetimev2.datetimerange", "resolution": { "values": [{"timex": "2017-06-21TMO","type": "datetimerange", "start": "2017-06-21 08:00:00", "end": "2017-06-21 12:00:00"}]} }```|
```builtin.datetimeV2.datetimerange```      |   ```tonight```  |```{ "entity": "tonight", "type": "builtin.datetimeV2.datetimerange", "resolution": { "values": [{"timex": "2017-06-20TNI","type": "datetimerange", "start": "2017-06-20 20:00:00", "end": "2017-06-20 23:59:59"}]} }```|
```builtin.datetimeV2.duration```      |    ```for 3 hours```    |```{ "type": "builtin.datetimeV2.duration", "entity": "3 hours", "resolution": { "values": [{ "timex": "PT3H", "type": "duration", "value": "10800"}] } }```|  
```builtin.datetimeV2.duration```      |    ```30 minutes long```   |```{ "type": "builtin.datetimeV2.duration", "entity": "30 minutes", "resolution": { "values": [{ "timex": "PT30M", "type": "duration", "value": "1800"}] }	}```|    
```builtin.datetimeV2.duration```      |    ```all day```    |```{ "type": "builtin.datetimeV2.duration", "entity": "all day", "resolution": { "values": [{ "timex": "P1D", "type": "duration", "value": "86400"}] }	}```|
```builtin.datetimeV2.set```    |   ```daily```   |```{ "type": "builtin.datetimeV2.set", "entity": "daily", "resolution": { "values": [{ "timex": "P1D", "type": "set", "value": "not resolved"}]}	}```|
```builtin.datetimeV2.set```    |   ```every tuesday```   |```{ "entity": "every tuesday", "type": "builtin.datetimeV2.set", "resolution": { "values": [{ "timex": "XXXX-WXX-2", "type": "set", "value": "not resolved"}]} }```|   
```builtin.datetimeV2.set```    |   ```every week```   |```{ "entity": "every week", "type": "builtin.datetimeV2.set", "resolution": {"time": "XXXX-WXX"} }```|

## builtin.datetime

<!--The **builtin.datetime** prebuilt entity is aware of the current date and time. In the following examples, the current date is 2017-06-21. Also, the **builtin.datetime** entity provides a resolution field that produces a machine-readable dictionary. -->

The **builtin.datetime** prebuilt entity is deprecated and replaced by [builtin.datetimeV2](#builtindatetimev2). 

To replace **builtin.datetime** with **builtin.datetimeV2** in your LUIS app, complete the following steps:

1. Open the **Entities** pane of the LUIS web interface. 
2. Delete the **datetime** prebuilt entity.
3. Click **Add prebuilt entity**
4. Select **datetimeV2** and click **Save**.

## Replacing datetime with datetimeV2

The following table provides a comparison of datetime and datetimeV2. In the examples, the current date is 2017-06-20.

Prebuilt entity   |   Example utterance   |   JSON
------|------|------|
```builtin.datetime.date```      |   ```tomorrow```   |```{ "type": "builtin.datetime.date", "entity": "tomorrow", "resolution": {"date": "2017-06-21"} }``` |
```builtin.datetimeV2.date```    |   ```tomorrow```   |```{ "type": "builtin.datetimeV2.date", "entity": "tomorrow", "resolution": {"values": [{"timex": "2017-06-21","type": "date", "value": "2017-06-21"}]} }``` |
```builtin.datetime.date```      |   ```january 10 2009```   |```{ "type": "builtin.datetime.date", "entity": "january 10 2009", "resolution": {"date": "2009-01-10"} }```|
```builtin.datetimeV2.date```    |   ```january 10 2009```   |```{ "type": "builtin.datetimeV2.date", "entity": "january 10 2009", "resolution": { "values": [ {"timex": "2009-01-10", "type": "date", "value": "2009-01-10" }] } }```|
```builtin.datetime.date```      |   ```monday```    |```{ "entity": "monday", "type": "builtin.datetime.date", "resolution": {"date": "XXXX-WXX-1"} }```|
```builtin.datetimeV2.date```    |   ```monday```    |```{ "entity": "monday", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "XXXX-WXX-1", "type": "date", "value": "2017-06-19" },{"timex": "XXXX-WXX-1","type": "date", "value": "2017-06-26"}]} }```|
```builtin.datetime.date```      |   ```next week```   |```{ "entity": "next week", "type": "builtin.datetime.date", "resolution": {"date":  "2017-W26"} }```|
```builtin.datetimeV2.daterange```    |   ```next week```   |```{ "entity": "next week", "type": "builtin.datetime.dateV2.daterange", "resolution": { "values": [{ "timex": "2017-W27", "type": "daterange", "start": "2017-06-26", "end": "2017-07-03"}] } }```|
```builtin.datetime.date```      |   ```next monday```   |```{ "entity": "next monday", "type": "builtin.datetime.date", "resolution": {"date": "2017-06-26"} }```|
```builtin.datetimeV2.date```    |   ```next monday```   |```{ "entity": "next monday", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "2017-06-26", "type": "date", "value": "2017-06-26" }] } }```|
```builtin.datetime.time```      |   ```3 : 00```   |```{ "type": "builtin.datetime.time", "entity": "3 : 00", "resolution": {"comment": "ampm", "time": "T03:00"}	}```|
```builtin.datetimeV2.time```      |   ```3 : 00```   |```{ "type": "builtin.datetimeV2.time", "entity": "3 : 00", "resolution": { "values": [{ "timex": "T03:00", "type": "time", "value": "03:00:00" }, { "timex": "T15:00", "type": "time", "value": "15:00:00" }]}	}```|
```builtin.datetime.time```      |   ```4 pm```     |```{ "type": "builtin.datetime.time", "entity": "4 pm", "resolution": {"time": "T16"}	}```|
```builtin.datetimeV2.time```      |   ```4 pm```     |```{ "type": "builtin.datetimeV2.time", "entity": "4 pm", "resolution": { "values": [{"timex": "T16",  "type": "time", "value": "16:00:00"}] }	}```|
```builtin.datetime.time```      |   ```tomorrow morning```   |```{ "entity": "tomorrow morning", "type": "builtin.datetime.time", "resolution": {"time": "2015-08-15TMO"} }```|
```builtin.datetimeV2.datetimerange```      |   ```tomorrow morning```   |```{ "entity": "tomorrow morning", "type": "builtin.datetimev2.datetimerange", "resolution": { "values": [{"timex": "2017-06-21TMO","type": "datetimerange", "start": "2017-06-21 08:00:00", "end": "2017-06-21 12:00:00"}]} }```|
```builtin.datetime.time```      |   ```tonight```  |```{ "entity": "tonight", "type": "builtin.datetime.time", "resolution": {"time": "2015-08-14TNI"} }```|
```builtin.datetimeV2.datetimerange```      |   ```tonight```  |```{ "entity": "tonight", "type": "builtin.datetimeV2.datetimerange", "resolution": { "values": [{"timex": "2017-06-20TNI","type": "datetimerange", "start": "2017-06-20 20:00:00", "end": "2017-06-20 23:59:59"}]} }```|
```builtin.datetime.duration```      |    ```for 3 hours```    |```{ "type": "builtin.datetime.duration", "entity": "3 hours", "resolution": {"duration": "PT3H"} }```|
```builtin.datetimeV2.duration```      |    ```for 3 hours```    |```{ "type": "builtin.datetimeV2.duration", "entity": "3 hours", "resolution": { "values": [{ "timex": "PT3H", "type": "duration", "value": "10800"}] } }```|
```builtin.datetime.duration```      |    ```30 minutes long```   |```{ "type": "builtin.datetime.duration", "entity": "30 minutes", "resolution": {"duration": "PT30M"}	}```|    
```builtin.datetimeV2.duration```      |    ```30 minutes long```   |```{ "type": "builtin.datetimeV2.duration", "entity": "30 minutes", "resolution": { "values": [{ "timex": "PT30M", "type": "duration", "value": "1800"}] }	}```|    
```builtin.datetime.duration```      |    ```all day```    |```{ "type": "builtin.datetime.duration", "entity": "all day", "resolution": {"duration": "P1D"}	}```|
```builtin.datetimeV2.duration```      |    ```all day```    |```{ "type": "builtin.datetimeV2.duration", "entity": "all day", "resolution": { "values": [{ "timex": "P1D", "type": "duration", "value": "86400"}] }	}```|
```builtin.datetime.set```    |   ```daily```   |```{ "type": "builtin.datetime.set", "entity": "daily", "resolution": "set": {"XXXX-XX-XX"}	}```|
```builtin.datetimeV2.set```    |   ```daily```   |```{ "type": "builtin.datetimeV2.set", "entity": "daily", "resolution": { "values": [{ "timex": "P1D", "type": "set", "value": "not resolved"}]}	}```|
```builtin.datetime.set```    |   ```every tuesday```   |```{ "entity": "every tuesday", "type": "builtin.datetime.set", "resolution":  {"time": "XXXX-WXX-2"} }```|  
```builtin.datetimeV2.set```    |   ```every tuesday```   |```{ "entity": "every tuesday", "type": "builtin.datetimeV2.set", "resolution": { "values": [{ "timex": "XXXX-WXX-2", "type": "set", "value": "not resolved"}]} }```|   
```builtin.datetime.set```    |   every week   |```{ "entity": "every week", "type": "builtin.datetime.set", "resolution": {"time": "XXXX-WXX"} }```|
```builtin.datetimeV2.set```    |   every week   |```{ "entity": "every week", "type": "builtin.datetimeV2.set", "resolution": {"time": "XXXX-WXX"} }```|

<!-- TODO: Re-add when the behavior of the following work as intended 
builtin.datetime.set    |   every morning   |```{ "type": "builtin.datetime.set", "entity": "every morning", "resolution": {"time": "XXXX-XX-XXTMO"}	}```|
builtin.datetimeV2.timerange    |   every morning   |```{ "type": "builtin.datetimeV2.timerange", "entity": "morning", "resolution": { "values": [{"timex": "TMO", "type": "timerange", "start": "08:00:00", "end": "12:00:00"}]	} }```|

builtin.datetime.date      |   week of september 30th   |```{ "entity": "week of september 30th", "type": "builtin.datetime.date", "resolution": {"comment": "weekof", "date": "XXXX-09-30"} }```|
builtin.datetimeV2.date      |   week of september 30th   |```{ "entity": "september 30th", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "XXXX-09-30", "type": "date", "value": "2016-09-30" },{"timex": "XXXX-09-30","type": "date", "value": "2017-09-30" }]} }```|
-->

*The **builtin.datetime** prebuilt entity is deprecated and replaced by [builtin.datetimeV2](#builtindatetimev2). 