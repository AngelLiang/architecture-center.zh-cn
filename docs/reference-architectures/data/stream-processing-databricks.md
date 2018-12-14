---
title: 使用 Azure Databricks 进行流处理
titleSuffix: Azure Reference Architectures
description: 使用 Azure Databricks 在 Azure 中创建端到端流处理管道。
author: petertaylor9999
ms.date: 11/30/2018
ms.custom: seodec18
ms.openlocfilehash: ceea89b554e11619823b2065a7f03919527b21fa
ms.sourcegitcommit: 8d951fd7e9534054b160be48a1881ae0857561ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53329409"
---
# <a name="create-a-stream-processing-pipeline-with-azure-databricks"></a><span data-ttu-id="25f9a-103">使用 Azure Databricks 创建流处理管道</span><span class="sxs-lookup"><span data-stu-id="25f9a-103">Create a stream processing pipeline with Azure Databricks</span></span>

<span data-ttu-id="25f9a-104">本参考体系结构演示一个端到端[流处理](/azure/architecture/data-guide/big-data/real-time-processing)管道。</span><span class="sxs-lookup"><span data-stu-id="25f9a-104">This reference architecture shows an end-to-end [stream processing](/azure/architecture/data-guide/big-data/real-time-processing) pipeline.</span></span> <span data-ttu-id="25f9a-105">此类管道包括四个阶段：引入、处理、存储，以及分析和报告。</span><span class="sxs-lookup"><span data-stu-id="25f9a-105">This type of pipeline has four stages: ingest, process, store, and analysis and reporting.</span></span> <span data-ttu-id="25f9a-106">本参考体系结构中的管道从两个源引入数据，针对每个流中的相关记录执行联接，丰富结果，然后实时计算平均值。</span><span class="sxs-lookup"><span data-stu-id="25f9a-106">For this reference architecture, the pipeline ingests data from two sources, performs a join on related records from each stream, enriches the result, and calculates an average in real time.</span></span> <span data-ttu-id="25f9a-107">将存储结果以供进一步分析。</span><span class="sxs-lookup"><span data-stu-id="25f9a-107">The results are stored for further analysis.</span></span> <span data-ttu-id="25f9a-108">[**部署此解决方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="25f9a-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

![使用 Azure Databricks 进行的流处理的参考体系结构](./images/stream-processing-databricks.png)

<span data-ttu-id="25f9a-110">**场景**：某家出租车公司需要收集有关出租车的每次行程的数据。</span><span class="sxs-lookup"><span data-stu-id="25f9a-110">**Scenario**: A taxi company collects data about each taxi trip.</span></span> <span data-ttu-id="25f9a-111">对于此场景，我们假设有两个不同的设备在发送数据。</span><span class="sxs-lookup"><span data-stu-id="25f9a-111">For this scenario, we assume there are two separate devices sending data.</span></span> <span data-ttu-id="25f9a-112">出租车的计量表发送有关每次行程的信息 &mdash; 持续时间、距离以及上车和下车地点。</span><span class="sxs-lookup"><span data-stu-id="25f9a-112">The taxi has a meter that sends information about each ride &mdash; the duration, distance, and pickup and dropoff locations.</span></span> <span data-ttu-id="25f9a-113">有一个单独的设备接受客户的付款，并发送有关费用的数据。</span><span class="sxs-lookup"><span data-stu-id="25f9a-113">A separate device accepts payments from customers and sends data about fares.</span></span> <span data-ttu-id="25f9a-114">为了绘制行程趋势图，该出租车公司想要实时计算每个周边区域的每英里平均小费。</span><span class="sxs-lookup"><span data-stu-id="25f9a-114">To spot ridership trends, the taxi company wants to calculate the average tip per mile driven, in real time, for each neighborhood.</span></span>

## <a name="architecture"></a><span data-ttu-id="25f9a-115">体系结构</span><span class="sxs-lookup"><span data-stu-id="25f9a-115">Architecture</span></span>

<span data-ttu-id="25f9a-116">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="25f9a-116">The architecture consists of the following components.</span></span>

<span data-ttu-id="25f9a-117">**数据源**。</span><span class="sxs-lookup"><span data-stu-id="25f9a-117">**Data sources**.</span></span> <span data-ttu-id="25f9a-118">在此体系结构中，有两个数据源实时生成数据流。</span><span class="sxs-lookup"><span data-stu-id="25f9a-118">In this architecture, there are two data sources that generate data streams in real time.</span></span> <span data-ttu-id="25f9a-119">第一个流包含行程信息，第二个流包含费用信息。</span><span class="sxs-lookup"><span data-stu-id="25f9a-119">The first stream contains ride information, and the second contains fare information.</span></span> <span data-ttu-id="25f9a-120">该参考体系结构包含一个模拟的数据生成器，该生成器读取一组静态文件，并将数据推送到事件中心。</span><span class="sxs-lookup"><span data-stu-id="25f9a-120">The reference architecture includes a simulated data generator that reads from a set of static files and pushes the data to Event Hubs.</span></span> <span data-ttu-id="25f9a-121">实际应用中的数据源是安装在出租车内部的设备。</span><span class="sxs-lookup"><span data-stu-id="25f9a-121">The data sources in a real application would be devices installed in the taxi cabs.</span></span>

<span data-ttu-id="25f9a-122">**Azure 事件中心**。</span><span class="sxs-lookup"><span data-stu-id="25f9a-122">**Azure Event Hubs**.</span></span> <span data-ttu-id="25f9a-123">[事件中心](/azure/event-hubs/)是一个事件引入服务。</span><span class="sxs-lookup"><span data-stu-id="25f9a-123">[Event Hubs](/azure/event-hubs/) is an event ingestion service.</span></span> <span data-ttu-id="25f9a-124">此体系结构使用两个事件中心实例，每个数据源各对应一个。</span><span class="sxs-lookup"><span data-stu-id="25f9a-124">This architecture uses two event hub instances, one for each data source.</span></span> <span data-ttu-id="25f9a-125">每个数据源将数据流发送到关联的事件中心。</span><span class="sxs-lookup"><span data-stu-id="25f9a-125">Each data source sends a stream of data to the associated event hub.</span></span>

<span data-ttu-id="25f9a-126">**Azure Databricks**。</span><span class="sxs-lookup"><span data-stu-id="25f9a-126">**Azure Databricks**.</span></span> <span data-ttu-id="25f9a-127">[Databricks](/azure/azure-databricks/) 是基于 Apache Spark 的分析平台，已针对 Microsoft Azure 云服务平台进行优化。</span><span class="sxs-lookup"><span data-stu-id="25f9a-127">[Databricks](/azure/azure-databricks/) is an Apache Spark-based analytics platform optimized for the Microsoft Azure cloud services platform.</span></span> <span data-ttu-id="25f9a-128">Databricks 用于关联出租车行程数据和费用数据，以及使用 Databricks 文件系统中存储的周边区域数据丰富关联的数据。</span><span class="sxs-lookup"><span data-stu-id="25f9a-128">Databricks is used to correlate of the taxi ride and fare data, and also to enrich the correlated data with neighborhood data stored in the Databricks file system.</span></span>

<span data-ttu-id="25f9a-129">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="25f9a-129">**Cosmos DB**.</span></span> <span data-ttu-id="25f9a-130">Azure Databricks 作业的输出是使用 Cassandra API 写入到 [Cosmos DB](/azure/cosmos-db/) 的一系列记录。</span><span class="sxs-lookup"><span data-stu-id="25f9a-130">The output from Azure Databricks job is a series of records, which are written to [Cosmos DB](/azure/cosmos-db/) using the Cassandra API.</span></span> <span data-ttu-id="25f9a-131">之所以使用 Cassandra API，是因为它支持时序数据建模。</span><span class="sxs-lookup"><span data-stu-id="25f9a-131">The Cassandra API is used because it supports time series data modeling.</span></span>

<span data-ttu-id="25f9a-132">**Azure Log Analytics**。</span><span class="sxs-lookup"><span data-stu-id="25f9a-132">**Azure Log Analytics**.</span></span> <span data-ttu-id="25f9a-133">[Azure Monitor](/azure/monitoring-and-diagnostics/) 收集的应用程序日志数据存储在 [Log Analytics 工作区](/azure/log-analytics)中。</span><span class="sxs-lookup"><span data-stu-id="25f9a-133">Application log data collected by [Azure Monitor](/azure/monitoring-and-diagnostics/) is stored in a [Log Analytics workspace](/azure/log-analytics).</span></span> <span data-ttu-id="25f9a-134">可以使用 Log Analytics 来分析和可视化指标，以及检查日志消息以确定应用程序中的问题。</span><span class="sxs-lookup"><span data-stu-id="25f9a-134">Log Analytics queries can be used to analyze and visualize metrics and inspect log messages to identify issues within the application.</span></span>

## <a name="data-ingestion"></a><span data-ttu-id="25f9a-135">数据引入</span><span class="sxs-lookup"><span data-stu-id="25f9a-135">Data ingestion</span></span>

<span data-ttu-id="25f9a-136">为了模拟数据源，此参考体系结构使用了[纽约市出租车数据](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797)数据集<sup>[[1]](#note1)</sup>。</span><span class="sxs-lookup"><span data-stu-id="25f9a-136">To simulate a data source, this reference architecture uses the [New York City Taxi Data](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797) dataset<sup>[[1]](#note1)</sup>.</span></span> <span data-ttu-id="25f9a-137">此数据集包含纽约市过去四年（2010 年 &ndash; 2013 年）的出租车行程数据。</span><span class="sxs-lookup"><span data-stu-id="25f9a-137">This dataset contains data about taxi trips in New York City over a four-year period (2010 &ndash; 2013).</span></span> <span data-ttu-id="25f9a-138">其包含两种类型的记录：行程数据和费用数据。</span><span class="sxs-lookup"><span data-stu-id="25f9a-138">It contains two types of record: Ride data and fare data.</span></span> <span data-ttu-id="25f9a-139">行程数据包括行程持续时间、行程距离以及上车和下车地点。</span><span class="sxs-lookup"><span data-stu-id="25f9a-139">Ride data includes trip duration, trip distance, and pickup and dropoff location.</span></span> <span data-ttu-id="25f9a-140">费用数据包括乘车费、税费和小费金额。</span><span class="sxs-lookup"><span data-stu-id="25f9a-140">Fare data includes fare, tax, and tip amounts.</span></span> <span data-ttu-id="25f9a-141">这两种记录类型中的通用字段包括牌照号、出租车驾照和供应商 ID。</span><span class="sxs-lookup"><span data-stu-id="25f9a-141">Common fields in both record types include medallion number, hack license, and vendor ID.</span></span> <span data-ttu-id="25f9a-142">这三个字段相结合，唯一标识了出租车和驾驶员。</span><span class="sxs-lookup"><span data-stu-id="25f9a-142">Together these three fields uniquely identify a taxi plus a driver.</span></span> <span data-ttu-id="25f9a-143">数据以 CSV 格式存储。</span><span class="sxs-lookup"><span data-stu-id="25f9a-143">The data is stored in CSV format.</span></span>

> <span data-ttu-id="25f9a-144">[1] <span id="note1">Donovan, Brian；Work, Dan (2016)：纽约市出租车行程数据 (2010-2013)。</span><span class="sxs-lookup"><span data-stu-id="25f9a-144">[1] <span id="note1">Donovan, Brian; Work, Dan (2016): New York City Taxi Trip Data (2010-2013).</span></span> <span data-ttu-id="25f9a-145">伊利诺伊大学厄巴纳-香槟分校。</span><span class="sxs-lookup"><span data-stu-id="25f9a-145">University of Illinois at Urbana-Champaign.</span></span> <span data-ttu-id="25f9a-146">https://doi.org/10.13012/J8PN93H8</span><span class="sxs-lookup"><span data-stu-id="25f9a-146">https://doi.org/10.13012/J8PN93H8</span></span>

<span data-ttu-id="25f9a-147">数据生成器是一个读取记录并将其发送到 Azure 事件中心的 .NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="25f9a-147">The data generator is a .NET Core application that reads the records and sends them to Azure Event Hubs.</span></span> <span data-ttu-id="25f9a-148">该生成器发送 JSON 格式的行程数据以及 CSV 格式的费用数据。</span><span class="sxs-lookup"><span data-stu-id="25f9a-148">The generator sends ride data in JSON format and fare data in CSV format.</span></span>

<span data-ttu-id="25f9a-149">事件中心使用[分区](/azure/event-hubs/event-hubs-features#partitions)将数据分段。</span><span class="sxs-lookup"><span data-stu-id="25f9a-149">Event Hubs uses [partitions](/azure/event-hubs/event-hubs-features#partitions) to segment the data.</span></span> <span data-ttu-id="25f9a-150">使用者可以通过分区功能并行读取每个分区。</span><span class="sxs-lookup"><span data-stu-id="25f9a-150">Partitions allow a consumer to read each partition in parallel.</span></span> <span data-ttu-id="25f9a-151">将数据发送到事件中心时，可以显式指定分区键。</span><span class="sxs-lookup"><span data-stu-id="25f9a-151">When you send data to Event Hubs, you can specify the partition key explicitly.</span></span> <span data-ttu-id="25f9a-152">否则，记录将以循环方式分配到分区。</span><span class="sxs-lookup"><span data-stu-id="25f9a-152">Otherwise, records are assigned to partitions in round-robin fashion.</span></span>

<span data-ttu-id="25f9a-153">在此场景中，给定出租车的行程数据和费用数据最终会获得相同的分区 ID。</span><span class="sxs-lookup"><span data-stu-id="25f9a-153">In this scenario, ride data and fare data should end up with the same partition ID for a given taxi cab.</span></span> <span data-ttu-id="25f9a-154">这样，在关联两个流时，Databricks 可以应用某种并行度。</span><span class="sxs-lookup"><span data-stu-id="25f9a-154">This enables Databricks to apply a degree of parallelism when it correlates the two streams.</span></span> <span data-ttu-id="25f9a-155">行程数据分区 *n* 中的记录将与费用数据分区 *n* 中的记录进行匹配。</span><span class="sxs-lookup"><span data-stu-id="25f9a-155">A record in partition *n* of the ride data will match a record in partition *n* of the fare data.</span></span>

![使用 Azure Databricks 和事件中心进行的流处理的图](./images/stream-processing-databricks-eh.png)

<span data-ttu-id="25f9a-157">在数据生成器中，这两种记录类型的通用数据模型具有 `PartitionKey` 属性，该属性是 `Medallion`、`HackLicense` 和 `VendorId` 的串联形式。</span><span class="sxs-lookup"><span data-stu-id="25f9a-157">In the data generator, the common data model for both record types has a `PartitionKey` property that is the concatenation of `Medallion`, `HackLicense`, and `VendorId`.</span></span>

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

<span data-ttu-id="25f9a-158">将数据发送到事件中心时，会使用此属性来提供显式分区键：</span><span class="sxs-lookup"><span data-stu-id="25f9a-158">This property is used to provide an explicit partition key when sending to Event Hubs:</span></span>

```csharp
using (var client = pool.GetObject())
{
    return client.Value.SendAsync(new EventData(Encoding.UTF8.GetBytes(
        t.GetData(dataFormat))), t.PartitionKey);
}
```

### <a name="event-hubs"></a><span data-ttu-id="25f9a-159">事件中心</span><span class="sxs-lookup"><span data-stu-id="25f9a-159">Event Hubs</span></span>

<span data-ttu-id="25f9a-160">事件中心的吞吐量容量以[吞吐量单位](/azure/event-hubs/event-hubs-features#throughput-units)来度量。</span><span class="sxs-lookup"><span data-stu-id="25f9a-160">The throughput capacity of Event Hubs is measured in [throughput units](/azure/event-hubs/event-hubs-features#throughput-units).</span></span> <span data-ttu-id="25f9a-161">可以通过启用[自动扩充](/azure/event-hubs/event-hubs-auto-inflate)来自动缩放事件中心。自动扩充可以根据流量，最高将吞吐量单位自动扩展到配置的上限。</span><span class="sxs-lookup"><span data-stu-id="25f9a-161">You can autoscale an event hub by enabling [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate), which automatically scales the throughput units based on traffic, up to a configured maximum.</span></span>

## <a name="stream-processing"></a><span data-ttu-id="25f9a-162">流处理</span><span class="sxs-lookup"><span data-stu-id="25f9a-162">Stream processing</span></span>

<span data-ttu-id="25f9a-163">在 Azure Databricks 中，数据处理由某个作业执行。</span><span class="sxs-lookup"><span data-stu-id="25f9a-163">In Azure Databricks, data processing is performed by a job.</span></span> <span data-ttu-id="25f9a-164">该作业分配到群集并在其上运行。</span><span class="sxs-lookup"><span data-stu-id="25f9a-164">The job is assigned to and runs on a cluster.</span></span> <span data-ttu-id="25f9a-165">作业可以是以 Java 编写的自定义代码，或 Spark [笔记本](https://docs.databricks.com/user-guide/notebooks/index.html)。</span><span class="sxs-lookup"><span data-stu-id="25f9a-165">The job can either be custom code written in Java, or a Spark [notebook](https://docs.databricks.com/user-guide/notebooks/index.html).</span></span>

<span data-ttu-id="25f9a-166">在本参考体系结构中，作业是一个 Java 存档，其中包含以 Java 和 Scala 编写的类。</span><span class="sxs-lookup"><span data-stu-id="25f9a-166">In this reference architecture, the job is a Java archive with classes written in both Java and Scala.</span></span> <span data-ttu-id="25f9a-167">为 Databricks 作业指定 Java 存档时，将指定该类，让 Databricks 群集执行。</span><span class="sxs-lookup"><span data-stu-id="25f9a-167">When specifying the Java archive for a Databricks job, the class is specified for execution by the Databricks cluster.</span></span> <span data-ttu-id="25f9a-168">此处，**com.microsoft.pnp.TaxiCabReader** 类的 **main** 方法包含数据处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="25f9a-168">Here, the **main** method of the **com.microsoft.pnp.TaxiCabReader** class contains the data processing logic.</span></span>

### <a name="reading-the-stream-from-the-two-event-hub-instances"></a><span data-ttu-id="25f9a-169">从两个事件中心实例读取流</span><span class="sxs-lookup"><span data-stu-id="25f9a-169">Reading the stream from the two event hub instances</span></span>

<span data-ttu-id="25f9a-170">数据处理逻辑使用 [Spark 结构化流](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html)从两个 Azure 事件中心实例读取数据：</span><span class="sxs-lookup"><span data-stu-id="25f9a-170">The data processing logic uses [Spark structured streaming](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html) to read from the two Azure event hub instances:</span></span>

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

### <a name="enriching-the-data-with-the-neighborhood-information"></a><span data-ttu-id="25f9a-171">使用周边区域信息丰富数据</span><span class="sxs-lookup"><span data-stu-id="25f9a-171">Enriching the data with the neighborhood information</span></span>

<span data-ttu-id="25f9a-172">行程数据包括上车和下车地点的纬度与经度坐标。</span><span class="sxs-lookup"><span data-stu-id="25f9a-172">The ride data includes the latitude and longitude coordinates of the pick up and drop off locations.</span></span> <span data-ttu-id="25f9a-173">尽管这些坐标很有用，但不可轻松用于分析。</span><span class="sxs-lookup"><span data-stu-id="25f9a-173">While these coordinates are useful, they are not easily consumed for analysis.</span></span> <span data-ttu-id="25f9a-174">因此，已使用从[形状文件](https://en.wikipedia.org/wiki/Shapefile)读取的周边区域数据丰富了这些数据。</span><span class="sxs-lookup"><span data-stu-id="25f9a-174">Therefore, this data is enriched with neighborhood data that is read from a [shapefile](https://en.wikipedia.org/wiki/Shapefile).</span></span>

<span data-ttu-id="25f9a-175">形状文件采用二进制格式且不可轻松分析，但 [GeoTools](http://geotools.org/) 库提供了相应的工具用于处理使用形状文件格式的地理空间数据。</span><span class="sxs-lookup"><span data-stu-id="25f9a-175">The shapefile format is binary and not easily parsed, but the [GeoTools](http://geotools.org/) library provides tools for geospatial data that use the shapefile format.</span></span> <span data-ttu-id="25f9a-176">**com.microsoft.pnp.GeoFinder** 类中使用了此库来根据上车和下车地点坐标确定周边区域名称。</span><span class="sxs-lookup"><span data-stu-id="25f9a-176">This library is used in the **com.microsoft.pnp.GeoFinder** class to determine the neighborhood name based on the pick up and drop off coordinates.</span></span>

```scala
val neighborhoodFinder = (lon: Double, lat: Double) => {
      NeighborhoodFinder.getNeighborhood(lon, lat).get()
    }
```

### <a name="joining-the-ride-and-fare-data"></a><span data-ttu-id="25f9a-177">联接行程数据和费用数据</span><span class="sxs-lookup"><span data-stu-id="25f9a-177">Joining the ride and fare data</span></span>

<span data-ttu-id="25f9a-178">首先转换行程数据和费用数据：</span><span class="sxs-lookup"><span data-stu-id="25f9a-178">First the ride and fare data is transformed:</span></span>

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

<span data-ttu-id="25f9a-179">然后将行程数据与费用数据相联接：</span><span class="sxs-lookup"><span data-stu-id="25f9a-179">And then the ride data is joined with the fare data:</span></span>

```scala
val mergedTaxiTrip = rides.join(fares, Seq("medallion", "hackLicense", "vendorId", "pickupTime"))
```

### <a name="processing-the-data-and-inserting-into-cosmos-db"></a><span data-ttu-id="25f9a-180">处理数据并将其插入到 Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="25f9a-180">Processing the data and inserting into Cosmos DB</span></span>

<span data-ttu-id="25f9a-181">按给定的时间间隔计算每个周边区域的平均费用金额：</span><span class="sxs-lookup"><span data-stu-id="25f9a-181">The average fare amount for each neighborhood is calculated for a given time interval:</span></span>

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

<span data-ttu-id="25f9a-182">然后将此金额插入到 Cosmos DB：</span><span class="sxs-lookup"><span data-stu-id="25f9a-182">Which is then inserted into Cosmos DB:</span></span>

```scala
maxAvgFarePerNeighborhood
      .writeStream
      .queryName("maxAvgFarePerNeighborhood_cassandra_insert")
      .outputMode(OutputMode.Append())
      .foreach(new CassandraSinkForeach(connector))
      .start()
      .awaitTermination()
```

## <a name="security-considerations"></a><span data-ttu-id="25f9a-183">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="25f9a-183">Security considerations</span></span>

<span data-ttu-id="25f9a-184">使用[管理员控制台](https://docs.databricks.com/administration-guide/admin-settings/index.html)控制对 Azure 数据库工作区的访问。</span><span class="sxs-lookup"><span data-stu-id="25f9a-184">Access to the Azure Database workspace is controlled using the [administrator console](https://docs.databricks.com/administration-guide/admin-settings/index.html).</span></span> <span data-ttu-id="25f9a-185">管理员控制台包含添加用户、管理用户权限和设置单一登录的功能。</span><span class="sxs-lookup"><span data-stu-id="25f9a-185">The administrator console includes functionality to add users, manage user permissions, and set up single sign-on.</span></span> <span data-ttu-id="25f9a-186">还可以通过管理员控制台设置工作区、群集、作业和表的访问控制。</span><span class="sxs-lookup"><span data-stu-id="25f9a-186">Access control for workspaces, clusters, jobs, and tables can also be set through the administrator console.</span></span>

### <a name="managing-secrets"></a><span data-ttu-id="25f9a-187">管理机密</span><span class="sxs-lookup"><span data-stu-id="25f9a-187">Managing secrets</span></span>

<span data-ttu-id="25f9a-188">Azure Databricks 包含一个用于存储机密（包括连接字符串、访问密钥、用户名和密码）的[机密存储](https://docs.azuredatabricks.net/user-guide/secrets/index.html)。</span><span class="sxs-lookup"><span data-stu-id="25f9a-188">Azure Databricks includes a [secret store](https://docs.azuredatabricks.net/user-guide/secrets/index.html) that is used to store secrets, including connection strings, access keys, user names, and passwords.</span></span> <span data-ttu-id="25f9a-189">Azure Databricks 机密存储中的机密按**范围**分区：</span><span class="sxs-lookup"><span data-stu-id="25f9a-189">Secrets within the Azure Databricks secret store are partitioned by **scopes**:</span></span>

```bash
databricks secrets create-scope --scope "azure-databricks-job"
```

<span data-ttu-id="25f9a-190">机密是在范围级别添加的：</span><span class="sxs-lookup"><span data-stu-id="25f9a-190">Secrets are added at the scope level:</span></span>

```bash
databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
```

> [!NOTE]
> <span data-ttu-id="25f9a-191">可以使用基于 Azure Key Vault 的范围来取代本机 Azure Databricks 范围。</span><span class="sxs-lookup"><span data-stu-id="25f9a-191">An Azure Key Vault-backed scope can be used instead of the native Azure Databricks scope.</span></span> <span data-ttu-id="25f9a-192">有关详细信息，请参阅[基于 Azure Key Vault 的范围](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes)。</span><span class="sxs-lookup"><span data-stu-id="25f9a-192">To learn more, see [Azure Key Vault-backed scopes](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes).</span></span>

<span data-ttu-id="25f9a-193">在代码中，可以通过 Azure Databricks [机密实用工具](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities)访问机密。</span><span class="sxs-lookup"><span data-stu-id="25f9a-193">In code, secrets are accessed via the Azure Databricks [secrets utilities](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities).</span></span>

## <a name="monitoring-considerations"></a><span data-ttu-id="25f9a-194">监视注意事项</span><span class="sxs-lookup"><span data-stu-id="25f9a-194">Monitoring considerations</span></span>

<span data-ttu-id="25f9a-195">Azure Databricks 基于 Apache Spark，两者都使用 [log4j](https://logging.apache.org/log4j/2.x/) 作为日志记录的标准库。</span><span class="sxs-lookup"><span data-stu-id="25f9a-195">Azure Databricks is based on Apache Spark, and both use [log4j](https://logging.apache.org/log4j/2.x/) as the standard library for logging.</span></span> <span data-ttu-id="25f9a-196">除了 Apache Spark 提供的默认日志记录以外，本参考体系结构还会将日志和指标发送到 [Azure Log Analytics](/azure/log-analytics/)。</span><span class="sxs-lookup"><span data-stu-id="25f9a-196">In addition to the default logging provided by Apache Spark, this reference architecture sends logs and metrics to [Azure Log Analytics](/azure/log-analytics/).</span></span>

<span data-ttu-id="25f9a-197">**com.microsoft.pnp.TaxiCabReader** 类将 Apache Spark 日志记录系统配置为使用 **log4j.properties** 文件中的值向 Azure Log Analytics 发送其日志。</span><span class="sxs-lookup"><span data-stu-id="25f9a-197">The **com.microsoft.pnp.TaxiCabReader** class configures the Apache Spark logging system to send its logs to Azure Log Analytics using the values in the **log4j.properties** file.</span></span> <span data-ttu-id="25f9a-198">Apache Spark 记录器消息为字符串，不过，Azure Log Analytics 要求日志消息采用 JSON 格式。</span><span class="sxs-lookup"><span data-stu-id="25f9a-198">While the Apache Spark logger messages are strings, Azure Log Analytics requires log messages to be formatted as JSON.</span></span> <span data-ttu-id="25f9a-199">**com.microsoft.pnp.log4j.LogAnalyticsAppender** 类可将这些消息转换为 JSON：</span><span class="sxs-lookup"><span data-stu-id="25f9a-199">The **com.microsoft.pnp.log4j.LogAnalyticsAppender** class transforms these messages to JSON:</span></span>

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

<span data-ttu-id="25f9a-200">**com.microsoft.pnp.TaxiCabReader** 类将处理行程和费用消息，如果其中一种消息的格式不当，则该消息无效。</span><span class="sxs-lookup"><span data-stu-id="25f9a-200">As the **com.microsoft.pnp.TaxiCabReader** class processes ride and fare messages, it's possible that either one may be malformed and therefore not valid.</span></span> <span data-ttu-id="25f9a-201">在生产环境中，必须分析这些格式不当的消息来识别数据源的问题，以便可以快速解决问题并防止数据丢失。</span><span class="sxs-lookup"><span data-stu-id="25f9a-201">In a production environment, it's important to analyze these malformed messages to identify a problem with the data sources so it can be fixed quickly to prevent data loss.</span></span> <span data-ttu-id="25f9a-202">**com.microsoft.pnp.TaxiCabReader** 类注册 Apache Spark Accumulator，该程序可以跟踪格式不当的费用记录和行程记录的数目：</span><span class="sxs-lookup"><span data-stu-id="25f9a-202">The **com.microsoft.pnp.TaxiCabReader** class registers an Apache Spark Accumulator that keeps track of the number of malformed fare and ride records:</span></span>

```scala
    @transient val appMetrics = new AppMetrics(spark.sparkContext)
    appMetrics.registerGauge("metrics.malformedrides", AppAccumulators.getRideInstance(spark.sparkContext))
    appMetrics.registerGauge("metrics.malformedfares", AppAccumulators.getFareInstance(spark.sparkContext))
    SparkEnv.get.metricsSystem.registerSource(appMetrics)
```

<span data-ttu-id="25f9a-203">Apache Spark 使用 Dropwizard 库发送指标；某些本机 Dropwizard 指标字段与 Azure Log Analytics 不兼容。</span><span class="sxs-lookup"><span data-stu-id="25f9a-203">Apache Spark uses the Dropwizard library to send metrics, and some of the native Dropwizard metrics fields are incompatible with Azure Log Analytics.</span></span> <span data-ttu-id="25f9a-204">因此，本参考体系结构包含了自定义的 Dropwizard 接收器和报告器。</span><span class="sxs-lookup"><span data-stu-id="25f9a-204">Therefore, this reference architecture includes a custom Dropwizard sink and reporter.</span></span> <span data-ttu-id="25f9a-205">它会根据 Azure Log Analytics 的预期设置指标格式。</span><span class="sxs-lookup"><span data-stu-id="25f9a-205">It formats the metrics in the format expected by Azure Log Analytics.</span></span> <span data-ttu-id="25f9a-206">当 Apache Spark 报告指标时，也会发送格式不当的行程数据和费用数据的自定义指标。</span><span class="sxs-lookup"><span data-stu-id="25f9a-206">When Apache Spark reports metrics, the custom metrics for the malformed ride and fare data are also sent.</span></span>

<span data-ttu-id="25f9a-207">要记录到 Azure Log Analytics 工作区的最后一个指标是 Spark 结构化流作业进度的累积进度。</span><span class="sxs-lookup"><span data-stu-id="25f9a-207">The last metric to be logged to the Azure Log Analytics workspace is the cumulative progress of the Spark Structured Streaming job progress.</span></span> <span data-ttu-id="25f9a-208">此指标是使用 **com.microsoft.pnp.StreamingMetricsListener** 类中实现的自定义 StreamingQuery 侦听器记录的。</span><span class="sxs-lookup"><span data-stu-id="25f9a-208">This is done using a custom StreamingQuery listener implemented in the **com.microsoft.pnp.StreamingMetricsListener** class.</span></span> <span data-ttu-id="25f9a-209">当作业运行时，此类将注册到 Apache Spark 会话：</span><span class="sxs-lookup"><span data-stu-id="25f9a-209">This class is registered to the Apache Spark Session when the job runs:</span></span>

```scala
spark.streams.addListener(new StreamingMetricsListener())
```

<span data-ttu-id="25f9a-210">每当发生结构化流事件、将日志消息和指标发送到 Azure Log Analytics 工作区时，Apache Spark 运行时都会调用 StreamingMetricsListener 中的方法。</span><span class="sxs-lookup"><span data-stu-id="25f9a-210">The methods in the StreamingMetricsListener are called by the Apache Spark runtime whenever a structured steaming event occurs, sending log messages and metrics to the Azure Log Analytics workspace.</span></span> <span data-ttu-id="25f9a-211">可在工作区中使用以下查询来监视应用程序：</span><span class="sxs-lookup"><span data-stu-id="25f9a-211">You can use the following queries in your workspace to monitor the application:</span></span>

### <a name="latency-and-throughput-for-streaming-queries"></a><span data-ttu-id="25f9a-212">流查询的延迟和吞吐量</span><span class="sxs-lookup"><span data-stu-id="25f9a-212">Latency and throughput for streaming queries</span></span>

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| project  mdc_inputRowsPerSecond_d, mdc_durationms_triggerExecution_d
| render timechart
```

### <a name="exceptions-logged-during-stream-query-execution"></a><span data-ttu-id="25f9a-213">流查询执行期间记录的异常</span><span class="sxs-lookup"><span data-stu-id="25f9a-213">Exceptions logged during stream query execution</span></span>

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| where Level contains "Error"
```

### <a name="accumulation-of-malformed-fare-and-ride-data"></a><span data-ttu-id="25f9a-214">格式不当的费用和行程数据的累积数目</span><span class="sxs-lookup"><span data-stu-id="25f9a-214">Accumulation of malformed fare and ride data</span></span>

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

### <a name="job-execution-to-trace-resiliency"></a><span data-ttu-id="25f9a-215">用于跟踪复原能力的作业执行</span><span class="sxs-lookup"><span data-stu-id="25f9a-215">Job execution to trace resiliency</span></span>

```shell
SparkMetric_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart
| where name_s contains "driver.DAGScheduler.job.allJobs"
```

## <a name="deploy-the-solution"></a><span data-ttu-id="25f9a-216">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="25f9a-216">Deploy the solution</span></span>

<span data-ttu-id="25f9a-217">若要部署并运行参考实现，请按 [GitHub 自述文件](https://github.com/mspnp/azure-databricks-streaming-analytics)中的步骤操作。</span><span class="sxs-lookup"><span data-stu-id="25f9a-217">To the deploy and run the reference implementation, follow the steps in the [GitHub readme](https://github.com/mspnp/azure-databricks-streaming-analytics).</span></span>

