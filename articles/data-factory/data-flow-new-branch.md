---
title: Multiple branches in mapping data flow
description: Replicating data streams in mapping data flow with multiple brnaches
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
---

# Creating a new branch in mapping data flow

Add a new branch to perform multiple sets of operations and transformations against the same data stream. Adding a new branch is useful when you want to use the same source to for multiple sinks or for self-joining data together.

A new branch can be added from the transformation list similar to other transformations. **New Branch** will only be available as an action when there is a subsequent transformation following the transformation you're attempting to branch.
![Adding a new branch](media/data-flow/new-branch2.png "Adding a new branch")

In the below example, the data flow is reading taxi trip data. Output aggregated by both day and vendor is required. Instead of creating two separate data flows that read from the same source, a new branch can be added. This way both aggregations can be executed as part of the same data flow. 

![Adding a new branch](media/data-flow/new-branch.png "Adding a new branch")
