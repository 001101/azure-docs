---
title: About Azure Lab Services | Microsoft Docs
description: Learn how Lab Services can make it easy to create, manage, and secure labs with virtual machines that can be used by developers, testers, educators, students, and others. 
services: lab-services
documentationcenter: na
author: spelluru
manager: 
editor: ''

ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/19/2018
ms.author: spelluru

---
# An introduction to Azure Lab Services (formerly Azure DevTest Labs)
Azure Lab Services enables you to quickly set up an environment for your team (for example: development environment, test environment, classroom lab environment) in the cloud. A lab owner creates a lab, provisions Windows, or Linux virtual machines, installs the necessary software and tools, and makes them available to lab users. Lab users connect to virtual machines (VMs) in the lab, and use them for their day-to-day work, short-term projects, or for doing classroom exercises. Once users start utilizing resources in the lab, a lab admin can analyze cost and usage across multiple labs, and set overarching policies to optimize your organization or team's costs.

## Types of labs
You can create two types of labs with Azure Lab Services: **managed labs**, and **custom labs**. 

If you want to just input what you need in a lab and let the service set up and manage infrastructure required for the lab, choose from one of the **managed labs**. Currently, **classroom lab** is the only type of managed lab that you can create with Azure Lab Services. 

If you want to manage your own infrastructure, create a **custom lab**. The custom lab is exactly same as the lab that you could create with Azure DevTest Labs service that is in general availability (GA). 

The following sections provide more details about these labs. 

### Managed labs
Managed labs offer different types of labs that fit for your specific need. Currently, Azure Lab Services supports only **classroom lab** as a managed lab. Managed labs enable you to get started right away, with minimal setup. The service itself handles all the management of the infrastructure for the lab, from spinning up the VMs to handling errors, and scaling the infrastructure. To create a managed lab, you need to create a lab account for your organization first. The lab account serves as the central account in which all labs in the organization are managed. 

When you create and use Azure resources in these managed labs, the service creates and manages resources in internal Microsoft subscriptions. They are not created in your own Azure subscription. The service keeps track of usage of these resources in internal Microsoft subscriptions. This usage is billed back to your Azure subscription that contains the lab account.   

Here are some of the **use cases for managed labs**: 

- Provide students with a lab of virtual machines that are configured with exactly what’s needed for a class. Give each student a limited number of hours for using the VMs for homework or personal projects.
- Set up a pool of high performance compute VMs to perform compute-intensive or graphics-intensive research. Run the VMs as needed, and clean up the machines once you are done. 
- Move your school’s physical computer lab into the cloud. Automatically scale the number of VMs only to the maximum usage and cost threshold that you set on the lab.  
- Quickly provision a lab of virtual machines for hosting a hackathon. Delete the lab with a single click once you’re done. 


### Custom labs (same as Azure DevTest Labs)
You may have scenarios where you want to manage all infrastructure and configuration yourself, within your own subscription. To do so, you can create a custom lab in the Azure portal. For custom labs, you do not need to create a lab account. Custom labs do not show up in the lab account (which exists for the managed labs).  

Here are some of the **use cases for using custom labs**: 

- Quickly provision a lab of virtual machines to host a hackathon or a hands-on session at a conference. Delete the lab with a single click once you’re done. 
- Create a pool of VMs configured with your application, and let your team easily use a virtual machine for bug-bashes.  
- Provide developers with virtual machines configured with all the tools they need. Schedule automatic start and shut down to minimize cost. 
- Repeatedly create a lab of test machines as part of your deployment. Test your latest bits and clean up the test machines once you are done. 
- Set up a variety of differently configured virtual machines and multiple test agents for scale and performance testing. 
- Offer training sessions to your customers using a lab configured with the latest version of your product. Give each customer limited number of hours for using in the lab. 


### Managed labs vs. custom labs
The following table compares two types of labs that are supported by Azure Lab Services: 

| Features | Managed labs | Custom labs |
| -------- | ----------------  | ---------- |
| Management of Azure infrastructure in the lab. | 	Automatically managed by the service | You manage on your own  |
| Built-in resiliency to infrastructure issues | Automatically handled by the service | You manage on your own  |
| Subscription management | Service handles allocation of resources within Microsoft subscriptions backing the service. Scaling is automatically handled by the service. | You manage on your own in your own Azure subscription. No auto-scaling of subscriptions. |
| Azure Resource Manager deployment within the lab | Not available | Available |

## Key capabilities
Azure Lab Services supports the following key capabilitiies/features: 

### Fast and flexible setup of a lab
Using Azure Lab Services, you can quickly set up a lab for your development, testing, or training needs. You can choose to create and manage the lab's Azure infrastructure in your own subscription, or choose from a variety of fit-for-purpose managed labs, such as classroom labs, to let the service take care of all the infrastructure work. The service provides built-in scaling and resiliency of infrastructure for labs that the service manages for you. You can use a custom template to quickly reproduce your lab's resources, and share customizations and policies across multiple labs. 

### Simplified experience for lab users
A lab owner adds users to a lab and provide them access to lab’s set of resources. A lab user views a single list of all the resources that they can access across labs. A lab owner gives permissions for lab users to manage and reuse data disks, and sets up reusable secrets. Lab users can integrate labs into their website or learning management systems. 

### Cost optimization and analysis
For custom labs, a lab owner can set lab schedules to automatically shut down and start up virtual machines. The lab owner can set a schedule to specify the time slots when the lab’s virtual machines are accessible to users, set usage policies per user or per lab to optimize cost, and analyze usage and activity trends in a lab. For managed labs such as classroom labs, currently some cost optimization and analysis options are available. 

### Embedded security
For custom labs in Azure Lab Services, a lab owner can set up a private virtual networks and a subnet for a lab, and enable a shared public IP address. Lab users can securely access resources using the virtual network configured with ExpressRoute or site-to-site VPN.

### Integration into your workflows and tools
Azure Lab Services allows you to integrate the labs into your organization’s website and management systems. You can automatically provision environments from within your continuous integration/continuous deployment (CI/CD) tools.

## User profiles
This section describes different user profiles in Azure Lab Services. 

### Lab account owner

Typically, an administrator of organization's cloud resources, who owns the Azure subscription acts as a lab account owner, who sets up a lab account for your organization, manages and configures policies across all labs, and gives permissions to people in the organization to create a lab under the lab account.

### Lab creator

Typically, users such as a development lead/manager, a teacher, a hackathon host, an online trainer creates labs under a lab account. A lab creator creates a lab, creates virtual machines in the lab, installs the appropriate software on virtual machines, specifies who can access the lab, and provides link to the lab to lab users.

### Lab user

A lab user uses the registration link that the lab creator shares to register and access the lab. The lab user connects to a virtual machine in the lab and use it for development, testing, or to do class work.

## Next steps
Get started with setting up a lab using Azure Lab Services:

- [Set up a classroom lab](tutorial-setup-classroom-lab.md)
- [Set up a custom lab](tutorial-create-custom-lab.md)
