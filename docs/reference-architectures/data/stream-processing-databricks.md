---
title: 使用 Azure Databricks 进行流处理
description: 使用 Azure Databricks 在 Azure 中创建端到端流处理管道
author: petertaylor9999
ms.date: 11/01/2018
ms.openlocfilehash: a7e9df57572c9b3a3b0e4f418f148449aa40b04c
ms.sourcegitcommit: 19a517a2fb70768b3edb9a7c3c37197baa61d9b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2018
ms.locfileid: "52295722"
---
# <a name="stream-processing-with-azure-databricks"></a>使用 Azure Databricks 进行流处理

本参考体系结构演示一个端到端[流处理](/azure/architecture/data-guide/big-data/real-time-processing)管道。 此类管道包括四个阶段：引入、处理、存储，以及分析和报告。 本参考体系结构中的管道从两个源引入数据，针对每个流中的相关记录执行联接，丰富结果，然后实时计算平均值。 将存储结果以供进一步分析。 [**部署此解决方案**。](#deploy-the-solution)

![](./images/stream-processing-databricks.png)

**场景**：某家出租车公司需要收集有关出租车的每次行程的数据。 对于此场景，我们假设有两个不同的设备在发送数据。 出租车的计量表发送有关每次行程的信息 &mdash; 持续时间、距离以及上车和下车地点。 有一个单独的设备接受客户的付款，并发送有关费用的数据。 为了绘制行程趋势图，该出租车公司想要实时计算每个周边区域的每英里平均小费。

## <a name="architecture"></a>体系结构

该体系结构包括以下组件。

**数据源**。 在此体系结构中，有两个数据源实时生成数据流。 第一个流包含行程信息，第二个流包含费用信息。 该参考体系结构包含一个模拟的数据生成器，该生成器读取一组静态文件，并将数据推送到事件中心。 实际应用中的数据源是安装在出租车内部的设备。

**Azure 事件中心**。 [事件中心](/azure/event-hubs/)是一个事件引入服务。 此体系结构使用两个事件中心实例，每个数据源各对应一个。 每个数据源将数据流发送到关联的事件中心。

**Azure Databricks**。 [Databricks](/azure/azure-databricks/) 是基于 Apache Spark 的分析平台，已针对 Microsoft Azure 云服务平台进行优化。 Databricks 用于关联出租车行程数据和费用数据，以及使用 Databricks 文件系统中存储的周边区域数据丰富关联的数据。

**Cosmos DB**。 Azure Databricks 作业的输出是使用 Cassandra API 写入到 [Cosmos DB](/azure/cosmos-db/) 的一系列记录。 之所以使用 Cassandra API，是因为它支持时序数据建模。

**Azure Log Analytics**。 [Azure Monitor](/azure/monitoring-and-diagnostics/) 收集的应用程序日志数据存储在 [Log Analytics 工作区](/azure/log-analytics)中。 可以使用 Log Analytics 来分析和可视化指标，以及检查日志消息以确定应用程序中的问题。

## <a name="data-ingestion"></a>数据引入

为了模拟数据源，此参考体系结构使用了[纽约市出租车数据](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797)数据集<sup>[[1]](#note1)</sup>。 此数据集包含纽约市过去四年（2010 年 &ndash; 2013 年）的出租车行程数据。 其包含两种类型的记录：行程数据和费用数据。 行程数据包括行程持续时间、行程距离以及上车和下车地点。 费用数据包括乘车费、税费和小费金额。 这两种记录类型中的通用字段包括牌照号、出租车驾照和供应商 ID。 这三个字段相结合，唯一标识了出租车和驾驶员。 数据以 CSV 格式存储。 

数据生成器是一个读取记录并将其发送到 Azure 事件中心的 .NET Core 应用程序。 该生成器发送 JSON 格式的行程数据以及 CSV 格式的费用数据。 

事件中心使用[分区](/azure/event-hubs/event-hubs-features#partitions)将数据分段。 使用者可以通过分区功能并行读取每个分区。 将数据发送到事件中心时，可以显式指定分区键。 否则，记录将以循环方式分配到分区。 

在此场景中，给定出租车的行程数据和费用数据最终会获得相同的分区 ID。 这样，在关联两个流时，Databricks 可以应用某种并行度。 行程数据分区 *n* 中的记录将与费用数据分区 *n* 中的记录进行匹配。

![](./images/stream-processing-databricks-eh.png)

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

```
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| project  mdc_inputRowsPerSecond_d, mdc_durationms_triggerExecution_d  
| render timechart
``` 
### <a name="exceptions-logged-during-stream-query-execution"></a>流查询执行期间记录的异常

```
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| where Level contains "Error" 
```

### <a name="accumulation-of-malformed-fare-and-ride-data"></a>格式不当的费用和行程数据的累积数目

```
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
```
SparkMetric_CL 
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart 
| where name_s contains "driver.DAGScheduler.job.allJobs" 
```

## <a name="deploy-the-solution"></a>部署解决方案

[GitHub](https://github.com/mspnp/reference-architectures/tree/master/data) 中提供了此参考体系结构的部署。 

### <a name="prerequisites"></a>先决条件

1. 克隆、下载[参考体系结构](https://github.com/mspnp/reference-architectures) GitHub 存储库的 zip 文件或创建其分支。

2. 安装运行数据生成器的 [Docker](https://www.docker.com/)。

3. 安装 [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)。

4. 安装 [Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html)。

5. 在命令提示符、bash 提示符或 PowerShell 提示符下，按如下所示登录到你的 Azure 帐户：
    ```
    az login
    ```
6. 安装包含以下资源的 Java IDE：
    - JDK 1.8
    - Scala SDK 2.11
    - Maven 3.5.4

### <a name="download-the-new-york-city-taxi-and-neighborhood-data-files"></a>下载纽约市出租车和周边区域数据文件

1. 在本地文件系统中的 `data/streaming_azuredatabricks` 目录下创建名为 `DataFile` 的目录。

2. 打开 Web 浏览器并导航到 https://uofi.app.box.com/v/NYCtaxidata/folder/2332219935。

3. 单击此页上的“下载”按钮，下载该年份的所有出租车数据 zip 文件。

4. 将 zip 文件解压缩到 `DataFile` 目录。

    > [!NOTE]
    > 此 zip 文件包含其他 zip 文件。 不要解压缩子 zip 文件。

    目录结构应如下所示：

    ```
    /data
        /streaming_azuredatabricks
            /DataFile
                /FOIL2013
                    trip_data_1.zip
                    trip_data_2.zip
                    trip_data_3.zip
                    ...
    ```

5. 打开 Web 浏览器并导航到 https://www.zillow.com/howto/api/neighborhood-boundaries.htm。 

6. 单击“New York Neighborhood Boundaries”以下载该文件。

7. 将浏览器的“下载”目录中的“ZillowNeighborhoods-NY.zip”文件复制到 `DataFile` 目录。

### <a name="deploy-the-azure-resources"></a>部署 Azure 资源

1. 在 shell 或 Windows 命令提示符下，运行以下命令并遵照登录提示操作：

    ```bash
    az login
    ```

2. 导航到 GitHub 存储库中的 `data/streaming_azuredatabricks` 文件夹。

    ```bash
    cd data/streaming_azuredatabricks
    ```

3. 运行以下命令以部署 Azure 资源：

    ```bash
    export resourceGroup='[Resource group name]'
    export resourceLocation='[Region]'
    export eventHubNamespace='[Event Hubs namespace name]'
    export databricksWorkspaceName='[Azure Databricks workspace name]'
    export cosmosDatabaseAccount='[Cosmos DB database name]'
    export logAnalyticsWorkspaceName='[Log Analytics workspace name]'
    export logAnalyticsWorkspaceRegion='[Log Analytics region]'

    # Create a resource group
    az group create --name $resourceGroup --location $resourceLocation

    # Deploy resources
    az group deployment create --resource-group $resourceGroup \
        --template-file ./azure/deployresources.json --parameters \
        eventHubNamespace=$eventHubNamespace \
        databricksWorkspaceName=$databricksWorkspaceName \
        cosmosDatabaseAccount=$cosmosDatabaseAccount \
        logAnalyticsWorkspaceName=$logAnalyticsWorkspaceName \
        logAnalyticsWorkspaceRegion=$logAnalyticsWorkspaceRegion
    ```

4. 完成后，部署的输出将写入到控制台。 搜索以下 JSON 的输出：

```JSON
"outputs": {
        "cosmosDb": {
          "type": "Object",
          "value": {
            "hostName": <value>,
            "secret": <value>,
            "username": <value>
          }
        },
        "eventHubs": {
          "type": "Object",
          "value": {
            "taxi-fare-eh": <value>,
            "taxi-ride-eh": <value>
          }
        },
        "logAnalytics": {
          "type": "Object",
          "value": {
            "secret": <value>,
            "workspaceId": <value>
          }
        }
},
```
这些值是在后续部分要添加到 Databricks 机密中的机密。 在这些部分添加这些机密之前，请将其妥善保存。

### <a name="add-a-cassandra-table-to-the-cosmos-db-account"></a>将 Cassandra 表添加到 Cosmos DB 帐户

1. 在 Azure 门户中，导航到在前面“部署 Azure 资源”部分创建的资源组。 单击“Azure Cosmos DB 帐户”。 使用 Cassandra API 创建一个表。

2. 在“概述”边栏选项卡中，单击“添加表”。

3. “添加表”边栏选项卡打开后，在“密钥空间名称”文本框中输入 `newyorktaxi`。 

4. 在“输入用于创建表的 CQL 命令”部分的 `newyorktaxi` 旁边的文本框中输入 `neighborhoodstats`。

5. 在下面的文本框中输入以下内容：
```
(neighborhood text, window_end timestamp, number_of_rides bigint,total_fare_amount double, primary key(neighborhood, window_end))
```
6. 在“吞吐量(1,000 - 1,000,000 RU/秒)”文本框中，输入值 `4000`。

7. 单击“确定”。

### <a name="add-the-databricks-secrets-using-the-databricks-cli"></a>使用 Databricks CLI 添加 Databricks 机密

首先输入事件中心的机密：

1. 使用先决条件部分的步骤 2 中安装的 **Azure Databricks CLI**，创建 Azure Databricks 机密范围：
    ```
    databricks secrets create-scope --scope "azure-databricks-job"
    ```
2. 添加出租车行程事件中心的机密：
    ```
    databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
    ```
    执行后，此命令会打开 vi 编辑器。 输入在执行“部署 Azure 资源”部分的步骤 4 时，**eventHubs** 输出部分显示的 **taxi-ride-eh** 值。 保存并退出 vi。

3. 添加出租车费用事件中心的机密：
    ```
    databricks secrets put --scope "azure-databricks-job" --key "taxi-fare"
    ```
    执行后，此命令会打开 vi 编辑器。 输入在执行“部署 Azure 资源”部分的步骤 4 时，**eventHubs** 输出部分显示的 **taxi-fare-eh** 值。 保存并退出 vi。

接下来，输入 Cosmos DB 的机密：

1. 打开 Azure 门户，导航到在“部署 Azure 资源”部分的步骤 3 中指定的资源组。 单击“Azure Cosmos DB 帐户”。

2. 使用 **Azure Databricks CLI** 添加 Cosmos DB 用户名的机密：
    ```
    databricks secrets put --scope azure-databricks-job --key "cassandra-username"
    ```
执行后，此命令会打开 vi 编辑器。 输入在执行“部署 Azure 资源”部分的步骤 4 时，**CosmosDb** 输出部分显示的 **username** 值。 保存并退出 vi。

3. 接下来，添加 Cosmos DB 密码的机密：
    ```
    databricks secrets put --scope azure-databricks-job --key "cassandra-password"
    ```

执行后，此命令会打开 vi 编辑器。 输入在执行“部署 Azure 资源”部分的步骤 4 时，**CosmosDb** 输出部分显示的 **secret** 值。 保存并退出 vi。

> [!NOTE]
> 如果使用[基于 Azure Key Vault 的机密范围](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes)，则必须将范围命名为 **azure-databricks-job**，并且机密名称必须与上述名称完全相同。

### <a name="add-the-zillow-neighborhoods-data-file-to-the-databricks-file-system"></a>将 Zillow Neighborhoods 数据文件添加到 Databricks 文件系统

1. 在 Databricks 文件系统中创建一个目录：
    ```bash
    dbfs mkdirs dbfs:/azure-databricks-jobs
    ```

2. 导航到 data/streaming_azuredatabricks/DataFile 并输入以下内容：
    ```bash
    dbfs cp ZillowNeighborhoods-NY.zip dbfs:/azure-databricks-jobs
    ```

### <a name="add-the-azure-log-analytics-workspace-id-and-primary-key-to-configuration-files"></a>将 Azure Log Analytics 工作区 ID 和主密钥添加到配置文件

在本部分，需要使用 Log Analytics 工作区 ID 和主密钥。 工作区 ID 是在执行“部署 Azure 资源”部分的步骤 4 时，**logAnalytics** 输出部分显示的 **workspaceId** 值。 主密钥是输出部分显示的 **secret** 值。 

1. 若要配置 log4j 日志记录，请打开 data\streaming_azuredatabricks\azure\AzureDataBricksJob\src\main\resources\com\microsoft\pnp\azuredatabricksjob\log4j.properties。 编辑以下两个值：
    ```
    log4j.appender.A1.workspaceId=<Log Analytics workspace ID>
    log4j.appender.A1.secret=<Log Analytics primary key>
    ```

2. 若要配置自定义日志记录，请打开 data\streaming_azuredatabricks\azure\azure-databricks-monitoring\scripts\metrics.properties。 编辑以下两个值：
    ``` 
    *.sink.loganalytics.workspaceId=<Log Analytics workspace ID>
    *.sink.loganalytics.secret=<Log Analytics primary key>
    ```

### <a name="build-the-jar-files-for-the-databricks-job-and-databricks-monitoring"></a>生成用于 Databricks 作业和 Databricks 监视的 .jar 文件

1. 使用 Java IDE 导入 **data/streaming_azuredatabricks** 目录所在的根目录中名为 **pom.xml** 的 Maven 项目文件。 

2. 执行全新生成。 此项生成的输出是名为 **azure-databricks-job-1.0-SNAPSHOT.jar** 和 **azure-databricks-monitoring-0.9.jar** 的文件。 

### <a name="configure-custom-logging-for-the-databricks-job"></a>为 Databricks 作业配置自定义日志记录

1. 在 **Databricks CLI** 中输入以下命令，将 **azure-databricks-monitoring-0.9.jar** 文件复制到 Databricks 文件系统：
    ```
    databricks fs cp --overwrite azure-databricks-monitoring-0.9.jar dbfs:/azure-databricks-job/azure-databricks-monitoring-0.9.jar
    ```

2. 输入以下命令，将 data\streaming_azuredatabricks\azure\azure-databricks-monitoring\scripts\metrics.properties 中的自定义日志记录属性复制到 Databricks 文件系统：
    ```
    databricks fs cp --overwrite metrics.properties dbfs:/azure-databricks-job/metrics.properties
    ```

3. 如果尚未确定 Databricks 群集的名称，现在请选择一个名称。 稍后将在群集的 Databricks 文件系统路径中输入以下名称。 输入以下命令，将 data\streaming_azuredatabricks\azure\azure-databricks-monitoring\scripts\spark.metrics 中的初始化脚本复制到 Databricks 文件系统：
    ```
    databricks fs cp --overwrite spark-metrics.sh dbfs:/databricks/init/<cluster-name>/spark-metrics.sh
    ```

### <a name="create-a-databricks-cluster"></a>创建 Databricks 群集

1. 在 Databricks 工作区中，依次单击“群集”、“创建群集”。 输入在前面“为 Databricks 作业配置自定义日志记录”部分的步骤 3 中创建的群集名称。 

2. 选择一种**标准**群集模式。

3. 将“Databricks 运行时版本”设置为“4.3 (包括 Apache Spark 2.3.1、Scala 2.11)”。

4. 将“Python 版本”设置为“2”。

5. 将“驱动程序类型”设置为“与辅助角色相同”。

6. 将“辅助角色类型”设置为“Standard_DS3_v2”。

7. 将“最小辅助角色数目”设置为“2”。

8. 取消选择“启用自动缩放”。 

9. 在“自动终止”对话框下面，单击“初始化脚本”。 

10. 输入 **dbfs:/databricks/init/<cluster-name>/spark-metrics.sh**（请将 <cluster-name> 替换为步骤 1 中创建的群集名称）。

11. 单击“添加”按钮。

12. 单击“创建群集”按钮。

### <a name="create-a-databricks-job"></a>创建 Databricks 作业

1. 在 Databricks 工作区中，依次单击“作业”、“创建作业”。

2. 输入作业名称。

3. 单击“设置 jar”，此时会打开“上传要运行的 JAR”对话框。

4. 将“生成 Databricks 作业的 .jar”部分创建的 **azure-databricks-job-1.0-SNAPSHOT.jar** 文件拖放到“在此处放置要上传的 JAR”框中。

5. 在“Main 类”字段中输入 **com.microsoft.pnp.TaxiCabReader**。

6. 在“参数”字段中输入以下内容：
    ```
    -n jar:file:/dbfs/azure-databricks-jobs/ZillowNeighborhoods-NY.zip!/ZillowNeighborhoods-NY.shp --taxi-ride-consumer-group taxi-ride-eh-cg --taxi-fare-consumer-group taxi-fare-eh-cg --window-interval "1 minute" --cassandra-host <Cosmos DB Cassandra host name from above> 
    ``` 

7. 遵循以下步骤安装依赖库：
    
    1. 在 Databricks 用户界面中，单击“开始”按钮。
    
    2. 在“用户”下拉列表中，单击你的用户帐户名打开帐户工作区设置。
    
    3. 单击帐户名旁边的下拉箭头，单击“创建”，然后单击“库”打开“新建库”对话框。
    
    4. 在“源”下拉控件中，选择“Maven 坐标”。
    
    5. 在“安装 Maven 项目”标题下的“坐标”文本框中输入 `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.5`。 
    
    6. 单击“创建库”打开“项目”窗口。
    
    7. 在“正在运行的群集的状态”下，选中“自动附加到所有群集”复选框。
    
    8. 针对 `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` Maven 坐标重复步骤 1 - 7。
    
    9. 针对 `org.geotools:gt-shapefile:19.2` Maven 坐标重复步骤 1 - 6。
    
    10. 单击“高级选项”。
    
    11. 在“存储库”文本框中输入 `http://download.osgeo.org/webdav/geotools/`。 
    
    12. 单击“创建库”打开“项目”窗口。 
    
    13. 在“正在运行的群集的状态”下，选中“自动附加到所有群集”复选框。

8. 将步骤 7 中添加的依赖库添加到完成步骤 6 时创建的作业：
    1. 在 Azure Databricks 工作区中，单击“作业”。

    2. 单击“创建 Databricks 作业”部分的步骤 2 中创建的作业名称。 
    
    3. 在“依赖库”部分的旁边，单击“添加”打开“添加依赖库”对话框。 
    
    4. 在“以下位置的库”下，选择“工作区”。
    
    5. 依次单击“用户”、你的用户名、`azure-eventhubs-spark_2.11:2.3.5`。 
    
    6. 单击“确定”。
    
    7. 针对 `spark-cassandra-connector_2.11:2.3.1` 和 `gt-shapefile:19.2` 重复步骤 1 - 6。

9. 在“群集:”旁边，单击“编辑”。 此时会打开“配置群集”对话框。 在“群集类型”下拉列表中，选择“现有群集”。 在“选择群集”下拉列表中，选择在“创建 Databricks 群集”部分创建的群集。 单击“确认”。

10. 单击“立即运行”。

### <a name="run-the-data-generator"></a>运行数据生成器

1. 导航到 GitHub 存储库中的 `data/streaming_azuredatabricks/onprem` 目录。

2. 按如下所示更新 **main.env** 文件中的值：

    ```
    RIDE_EVENT_HUB=[Connection string for the taxi-ride event hub]
    FARE_EVENT_HUB=[Connection string for the taxi-fare event hub]
    RIDE_DATA_FILE_PATH=/DataFile/FOIL2013
    MINUTES_TO_LEAD=0
    PUSH_RIDE_DATA_FIRST=false
    ```
    taxi-ride 事件中心的连接字符串是在执行“部署 Azure 资源”部分的步骤 4 时，**eventHubs** 输出部分显示的 **taxi-ride-eh** 值。 taxi-fare 事件中心的连接字符串是在执行“部署 Azure 资源”部分的步骤 4 时，**eventHubs** 输出部分显示的 **taxi-fare-eh** 值。

3. 运行以下命令以生成 Docker 映像。

    ```bash
    docker build --no-cache -t dataloader .
    ```

4. 导航回到父目录 `data/stream_azuredatabricks`。

    ```bash
    cd ..
    ```

5. 运行以下命令以运行 Docker 映像。

    ```bash
    docker run -v `pwd`/DataFile:/DataFile --env-file=onprem/main.env dataloader:latest
    ```

输出应如下所示：

```
Created 10000 records for TaxiFare
Created 10000 records for TaxiRide
Created 20000 records for TaxiFare
Created 20000 records for TaxiRide
Created 30000 records for TaxiFare
...
```

若要验证 Databricks 作业是否在正常运行，请打开 Azure 门户并导航到 Cosmos DB 数据库。 打开“数据资源管理器”边栏选项卡，并检查“出租车记录”表中的数据。 

[1] <span id="note1">Donovan, Brian；Work, Dan (2016)：纽约市出租车行程数据 (2010-2013)。 伊利诺伊大学厄巴纳-香槟分校。 https://doi.org/10.13012/J8PN93H8
