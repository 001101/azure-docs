---
title: Monitor node and task resource states - Azure Batch | Microsoft Docs
description: Monitor the state of Azure Batch tasks and nodes to help manage and monitor Batch solutions.
services: batch
author: dlepow
manager: jeconnoc

ms.service: batch
ms.topic: article
ms.date: 06/26/2017
ms.author: danlep

---
# Monitor Batch solutions by counting node and tasks by state

To monitor and manage large-scale Azure Batch solutions, you need accurate counts of resources in various states. Azure Batch provides an efficient way to get these counts for Batch *tasks* and *compute nodes*. Use these operations instead of time-consuming API calls that return detailed information about large collections of tasks or nodes.

* [Get Task Counts][rest_get_task_counts] gets an aggregate count of active, running, and completed tasks in a job, and of tasks that succeeded or failed. By counting tasks in each state, you can more easily display job progress to a user, or detect unexpected delays or failures that may affect the job. Get Task Counts is available as of Batch Service API version 2017-06-01.5.1 and related SDKs and tools.

* [List Pool Node Counts][rest_get_node_counts] gets the number of dedicated and low-priority compute nodes in each pool that are in states including creating, idle, offline, preempted, rebooting, reimaging, starting, and others. By counting nodes in each state, you can determine when you have adequate compute resources to run your jobs, and identify potential resource bottlenecks. List Pool Node Counts is available as of Batch Service API version 2018-03-01.6.1
 and related SDKs and tools.

If you're using a version of the service that doesn't support the task count or node count operations, use a list query to count these resources instead. Also use a list query to get information about applications, files, jobs, and other Batch resources. For more information about applying filters to list queries, see [Create queries to list Batch resources efficiently](batch-efficient-list-queries.md).

## Task state counts

The Get Task Counts operation counts tasks by the following states:

- **Active** - A task that is queued and able to run, but is not currently assigned to a compute node. A task is also `active` if it is [dependent on a parent task](batch-task-dependencies.md) that has not yet completed. 
- **Running** - A task that has been assigned to a compute node, but has not yet completed. A task is counted as `running` when its state is either `preparing` or `running`, as indicated by the [Get information about a task][rest_get_task] operation.
- **Completed** - A task that is no longer eligible to run, because it either finished successfully, or finished unsuccessfully and also exhausted its retry limit. 
- **Succeeded** - A task whose result of task execution is `success`. Batch determines whether a task has succeeded or failed by checking the `TaskExecutionResult` property of the [executionInfo][rest_get_exec_info] property.
- **Failed** A task whose result of task execution is `failure`.

The following .NET code sample shows how to retrieve task counts by state: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> You can use a similar pattern for REST and other supported languages to get task counts for a job. 
> 
> 

### Consistency checking for task counts

Batch provides additional validation for state counts by performing consistency checks against multiple components of the system. In the unlikely event that the consistency check finds errors, Batch corrects the result of the Get Tasks Counts operation based on the results of the consistency check.

The `validationStatus` property in the response indicates whether Batch performed the consistency check. If Batch hasn't checked state counts against the actual states held in the system, then the `validationStatus` property is set to `unvalidated`. For performance reasons, Batch does not perform the consistency check if the job includes more than 200,000 tasks, so the `validationStatus` property is set to `unvalidated` in this case. (The task count is not necessarily wrong in this case, as even a very limited data loss is highly unlikely.) 

When a task changes state, the aggregation pipeline processes the change within a few seconds. The Get Task Counts operation reflects the updated task counts within that period. However, if the aggregation pipeline misses a change in a task state, then that change is not registered until the next validation pass. During this time, task counts may be slightly inaccurate due to the missed event, but they are corrected on the next validation pass.

## Node state counts

The List Pool Node Counts operation counts compute nodes by the following states in each pool:

- **Creating** - An Azure-allocated VM that has not yet started to join a pool.
- **Idle** - An available node that is not currently running a task.
- **LeavingPool** - A node that is leaving the pool, either because the user explicitly removed it or because the pool is resizing or autoscaling down.
- **Offline** - A node that Batch cannot use to schedule new tasks.
- **Preempted** - A low-priority node that was removed from the pool because Azure reclaimed the VM. A `preempted` node can be reinitialized when replacement low-priority VM capacity is available.
- **Rebooting** - A node that is restarting.
- **Reimaing** - A node on which the operating system is being reinstalled.
- **Running** - A node that is running one or more tasks (other than the start task).
- **Starting** - A node on which the Batch service is starting. 
- **StartTaskFailed** - A node on which the [start task][rest_start_task] failed and exhausted all retries, and on which `waitForSuccess` is set on the start task. The node is not usable for running tasks.
- **Unknown** - A node that lost contact with the Batch service and whose state isn't known.
- **Unusable** - A node that can't be used for task execution because of errors.
- **WaitingForStartTask** - A node on with the start task has started running, but `waitForSuccess` is set and the start task has not completed.

The following C# snippet shows how to retrieve node counts by state:

```csharp
var nodeCounts = await batchClient.NodeCounts();

Console.WriteLine("Node count in creating state: {0}", nodeCounts.Creating);
Console.WriteLine("Node count in idle state: {0}", nodeCounts.Idle);
...
```
## Next steps

* See the [Batch feature overview](batch-api-basics.md) to learn more about Batch service concepts and features. The article discusses the primary Batch resources such as pools, compute nodes, jobs, and tasks, and provides an overview of the service's features.


[rest_get_task_counts]: /rest/api/batchservice/job/gettaskcounts
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
[rest_get_task]: /rest/api/batchservice/task/get
[rest_list_tasks]: /rest/api/batchservice/task/list
[rest_get_exec_info]: /rest/api/batchservice/task/get#executionInfo
[rest_start_task]: /rest/api/batchservice/pool/add#starttask

