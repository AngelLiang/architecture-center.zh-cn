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

# <a name="configure-azure-databricks-to-send-metrics-to-azure-monitor"></a>配置 Azure Databricks 将发送到 Azure Monitor 的指标

本文介绍如何配置 Azure Databricks 群集中发送到的指标[Log Analytics 工作区](/azure/azure-monitor/platform/manage-access)。 它使用[Azure Databricks 监视库](https://github.com/mspnp/spark-monitoring)，这是 GitHub 上提供。 了解 Java、 Scala 和 Maven 为 prerequisistes 建议。

## <a name="about-the-azure-databricks-monitoring-library"></a>有关 Azure Databricks 监视库

[GitHub 存储库](https://github.com/mspnp/spark-monitoring)有关 Azure Databricks 监视库具有以下目录结构：

```
/src  
    /spark-jobs  
    /spark-listeners-loganalytics  
    /spark-listeners  
    /pom.xml  
```

**Spark 作业**目录是演示如何实现 Spark 应用程序指标计数器的示例 Spark 应用程序。

**Spark 侦听器**目录中包括的功能，使 Azure Databricks 将在服务级别上的 Apache Spark 事件发送到 Azure Log Analytics 工作区。 Azure Databricks 是基于 Apache Spark，其中包括一组结构化 Api，可用于使用数据集、 数据框架和 SQL 处理数据的批处理的服务。 使用 Apache Spark 2.0 中，添加了对支持[结构化流式处理](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)、 数据流式处理基于 Spark 的批处理 Api 的 API。

**Spark 侦听器 loganalytics**目录包含适用于 Spark 侦听器、 DropWizard，接收器和 Azure Log Analytics 工作区的客户端的接收器。 此目录还包含 Apache Spark 应用程序日志 log4j 追加器。

**Spark 侦听器 loganalytics**并**spark 侦听器**目录包含用于生成部署到 Databricks 群集的两个 JAR 文件的代码。 **Spark 侦听器**目录中包括**脚本**目录，其中包含群集节点初始化脚本将 JAR 复制到 Azure Databricks 文件系统文件从临时目录执行节点。

**Pom.xml**文件是整个项目的主要 Maven 生成文件。

## <a name="prerequisites"></a>必备组件

若要开始，将部署以下 Azure 资源：

- Azure Databricks 工作区中。 请参阅[快速入门：使用 Azure 门户在 Azure Databricks 上运行 Spark 作业](/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。
- Log Analytics 工作区。 请参阅[Azure 门户中创建 Log Analytics 工作区](/azure/azure-monitor/learn/quick-create-workspace)。

接下来，安装[Azure Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 使用 CLI 需要一个 Azure Databricks 个人访问令牌。 有关说明，请参阅[生成令牌](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)。

查找 Log Analytics 工作区 ID 和密钥。 有关说明，请参阅[获取工作区 ID 和密钥](/azure/azure-monitor/platform/agent-windows#obtain-workspace-id-and-key)。 配置在 Databricks 群集时，将需要这些值。

若要生成监视库，您将需要 Java IDE，它具有以下资源：

- [Java 开发工具包 (JDK) 1.8 版](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
- [Scala 语言 SDK 2.11](https://www.scala-lang.org/download/)
- [Apache Maven 3.5.4](http://maven.apache.org/download.cgi)

## <a name="build-the-azure-databricks-monitoring-library"></a>生成 Azure Databricks 监视库

若要生成 Azure Databricks 监视库，请执行以下步骤：

1. 克隆、 分叉或下载[GitHub 存储库](https://github.com/mspnp/spark-monitoring)。

1. 导入 Maven 项目对象模型文件中， _pom.xml_，它位于 **/src**文件夹导入到你的项目。 这将导入三个项目：

    - spark-jobs
    - spark-listeners
    - spark-listeners-loganalytics

1. 执行 Maven**包**Java IDE 来生成这些项目的每个 JAR 文件中的生成阶段：

    |Project| JAR 文件|
    |-------|---------|
    |spark-jobs|spark-jobs-1.0-SNAPSHOT.jar|
    |spark-listeners|spark-listeners-1.0-SNAPSHOT.jar|
    |spark-listeners-loganalytics|spark-listeners-loganalytics-1.0-SNAPSHOT.jar|

1. 使用 Azure Databricks CLI 来创建一个名为**dbfs: / 监视 databricks 的暂存**:  

    ```bash
    dbfs mkdirs dbfs:/databricks/monitoring-staging
    ```

1. 使用 Azure Databricks CLI 来复制 **/src/spark-listeners/scripts/listeners.sh**到以前的目录：

    ```bash
    dbfs cp ./src/spark-listeners/scripts/listeners.sh dbfs:/databricks/monitoring-staging/listeners.sh
    ```

1. 使用 Azure Databricks CLI 来复制 **/src/spark-listeners/scripts/metrics.properties**为以前创建的目录：

    ```bash
    dbfs cp <local path to metrics.properties> dbfs:/databricks/monitoring-staging/metrics.properties
    ```

1. 使用 Azure Databricks CLI 来复制**spark-侦听器-1.0-SNAPSHOT.jar**并**spark-侦听器-loganalytics-1.0-SNAPSHOT.jar**为以前创建的目录：

    ```bash
    dbfs cp ./src/spark-listeners/target/spark-listeners-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-1.0-SNAPSHOT.jar
    dbfs cp ./src/spark-listeners-loganalytics/target/spark-listeners-loganalytics-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-loganalytics-1.0-SNAPSHOT.jar
    ```

## <a name="create-and-configure-an-azure-databricks-cluster"></a>创建和配置 Azure Databricks 群集

若要创建和配置 Azure Databricks 群集中，执行以下步骤：

1. 导航到你在 Azure 门户中的 Azure Databricks 工作区。
1. 在主页上，单击**新的群集**。
1. 输入您的群集的名称**群集名称**文本框。
1. 在中**Databricks 运行时版本**下拉列表中，选择**4.3**或更高版本。
1. 下**高级选项**，单击**Spark**选项卡。输入中的以下名称 / 值对**Spark 配置**文本框中：

    ```
    spark.extraListeners com.databricks.backend.daemon.driver.DBCEventLoggingListener,org.apache.spark.listeners.UnifiedSparkListener
    spark.unifiedListener.sink org.apache.spark.listeners.sink.loganalytics.LogAnalyticsListenerSink
    spark.unifiedListener.logBlockUpdates false
    ```

1. 尽管仍下**Spark**选项卡上，输入以下值中的**环境变量**文本框中：

    ```
    LOG_ANALYTICS_WORKSPACE_ID=[your Azure Log Analytics workspace ID]
    LOG_ANALYTICS_WORKSPACE_KEY=[your Azure Log Analytics workspace key]
    ```

    ![Databricks UI 的屏幕快照](./_images/create-cluster1.png)

1. 尽管仍下**高级选项**部分中，单击**Init 脚本**选项卡。
1. 在中**目标**下拉列表中，选择**DBFS**。
1. 下**Init 脚本路径**，输入`dbfs:/databricks/monitoring-staging/listeners.sh`。
1. 单击“添加”。

    ![Databricks UI 的屏幕快照](./_images/create-cluster2.png)

1. 单击**创建群集**来创建群集。

## <a name="view-metrics"></a>查看指标

完成这些步骤后，Databricks 群集将流式传输到 Azure Monitor 群集本身有关的一些指标数据。 在 Azure Log Analytics 工作区下中会提供此日志数据"活动 |自定义日志 |SparkMetric_CL"的架构。 有关类型的记录的指标的详细信息，请参阅[指标核心](https://metrics.dropwizard.io/4.0.0/manual/core.html)Dropwizard 文档中。 指标类型，如仪表、 计数器或直方图，写入到类型字段。

此外，Apache Spark 级别事件和从你的作业到 Azure Monitor 的 Spark 结构化流的指标，将流式传输库。 不需要对这些事件和指标的应用程序代码进行任何更改。 Spark 结构化流式处理日志数据位于下"活动 |自定义日志 |SparkListenerEvent_CL"的架构。

![Log Analytics 工作区的屏幕截图](./_images/workspace.png)

有关查看日志的详细信息，请参阅[查看和分析 Azure Monitor 中的日志数据](/azure/azure-monitor/log-query/portals)。

## <a name="next-steps"></a>后续步骤

本文介绍了如何将指标发送到 Azure Monitor 将群集配置。 下一篇文章演示如何使用 Azure Databricks 监视库发送应用程序指标和日志。

> [!div class="nextstepaction"]
> [将应用程序日志发送到 Azure Monitor](./application-logs.md)
