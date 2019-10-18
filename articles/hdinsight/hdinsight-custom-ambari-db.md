---
title: Custom Apache Ambari database 
description: Learn how to create HDInsight clusters with your own custom Apache Ambari database.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: hrasheed
---
# Setup HDInsight clusters with a custom Ambari DB

Apache Ambari simplifies the management and monitoring of an Apache Hadoop cluster. Ambari provides an easy to use web UI and REST API. Ambari is included on HDInsight clusters, and is used to monitor the cluster and make configuration changes.

In normal cluster creation, as described in other articles such as [Set up clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md), Ambari is deployed in an [S0 Azure SQL database](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) that is managed by HDInsight and is not accessible to users.

The custom Ambari DB feature allows you to deploy a new cluster and setup Ambari in an external database that you manage. This feature has the following benefits:

- **Customization** - you choose the size and processing capacity of the database. If you have large clusters processing intensive workloads, an Ambari database with lower specifications could become a bottleneck for management operations.
- **Flexibility** - you can scale the database as needed to suit your requirements.
- **Control** - you can manage backups and security for your database in a way that fits with your organizations requirements.

The remainder of this article discusses the following points:

- requirements to use the custom Ambari DB feature
- the steps necessary to provision HDInsight clusters using your own external database for Apache Ambari

## Requirements

The custom Ambari DB has the following requirements:

1. You can deploy a custom Ambari DB with all cluster types and versions.
1. Multiple clusters cannot use the same Ambari DB.
1. You should have an existing Azure SQL DB server with a database already provisioned.
1. The database that you provide for Ambari setup must be empty. There should be no tables in the default dbo schema.
1. The IP addresses (from HDInsight service) need to be allowed in the SQL Server. See [HDInsight management IP addresses](hdinsight-management-ip-addresses.md) for a list of the IP addresses that must be added to the SQL server firewall.

When you host your Apache Ambari DB in an external database, there are important things to remember:

- You're responsible for the additional costs of the Azure SQL DB that holds Ambari.
- Back up your custom Ambari DB periodically. Azure SQL Database generates backups automatically, but the backup retention time-frame varies. For more information, see [Learn about automatic SQL Database backups](../sql-database/sql-database-automated-backups.md).

## Deploy clusters with a custom Ambari DB

To create an HDInsight cluster that uses your own external Ambari database, use the [custom Ambari + Hive Metastore DB Quickstart template)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Edit the parameters in the `azuredeploy.parameters.json` to specify information about your new cluster and the database that will hold Ambari.

You can begin the deployment using the Azure CLI. Replace `<RESOURCEGROUPNAME>` with the resource group where you want to deploy your cluster.

```azure-cli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## Next steps

- [Use external metadata stores in Azure HDInsight](hdinsight-use-external-metadata-stores.md)