---
title: Azure Databricks 应用程序日志发送到 Azure Monitor
description: 如何从 Azure Databricks 中将自定义日志和指标发送到 Azure Monitor
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: 49c631687fb3e3bbd807ffbbb49d9c5f6526bfb4
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503370"
---
# <a name="send-azure-databricks-application-logs-to-azure-monitor"></a>Azure Databricks 应用程序日志发送到 Azure Monitor

本文介绍如何将应用程序日志和指标发送到 Azure Databricks 从[Log Analytics 工作区](/azure/azure-monitor/platform/manage-access)。 它使用[Azure Databricks 监视库](https://github.com/mspnp/spark-monitoring)，这是 GitHub 上提供。

## <a name="prerequisites"></a>必备组件

使用监视库，将 Azure Databricks 群集配置中所述[配置 Azure Databricks 以发送到 Azure Monitor 指标][config-cluster]。

> [!NOTE]
> 监视库传输的 Apache Spark 级别事件和从你的作业到 Azure Monitor 的 Spark 结构化流的指标。 不需要对这些事件和指标的应用程序代码进行任何更改。

## <a name="send-application-metrics-using-dropwizard"></a>发送使用 Dropwizard 的应用程序指标

Spark 使用基于 Dropwizard 指标库的可配置的指标系统。 有关详细信息，请参阅[指标](https://spark.apache.org/docs/latest/monitoring.html#metrics)Spark 文档中。

若要从 Azure Databricks 的应用程序代码的应用程序指标发送到 Azure Monitor，请执行以下步骤：

1. 构建**spark-侦听器-loganalytics-1.0-SNAPSHOT.jar** JAR 文件中所述[构建 Azure Databricks 监视库][build-lib]。

1. 创建 Dropwizard[仪表或计数器](https://metrics.dropwizard.io/4.0.0/manual/core.html)在应用程序代码。 可以使用`UserMetricsSystem`监视库中定义的类。 下面的示例创建一个计数器名为`counter1`。

    ```Scala
    import org.apache.spark.metrics.UserMetricsSystems
    import org.apache.spark.sql.SparkSession

    object StreamingQueryListenerSampleJob  {

      private final val METRICS_NAMESPACE = "samplejob"
      private final val COUNTER_NAME = "counter1"

      def main(args: Array[String]): Unit = {

        val spark = SparkSession
          .builder
          .getOrCreate

        val driverMetricsSystem = UserMetricsSystems
            .getMetricSystem(METRICS_NAMESPACE, builder => {
              builder.registerCounter(COUNTER_NAME)
            })

        driverMetricsSystem.counter(COUNTER_NAME).inc(5)
      }
    }
    ```

    监视库包括[示例应用程序][ sample-app]演示如何使用`UserMetricsSystem`类。

## <a name="send-application-logs-using-log4j"></a>发送使用 Log4j 的应用程序日志

若要将 Azure Databricks 应用程序日志发送到使用 Azure Log Analytics [Log4j 追加器](https://logging.apache.org/log4j/2.x/manual/appenders.html)在库中，请执行以下步骤：

1. 构建**spark-侦听器-loganalytics-1.0-SNAPSHOT.jar** JAR 文件中所述[构建 Azure Databricks 监视库][build-lib]。

1. 创建**log4j.properties** [配置文件](https://logging.apache.org/log4j/2.x/manual/configuration.html)为应用程序。 包括以下配置属性。 替换你的应用程序包名称和日志级别指示的位置：

    ```YAML
    log4j.appender.A1=com.microsoft.pnp.logging.loganalytics.LogAnalyticsAppender
    log4j.appender.A1.layout=com.microsoft.pnp.logging.JSONLayout
    log4j.appender.A1.layout.LocationInfo=false
    log4j.additivity.<your application package name>=false
    log4j.logger.<your application package name>=<log level>, A1
    ```

    您可以找到示例配置文件[这里][log4j.properties]。

1. 在应用程序代码中，包括**spark 侦听器 loganalytics**项目，并导入`com.microsoft.pnp.logging.Log4jconfiguration`对应用程序代码。

    ```Scala
    import com.microsoft.pnp.logging.Log4jConfiguration
    ```

1. 配置使用 Log4j **log4j.properties**步骤 3 中创建的文件：

    ```Scala
    getClass.getResourceAsStream("<path to file in your JAR file>/log4j.properties")) {
          stream => {
            Log4jConfiguration.configure(stream)
          }
    }
    ```

1. 根据需要在代码中的适当级别添加 Apache Spark 的日志消息。 例如，使用`logDebug`方法发送调试日志消息。 有关详细信息，请参阅[日志记录][ spark-logging] Spark 文档中。

    ```Scala
    logTrace("Trace message")
    logDebug("Debug message")
    logInfo("Info message")
    logWarning("Warning message")
    logError("Error message")
    ```

## <a name="run-the-sample-application"></a>运行示例应用程序

监视库包括[示例应用程序][ sample-app]演示如何将应用程序指标和应用程序日志发送到 Azure Monitor。 运行示例：

1. 构建**spark 作业**中所述，在监视的库中，项目[构建 Azure Databricks 监视库][build-lib]。

1. 导航到 Databricks 工作区并创建新的作业，如所述[此处](https://docs.azuredatabricks.net/user-guide/jobs.html#create-a-job)。

1. 在作业详细信息页中，选择**设置 JAR**。

1. 将从 JAR 文件上传`/src/spark-jobs/target/spark-jobs-1.0-SNAPSHOT.jar`。

1. 有关**Main 类**，输入`com.microsoft.pnp.samplejob.StreamingQueryListenerSampleJob`。

1. 选择已配置为使用监视库的群集。 请参阅[配置 Azure Databricks 以发送到 Azure Monitor 指标][config-cluster]。

运行作业时，可以在 Log Analytics 工作区中查看应用程序日志和指标。

应用程序日志显示在 SparkLoggingEvent_CL 下：

```Kusto
SparkLoggingEvent_CL | where logger_name_s contains "com.microsoft.pnp"
```

应用程序指标会显示在 SparkMetric_CL 下：

```Kusto
SparkMetric_CL | where name_s contains "rowcounter" | limit 50
```

## <a name="next-steps"></a>后续步骤

部署性能监视仪表板，其中随附此代码库来解决在生产 Azure Databricks 工作负荷的性能问题。

> [!div class="nextstepaction"]
> [使用仪表板直观显示 Azure Databricks 指标](./dashboards.md)

<!-- links -->

[build-lib]: ./configure-cluster.md##build-the-azure-databricks-monitoring-library
[config-cluster]: ./configure-cluster.md
[log4j.properties]: https://github.com/mspnp/spark-monitoring/blob/master/src/spark-jobs/src/main/resources/com/microsoft/pnp/samplejob/log4j.properties
[sample-app]: https://github.com/mspnp/spark-monitoring/tree/master/src/spark-jobs
[spark-logging]: https://spark.apache.org/docs/2.3.0/api/java/org/apache/spark/internal/Logging.html