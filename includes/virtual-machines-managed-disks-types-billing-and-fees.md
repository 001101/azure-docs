---
 title: include file
 description: include file
 services: virtual-machines
 author: roygara
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 12/03/2018
 ms.author: rogarana
 ms.custom: include file
---
**Outbound data transfers**: [Outbound data transfers](https://azure.microsoft.com/pricing/details/bandwidth/) (data going out of Azure data centers) incur billing for bandwidth usage.

**Transactions**: You are billed for the number of transactions that you perform on a standard managed disk. Azure charges $0.0036 per 100,000 transactions for standard HDDs. Transactions include both read and write operations to storage.

Standard SSD Disks use IO Unit size of 256 KB. If the data being transferred is less than 256 KB, it is considered one I/O unit. Larger I/O sizes are counted as multiple I/Os of size 256 KB. For example, a 1,100 KB I/O is counted as five I/O units.

There is no cost for transactions for a premium managed disk.

For detailed information on pricing for Managed Disks, see [Managed Disks Pricing](https://azure.microsoft.com/pricing/details/managed-disks).

### Ultra SSD VM reservation fee

We are introducing a capability on the VM that indicates your VM is Ultra SSD compatible. An Ultra SSD Compatible VM allocates dedicated bandwidth capacity between the compute VM instance and the block storage scale unit to optimize the performance and reduce latency. Adding this capability on the VM results in a reservation charge that is only imposed if you enabled Ultra SSD capability on the VM without attaching an Ultra SSD disk to it. When an Ultra SSD disk is attached to the Ultra SSD compatible VM, this charge would not be applied. This charge is per vCPU provisioned on the VM.

Refer to the [Azure Disks pricing page](https://azure.microsoft.com/pricing/details/managed-disks/) for the new Ultra SSD Disks price details available in limited preview.