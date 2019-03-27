---
title: 使用 Azure Databricks 进行流处理
titleSuffix: Azure Reference Architectures
description: 使用 Azure Databricks 在 Azure 中创建端到端流处理管道。
author: petertaylor9999
ms.date: 11/30/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 748b191aeee931d580dd27b1ad54c4f4bd63e369
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243838"
---
# <a name="create-a-stream-processing-pipeline-with-azure-databricks"></a>使用 Azure Databricks 创建流处理管道

本参考体系结构演示一个端到端[流处理](/azure/architecture/data-guide/big-data/real-time-processing)管道。 此类管道包括四个阶段：引入、处理、存储，以及分析和报告。 本参考体系结构中的管道从两个源引入数据，针对每个流中的相关记录执行联接，丰富结果，然后实时计算平均值。 将存储结果以供进一步分析。 [**部署此解决方案**](#deploy-the-solution)。

![使用 Azure Databricks 进行的流处理的参考体系结构](./images/stream-processing-databricks.png)

**场景**：某家出租车公司需要收集有关出租车的每次行程的数据。 对于此场景，我们假设有两个不同的设备在发送数据。 出租车的计量表发送有关每次行程的信息 &mdash; 持续时间、距离以及上车和下车地点。 有一个单独的设备接受客户的付款，并发送有关费用的数据。 为了绘制行程趋势图，该出租车公司想要实时计算每个周边区域的每英里平均小费。

## <a name="architecture"></a>体系结构

该体系结构包括以下组件。

**数据源**。 在此体系结构中，有两个数据源实时生成数据流。 第一个流包含行程信息，第二个流包含费用信息。 该参考体系结构包含一个模拟的数据生成器，该生成器读取一组静态文件，并将数据推送到事件中心。 实际应用中的数据源是安装在出租车内部的设备。

**Azure 事件中心**。 [事件中心](/azure/event-hubs/)是一个事件引入服务。 此体系结构使用两个事件中心实例，每个数据源各对应一个。 每个数据源将数据流发送到关联的事件中心。

**Azure Databricks**。 [Databricks](/azure/azure-databricks/) 是基于 Apache Spark 的分析平台，已针对 Microsoft Azure 云服务平台进行优化。 Databricks 用于关联出租车行程数据和费用数据，以及使用 Databricks 文件系统中存储的周边区域数据丰富关联的数据。

**Cosmos DB**。 Azure Databricks 作业的输出是使用 Cassandra API 写入到 [Cosmos DB](/azure/cosmos-db/) 的一系列记录。 之所以使用 Cassandra API，是因为它支持时序数据建模。

**Azure Log Analytics**。 [Azure Monitor](/azure/monitoring-and-diagnostics/) 收集的应用程序日志数据存储在 [Log Analytics 工作区](/azure/log-analytics)中。 可以使用 Log Analytics 来分析和可视化指标，以及检查日志消息以确定应用程序中的问题。

## <a name="data-ingestion"></a>数据引入

<!-- markdownlint-disable MD033 -->

为了模拟数据源，此参考体系结构使用了[纽约市出租车数据](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797)数据集<sup>[[1]](#note1)</sup>。 此数据集包含纽约市过去四年（2010 年 &ndash; 2013 年）的出租车行程数据。 其包含两种类型的记录：行程数据和费用数据。 行程数据包括行程持续时间、行程距离以及上车和下车地点。 费用数据包括乘车费、税费和小费金额。 这两种记录类型中的通用字段包括牌照号、出租车驾照和供应商 ID。 这三个字段相结合，唯一标识了出租车和驾驶员。 数据以 CSV 格式存储。

> [1] <span id="note1">Donovan, Brian；Work, Dan (2016)：纽约市出租车行程数据 (2010-2013)。 伊利诺伊大学厄巴纳-香槟分校。 <https://doi.org/10.13012/J8PN93H8>

<!-- markdownlint-enable MD033 -->

数据生成器是一个读取记录并将其发送到 Azure 事件中心的 .NET Core 应用程序。 该生成器发送 JSON 格式的行程数据以及 CSV 格式的费用数据。

事件中心使用[分区](/azure/event-hubs/event-hubs-features#partitions)将数据分段。 使用者可以通过分区功能并行读取每个分区。 将数据发送到事件中心时，可以显式指定分区键。 否则，记录将以循环方式分配到分区。

在此场景中，给定出租车的行程数据和费用数据最终会获得相同的分区 ID。 这样，在关联两个流时，Databricks 可以应用某种并行度。 行程数据分区 *n* 中的记录将与费用数据分区 *n* 中的记录进行匹配。

![使用 Azure Databricks 和事件中心进行的流处理的图](./images/stream-processing-databricks-eh.png)

在数据生成器中，这两种记录类型的通用数据模型具有 `PartitionKey` 属性，该属性是 `Medallion`、`HackLicense` 和 `VendorId` 的串联形式。

```csharp
public abstract class TaxiData
{
    public TaxiData()
    {
    }

    [JsonProperty]
    public long Medallion { get; set; }

    [JsonProperty]
    public long HackLicense { get; set; }

    [JsonProperty]
    public string VendorId { get; set; }

    [JsonProperty]
    public DateTimeOffset PickupTime { get; set; }

    [JsonIgnore]
    public string PartitionKey
    {
        get => $"{Medallion}_{HackLicense}_{VendorId}";
    }
```

将数据发送到事件中心时，会使用此属性来提供显式分区键：

```csharp
using (var client = pool.GetObject())
{
    return client.Value.SendAsync(new EventData(Encoding.UTF8.GetBytes(
        t.GetData(dataFormat))), t.PartitionKey);
}
```

### <a name="event-hubs"></a>事件中心

事件中心的吞吐量容量以[吞吐量单位](/azure/event-hubs/event-hubs-features#throughput-units)来度量。 可以通过启用[自动扩充](/azure/event-hubs/event-hubs-auto-inflate)来自动缩放事件中心。自动扩充可以根据流量，最高将吞吐量单位自动扩展到配置的上限。

## <a name="stream-processing"></a>流处理

在 Azure Databricks 中，数据处理由某个作业执行。 该作业分配到群集并在其上运行。 作业可以是以 Java 编写的自定义代码，或 Spark [笔记本](https://docs.databricks.com/user-guide/notebooks/index.html)。

在本参考体系结构中，作业是一个 Java 存档，其中包含以 Java 和 Scala 编写的类。 为 Databricks 作业指定 Java 存档时，将指定该类，让 Databricks 群集执行。 此处，**com.microsoft.pnp.TaxiCabReader** 类的 **main** 方法包含数据处理逻辑。

### <a name="reading-the-stream-from-the-two-event-hub-instances"></a>从两个事件中心实例读取流

数据处理逻辑使用 [Spark 结构化流](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html)从两个 Azure 事件中心实例读取数据：

```scala
val rideEventHubOptions = EventHubsConf(rideEventHubConnectionString)
      .setConsumerGroup(conf.taxiRideConsumerGroup())
      .setStartingPosition(EventPosition.fromStartOfStream)
    val rideEvents = spark.readStream
      .format("eventhubs")
      .options(rideEventHubOptions.toMap)
      .load

    val fareEventHubOptions = EventHubsConf(fareEventHubConnectionString)
      .setConsumerGroup(conf.taxiFareConsumerGroup())
      .setStartingPosition(EventPosition.fromStartOfStream)
    val fareEvents = spark.readStream
      .format("eventhubs")
      .options(fareEventHubOptions.toMap)
      .load
```

### <a name="enriching-the-data-with-the-neighborhood-information"></a>使用周边区域信息丰富数据

行程数据包括上车和下车地点的纬度与经度坐标。 尽管这些坐标很有用，但不可轻松用于分析。 因此，已使用从[形状文件](https://en.wikipedia.org/wiki/Shapefile)读取的周边区域数据丰富了这些数据。

形状文件采用二进制格式且不可轻松分析，但 [GeoTools](http://geotools.org/) 库提供了相应的工具用于处理使用形状文件格式的地理空间数据。 **com.microsoft.pnp.GeoFinder** 类中使用了此库来根据上车和下车地点坐标确定周边区域名称。

```scala
val neighborhoodFinder = (lon: Double, lat: Double) => {
      NeighborhoodFinder.getNeighborhood(lon, lat).get()
    }
```

### <a name="joining-the-ride-and-fare-data"></a>联接行程数据和费用数据

首先转换行程数据和费用数据：

```scala
    val rides = transformedRides
      .filter(r => {
        if (r.isNullAt(r.fieldIndex("errorMessage"))) {
          true
        }
        else {
          malformedRides.add(1)
          false
        }
      })
      .select(
        $"ride.*",
        to_neighborhood($"ride.pickupLon", $"ride.pickupLat")
          .as("pickupNeighborhood"),
        to_neighborhood($"ride.dropoffLon", $"ride.dropoffLat")
          .as("dropoffNeighborhood")
      )
      .withWatermark("pickupTime", conf.taxiRideWatermarkInterval())

    val fares = transformedFares
      .filter(r => {
        if (r.isNullAt(r.fieldIndex("errorMessage"))) {
          true
        }
        else {
          malformedFares.add(1)
          false
        }
      })
      .select(
        $"fare.*",
        $"pickupTime"
      )
      .withWatermark("pickupTime", conf.taxiFareWatermarkInterval())
```

然后将行程数据与费用数据相联接：

```scala
val mergedTaxiTrip = rides.join(fares, Seq("medallion", "hackLicense", "vendorId", "pickupTime"))
```

### <a name="processing-the-data-and-inserting-into-cosmos-db"></a>处理数据并将其插入到 Cosmos DB

按给定的时间间隔计算每个周边区域的平均费用金额：

```scala
val maxAvgFarePerNeighborhood = mergedTaxiTrip.selectExpr("medallion", "hackLicense", "vendorId", "pickupTime", "rateCode", "storeAndForwardFlag", "dropoffTime", "passengerCount", "tripTimeInSeconds", "tripDistanceInMiles", "pickupLon", "pickupLat", "dropoffLon", "dropoffLat", "paymentType", "fareAmount", "surcharge", "mtaTax", "tipAmount", "tollsAmount", "totalAmount", "pickupNeighborhood", "dropoffNeighborhood")
      .groupBy(window($"pickupTime", conf.windowInterval()), $"pickupNeighborhood")
      .agg(
        count("*").as("rideCount"),
        sum($"fareAmount").as("totalFareAmount"),
        sum($"tipAmount").as("totalTipAmount")
      )
      .select($"window.start", $"window.end", $"pickupNeighborhood", $"rideCount", $"totalFareAmount", $"totalTipAmount")
```

然后将此金额插入到 Cosmos DB：

```scala
maxAvgFarePerNeighborhood
      .writeStream
      .queryName("maxAvgFarePerNeighborhood_cassandra_insert")
      .outputMode(OutputMode.Append())
      .foreach(new CassandraSinkForeach(connector))
      .start()
      .awaitTermination()
```

## <a name="security-considerations"></a>安全注意事项

使用[管理员控制台](https://docs.databricks.com/administration-guide/admin-settings/index.html)控制对 Azure 数据库工作区的访问。 管理员控制台包含添加用户、管理用户权限和设置单一登录的功能。 还可以通过管理员控制台设置工作区、群集、作业和表的访问控制。

### <a name="managing-secrets"></a>管理机密

Azure Databricks 包含一个用于存储机密（包括连接字符串、访问密钥、用户名和密码）的[机密存储](https://docs.azuredatabricks.net/user-guide/secrets/index.html)。 Azure Databricks 机密存储中的机密按**范围**分区：

```bash
databricks secrets create-scope --scope "azure-databricks-job"
```

机密是在范围级别添加的：

```bash
databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
```

> [!NOTE]
> 可以使用基于 Azure Key Vault 的范围来取代本机 Azure Databricks 范围。 有关详细信息，请参阅[基于 Azure Key Vault 的范围](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes)。

在代码中，可以通过 Azure Databricks [机密实用工具](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities)访问机密。

## <a name="monitoring-considerations"></a>监视注意事项

Azure Databricks 基于 Apache Spark，两者都使用 [log4j](https://logging.apache.org/log4j/2.x/) 作为日志记录的标准库。 除了 Apache Spark 提供的默认日志记录以外，本参考体系结构还会将日志和指标发送到 [Azure Log Analytics](/azure/log-analytics/)。

**com.microsoft.pnp.TaxiCabReader** 类将 Apache Spark 日志记录系统配置为使用 **log4j.properties** 文件中的值向 Azure Log Analytics 发送其日志。 Apache Spark 记录器消息为字符串，不过，Azure Log Analytics 要求日志消息采用 JSON 格式。 **com.microsoft.pnp.log4j.LogAnalyticsAppender** 类可将这些消息转换为 JSON：

```scala

    @Override
    protected void append(LoggingEvent loggingEvent) {
        if (this.layout == null) {
            this.setLayout(new JSONLayout());
        }

        String json = this.getLayout().format(loggingEvent);
        try {
            this.client.send(json, this.logType);
        } catch(IOException ioe) {
            LogLog.warn("Error sending LoggingEvent to Log Analytics", ioe);
        }
    }

```

**com.microsoft.pnp.TaxiCabReader** 类将处理行程和费用消息，如果其中一种消息的格式不当，则该消息无效。 在生产环境中，必须分析这些格式不当的消息来识别数据源的问题，以便可以快速解决问题并防止数据丢失。 **com.microsoft.pnp.TaxiCabReader** 类注册 Apache Spark Accumulator，该程序可以跟踪格式不当的费用记录和行程记录的数目：

```scala
    @transient val appMetrics = new AppMetrics(spark.sparkContext)
    appMetrics.registerGauge("metrics.malformedrides", AppAccumulators.getRideInstance(spark.sparkContext))
    appMetrics.registerGauge("metrics.malformedfares", AppAccumulators.getFareInstance(spark.sparkContext))
    SparkEnv.get.metricsSystem.registerSource(appMetrics)
```

Apache Spark 使用 Dropwizard 库发送指标；某些本机 Dropwizard 指标字段与 Azure Log Analytics 不兼容。 因此，本参考体系结构包含了自定义的 Dropwizard 接收器和报告器。 它会根据 Azure Log Analytics 的预期设置指标格式。 当 Apache Spark 报告指标时，也会发送格式不当的行程数据和费用数据的自定义指标。

要记录到 Azure Log Analytics 工作区的最后一个指标是 Spark 结构化流作业进度的累积进度。 此指标是使用 **com.microsoft.pnp.StreamingMetricsListener** 类中实现的自定义 StreamingQuery 侦听器记录的。 当作业运行时，此类将注册到 Apache Spark 会话：

```scala
spark.streams.addListener(new StreamingMetricsListener())
```

每当发生结构化流事件、将日志消息和指标发送到 Azure Log Analytics 工作区时，Apache Spark 运行时都会调用 StreamingMetricsListener 中的方法。 可在工作区中使用以下查询来监视应用程序：

### <a name="latency-and-throughput-for-streaming-queries"></a>流查询的延迟和吞吐量

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| project  mdc_inputRowsPerSecond_d, mdc_durationms_triggerExecution_d
| render timechart
```

### <a name="exceptions-logged-during-stream-query-execution"></a>流查询执行期间记录的异常

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| where Level contains "Error"
```

### <a name="accumulation-of-malformed-fare-and-ride-data"></a>格式不当的费用和行程数据的累积数目

```shell
SparkMetric_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart
| where name_s contains "metrics.malformedrides"

SparkMetric_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart
| where name_s contains "metrics.malformedfares"
```

### <a name="job-execution-to-trace-resiliency"></a>用于跟踪复原能力的作业执行

```shell
SparkMetric_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart
| where name_s contains "driver.DAGScheduler.job.allJobs"
```

## <a name="deploy-the-solution"></a>部署解决方案

若要部署并运行参考实现，请按 [GitHub 自述文件](https://github.com/mspnp/azure-databricks-streaming-analytics)中的步骤操作。
