---
title: Ansible matrix
description: Ansible matrix
ms.service: ansible
keywords: ansible, roles, matrix, version, azure, devops
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/11/2018
ms.topic: article
---

# Ansible matrix

| Ansible modules for Azure                   |  Ansible 2.4 |  Playbook Role [azure_module](#introduction-to-azure-module) |  Playbook Role [azure_preview_module](#introduction-to-azure_preview_module) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| azure_rm_availabilityset                    | Yes          | Yes                         | Yes                                 | 
| azure_rm_availabilityset_facts              | Yes          | Yes                         | Yes                                 | 
| azure_rm_deployment                         | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachineimage_facts          | Yes          | Yes                         | Yes                                 | 
| azure_rm_resourcegroup                      | Yes          | Yes                         | Yes                                 | 
| azure_rm_resourcegroup_facts                | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine                     | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine_extension           | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualmachine_scaleset            | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualnetwork                     | Yes          | Yes                         | Yes                                 | 
| azure_rm_virtualnetwork_facts               | Yes          | Yes                         | Yes                                 | 
| azure_rm_subnet                             | Yes          | Yes                         | Yes                                 | 
| azure_rm_networkinterface                   | Yes          | Yes                         | Yes                                 | 
| azure_rm_networkinterface_facts             | Yes          | Yes                         | Yes                                 | 
| azure_rm_publicipaddress                    | Yes          | Yes                         | Yes                                 | 
| azure_rm_publicipaddress_facts              | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnsrecordset                       | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnsrecordset_facts                 | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnszone                            | Yes          | Yes                         | Yes                                 | 
| azure_rm_dnszone_facts                      | Yes          | Yes                         | Yes                                 | 
| azure_rm_loadbalancer                       | Yes          | Yes                         | Yes                                 | 
| azure_rm_loadbalancer_facts                 | Yes          | Yes                         | Yes                                 | 
| azure_rm_applicationgateway-                | -            | Yes                         |                                     | 
| azure_rm_applicationgateway_facts           | -            | -                           | Yes                                 | 
| azure_rm_securitygroup                      | -            | -                           | Yes                                 | 
| azure_rm_securitygroup_facts                | -            | -                           | Yes                                 | 
| azure_rm_storageaccount                     | Yes          | Yes                         | Yes                                 | 
| azure_rm_storageaccount_facts               | Yes          | Yes                         | Yes                                 | 
| azure_rm_storageblob                        | Yes          | Yes                         | Yes                                 | 
| azure_rm_managed_disk                       | Yes          | Yes                         | Yes                                 | 
| azure_rm_managed_disk_facts                 | Yes          | Yes                         | Yes                                 | 
| azure_rm_acs                                | Yes          | Yes                         | Yes                                 | 
| azure_rm_containerinstance                  | -            | -Yes                        |                                     | 
| azure_rm_containerinstance_facts            | -            | -                           | Yes                                 | 
| azure_rm_containerregistry                  | -            | Yes                         | Yes                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | Yes                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | Yes                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | Yes                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | Yes                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | Yes                                 | 
| azure_rm_functionapp                        | Yes          | Yes                         | Yes                                 | 
| azure_rm_functionapp_facts                  | Yes          | Yes                         | Yes                                 | 
| azure_rm_sqlserver                          | -            | Yes                         | Yes                                 | 
| azure_rm_sqlserver_facts                    | -            | -                           | Yes                                 | 
| azure_rm_sqldatabase                        | -            | -                           | Yes                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | Yes                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | Yes                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | Yes                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | Yes                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | Yes                                 | 
| azure_rm_mysqlserver                        | -            | Yes                         | Yes                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | Yes                                 | 
| azure_rm_mysqldatabase                      | -            | -                           | Yes                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | Yes                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | Yes                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | Yes                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | Yes                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | Yes                                 | 
| azure_rm_postgresqlserver                   | -            | Yes                         | Yes                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | Yes                                 | 
| azure_rm_postgresqldatabase                 | -            | -                           | Yes                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | Yes                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | Yes                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | Yes                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | Yes                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | Yes                                 | 

## Introduction to azure_module
The [azure_module playbook role](https://galaxy.ansible.com/Azure/azure_modules/) includes the latest changes and bug fixes for Azure modules from the [devel branch of Ansible repository](https://github.com/ansible/ansible/tree/devel). If you cannot wait for Ansible's next release, installing this role is a good choice.

The azure_module playbook role is released every three weeks.

## Introduction to azure_preview_module
The [azure_preview_module playbook role](https://galaxy.ansible.com/Azure/azure_preview_modules/) is the most complete and includes all the latest Azure modules. The update and bug fixes are done in a more timely manner than the official Ansible release. If you use Ansible for Azure resource provisioning purposes, you're encouraged to install this role.

The azure_preview_module playbook role is released every three weeks.

## Next steps
More information related to playbook roles, refer to [Creating Reusable Playbooks](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 