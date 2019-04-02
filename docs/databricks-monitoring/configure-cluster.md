---
title: 配置 Azure Databricks 将发送到 Azure Monitor 的指标
description: 一种 scala 库以启用监视的指标和 Azure Log Analytics 中的日志记录数据
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: af6b6433f87964ac60c179ecf498e54129344126
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503380"
---
<!-- markdownlint-disable MD040 -->

# <a name="configure-azure-databricks-to-send-metrics-to-azure-monitor"></a><span data-ttu-id="5dac3-103">配置 Azure Databricks 将发送到 Azure Monitor 的指标</span><span class="sxs-lookup"><span data-stu-id="5dac3-103">Configure Azure Databricks to send metrics to Azure Monitor</span></span>

<span data-ttu-id="5dac3-104">本文介绍如何配置 Azure Databricks 群集中发送到的指标[Log Analytics 工作区](/azure/azure-monitor/platform/manage-access)。</span><span class="sxs-lookup"><span data-stu-id="5dac3-104">This article shows how to configure an Azure Databricks cluster to send metrics to a [Log Analytics workspace](/azure/azure-monitor/platform/manage-access).</span></span> <span data-ttu-id="5dac3-105">它使用[Azure Databricks 监视库](https://github.com/mspnp/spark-monitoring)，这是 GitHub 上提供。</span><span class="sxs-lookup"><span data-stu-id="5dac3-105">It uses the [Azure Databricks Monitoring Library](https://github.com/mspnp/spark-monitoring), which is available on GitHub.</span></span> <span data-ttu-id="5dac3-106">了解 Java、 Scala 和 Maven 为 prerequisistes 建议。</span><span class="sxs-lookup"><span data-stu-id="5dac3-106">Understanding of Java, Scala, and Maven are recommended as prerequisistes.</span></span>

## <a name="about-the-azure-databricks-monitoring-library"></a><span data-ttu-id="5dac3-107">有关 Azure Databricks 监视库</span><span class="sxs-lookup"><span data-stu-id="5dac3-107">About the Azure Databricks Monitoring Library</span></span>

<span data-ttu-id="5dac3-108">[GitHub 存储库](https://github.com/mspnp/spark-monitoring)有关 Azure Databricks 监视库具有以下目录结构：</span><span class="sxs-lookup"><span data-stu-id="5dac3-108">The [GitHub repo](https://github.com/mspnp/spark-monitoring) for the Azure Databricks Monitoring Library has following directory structure:</span></span>

```
/src  
    /spark-jobs  
    /spark-listeners-loganalytics  
    /spark-listeners  
    /pom.xml  
```

<span data-ttu-id="5dac3-109">**Spark 作业**目录是演示如何实现 Spark 应用程序指标计数器的示例 Spark 应用程序。</span><span class="sxs-lookup"><span data-stu-id="5dac3-109">The **spark-jobs** directory is a sample Spark application demonstrating how to implement a Spark application metric counter.</span></span>

<span data-ttu-id="5dac3-110">**Spark 侦听器**目录中包括的功能，使 Azure Databricks 将在服务级别上的 Apache Spark 事件发送到 Azure Log Analytics 工作区。</span><span class="sxs-lookup"><span data-stu-id="5dac3-110">The **spark-listeners** directory includes functionality that enables Azure Databricks to send Apache Spark events at the service level to an Azure Log Analytics workspace.</span></span> <span data-ttu-id="5dac3-111">Azure Databricks 是基于 Apache Spark，其中包括一组结构化 Api，可用于使用数据集、 数据框架和 SQL 处理数据的批处理的服务。</span><span class="sxs-lookup"><span data-stu-id="5dac3-111">Azure Databricks is a service based on Apache Spark, which includes a set of structured APIs for batch processing data using Datasets, DataFrames, and SQL.</span></span> <span data-ttu-id="5dac3-112">使用 Apache Spark 2.0 中，添加了对支持[结构化流式处理](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)、 数据流式处理基于 Spark 的批处理 Api 的 API。</span><span class="sxs-lookup"><span data-stu-id="5dac3-112">With Apache Spark 2.0, support was added for [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html), a data stream processing API built on Spark's batch processing APIs.</span></span>

<span data-ttu-id="5dac3-113">**Spark 侦听器 loganalytics**目录包含适用于 Spark 侦听器、 DropWizard，接收器和 Azure Log Analytics 工作区的客户端的接收器。</span><span class="sxs-lookup"><span data-stu-id="5dac3-113">The **spark-listeners-loganalytics** directory includes a sink for Spark listeners, a sink for DropWizard, and a client for an Azure Log Analytics Workspace.</span></span> <span data-ttu-id="5dac3-114">此目录还包含 Apache Spark 应用程序日志 log4j 追加器。</span><span class="sxs-lookup"><span data-stu-id="5dac3-114">This directory also includes a log4j Appender for your Apache Spark application logs.</span></span>

<span data-ttu-id="5dac3-115">**Spark 侦听器 loganalytics**并**spark 侦听器**目录包含用于生成部署到 Databricks 群集的两个 JAR 文件的代码。</span><span class="sxs-lookup"><span data-stu-id="5dac3-115">The **spark-listeners-loganalytics** and **spark-listeners** directories contain the code for building the two JAR files that are deployed to the Databricks cluster.</span></span> <span data-ttu-id="5dac3-116">**Spark 侦听器**目录中包括**脚本**目录，其中包含群集节点初始化脚本将 JAR 复制到 Azure Databricks 文件系统文件从临时目录执行节点。</span><span class="sxs-lookup"><span data-stu-id="5dac3-116">The **spark-listeners** directory includes a **scripts** directory that contains a cluster node initialization script to copy the JAR files from a staging directory in the Azure Databricks file system to execution nodes.</span></span>

<span data-ttu-id="5dac3-117">**Pom.xml**文件是整个项目的主要 Maven 生成文件。</span><span class="sxs-lookup"><span data-stu-id="5dac3-117">The **pom.xml** file is the main Maven build file for the entire project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5dac3-118">必备组件</span><span class="sxs-lookup"><span data-stu-id="5dac3-118">Prerequisites</span></span>

<span data-ttu-id="5dac3-119">若要开始，将部署以下 Azure 资源：</span><span class="sxs-lookup"><span data-stu-id="5dac3-119">To get started, deploy the following Azure resources:</span></span>

- <span data-ttu-id="5dac3-120">Azure Databricks 工作区中。</span><span class="sxs-lookup"><span data-stu-id="5dac3-120">An Azure Databricks workspace.</span></span> <span data-ttu-id="5dac3-121">请参阅[快速入门：使用 Azure 门户在 Azure Databricks 上运行 Spark 作业](/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。</span><span class="sxs-lookup"><span data-stu-id="5dac3-121">See [Quickstart: Run a Spark job on Azure Databricks using the Azure portal](/azure/azure-databricks/quickstart-create-databricks-workspace-portal).</span></span>
- <span data-ttu-id="5dac3-122">Log Analytics 工作区。</span><span class="sxs-lookup"><span data-stu-id="5dac3-122">A Log Analytics workspace.</span></span> <span data-ttu-id="5dac3-123">请参阅[Azure 门户中创建 Log Analytics 工作区](/azure/azure-monitor/learn/quick-create-workspace)。</span><span class="sxs-lookup"><span data-stu-id="5dac3-123">See [Create a Log Analytics workspace in the Azure portal](/azure/azure-monitor/learn/quick-create-workspace).</span></span>

<span data-ttu-id="5dac3-124">接下来，安装[Azure Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#install-the-cli)。</span><span class="sxs-lookup"><span data-stu-id="5dac3-124">Next, install the [Azure Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#install-the-cli).</span></span> <span data-ttu-id="5dac3-125">使用 CLI 需要一个 Azure Databricks 个人访问令牌。</span><span class="sxs-lookup"><span data-stu-id="5dac3-125">An Azure Databricks personal access token is required to use the CLI.</span></span> <span data-ttu-id="5dac3-126">有关说明，请参阅[生成令牌](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)。</span><span class="sxs-lookup"><span data-stu-id="5dac3-126">For instructions, see [Generate a token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).</span></span>

<span data-ttu-id="5dac3-127">查找 Log Analytics 工作区 ID 和密钥。</span><span class="sxs-lookup"><span data-stu-id="5dac3-127">Find your Log Analytics workspace ID and key.</span></span> <span data-ttu-id="5dac3-128">有关说明，请参阅[获取工作区 ID 和密钥](/azure/azure-monitor/platform/agent-windows#obtain-workspace-id-and-key)。</span><span class="sxs-lookup"><span data-stu-id="5dac3-128">For instructions, see [Obtain workspace ID and key](/azure/azure-monitor/platform/agent-windows#obtain-workspace-id-and-key).</span></span> <span data-ttu-id="5dac3-129">配置在 Databricks 群集时，将需要这些值。</span><span class="sxs-lookup"><span data-stu-id="5dac3-129">You will need these values when you configure the Databricks cluster.</span></span>

<span data-ttu-id="5dac3-130">若要生成监视库，您将需要 Java IDE，它具有以下资源：</span><span class="sxs-lookup"><span data-stu-id="5dac3-130">To build the monitoring library, you will need a Java IDE with the following resources:</span></span>

- [<span data-ttu-id="5dac3-131">Java 开发工具包 (JDK) 1.8 版</span><span class="sxs-lookup"><span data-stu-id="5dac3-131">Java Development Kit (JDK) version 1.8</span></span>](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
- [<span data-ttu-id="5dac3-132">Scala 语言 SDK 2.11</span><span class="sxs-lookup"><span data-stu-id="5dac3-132">Scala language SDK 2.11</span></span>](https://www.scala-lang.org/download/)
- [<span data-ttu-id="5dac3-133">Apache Maven 3.5.4</span><span class="sxs-lookup"><span data-stu-id="5dac3-133">Apache Maven 3.5.4</span></span>](http://maven.apache.org/download.cgi)

## <a name="build-the-azure-databricks-monitoring-library"></a><span data-ttu-id="5dac3-134">生成 Azure Databricks 监视库</span><span class="sxs-lookup"><span data-stu-id="5dac3-134">Build the Azure Databricks Monitoring Library</span></span>

<span data-ttu-id="5dac3-135">若要生成 Azure Databricks 监视库，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="5dac3-135">To build the Azure Databricks Monitoring Library, perform the following steps:</span></span>

1. <span data-ttu-id="5dac3-136">克隆、 分叉或下载[GitHub 存储库](https://github.com/mspnp/spark-monitoring)。</span><span class="sxs-lookup"><span data-stu-id="5dac3-136">Clone, fork, or download the [GitHub repository](https://github.com/mspnp/spark-monitoring).</span></span>

1. <span data-ttu-id="5dac3-137">导入 Maven 项目对象模型文件中， _pom.xml_，它位于 **/src**文件夹导入到你的项目。</span><span class="sxs-lookup"><span data-stu-id="5dac3-137">Import the Maven project object model file, _pom.xml_, located in the **/src** folder into your project.</span></span> <span data-ttu-id="5dac3-138">这将导入三个项目：</span><span class="sxs-lookup"><span data-stu-id="5dac3-138">This will import three projects:</span></span>

    - <span data-ttu-id="5dac3-139">spark-jobs</span><span class="sxs-lookup"><span data-stu-id="5dac3-139">spark-jobs</span></span>
    - <span data-ttu-id="5dac3-140">spark-listeners</span><span class="sxs-lookup"><span data-stu-id="5dac3-140">spark-listeners</span></span>
    - <span data-ttu-id="5dac3-141">spark-listeners-loganalytics</span><span class="sxs-lookup"><span data-stu-id="5dac3-141">spark-listeners-loganalytics</span></span>

1. <span data-ttu-id="5dac3-142">执行 Maven**包**Java IDE 来生成这些项目的每个 JAR 文件中的生成阶段：</span><span class="sxs-lookup"><span data-stu-id="5dac3-142">Execute the Maven **package** build phase in your Java IDE to build the JAR files for each of these projects:</span></span>

    |<span data-ttu-id="5dac3-143">Project</span><span class="sxs-lookup"><span data-stu-id="5dac3-143">Project</span></span>| <span data-ttu-id="5dac3-144">JAR 文件</span><span class="sxs-lookup"><span data-stu-id="5dac3-144">JAR file</span></span>|
    |-------|---------|
    |<span data-ttu-id="5dac3-145">spark-jobs</span><span class="sxs-lookup"><span data-stu-id="5dac3-145">spark-jobs</span></span>|<span data-ttu-id="5dac3-146">spark-jobs-1.0-SNAPSHOT.jar</span><span class="sxs-lookup"><span data-stu-id="5dac3-146">spark-jobs-1.0-SNAPSHOT.jar</span></span>|
    |<span data-ttu-id="5dac3-147">spark-listeners</span><span class="sxs-lookup"><span data-stu-id="5dac3-147">spark-listeners</span></span>|<span data-ttu-id="5dac3-148">spark-listeners-1.0-SNAPSHOT.jar</span><span class="sxs-lookup"><span data-stu-id="5dac3-148">spark-listeners-1.0-SNAPSHOT.jar</span></span>|
    |<span data-ttu-id="5dac3-149">spark-listeners-loganalytics</span><span class="sxs-lookup"><span data-stu-id="5dac3-149">spark-listeners-loganalytics</span></span>|<span data-ttu-id="5dac3-150">spark-listeners-loganalytics-1.0-SNAPSHOT.jar</span><span class="sxs-lookup"><span data-stu-id="5dac3-150">spark-listeners-loganalytics-1.0-SNAPSHOT.jar</span></span>|

1. <span data-ttu-id="5dac3-151">使用 Azure Databricks CLI 来创建一个名为**dbfs: / 监视 databricks 的暂存**:</span><span class="sxs-lookup"><span data-stu-id="5dac3-151">Use the Azure Databricks CLI to create a directory named **dbfs:/databricks/monitoring-staging**:</span></span>  

    ```bash
    dbfs mkdirs dbfs:/databricks/monitoring-staging
    ```

1. <span data-ttu-id="5dac3-152">使用 Azure Databricks CLI 来复制 **/src/spark-listeners/scripts/listeners.sh**到以前的目录：</span><span class="sxs-lookup"><span data-stu-id="5dac3-152">Use the Azure Databricks CLI to copy **/src/spark-listeners/scripts/listeners.sh** to the directory previously:</span></span>

    ```bash
    dbfs cp ./src/spark-listeners/scripts/listeners.sh dbfs:/databricks/monitoring-staging/listeners.sh
    ```

1. <span data-ttu-id="5dac3-153">使用 Azure Databricks CLI 来复制 **/src/spark-listeners/scripts/metrics.properties**为以前创建的目录：</span><span class="sxs-lookup"><span data-stu-id="5dac3-153">Use the Azure Databricks CLI to copy **/src/spark-listeners/scripts/metrics.properties** to the directory created previously:</span></span>

    ```bash
    dbfs cp <local path to metrics.properties> dbfs:/databricks/monitoring-staging/metrics.properties
    ```

1. <span data-ttu-id="5dac3-154">使用 Azure Databricks CLI 来复制**spark-侦听器-1.0-SNAPSHOT.jar**并**spark-侦听器-loganalytics-1.0-SNAPSHOT.jar**为以前创建的目录：</span><span class="sxs-lookup"><span data-stu-id="5dac3-154">Use the Azure Databricks CLI to copy **spark-listeners-1.0-SNAPSHOT.jar** and **spark-listeners-loganalytics-1.0-SNAPSHOT.jar** to the directory created previously:</span></span>

    ```bash
    dbfs cp ./src/spark-listeners/target/spark-listeners-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-1.0-SNAPSHOT.jar
    dbfs cp ./src/spark-listeners-loganalytics/target/spark-listeners-loganalytics-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-loganalytics-1.0-SNAPSHOT.jar
    ```

## <a name="create-and-configure-an-azure-databricks-cluster"></a><span data-ttu-id="5dac3-155">创建和配置 Azure Databricks 群集</span><span class="sxs-lookup"><span data-stu-id="5dac3-155">Create and configure an Azure Databricks cluster</span></span>

<span data-ttu-id="5dac3-156">若要创建和配置 Azure Databricks 群集中，执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="5dac3-156">To create and configure an Azure Databricks cluster, follow these steps:</span></span>

1. <span data-ttu-id="5dac3-157">导航到你在 Azure 门户中的 Azure Databricks 工作区。</span><span class="sxs-lookup"><span data-stu-id="5dac3-157">Navigate to your Azure Databricks workspace in the Azure portal.</span></span>
1. <span data-ttu-id="5dac3-158">在主页上，单击**新的群集**。</span><span class="sxs-lookup"><span data-stu-id="5dac3-158">On the home page, click **New Cluster**.</span></span>
1. <span data-ttu-id="5dac3-159">输入您的群集的名称**群集名称**文本框。</span><span class="sxs-lookup"><span data-stu-id="5dac3-159">Enter a name for your cluster in the **Cluster Name** text box.</span></span>
1. <span data-ttu-id="5dac3-160">在中**Databricks 运行时版本**下拉列表中，选择**4.3**或更高版本。</span><span class="sxs-lookup"><span data-stu-id="5dac3-160">In the **Databricks Runtime Version** dropdown, select **4.3** or greater.</span></span>
1. <span data-ttu-id="5dac3-161">下**高级选项**，单击**Spark**选项卡。输入中的以下名称 / 值对**Spark 配置**文本框中：</span><span class="sxs-lookup"><span data-stu-id="5dac3-161">Under **Advanced Options**, click on the **Spark** tab. Enter the following name-value pairs in the **Spark Config** text box:</span></span>

    ```
    spark.extraListeners com.databricks.backend.daemon.driver.DBCEventLoggingListener,org.apache.spark.listeners.UnifiedSparkListener
    spark.unifiedListener.sink org.apache.spark.listeners.sink.loganalytics.LogAnalyticsListenerSink
    spark.unifiedListener.logBlockUpdates false
    ```

1. <span data-ttu-id="5dac3-162">尽管仍下**Spark**选项卡上，输入以下值中的**环境变量**文本框中：</span><span class="sxs-lookup"><span data-stu-id="5dac3-162">While still under the **Spark** tab, enter the following values in the **Environment Variables** text box:</span></span>

    ```
    LOG_ANALYTICS_WORKSPACE_ID=[your Azure Log Analytics workspace ID]
    LOG_ANALYTICS_WORKSPACE_KEY=[your Azure Log Analytics workspace key]
    ```

    ![Databricks UI 的屏幕快照](./_images/create-cluster1.png)

1. <span data-ttu-id="5dac3-164">尽管仍下**高级选项**部分中，单击**Init 脚本**选项卡。</span><span class="sxs-lookup"><span data-stu-id="5dac3-164">While still under the **Advanced Options** section, click on the **Init Scripts** tab.</span></span>
1. <span data-ttu-id="5dac3-165">在中**目标**下拉列表中，选择**DBFS**。</span><span class="sxs-lookup"><span data-stu-id="5dac3-165">In the **Destination** dropdown, select **DBFS**.</span></span>
1. <span data-ttu-id="5dac3-166">下**Init 脚本路径**，输入`dbfs:/databricks/monitoring-staging/listeners.sh`。</span><span class="sxs-lookup"><span data-stu-id="5dac3-166">Under **Init Script Path**, enter `dbfs:/databricks/monitoring-staging/listeners.sh`.</span></span>
1. <span data-ttu-id="5dac3-167">单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="5dac3-167">Click **Add**.</span></span>

    ![Databricks UI 的屏幕快照](./_images/create-cluster2.png)

1. <span data-ttu-id="5dac3-169">单击**创建群集**来创建群集。</span><span class="sxs-lookup"><span data-stu-id="5dac3-169">Click **Create Cluster** to create the cluster.</span></span>

## <a name="view-metrics"></a><span data-ttu-id="5dac3-170">查看指标</span><span class="sxs-lookup"><span data-stu-id="5dac3-170">View metrics</span></span>

<span data-ttu-id="5dac3-171">完成这些步骤后，Databricks 群集将流式传输到 Azure Monitor 群集本身有关的一些指标数据。</span><span class="sxs-lookup"><span data-stu-id="5dac3-171">After you complete these steps, your Databricks cluster streams some metric data about the cluster itself to Azure Monitor.</span></span> <span data-ttu-id="5dac3-172">在 Azure Log Analytics 工作区下中会提供此日志数据"活动 |自定义日志 |SparkMetric_CL"的架构。</span><span class="sxs-lookup"><span data-stu-id="5dac3-172">This log data is available in your Azure Log Analytics workspace under the "Active | Custom Logs | SparkMetric_CL" schema.</span></span> <span data-ttu-id="5dac3-173">有关类型的记录的指标的详细信息，请参阅[指标核心](https://metrics.dropwizard.io/4.0.0/manual/core.html)Dropwizard 文档中。</span><span class="sxs-lookup"><span data-stu-id="5dac3-173">For more information about the types of metrics that are logged, see [Metrics Core](https://metrics.dropwizard.io/4.0.0/manual/core.html) in the Dropwizard documentation.</span></span> <span data-ttu-id="5dac3-174">指标类型，如仪表、 计数器或直方图，写入到类型字段。</span><span class="sxs-lookup"><span data-stu-id="5dac3-174">The metric type, such as gauge, counter, or histogram, is written to the Type field.</span></span>

<span data-ttu-id="5dac3-175">此外，Apache Spark 级别事件和从你的作业到 Azure Monitor 的 Spark 结构化流的指标，将流式传输库。</span><span class="sxs-lookup"><span data-stu-id="5dac3-175">In addition, the library streams Apache Spark level events and Spark Structured Streaming metrics from your jobs to Azure Monitor.</span></span> <span data-ttu-id="5dac3-176">不需要对这些事件和指标的应用程序代码进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="5dac3-176">You don't need to make any changes to your application code for these events and metrics.</span></span> <span data-ttu-id="5dac3-177">Spark 结构化流式处理日志数据位于下"活动 |自定义日志 |SparkListenerEvent_CL"的架构。</span><span class="sxs-lookup"><span data-stu-id="5dac3-177">Spark Structured Streaming log data is available under the "Active | Custom Logs | SparkListenerEvent_CL" schema.</span></span>

![Log Analytics 工作区的屏幕截图](./_images/workspace.png)

<span data-ttu-id="5dac3-179">有关查看日志的详细信息，请参阅[查看和分析 Azure Monitor 中的日志数据](/azure/azure-monitor/log-query/portals)。</span><span class="sxs-lookup"><span data-stu-id="5dac3-179">For more information about viewing logs, see [Viewing and analyzing log data in Azure Monitor](/azure/azure-monitor/log-query/portals).</span></span>

## <a name="next-steps"></a><span data-ttu-id="5dac3-180">后续步骤</span><span class="sxs-lookup"><span data-stu-id="5dac3-180">Next steps</span></span>

<span data-ttu-id="5dac3-181">本文介绍了如何将指标发送到 Azure Monitor 将群集配置。</span><span class="sxs-lookup"><span data-stu-id="5dac3-181">This article described how to configure your cluster to send metrics to Azure Monitor.</span></span> <span data-ttu-id="5dac3-182">下一篇文章演示如何使用 Azure Databricks 监视库发送应用程序指标和日志。</span><span class="sxs-lookup"><span data-stu-id="5dac3-182">The next article shows how to use the Azure Databricks Monitoring Library to send application metrics and logs.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5dac3-183">将应用程序日志发送到 Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="5dac3-183">Send application logs to Azure Monitor</span></span>](./application-logs.md)
