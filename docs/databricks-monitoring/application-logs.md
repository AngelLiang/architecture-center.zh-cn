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
# <a name="send-azure-databricks-application-logs-to-azure-monitor"></a><span data-ttu-id="7fbf7-103">Azure Databricks 应用程序日志发送到 Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="7fbf7-103">Send Azure Databricks application logs to Azure Monitor</span></span>

<span data-ttu-id="7fbf7-104">本文介绍如何将应用程序日志和指标发送到 Azure Databricks 从[Log Analytics 工作区](/azure/azure-monitor/platform/manage-access)。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-104">This article shows how to send application logs and metrics from Azure Databricks to a [Log Analytics workspace](/azure/azure-monitor/platform/manage-access).</span></span> <span data-ttu-id="7fbf7-105">它使用[Azure Databricks 监视库](https://github.com/mspnp/spark-monitoring)，这是 GitHub 上提供。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-105">It uses the [Azure Databricks Monitoring Library](https://github.com/mspnp/spark-monitoring), which is available on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7fbf7-106">必备组件</span><span class="sxs-lookup"><span data-stu-id="7fbf7-106">Prerequisites</span></span>

<span data-ttu-id="7fbf7-107">使用监视库，将 Azure Databricks 群集配置中所述[配置 Azure Databricks 以发送到 Azure Monitor 指标][config-cluster]。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-107">Configure your Azure Databricks cluster to use the monitoring library, as described in [Configure Azure Databricks to send metrics to Azure Monitor][config-cluster].</span></span>

> [!NOTE]
> <span data-ttu-id="7fbf7-108">监视库传输的 Apache Spark 级别事件和从你的作业到 Azure Monitor 的 Spark 结构化流的指标。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-108">The monitoring library streams Apache Spark level events and Spark Structured Streaming metrics from your jobs to Azure Monitor.</span></span> <span data-ttu-id="7fbf7-109">不需要对这些事件和指标的应用程序代码进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-109">You don't need to make any changes to your application code for these events and metrics.</span></span>

## <a name="send-application-metrics-using-dropwizard"></a><span data-ttu-id="7fbf7-110">发送使用 Dropwizard 的应用程序指标</span><span class="sxs-lookup"><span data-stu-id="7fbf7-110">Send application metrics using Dropwizard</span></span>

<span data-ttu-id="7fbf7-111">Spark 使用基于 Dropwizard 指标库的可配置的指标系统。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-111">Spark uses a configurable metrics system based on the Dropwizard Metrics Library.</span></span> <span data-ttu-id="7fbf7-112">有关详细信息，请参阅[指标](https://spark.apache.org/docs/latest/monitoring.html#metrics)Spark 文档中。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-112">For more information, see [Metrics](https://spark.apache.org/docs/latest/monitoring.html#metrics) in the Spark documentation.</span></span>

<span data-ttu-id="7fbf7-113">若要从 Azure Databricks 的应用程序代码的应用程序指标发送到 Azure Monitor，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="7fbf7-113">To send application metrics from Azure Databricks application code to Azure Monitor, follow these steps:</span></span>

1. <span data-ttu-id="7fbf7-114">构建**spark-侦听器-loganalytics-1.0-SNAPSHOT.jar** JAR 文件中所述[构建 Azure Databricks 监视库][build-lib]。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-114">Build the **spark-listeners-loganalytics-1.0-SNAPSHOT.jar** JAR file as described in [Build the Azure Databricks Monitoring Library][build-lib].</span></span>

1. <span data-ttu-id="7fbf7-115">创建 Dropwizard[仪表或计数器](https://metrics.dropwizard.io/4.0.0/manual/core.html)在应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-115">Create Dropwizard [gauges or counters](https://metrics.dropwizard.io/4.0.0/manual/core.html) in your application code.</span></span> <span data-ttu-id="7fbf7-116">可以使用`UserMetricsSystem`监视库中定义的类。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-116">You can use the `UserMetricsSystem` class defined in the monitoring library.</span></span> <span data-ttu-id="7fbf7-117">下面的示例创建一个计数器名为`counter1`。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-117">The following example creates a counter named `counter1`.</span></span>

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

    <span data-ttu-id="7fbf7-118">监视库包括[示例应用程序][ sample-app]演示如何使用`UserMetricsSystem`类。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-118">The monitoring library includes a [sample application][sample-app] that demonstrates how to use the `UserMetricsSystem` class.</span></span>

## <a name="send-application-logs-using-log4j"></a><span data-ttu-id="7fbf7-119">发送使用 Log4j 的应用程序日志</span><span class="sxs-lookup"><span data-stu-id="7fbf7-119">Send application logs using Log4j</span></span>

<span data-ttu-id="7fbf7-120">若要将 Azure Databricks 应用程序日志发送到使用 Azure Log Analytics [Log4j 追加器](https://logging.apache.org/log4j/2.x/manual/appenders.html)在库中，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="7fbf7-120">To send your Azure Databricks application logs to Azure Log Analytics using the [Log4j appender](https://logging.apache.org/log4j/2.x/manual/appenders.html) in the library, follow these steps:</span></span>

1. <span data-ttu-id="7fbf7-121">构建**spark-侦听器-loganalytics-1.0-SNAPSHOT.jar** JAR 文件中所述[构建 Azure Databricks 监视库][build-lib]。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-121">Build the **spark-listeners-loganalytics-1.0-SNAPSHOT.jar** JAR file as described in [Build the Azure Databricks Monitoring Library][build-lib].</span></span>

1. <span data-ttu-id="7fbf7-122">创建**log4j.properties** [配置文件](https://logging.apache.org/log4j/2.x/manual/configuration.html)为应用程序。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-122">Create a **log4j.properties** [configuration file](https://logging.apache.org/log4j/2.x/manual/configuration.html) for your application.</span></span> <span data-ttu-id="7fbf7-123">包括以下配置属性。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-123">Include the following configuration properties.</span></span> <span data-ttu-id="7fbf7-124">替换你的应用程序包名称和日志级别指示的位置：</span><span class="sxs-lookup"><span data-stu-id="7fbf7-124">Substitute your application package name and log level where indicated:</span></span>

    ```YAML
    log4j.appender.A1=com.microsoft.pnp.logging.loganalytics.LogAnalyticsAppender
    log4j.appender.A1.layout=com.microsoft.pnp.logging.JSONLayout
    log4j.appender.A1.layout.LocationInfo=false
    log4j.additivity.<your application package name>=false
    log4j.logger.<your application package name>=<log level>, A1
    ```

    <span data-ttu-id="7fbf7-125">您可以找到示例配置文件[这里][log4j.properties]。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-125">You can find a sample configuration file [here][log4j.properties].</span></span>

1. <span data-ttu-id="7fbf7-126">在应用程序代码中，包括**spark 侦听器 loganalytics**项目，并导入`com.microsoft.pnp.logging.Log4jconfiguration`对应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-126">In your application code, include the **spark-listeners-loganalytics** project, and import `com.microsoft.pnp.logging.Log4jconfiguration` to your application code.</span></span>

    ```Scala
    import com.microsoft.pnp.logging.Log4jConfiguration
    ```

1. <span data-ttu-id="7fbf7-127">配置使用 Log4j **log4j.properties**步骤 3 中创建的文件：</span><span class="sxs-lookup"><span data-stu-id="7fbf7-127">Configure Log4j using the **log4j.properties** file you created in step 3:</span></span>

    ```Scala
    getClass.getResourceAsStream("<path to file in your JAR file>/log4j.properties")) {
          stream => {
            Log4jConfiguration.configure(stream)
          }
    }
    ```

1. <span data-ttu-id="7fbf7-128">根据需要在代码中的适当级别添加 Apache Spark 的日志消息。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-128">Add Apache Spark log messages at the appropriate level in your code as required.</span></span> <span data-ttu-id="7fbf7-129">例如，使用`logDebug`方法发送调试日志消息。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-129">For example, use the `logDebug` method to send a debug log meesage.</span></span> <span data-ttu-id="7fbf7-130">有关详细信息，请参阅[日志记录][ spark-logging] Spark 文档中。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-130">For more information, see [Logging][spark-logging] in the Spark documentation.</span></span>

    ```Scala
    logTrace("Trace message")
    logDebug("Debug message")
    logInfo("Info message")
    logWarning("Warning message")
    logError("Error message")
    ```

## <a name="run-the-sample-application"></a><span data-ttu-id="7fbf7-131">运行示例应用程序</span><span class="sxs-lookup"><span data-stu-id="7fbf7-131">Run the sample application</span></span>

<span data-ttu-id="7fbf7-132">监视库包括[示例应用程序][ sample-app]演示如何将应用程序指标和应用程序日志发送到 Azure Monitor。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-132">The monitoring library includes a [sample application][sample-app] that demonstrates how to send both application metrics and application logs to Azure Monitor.</span></span> <span data-ttu-id="7fbf7-133">运行示例：</span><span class="sxs-lookup"><span data-stu-id="7fbf7-133">To run the sample:</span></span>

1. <span data-ttu-id="7fbf7-134">构建**spark 作业**中所述，在监视的库中，项目[构建 Azure Databricks 监视库][build-lib]。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-134">Build the **spark-jobs** project in the monitoring library, as described in [Build the Azure Databricks Monitoring Library][build-lib].</span></span>

1. <span data-ttu-id="7fbf7-135">导航到 Databricks 工作区并创建新的作业，如所述[此处](https://docs.azuredatabricks.net/user-guide/jobs.html#create-a-job)。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-135">Navigate to your Databricks workspace and create a new job, as described [here](https://docs.azuredatabricks.net/user-guide/jobs.html#create-a-job).</span></span>

1. <span data-ttu-id="7fbf7-136">在作业详细信息页中，选择**设置 JAR**。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-136">In the job detail page, select **Set JAR**.</span></span>

1. <span data-ttu-id="7fbf7-137">将从 JAR 文件上传`/src/spark-jobs/target/spark-jobs-1.0-SNAPSHOT.jar`。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-137">Upload the JAR file from `/src/spark-jobs/target/spark-jobs-1.0-SNAPSHOT.jar`.</span></span>

1. <span data-ttu-id="7fbf7-138">有关**Main 类**，输入`com.microsoft.pnp.samplejob.StreamingQueryListenerSampleJob`。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-138">For **Main class**, enter `com.microsoft.pnp.samplejob.StreamingQueryListenerSampleJob`.</span></span>

1. <span data-ttu-id="7fbf7-139">选择已配置为使用监视库的群集。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-139">Select a cluster that is already configured to use the monitoring library.</span></span> <span data-ttu-id="7fbf7-140">请参阅[配置 Azure Databricks 以发送到 Azure Monitor 指标][config-cluster]。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-140">See [Configure Azure Databricks to send metrics to Azure Monitor][config-cluster].</span></span>

<span data-ttu-id="7fbf7-141">运行作业时，可以在 Log Analytics 工作区中查看应用程序日志和指标。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-141">When the job runs, you can view the application logs and metrics in your Log Analytics workspace.</span></span>

<span data-ttu-id="7fbf7-142">应用程序日志显示在 SparkLoggingEvent_CL 下：</span><span class="sxs-lookup"><span data-stu-id="7fbf7-142">Application logs appear under SparkLoggingEvent_CL:</span></span>

```Kusto
SparkLoggingEvent_CL | where logger_name_s contains "com.microsoft.pnp"
```

<span data-ttu-id="7fbf7-143">应用程序指标会显示在 SparkMetric_CL 下：</span><span class="sxs-lookup"><span data-stu-id="7fbf7-143">Application metrics appear under SparkMetric_CL:</span></span>

```Kusto
SparkMetric_CL | where name_s contains "rowcounter" | limit 50
```

## <a name="next-steps"></a><span data-ttu-id="7fbf7-144">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7fbf7-144">Next steps</span></span>

<span data-ttu-id="7fbf7-145">部署性能监视仪表板，其中随附此代码库来解决在生产 Azure Databricks 工作负荷的性能问题。</span><span class="sxs-lookup"><span data-stu-id="7fbf7-145">Deploy the performance monitoring dashboard that accompanies this code library to troubleshoot performance issues in your production Azure Databricks workloads.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7fbf7-146">使用仪表板直观显示 Azure Databricks 指标</span><span class="sxs-lookup"><span data-stu-id="7fbf7-146">Use dashboards to visualize Azure Databricks metrics</span></span>](./dashboards.md)

<!-- links -->

[build-lib]: ./configure-cluster.md##build-the-azure-databricks-monitoring-library
[config-cluster]: ./configure-cluster.md
[log4j.properties]: https://github.com/mspnp/spark-monitoring/blob/master/src/spark-jobs/src/main/resources/com/microsoft/pnp/samplejob/log4j.properties
[sample-app]: https://github.com/mspnp/spark-monitoring/tree/master/src/spark-jobs
[spark-logging]: https://spark.apache.org/docs/2.3.0/api/java/org/apache/spark/internal/Logging.html