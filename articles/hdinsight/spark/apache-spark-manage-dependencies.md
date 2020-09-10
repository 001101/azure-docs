---
title: Manage Spark application dependencies on Azure HDInsight
description: This article provides an introduction of how to manage Spark dependencies in HDInsight Spark cluster for PySpark and Scala applications.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020

#customer intent: As a developer for Apache Spark and Apache Spark in Azure HDInsight, I want to learn how to manage my Spark application dependencies and install packages on my HDInsight cluster.
---

# Manage Spark application dependencies

In this article, you learn how to manage dependencies for your Spark applications running on HDInsight. We cover both Scala and PySpark. 

Use quick links to jump to the session based on your user case.
* [Session scoped jar dependencies - use Jupyter notebook]()
* [Session scoped jar dependencies - use Apache Livy]()
* [Session scoped jar dependencies - use Use Azure Toolkit for IntelliJ]()
* [Cluster scoped jar dependencies - use script actions]()
* [Safely manage jar dependencies - shading jars]()
* [Session scoped Python packages - use Jupyter notebook]()
* [Session scoped Python packages - use Apache Livy notebook]()
* [Safely manage cluster scoped Python packages - use script actions]()

## Session scoped jar dependencies
### Use Jupyter notebook
When a Spark session starts in Jupyter Notebook on Spark kernel, you can configure packages from:

* [Maven Repository](https://search.maven.org/), or community-contributed packages at [Spark Packages](https://spark-packages.org/).
* Jar files stored on your cluster’s primary storage.

You'll use the `%%configure` magic to configure the notebook to use an external package. In notebooks that use external packages, make sure you call the `%%configure` magic in the first code cell. This ensures that the kernel is configured to use the package before the session starts.

	>[!IMPORTANT]  
	>If you forget to configure the kernel in the first cell, you can use the `%%configure` with the `-f` parameter, but that will restart the session and all progress will be lost.

**Sample for packages from Maven Repository or Spark Packages**

After locating the package from Maven Repository, gather the values for **GroupId**, **ArtifactId**, and **Version**. Concatenate the three values, separated by a colon (**:**).
   ![Concatenate package schema](./media/apache-spark-manage-dependencies/spark-package-schema.png "Concatenate package schema")

Make sure that the values you gather match your cluster. In this case, we're using Spark Cosmos DB connector package for Scala 2.11 and Spark 2.3 for HDInsight 3.6 Spark cluster. If you are not sure, run `scala.util.Properties.versionString` in code cell on Spark kernel to get cluster Scala version. Run `sc.version` to get cluster Spark version.

`%%configure`<br>`{ "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}`

**Sample for Jars stored on primary storage**

Use the [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme) for jar files on your clusters primary storage. This would be `wasb://` for Azure Storage, `abfs://` for Azure Data Lake Storage Gen2 or `adl://` for Azure Data Lake Storage Gen1. If secure transfer is enabled for Azure Storage or Data Lake Storage Gen2, the URI would be `wasbs://` or `abfss://`. See [secure transfer](../../storage/common/storage-require-secure-transfer.md).

Use comma-separated list of jar paths for multiple jar files, Globs are allowed. The jars are included on the driver and executor classpaths.

`%%configure`<br>`{ "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}`

After configuring external packages, you can run import in code cell to verify if the packages has been placed correctly.

    ```scala
    import com.microsoft.azure.cosmosdb.spark._
    ```

### Use Apache Livy
Apache Livy accepts a list of jar paths in the POST request for creating Spark session. Use `jars` for it. 

**Sample for submitting a Spark batch job with referenced jar files**

    ```cmd
    curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>", "jars":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
    ```

Use the [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme) for jar files on your clusters primary storage. Note that HDInsight disables use of local file paths to access jars through Livy. 

### Use Azure Toolkit for IntelliJ
[Azure Toolkit for IntelliJ plug-in](./apache-spark-intellij-tool-plugin.md) provides UI experience to submit Spark Scala application to an HDInsight cluster. It provides `Referenced Jars` and `Referenced Files` property to configure reference jar paths when submitting Spark application. See more details [here](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)

![The Spark Submission dialog box](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)


## Cluster scoped jar dependencies
In some cases, you may want to configure the jar dependency at cluster level so that every application can set up with same dependencies by default. 

1. Run below sample script actions to copy jar files from primary storage `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` to cluster local file system `/usr/libs/sparklibs`. The step is needed as linux uses `:` to separate class path list, but HDInsight only support storage paths with scheme like `wasb://`. The remote storage path won't work correctly if you directly add it to class path.

    ```bash
    sudo rm -rf /usr/libs/sparklibs
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Change Spark service configuration from Ambari. Go to **Ambari > Spark > Configs > Custom Spark2-defaults**. **Add Property** as follows. Use `:` to separate paths if you have more than one path to add. Globs are allowed.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![Change Spark default config](./media/apache-spark-manage-dependencies/change-spark-default-config.png "Change Spark default config")

3. Save the changed configurations and restart impacted services.

   ![Restart impacted services](./media/apache-spark-manage-dependencies/restart-impacted-services.png "Restart impacted services")

You can automate the steps using [script actions](../hdinsight-hadoop-customize-cluster-linux.md). Script action for [adding Hive custom libraries](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) is a good reference.

## Safely manage jar dependencies
HDInsight cluster has built-in jar dependencies. To avoid version conflict between built-in jars and the jars you bring for reference, consider [shading your application dependencies](./safely-manage-jar-dependency.md).

## Session scoped Python packages
### Use Jupyter notebook
HDInsight Jupyter notebook PySpark kernel doesn't support installing Python packages from PyPi or Anaconda directly. If you have `.zip`, `.egg`, or `.py` dependencies, you can upload files to the cluster primary storage and use the [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

Use comma-separated list of `.zip`, `.egg`, or `.py` paths for multiple files, Globs are allowed. These dependencies will be placed to PYTHONPATH for Python apps.

`%%configure`<br>`{ "conf": {"spark.submit.pyFiles": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/mymodule.py" }}`

### Use Apache Livy
Apache Livy accepts a list of Python file paths in the POST request for creating Spark session. Use `pyFiles` for it. 

**Sample for submitting a Spark batch job with referenced Python files**

    ```cmd
    curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>", "pyFiles":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/mymodule.py" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
    ```

Use the [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme) for Python files on your clusters primary storage. Note that HDInsight disables use of local file paths to access files through Livy. 

## Set up cluster scoped Python packages
You can install Python packages from Anaconda to the cluster using conda command via script actions. The packages installed are cluster scoped and apply to all applications. 

HDInsight Spark cluster has two built-in Python installations, Anaconda Python 2.7 and Anaconda Python 3.5. To understand more about default Python settings for services, and how to safely install external Python packages without breaking the cluster, see more details [here](./apache-spark-python-package-installation.md).
