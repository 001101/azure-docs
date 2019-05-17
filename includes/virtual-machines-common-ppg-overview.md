---
 title: include file
 description: include file
 services: virtual-machines
 author: cynthn
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 05/14/2019
 ms.author: cynthn
 ms.custom: include file
---


Placing VMs in a single region can reduce the distance and placing them in zones is another step to bringing them physically closer together. But, to get VMs as close as possible, you should deploy them within a proximity placement group.

A proximity placement group is a logical grouping used to make sure that Azure compute resources are physically located close to each other in a region or Availability Zone. Proximity placement groups are useful for workloads where low latency is a requirement. 

- Low latency between stand-alone VMs.
- Low Latency between VMs in a single availability set or a virtual machine scale set. 
- Low latency between stand-alone VMs, VMs in multiple Availability Sets, or multiple scale sets. You can have multiple compute resources in a single placement group to bring together a multi-tiered application. 
- Low latency between multiple application tiers using different hardware types. For example, running the backend using M-series in an availability set and the front end on a D-series instance, in a scale set, in a single proximity placement group.



## Best practices 
- Deploy all VMs at once with a template.
- Deploy largest and rarest VM types first. If a VM type is available in a limited number of regions, it is more rare. You can use [this page](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) to check how many reqions support the various sizes.  
- When re-using an existing placement group from which VMs were deleted, wait for the deletion to fully complete before adding VMs to it.
- Put VMs in a proximity placement group and the entire solution in an availability zone.