---
title: Process change feed in Azure Blob Storage (Preview) | Microsoft Docs
description: Learn how to process change feed logs in a .NET client application
author: normesta
ms.author: normesta
ms.date: 10/20/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
---

# Process change feed in Azure Blob Storage (Preview)

Change feed provides transaction logs of all the changes that occur to the blobs and the blob metadata in your storage account. This article shows you how to read change feed records by using the blob change feed processor library that is provided with the SDK.

To learn more about the change feed, see [Change feed in Azure Blob Storage (Preview)](storage-blob-change-feed.md).

> [!NOTE]
> The change feed is in public preview, and is available in the **westcentralus** and **westus2** regions. To learn more about this feature along with known issues and limitations, see [Change feed support in Azure Blob Storage](storage-blob-change-feed.md).

## Connect to the storage account

Parse the connection string by calling the [CloudStorageAccount.TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) method. 

Then, create an object that represents Blob storage in your storage account by calling the [CloudStorageAccount.CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet) method.

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## Initialize the change feed

Add the following using statements to the top of your code file. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Then, create an instance of the **ChangeFeed** class by calling the **GetContainerReference** method. Pass in the name of the change feed container.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## Reading records

> [!NOTE]
> The change feed is an immutable and read-only entity in your storage account. Any number of applications can choose to read and process the change feed simultenosly and independently at thier own convenience. Records are not removed from the change feed when an application reads them. The read or iteration state of each consuming reader is independent and maintained by your application only.

The simplest way to read records is to create an instance of the **ChangeFeedReader** class. 

This example iterates through all records in the change feed, and then prints to the console a few values from each record. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## Resuming reading records from a saved position

You can choose to save your read position in your change feed and resume iterating the records at a future time. You can save the state of your iteration of the change feed at any time using the **ChangeFeedReader.SerializeState()** method. The state is a **string** and you application can choose to save it based on your needs for eg. to a database or a file.

```csharp
    string currentReadState = processor.SerializeState();
```

You can resume iterating from the last state by creating the **ChangeFeedReader** using the **CreateChangeFeedReaderFromPointerAsync** method

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await cf.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## Stream processing of records

You can choose to wait on the tail of appended records in the change feed and process them as they arrive. See [Specifications](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## Reading records within a time range

Change feed is organized into hourly segments based on the change event time. See [Specifications](storage-blob-change-feed.md#specifications). You can choose to read records within happened within a time range by reading the records from the change feed segments in that time range.

### Selecting segments for a time range

```csharp
public List<DateTimeOffset> GetChangeFeedSegmentRefsForTimeRange(ChangeFeed cf, DateTimeOffset st, DateTimeOffset en)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = st.AddMinutes(-15);
    DateTimeOffset enAdj = en.AddMinutes(15);

    DateTimeOffset lastConsumable = cf.LastConsumable;

    List<DateTimeOffset> segments = (await cf.ListAvailableSegmentTimesAsync()).ToList();
    foreach(segmentStart in segments)
    {
        if(lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if(enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && segStart.CompareTo(enAdj) < 0;
        if(overlaps)
        {
            result.Add(segmentStart)
        }
    }

    return result;
}
```

### Reading records in a segment

You can read records from individual segments or ranges of segments.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, cf);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await currentWindow.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## Read records starting from a time

You can choose to read the records of the change feed from a starting segment till the end. Similar to reading records within a time range, you can list the segments and choose a segment to start iterating from.

```csharp
public DateTimeOffset GetChangeFeedSegmentRefAfterTime(ChangeFeed cf, DateTimeOffset timestamp)
{
    DateTimeOffset result;

    DateTimeOffset lastConsumable = cf.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if(lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await cf.ListAvailableSegmentTimesAsync()).ToList();
    foreach(segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if(timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);
    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsWindowConsumableAsync(segment, changeFeed))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentRecord.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (currentWindow.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        ChangeFeedSegment nextWindow = await currentWindow.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await currentWindow.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changefeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> A segment of the can have change feed logs in one or more *chunkFilePath*. In case of multiple *chunkFilePath* the system has internally partitioned the records into multiple shards to manage publishing throughput. It is guaranteed that each partition of the segment will contain changes for mututally exclusive blobs and can be processed independently without voilating the ordering. You can use the **ChangeFeedSegmentShardReader** class to iterate through records at the shard level if that's most efficient for your scenario.

## Next steps

Learn more about change feed logs. See [Change feed in Azure Blob Storage (Preview)](storage-blob-change-feed.md)
