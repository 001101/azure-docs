---
title: Understand Azure reservation discount and usage for SUSE | Microsoft Docs
description: Learn how a reservation discount is applied and the usage when you run SUSE software on virtual machines.
services: 'billing'
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''

ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2018
ms.author: yashar
---
# Understand how the SUSE Linux Enterprise software reservation discount is applied

After you buy a SUSE Linux Enterprise Server software reservation, the reservation discount is automatically applied when you deploy a SUSE image and virtual machine (VM) that matches the reservation. A SUSE Linux Enterprise Server software reservation covers the cost of running the SUSE software on an Azure VM.

To get the full benefit of the reservation discount, you need to understand your SUSE usage and vCPUs on the VMs where you run SUSE. Use the following sections to help identify from your usage CSV file what reservation to buy.  

## Understand reservation usage for SUSE

The following tables show the software plans you can buy a reservation for, their associated usage meters, and the ratios for each. The ratio column that's listed in the following tables compares the relative footprint for each meter in that group. The ratio depends on the VM vCPUs. Use the ratio value to calculate how many instances of SUSE the reservation discount covers.

For example, if you buy a reservation for SUSE Linux Enterprise Server for HPC Priority for a VM with 3 or 4 vCPU, the ratio for that reservation is 2. The discount covers the SUSE software cost for:

- 2 deployed VMs with 1 or 2 vCPUs,
- 1 deployed VM with 3 or 4 vCPUs,
- or 0.77 or about 77% of a VM with 5 or more vCPUs.

The ratio for 5 or more vCPU is 2.6. So a reservation for SUSE with a VM with 5 or more vCPU covers a only portion of the software cost, which is about 77%.

## SUSE Linux Enterprise Server for HPC Priority

Name in Azure portal marketplace:

|Meter/VM Name |  MeterId | Ratio| Example VM size|
| ------- | ------------------------| --- |--- |
|SLES for HPC (1-2 vCPU)|e275a668-ce79-44e2-a659-f43443265e98|1||
|SLES for HPC (3-4 vCPU)|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2||
|SLES for HPC (5+ vCPU)|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6||

### SUSE Linux Enterprise Server for HPC Standard

|Meter/VM Name |  MeterId | Ratio|
| ------- | ------------------------| --- |
|SLES for HPC (1-2 vCPU)|8c94ad45-b93b-4772-aab1-ff92fcec6610|1|
|SLES for HPC (3-4 vCPU)|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|
|SLES for HPC (5+ vCPU)|907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|

## SUSE Linux Enterprise Server for SAP Priority

|Meter/VM Name |  MeterId | Ratio|
| ------- | ------------------------| --- |
|SLES for SAP Priority (1-2 vCPU)|497fe0b6-fa3c-4e3d-a66b-836097244142|1|
|SLES for SAP Priority (3-4 vCPU)|847887de-68ce-4adc-8a33-7a3f4133312f|2|
|SLES for SAP Priority (5+ vCPU)|18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|

## SUSE Linux Enterprise Server Priority

|Meter/VM Name |  MeterId | Ratio|
| ------- | ------------------------| --- |
|SLES (1vCPU)|462cd632-ec6b-4663-b79f-39715f4e8b38|1|
|SLES (2vCPU)|924bee71-5eb8-424f-83ed-a58823c33908|2|
|SLES (4vCPU)|60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|
|SLES (6 vCPU)|e8862232-6131-4dbe-bde4-e2ae383afc6f|3|
|SLES (8 vCPU)|e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|
|SLES (12 vCPU)|a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2|
|SLES (16 vCPU)|bb21066f-fe46-46d3-8006-b326b1663e52|3.2|
|SLES (20 vCPU)|c5228804-1de6-4bd4-a61c-501d9003acc8|3.2|
|SLES (24 vCPU)|085dc9ee-005d-4075-ac11-822ccde9e8f6|3.2|
|SLES (32 vCPU)|180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2|
|SLES (40 vCPU)|a161d3d3-0592-4956-9b64-6829678b6506|3.2|
|SLES (64 vCPU)|7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2|
|SLES (72 vCPU)|93329a72-24d7-4faa-93d9-203f367ed334|3.2|
|SLES (96 vCPU)|2018c3a8-ff13-41f8-b64d-9558c5206547|3.2|
|SLES (128 vCPU)|ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2|

## SUSE Linux Enterprise Server Standard

|Meter/VM Name |  MeterId | Ratio|
| ------- | ------------------------| --- |
|SLES (1-2 vCPU)|4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|
|SLES (3-4 vCPU)|0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|
|SLES (5+ vCPU)|7b349b65-d906-42e5-833f-b2af38513468|2.30769|

## Next steps

To learn more about reservations, see the following articles:

- [What are Azure reservations?](billing-save-compute-costs-reservations.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity](../sql-database/sql-database-reserved-capacity.md)
- [Manage Azure reservations](billing-manage-reserved-vm-instance.md)
- [Understand reservation usage for your Pay-As-You-Go subscription](billing-understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment](billing-understand-reserved-instance-usage-ea.md)
- [Understand reservation usage for CSP subscriptions](https://docs.microsoft.com/partner-center/azure-reservations)
- [Windows software costs not included with reservations](billing-reserved-instance-windows-software-costs.md)

## Need help? Contact support

If you still have further questions, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to get your issue resolved quickly.