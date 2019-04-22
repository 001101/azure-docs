---
title: Premium Files troubleshooting guide
description: An overview of Azure Files, a service that enables you to create and use network file shares in the cloud using the industry standard SMB protocol.
services: storage
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
#Customer intent: As a < type of user >, I want < what? > so that < why? >.
---

# Troubleshooting Guide

## High latency, low throughput, and general performance issues

### Cause 1: Share experiencing throttling

The default quota on a share is 100 GiB, which provides 100 baseline IOPS (with a potential to burst up to 300 for an hour). For more information on provision and its relationship to IOPS, see the [Provisioned shares](storage-files-planning.md#provisioned-shares) section of the planning guide.

### Solution

- Increase share provisioned capacity by specifying a higher quota on your share.

### Cause 2: Metadata/namespace heavy workload

If the majority of your requests are metadata centric, (such as createfile/openfile/closefile/queryinfo/querydirectory) then the latency will be worse when compared to read/write operations.

### Workaround

- Check if the application can be modified to reduce the number of metadata operations.

### Cause 3: Single-threaded application

If the application being used by the customer is single-threaded, this can result in significantly lower IOPS/throughput than the maximum possible based on your provisioned share size.

### Solution

- Increase application parallelism by increasing the number of threads.
- Switch to applications where parallelism is possible. For example, for copy operations, customers could use AzCopy from Windows clients or the **parallel** command on Linux clients.

## Very high latency for requests

### Cause

The client VM could be located in a different region than the premium file share.

### Solution

- Run the application from a VM that is located in the same region as the premium file share.

## Client unable to achieve maximum throughput supported by the network

One potential cause of this is a lack fo SMB multi-channel support. Currently, premium files only support single channel, so there is only one connection from the client VM to the server. This single connection is pegged to a single core on the client VM, so the maximum throughput achievable from a VM is bound by a single core.

### Workaround

- Obtaining a VM with a bigger core may help improve throughput.
- Running the client application from multiple VMs will increase throughput.
- Use REST APIs where possible.

## Throughput on Linux clients is significantly lower when compared to Windows clients.

### Cause

This is a known issue with the implementation of SMB client on Linux.

### Workaround

- Spread the load across multiple VMs
- On the same VM, use multiple mount points with **nosharesock** option, and spread the load across these mount points.

## High latencies for metadata heavy workloads involving extensive open/close operations.

### Cause

Lack of support for directory leases.

### Workaround

- If possible, avoid excessive opening/closing handle on the same directory within a short period of time.
- For Linux VMs, increase the directory entry cache timeout by specifying **actimeo=<sec>** as a mount option. By default, it is one second, so a larger value like three or five might help.
- For Linux VMs, upgrade the kernel to 4.20 or higher.

## Low IOPS on CentOS

### Cause

IO depth greater than one is not supported on CentOS.

### Workaround

- Change to Ubuntu.

## Jittery/saw-tooth pattern for IOPS

### Cause

Client application consistently exceeds baseline IOPS. Currently, there is no service side smoothing of the request load, so if the client exceeds baseline IOPS, it will get throttled by the service. That throttling can result in the client experiencing a jittery/saw-tooth IOPS pattern. In this case, average IOPS achieved by the client might be lower than the baseline IOPS.

### Workaround

- Reduce the request load from the client application, so that the share does not get throttled.
- Increase the quota of the share so that the share does not get throttled.