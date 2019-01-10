---
title: 使用 Azure 流分析进行流处理
titleSuffix: Azure Reference Architectures
description: 在 Azure 中创建端到端流处理管道。
author: MikeWasson
ms.date: 11/06/2018
ms.custom: seodec18
ms.openlocfilehash: abd020fa12883ae3d23623c53e15fe025590de6f
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54011576"
---
# <a name="create-a-stream-processing-pipeline-with-azure-stream-analytics"></a><span data-ttu-id="d300b-103">使用 Azure 流分析创建流处理管道</span><span class="sxs-lookup"><span data-stu-id="d300b-103">Create a stream processing pipeline with Azure Stream Analytics</span></span>

<span data-ttu-id="d300b-104">本参考体系结构演示一个端到端[流处理](/azure/architecture/data-guide/big-data/real-time-processing)管道。</span><span class="sxs-lookup"><span data-stu-id="d300b-104">This reference architecture shows an end-to-end [stream processing](/azure/architecture/data-guide/big-data/real-time-processing) pipeline.</span></span> <span data-ttu-id="d300b-105">该管道从两个源引入数据、关联两个流中的记录，然后计算某个时间窗口的移动平均值。</span><span class="sxs-lookup"><span data-stu-id="d300b-105">The pipeline ingests data from two sources, correlates records in the two streams, and calculates a rolling average across a time window.</span></span> <span data-ttu-id="d300b-106">将存储结果以供进一步分析。</span><span class="sxs-lookup"><span data-stu-id="d300b-106">The results are stored for further analysis.</span></span>

<span data-ttu-id="d300b-107">[GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="d300b-107">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![适用于通过 Azure 流分析创建流处理管道的参考体系结构](./images/stream-processing-asa/stream-processing-asa.png)

<span data-ttu-id="d300b-109">**场景**：某家出租车公司需要收集有关出租车的每次行程的数据。</span><span class="sxs-lookup"><span data-stu-id="d300b-109">**Scenario**: A taxi company collects data about each taxi trip.</span></span> <span data-ttu-id="d300b-110">对于此场景，我们假设有两个不同的设备在发送数据。</span><span class="sxs-lookup"><span data-stu-id="d300b-110">For this scenario, we assume there are two separate devices sending data.</span></span> <span data-ttu-id="d300b-111">出租车的计量表发送有关每次行程的信息 &mdash; 持续时间、距离以及上车和下车地点。</span><span class="sxs-lookup"><span data-stu-id="d300b-111">The taxi has a meter that sends information about each ride &mdash; the duration, distance, and pickup and dropoff locations.</span></span> <span data-ttu-id="d300b-112">有一个单独的设备接受客户的付款，并发送有关费用的数据。</span><span class="sxs-lookup"><span data-stu-id="d300b-112">A separate device accepts payments from customers and sends data about fares.</span></span> <span data-ttu-id="d300b-113">该出租车公司想要实时计算每英里的平均小费，以绘制趋势图。</span><span class="sxs-lookup"><span data-stu-id="d300b-113">The taxi company wants to calculate the average tip per mile driven, in real time, in order to spot trends.</span></span>

## <a name="architecture"></a><span data-ttu-id="d300b-114">体系结构</span><span class="sxs-lookup"><span data-stu-id="d300b-114">Architecture</span></span>

<span data-ttu-id="d300b-115">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="d300b-115">The architecture consists of the following components.</span></span>

<span data-ttu-id="d300b-116">**数据源**。</span><span class="sxs-lookup"><span data-stu-id="d300b-116">**Data sources**.</span></span> <span data-ttu-id="d300b-117">在此体系结构中，有两个数据源实时生成数据流。</span><span class="sxs-lookup"><span data-stu-id="d300b-117">In this architecture, there are two data sources that generate data streams in real time.</span></span> <span data-ttu-id="d300b-118">第一个流包含行程信息，第二个流包含费用信息。</span><span class="sxs-lookup"><span data-stu-id="d300b-118">The first stream contains ride information, and the second contains fare information.</span></span> <span data-ttu-id="d300b-119">该参考体系结构包含一个模拟的数据生成器，该生成器读取一组静态文件，并将数据推送到事件中心。</span><span class="sxs-lookup"><span data-stu-id="d300b-119">The reference architecture includes a simulated data generator that reads from a set of static files and pushes the data to Event Hubs.</span></span> <span data-ttu-id="d300b-120">在实际应用中，数据源是安装在出租车内部的设备。</span><span class="sxs-lookup"><span data-stu-id="d300b-120">In a real application, the data sources would be devices installed in the taxi cabs.</span></span>

<span data-ttu-id="d300b-121">**Azure 事件中心**。</span><span class="sxs-lookup"><span data-stu-id="d300b-121">**Azure Event Hubs**.</span></span> <span data-ttu-id="d300b-122">[事件中心](/azure/event-hubs/)是一个事件引入服务。</span><span class="sxs-lookup"><span data-stu-id="d300b-122">[Event Hubs](/azure/event-hubs/) is an event ingestion service.</span></span> <span data-ttu-id="d300b-123">此体系结构使用两个事件中心实例，每个数据源各对应一个。</span><span class="sxs-lookup"><span data-stu-id="d300b-123">This architecture uses two event hub instances, one for each data source.</span></span> <span data-ttu-id="d300b-124">每个数据源将数据流发送到关联的事件中心。</span><span class="sxs-lookup"><span data-stu-id="d300b-124">Each data source sends a stream of data to the associated event hub.</span></span>

<span data-ttu-id="d300b-125">**Azure 流分析**。</span><span class="sxs-lookup"><span data-stu-id="d300b-125">**Azure Stream Analytics**.</span></span> <span data-ttu-id="d300b-126">[流分析](/azure/stream-analytics/)是一个事件处理引擎。</span><span class="sxs-lookup"><span data-stu-id="d300b-126">[Stream Analytics](/azure/stream-analytics/) is an event-processing engine.</span></span> <span data-ttu-id="d300b-127">流分析作业从两个事件中心读取数据流，并执行流处理。</span><span class="sxs-lookup"><span data-stu-id="d300b-127">A Stream Analytics job reads the data streams from the two event hubs and performs stream processing.</span></span>

<span data-ttu-id="d300b-128">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="d300b-128">**Cosmos DB**.</span></span> <span data-ttu-id="d300b-129">流分析作业的输出是一系列记录，这些记录编写为 Cosmos DB 文档数据库中的 JSON 文档。</span><span class="sxs-lookup"><span data-stu-id="d300b-129">The output from the Stream Analytics job is a series of records, which are written as JSON documents to a Cosmos DB document database.</span></span>

<span data-ttu-id="d300b-130">**Microsoft Power BI**。</span><span class="sxs-lookup"><span data-stu-id="d300b-130">**Microsoft Power BI**.</span></span> <span data-ttu-id="d300b-131">Power BI 是一套商业分析工具，用于分析数据以获取商业见解。</span><span class="sxs-lookup"><span data-stu-id="d300b-131">Power BI is a suite of business analytics tools to analyze data for business insights.</span></span> <span data-ttu-id="d300b-132">在此体系结构中，Power BI 从 Cosmos DB 加载数据。</span><span class="sxs-lookup"><span data-stu-id="d300b-132">In this architecture, it loads the data from Cosmos DB.</span></span> <span data-ttu-id="d300b-133">这样，用户便可以分析收集的完整历史数据集。</span><span class="sxs-lookup"><span data-stu-id="d300b-133">This allows users to analyze the complete set of historical data that's been collected.</span></span> <span data-ttu-id="d300b-134">还可将结果直接从流分析流式传输到 Power BI，以显示数据的实时视图。</span><span class="sxs-lookup"><span data-stu-id="d300b-134">You could also stream the results directly from Stream Analytics to Power BI for a real-time view of the data.</span></span> <span data-ttu-id="d300b-135">有关详细信息，请参阅 [Power BI 中的实时流式处理](/power-bi/service-real-time-streaming)。</span><span class="sxs-lookup"><span data-stu-id="d300b-135">For more information, see [Real-time streaming in Power BI](/power-bi/service-real-time-streaming).</span></span>

<span data-ttu-id="d300b-136">**Azure Monitor**。</span><span class="sxs-lookup"><span data-stu-id="d300b-136">**Azure Monitor**.</span></span> <span data-ttu-id="d300b-137">[Azure Monitor](/azure/monitoring-and-diagnostics/) 收集解决方案中部署的 Azure 服务的性能指标。</span><span class="sxs-lookup"><span data-stu-id="d300b-137">[Azure Monitor](/azure/monitoring-and-diagnostics/) collects performance metrics about the Azure services deployed in the solution.</span></span> <span data-ttu-id="d300b-138">通过在仪表板中可视化这些信息，可以洞察解决方案的运行状况。</span><span class="sxs-lookup"><span data-stu-id="d300b-138">By visualizing these in a dashboard, you can get insights into the health of the solution.</span></span>

## <a name="data-ingestion"></a><span data-ttu-id="d300b-139">数据引入</span><span class="sxs-lookup"><span data-stu-id="d300b-139">Data ingestion</span></span>

<!-- markdownlint-disable MD033 -->

<span data-ttu-id="d300b-140">为了模拟数据源，此参考体系结构使用了[纽约市出租车数据](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797)数据集<sup>[[1]](#note1)</sup>。</span><span class="sxs-lookup"><span data-stu-id="d300b-140">To simulate a data source, this reference architecture uses the [New York City Taxi Data](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797) dataset<sup>[[1]](#note1)</sup>.</span></span> <span data-ttu-id="d300b-141">此数据集包含纽约市过去 4 年（2010 年 &ndash; 2013 年）的出租车行程数据。</span><span class="sxs-lookup"><span data-stu-id="d300b-141">This dataset contains data about taxi trips in New York City over a 4-year period (2010 &ndash; 2013).</span></span> <span data-ttu-id="d300b-142">其包含两种类型的记录：行程数据和费用数据。</span><span class="sxs-lookup"><span data-stu-id="d300b-142">It contains two types of record: Ride data and fare data.</span></span> <span data-ttu-id="d300b-143">行程数据包括行程持续时间、行程距离以及上车和下车地点。</span><span class="sxs-lookup"><span data-stu-id="d300b-143">Ride data includes trip duration, trip distance, and pickup and dropoff location.</span></span> <span data-ttu-id="d300b-144">费用数据包括乘车费、税费和小费金额。</span><span class="sxs-lookup"><span data-stu-id="d300b-144">Fare data includes fare, tax, and tip amounts.</span></span> <span data-ttu-id="d300b-145">这两种记录类型中的通用字段包括牌照号、出租车驾照和供应商 ID。</span><span class="sxs-lookup"><span data-stu-id="d300b-145">Common fields in both record types include medallion number, hack license, and vendor ID.</span></span> <span data-ttu-id="d300b-146">这三个字段相结合，唯一标识了出租车和驾驶员。</span><span class="sxs-lookup"><span data-stu-id="d300b-146">Together these three fields uniquely identify a taxi plus a driver.</span></span> <span data-ttu-id="d300b-147">数据以 CSV 格式存储。</span><span class="sxs-lookup"><span data-stu-id="d300b-147">The data is stored in CSV format.</span></span>

<span data-ttu-id="d300b-148">[1] <span id="note1">Donovan, Brian；Work, Dan (2016)：纽约市出租车行程数据 (2010-2013)。</span><span class="sxs-lookup"><span data-stu-id="d300b-148">[1] <span id="note1">Donovan, Brian; Work, Dan (2016): New York City Taxi Trip Data (2010-2013).</span></span> <span data-ttu-id="d300b-149">伊利诺伊大学厄巴纳-香槟分校。</span><span class="sxs-lookup"><span data-stu-id="d300b-149">University of Illinois at Urbana-Champaign.</span></span> <https://doi.org/10.13012/J8PN93H8>

<!-- markdownlint-enable MD033 -->

<span data-ttu-id="d300b-150">数据生成器是一个读取记录并将其发送到 Azure 事件中心的 .NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="d300b-150">The data generator is a .NET Core application that reads the records and sends them to Azure Event Hubs.</span></span> <span data-ttu-id="d300b-151">该生成器发送 JSON 格式的行程数据以及 CSV 格式的费用数据。</span><span class="sxs-lookup"><span data-stu-id="d300b-151">The generator sends ride data in JSON format and fare data in CSV format.</span></span>

<span data-ttu-id="d300b-152">事件中心使用[分区](/azure/event-hubs/event-hubs-features#partitions)将数据分段。</span><span class="sxs-lookup"><span data-stu-id="d300b-152">Event Hubs uses [partitions](/azure/event-hubs/event-hubs-features#partitions) to segment the data.</span></span> <span data-ttu-id="d300b-153">使用者可以通过分区功能并行读取每个分区。</span><span class="sxs-lookup"><span data-stu-id="d300b-153">Partitions allow a consumer to read each partition in parallel.</span></span> <span data-ttu-id="d300b-154">将数据发送到事件中心时，可以显式指定分区键。</span><span class="sxs-lookup"><span data-stu-id="d300b-154">When you send data to Event Hubs, you can specify the partition key explicitly.</span></span> <span data-ttu-id="d300b-155">否则，记录将以循环方式分配到分区。</span><span class="sxs-lookup"><span data-stu-id="d300b-155">Otherwise, records are assigned to partitions in round-robin fashion.</span></span>

<span data-ttu-id="d300b-156">在此特定场景中，给定出租车的行程数据和费用数据最终会获得相同的分区 ID。</span><span class="sxs-lookup"><span data-stu-id="d300b-156">In this particular scenario, ride data and fare data should end up with the same partition ID for a given taxi cab.</span></span> <span data-ttu-id="d300b-157">这样，在关联两个流时，流分析可以应用某种并行度。</span><span class="sxs-lookup"><span data-stu-id="d300b-157">This enables Stream Analytics to apply a degree of parallelism when it correlates the two streams.</span></span> <span data-ttu-id="d300b-158">行程数据分区 *n* 中的记录将与费用数据分区 *n* 中的记录进行匹配。</span><span class="sxs-lookup"><span data-stu-id="d300b-158">A record in partition *n* of the ride data will match a record in partition *n* of the fare data.</span></span>

![使用 Azure 流分析和事件中心进行的流处理的图](./images/stream-processing-asa/stream-processing-eh.png)

<span data-ttu-id="d300b-160">在数据生成器中，这两种记录类型的通用数据模型具有 `PartitionKey` 属性，该属性是 `Medallion`、`HackLicense` 和 `VendorId` 的串联形式。</span><span class="sxs-lookup"><span data-stu-id="d300b-160">In the data generator, the common data model for both record types has a `PartitionKey` property which is the concatenation of `Medallion`, `HackLicense`, and `VendorId`.</span></span>

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

<span data-ttu-id="d300b-161">将数据发送到事件中心时，会使用此属性来提供显式分区键：</span><span class="sxs-lookup"><span data-stu-id="d300b-161">This property is used to provide an explicit partition key when sending to Event Hubs:</span></span>

```csharp
using (var client = pool.GetObject())
{
    return client.Value.SendAsync(new EventData(Encoding.UTF8.GetBytes(
        t.GetData(dataFormat))), t.PartitionKey);
}
```

## <a name="stream-processing"></a><span data-ttu-id="d300b-162">流处理</span><span class="sxs-lookup"><span data-stu-id="d300b-162">Stream processing</span></span>

<span data-ttu-id="d300b-163">流处理作业是使用 SQL 查询通过多个不同的步骤定义的。</span><span class="sxs-lookup"><span data-stu-id="d300b-163">The stream processing job is defined using a SQL query with several distinct steps.</span></span> <span data-ttu-id="d300b-164">前两个步骤只是从两个输入流中选择记录。</span><span class="sxs-lookup"><span data-stu-id="d300b-164">The first two steps simply select records from the two input streams.</span></span>

```sql
WITH
Step1 AS (
    SELECT PartitionId,
           TRY_CAST(Medallion AS nvarchar(max)) AS Medallion,
           TRY_CAST(HackLicense AS nvarchar(max)) AS HackLicense,
           VendorId,
           TRY_CAST(PickupTime AS datetime) AS PickupTime,
           TripDistanceInMiles
    FROM [TaxiRide] PARTITION BY PartitionId
),
Step2 AS (
    SELECT PartitionId,
           medallion AS Medallion,
           hack_license AS HackLicense,
           vendor_id AS VendorId,
           TRY_CAST(pickup_datetime AS datetime) AS PickupTime,
           tip_amount AS TipAmount
    FROM [TaxiFare] PARTITION BY PartitionId
),
```

<span data-ttu-id="d300b-165">下一个步骤联接两个输入流，以便从每个流中选择匹配的记录。</span><span class="sxs-lookup"><span data-stu-id="d300b-165">The next step joins the two input streams to select matching records from each stream.</span></span>

```sql
Step3 AS (
  SELECT
         tr.Medallion,
         tr.HackLicense,
         tr.VendorId,
         tr.PickupTime,
         tr.TripDistanceInMiles,
         tf.TipAmount
    FROM [Step1] tr
    PARTITION BY PartitionId
    JOIN [Step2] tf PARTITION BY PartitionId
      ON tr.Medallion = tf.Medallion
     AND tr.HackLicense = tf.HackLicense
     AND tr.VendorId = tf.VendorId
     AND tr.PickupTime = tf.PickupTime
     AND tr.PartitionId = tf.PartitionId
     AND DATEDIFF(minute, tr, tf) BETWEEN 0 AND 15
)
```

<span data-ttu-id="d300b-166">此查询将记录联接到唯一标识匹配记录（Medallion、HackLicense、VendorId 和 PickupTime）的一组字段中。</span><span class="sxs-lookup"><span data-stu-id="d300b-166">This query joins records on a set of fields that uniquely identify matching records (Medallion, HackLicense, VendorId, and PickupTime).</span></span> <span data-ttu-id="d300b-167">`JOIN` 语句还包含分区 ID。</span><span class="sxs-lookup"><span data-stu-id="d300b-167">The `JOIN` statement also includes the partition ID.</span></span> <span data-ttu-id="d300b-168">如前所述，此功能利用了以下事实：此场景中匹配的记录始终具有相同的分区 ID。</span><span class="sxs-lookup"><span data-stu-id="d300b-168">As mentioned, this takes advantage of the fact that matching records always have the same partition ID in this scenario.</span></span>

<span data-ttu-id="d300b-169">在流分析中，联接是时态性的，也就是说，将在特定的时间窗口中联接记录。</span><span class="sxs-lookup"><span data-stu-id="d300b-169">In Stream Analytics, joins are *temporal*, meaning records are joined within a particular window of time.</span></span> <span data-ttu-id="d300b-170">否则，作业可能需要无限期等待出现匹配项。</span><span class="sxs-lookup"><span data-stu-id="d300b-170">Otherwise, the job might need to wait indefinitely for a match.</span></span> <span data-ttu-id="d300b-171">[DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/join-azure-stream-analytics) 函数指定在进行匹配时，两条匹配的记录在时间上可以相隔多久。</span><span class="sxs-lookup"><span data-stu-id="d300b-171">The [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/join-azure-stream-analytics) function specifies how far two matching records can be separated in time for a match.</span></span>

<span data-ttu-id="d300b-172">作业中的最后一个步骤计算每英里的平均小费，该值按 5 分钟的跳跃窗口分组。</span><span class="sxs-lookup"><span data-stu-id="d300b-172">The last step in the job computes the average tip per mile, grouped by a hopping window of 5 minutes.</span></span>

```sql
SELECT System.Timestamp AS WindowTime,
       SUM(tr.TipAmount) / SUM(tr.TripDistanceInMiles) AS AverageTipPerMile
  INTO [TaxiDrain]
  FROM [Step3] tr
  GROUP BY HoppingWindow(Duration(minute, 5), Hop(minute, 1))
```

<span data-ttu-id="d300b-173">流分析提供多个[开窗函数](/azure/stream-analytics/stream-analytics-window-functions)。</span><span class="sxs-lookup"><span data-stu-id="d300b-173">Stream Analytics provides several [windowing functions](/azure/stream-analytics/stream-analytics-window-functions).</span></span> <span data-ttu-id="d300b-174">跳跃窗口的时间按固定的时段前移，在本例中，每个跳跃时段为 1 分钟。</span><span class="sxs-lookup"><span data-stu-id="d300b-174">A hopping window moves forward in time by a fixed period, in this case 1 minute per hop.</span></span> <span data-ttu-id="d300b-175">结果是计算过去 5 分钟的移动平均值。</span><span class="sxs-lookup"><span data-stu-id="d300b-175">The result is to calculate a moving average over the past 5 minutes.</span></span>

<span data-ttu-id="d300b-176">在此处所示的体系结构中，只会将流分析作业的结果保存到 Cosmos DB。</span><span class="sxs-lookup"><span data-stu-id="d300b-176">In the architecture shown here, only the results of the Stream Analytics job are saved to Cosmos DB.</span></span> <span data-ttu-id="d300b-177">在大数据方案中，另请考虑使用[事件中心捕获](/azure/event-hubs/event-hubs-capture-overview)功能将原始事件数据保存到 Azure Blob 存储。</span><span class="sxs-lookup"><span data-stu-id="d300b-177">For a big data scenario, consider also using [Event Hubs Capture](/azure/event-hubs/event-hubs-capture-overview) to save the raw event data into Azure Blob storage.</span></span> <span data-ttu-id="d300b-178">保存原始数据可方便以后针对历史数据运行批量查询，以从数据中推断出新的见解。</span><span class="sxs-lookup"><span data-stu-id="d300b-178">Keeping the raw data will allow you to run batch queries over your historical data at later time, in order to derive new insights from the data.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="d300b-179">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="d300b-179">Scalability considerations</span></span>

### <a name="event-hubs"></a><span data-ttu-id="d300b-180">事件中心</span><span class="sxs-lookup"><span data-stu-id="d300b-180">Event Hubs</span></span>

<span data-ttu-id="d300b-181">事件中心的吞吐量容量以[吞吐量单位](/azure/event-hubs/event-hubs-features#throughput-units)来度量。</span><span class="sxs-lookup"><span data-stu-id="d300b-181">The throughput capacity of Event Hubs is measured in [throughput units](/azure/event-hubs/event-hubs-features#throughput-units).</span></span> <span data-ttu-id="d300b-182">可以通过启用[自动扩充](/azure/event-hubs/event-hubs-auto-inflate)来自动缩放事件中心。自动扩充可以根据流量，最高将吞吐量单位自动扩展到配置的上限。</span><span class="sxs-lookup"><span data-stu-id="d300b-182">You can autoscale an event hub by enabling [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate), which automatically scales the throughput units based on traffic, up to a configured maximum.</span></span>

### <a name="stream-analytics"></a><span data-ttu-id="d300b-183">流分析</span><span class="sxs-lookup"><span data-stu-id="d300b-183">Stream Analytics</span></span>

<span data-ttu-id="d300b-184">对于流分析而言，分配给作业的计算资源以流单元来度量。</span><span class="sxs-lookup"><span data-stu-id="d300b-184">For Stream Analytics, the computing resources allocated to a job are measured in Streaming Units.</span></span> <span data-ttu-id="d300b-185">如果能够将流分析作业并行化，则这些作业可获得最佳的可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="d300b-185">Stream Analytics jobs scale best if the job can be parallelized.</span></span> <span data-ttu-id="d300b-186">这样，流分析便可以跨多个计算节点分配作业。</span><span class="sxs-lookup"><span data-stu-id="d300b-186">That way, Stream Analytics can distribute the job across multiple compute nodes.</span></span>

<span data-ttu-id="d300b-187">对于事件中心输入，请使用 `PARTITION BY` 关键字将流分析作业分区。</span><span class="sxs-lookup"><span data-stu-id="d300b-187">For Event Hubs input, use the `PARTITION BY` keyword to partition the Stream Analytics job.</span></span> <span data-ttu-id="d300b-188">根据事件中心分区将数据划分为子集。</span><span class="sxs-lookup"><span data-stu-id="d300b-188">The data will be divided into subsets based on the Event Hubs partitions.</span></span>

<span data-ttu-id="d300b-189">开窗函数和时态联接需要额外的 SU。</span><span class="sxs-lookup"><span data-stu-id="d300b-189">Windowing functions and temporal joins require additional SU.</span></span> <span data-ttu-id="d300b-190">尽量使用 `PARTITION BY`，以便单独处理每个分区。</span><span class="sxs-lookup"><span data-stu-id="d300b-190">When possible, use `PARTITION BY` so that each partition is processed separately.</span></span> <span data-ttu-id="d300b-191">有关详细信息，请参阅[了解和调整流单元](/azure/stream-analytics/stream-analytics-streaming-unit-consumption#windowed-aggregates)。</span><span class="sxs-lookup"><span data-stu-id="d300b-191">For more information, see [Understand and adjust Streaming Units](/azure/stream-analytics/stream-analytics-streaming-unit-consumption#windowed-aggregates).</span></span>

<span data-ttu-id="d300b-192">如果无法并行化整个流分析作业，请尝试将作业分解为多个步骤，并从一个或多个并行步骤开始。</span><span class="sxs-lookup"><span data-stu-id="d300b-192">If it's not possible to parallelize the entire Stream Analytics job, try to break the job into multiple steps, starting with one or more parallel steps.</span></span> <span data-ttu-id="d300b-193">这样，前几个步骤便可并行运行。</span><span class="sxs-lookup"><span data-stu-id="d300b-193">That way, the first steps can run in parallel.</span></span> <span data-ttu-id="d300b-194">例如，在以下参考体系结构中：</span><span class="sxs-lookup"><span data-stu-id="d300b-194">For example, in this reference architecture:</span></span>

- <span data-ttu-id="d300b-195">步骤 1 和 2 是在单个分区中选择记录的简单 `SELECT` 语句。</span><span class="sxs-lookup"><span data-stu-id="d300b-195">Steps 1 and 2 are simple `SELECT` statements that select records within a single partition.</span></span>
- <span data-ttu-id="d300b-196">步骤 3 跨两个输入流执行分区的联接。</span><span class="sxs-lookup"><span data-stu-id="d300b-196">Step 3 performs a partitioned join across two input streams.</span></span> <span data-ttu-id="d300b-197">此步骤利用了以下事实：匹配的记录具有相同的分区键，因此保证这些记录在每个输入流中具有相同的分区 ID。</span><span class="sxs-lookup"><span data-stu-id="d300b-197">This step takes advantage of the fact that matching records share the same partition key, and so are guaranteed to have the same partition ID in each input stream.</span></span>
- <span data-ttu-id="d300b-198">步骤 4 跨所有分区聚合数据。</span><span class="sxs-lookup"><span data-stu-id="d300b-198">Step 4 aggregates across all of the partitions.</span></span> <span data-ttu-id="d300b-199">此步骤无法并行化。</span><span class="sxs-lookup"><span data-stu-id="d300b-199">This step cannot be parallelized.</span></span>

<span data-ttu-id="d300b-200">使用流分析[作业关系图](/azure/stream-analytics/stream-analytics-job-diagram-with-metrics)可以查看已将多少个分区分配到了作业中的每个步骤。</span><span class="sxs-lookup"><span data-stu-id="d300b-200">Use the Stream Analytics [job diagram](/azure/stream-analytics/stream-analytics-job-diagram-with-metrics) to see how many partitions are assigned to each step in the job.</span></span> <span data-ttu-id="d300b-201">下图显示了此参考体系结构的作业关系图：</span><span class="sxs-lookup"><span data-stu-id="d300b-201">The following diagram shows the job diagram for this reference architecture:</span></span>

![作业关系图](./images/stream-processing-asa/job-diagram.png)

### <a name="cosmos-db"></a><span data-ttu-id="d300b-203">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="d300b-203">Cosmos DB</span></span>

<span data-ttu-id="d300b-204">Cosmos DB 的吞吐量容量以[请求单位](/azure/cosmos-db/request-units) (RU) 来度量。</span><span class="sxs-lookup"><span data-stu-id="d300b-204">Throughput capacity for Cosmos DB is measured in [Request Units](/azure/cosmos-db/request-units) (RU).</span></span> <span data-ttu-id="d300b-205">若要将某个 Cosmos DB 容器扩展到超过 10,000 RU，必须在创建该容器时指定[分区键](/azure/cosmos-db/partition-data)，并在每个文档中包含该分区键。</span><span class="sxs-lookup"><span data-stu-id="d300b-205">In order to scale a Cosmos DB container past 10,000 RU, you must specify a [partition key](/azure/cosmos-db/partition-data) when you create the container, and include the partition key in every document.</span></span>

<span data-ttu-id="d300b-206">在此参考体系结构中，每分钟（跳跃窗口间隔）只会创建新文档一次，因此吞吐量要求相当低。</span><span class="sxs-lookup"><span data-stu-id="d300b-206">In this reference architecture, new documents are created only once per minute (the hopping window interval), so the throughput requirements are quite low.</span></span> <span data-ttu-id="d300b-207">因此，在此场景中不需要分配分区键。</span><span class="sxs-lookup"><span data-stu-id="d300b-207">For that reason, there's no need to assign a partition key in this scenario.</span></span>

## <a name="monitoring-considerations"></a><span data-ttu-id="d300b-208">监视注意事项</span><span class="sxs-lookup"><span data-stu-id="d300b-208">Monitoring considerations</span></span>

<span data-ttu-id="d300b-209">使用任何流处理解决方案时，必须监视系统的性能和运行状况。</span><span class="sxs-lookup"><span data-stu-id="d300b-209">With any stream processing solution, it's important to monitor the performance and health of the system.</span></span> <span data-ttu-id="d300b-210">[Azure Monitor](/azure/monitoring-and-diagnostics/) 收集体系结构中使用的 Azure 服务的指标和诊断日志。</span><span class="sxs-lookup"><span data-stu-id="d300b-210">[Azure Monitor](/azure/monitoring-and-diagnostics/) collects metrics and diagnostics logs for the Azure services used in the architecture.</span></span> <span data-ttu-id="d300b-211">Azure Monitor 内置于 Azure 平台中，不需要在应用程序中编写任何附加代码。</span><span class="sxs-lookup"><span data-stu-id="d300b-211">Azure Monitor is built into the Azure platform and does not require any additional code in your application.</span></span>

<span data-ttu-id="d300b-212">以下任何警告信号指示应该横向扩展相关的 Azure 资源：</span><span class="sxs-lookup"><span data-stu-id="d300b-212">Any of the following warning signals indicate that you should scale out the relevant Azure resource:</span></span>

- <span data-ttu-id="d300b-213">事件中心限制了请求，或者即将达到每日消息配额。</span><span class="sxs-lookup"><span data-stu-id="d300b-213">Event Hubs throttles requests or is close to the daily message quota.</span></span>
- <span data-ttu-id="d300b-214">流分析作业一贯使用超过 80% 的分配流单元 (SU)。</span><span class="sxs-lookup"><span data-stu-id="d300b-214">The Stream Analytics job consistently uses more than 80% of allocated Streaming Units (SU).</span></span>
- <span data-ttu-id="d300b-215">Cosmos DB 开始限制请求。</span><span class="sxs-lookup"><span data-stu-id="d300b-215">Cosmos DB begins to throttle requests.</span></span>

<span data-ttu-id="d300b-216">参考体系结构包含部署在 Azure 门户中的自定义仪表板。</span><span class="sxs-lookup"><span data-stu-id="d300b-216">The reference architecture includes a custom dashboard, which is deployed to the Azure portal.</span></span> <span data-ttu-id="d300b-217">部署体系结构后，可以通过打开 [Azure 门户](https://portal.azure.com)并从仪表板列表中选择 `TaxiRidesDashboard` 来查看仪表板。</span><span class="sxs-lookup"><span data-stu-id="d300b-217">After you deploy the architecture, you can view the dashboard by opening the [Azure Portal](https://portal.azure.com) and selecting `TaxiRidesDashboard` from list of dashboards.</span></span> <span data-ttu-id="d300b-218">有关在 Azure 门户中创建和部署自定义仪表板的详细信息，请参阅[以编程方式创建 Azure 仪表板](/azure/azure-portal/azure-portal-dashboards-create-programmatically)。</span><span class="sxs-lookup"><span data-stu-id="d300b-218">For more information about creating and deploying custom dashboards in the Azure portal, see [Programmatically create Azure Dashboards](/azure/azure-portal/azure-portal-dashboards-create-programmatically).</span></span>

<span data-ttu-id="d300b-219">下图显示了在流分析作业大约运行一小时之后的仪表板。</span><span class="sxs-lookup"><span data-stu-id="d300b-219">The following image shows the dashboard after the Stream Analytics job ran for about an hour.</span></span>

![“打车”仪表板的屏幕截图](./images/stream-processing-asa/asa-dashboard.png)

<span data-ttu-id="d300b-221">左下方的面板显示流分析作业的 SU 消耗量在前 15 分钟不断攀升，然后保持稳定。</span><span class="sxs-lookup"><span data-stu-id="d300b-221">The panel on the lower left shows that the SU consumption for the Stream Analytics job climbs during the first 15 minutes and then levels off.</span></span> <span data-ttu-id="d300b-222">这是作业在达到稳定状态时的典型模式。</span><span class="sxs-lookup"><span data-stu-id="d300b-222">This is a typical pattern as the job reaches a steady state.</span></span>

<span data-ttu-id="d300b-223">在右上方面板中可以看到，事件中心正在限制请求。</span><span class="sxs-lookup"><span data-stu-id="d300b-223">Notice that Event Hubs is throttling requests, shown in the upper right panel.</span></span> <span data-ttu-id="d300b-224">偶发性的限制请求并不是问题，因为事件中心客户端 SDK 在收到限制错误时会自动重试。</span><span class="sxs-lookup"><span data-stu-id="d300b-224">An occasional throttled request is not a problem, because the Event Hubs client SDK automatically retries when it receives a throttling error.</span></span> <span data-ttu-id="d300b-225">但是，如果一直出现限制错误，则意味着事件中心需要更多的吞吐量单位。</span><span class="sxs-lookup"><span data-stu-id="d300b-225">However, if you see consistent throttling errors, it means the event hub needs more throughput units.</span></span> <span data-ttu-id="d300b-226">下图显示了一个使用事件中心自动扩充功能的测试运行。该功能会按需自动横向扩展吞吐量单位。</span><span class="sxs-lookup"><span data-stu-id="d300b-226">The following graph shows a test run using the Event Hubs auto-inflate feature, which automatically scales out the throughput units as needed.</span></span>

![事件中心自动缩放的屏幕截图](./images/stream-processing-asa/stream-processing-eh-autoscale.png)

<span data-ttu-id="d300b-228">自动扩充是在大约 06:35 标记处启用的。</span><span class="sxs-lookup"><span data-stu-id="d300b-228">Auto-inflate was enabled at about the 06:35 mark.</span></span> <span data-ttu-id="d300b-229">可以看到，当事件中心自动纵向扩展到 3 个吞吐量单位时，限制的请求中出现 p 下降。</span><span class="sxs-lookup"><span data-stu-id="d300b-229">You can see the p drop in throttled requests, as Event Hubs automatically scaled up to 3 throughput units.</span></span>

<span data-ttu-id="d300b-230">有趣的是，这对流分析作业中的 SU 利用率增长造成了负面影响。</span><span class="sxs-lookup"><span data-stu-id="d300b-230">Interestingly, this had the side effect of increasing the SU utilization in the Stream Analytics job.</span></span> <span data-ttu-id="d300b-231">通过限制，事件中心人为地降低了流分析作业的引入速率。</span><span class="sxs-lookup"><span data-stu-id="d300b-231">By throttling, Event Hubs was artificially reducing the ingestion rate for the Stream Analytics job.</span></span> <span data-ttu-id="d300b-232">解决一个性能瓶颈的同时却又揭露了另一个瓶颈，这种情况确实很常见。</span><span class="sxs-lookup"><span data-stu-id="d300b-232">It's actually common that resolving one performance bottleneck reveals another.</span></span> <span data-ttu-id="d300b-233">在这种情况下，为流分析作业分配更多的 SU 便可解决问题。</span><span class="sxs-lookup"><span data-stu-id="d300b-233">In this case, allocating additional SU for the Stream Analytics job resolved the issue.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="d300b-234">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="d300b-234">Deploy the solution</span></span>

<span data-ttu-id="d300b-235">若要部署并运行参考实现，请按 [GitHub 自述文件][github]中的步骤操作。</span><span class="sxs-lookup"><span data-stu-id="d300b-235">To the deploy and run the reference implementation, follow the steps in the [GitHub readme][github].</span></span>

## <a name="related-resources"></a><span data-ttu-id="d300b-236">相关资源</span><span class="sxs-lookup"><span data-stu-id="d300b-236">Related resources</span></span>

<span data-ttu-id="d300b-237">可以查看以下 [Azure 示例方案](/azure/architecture/example-scenario)，了解使用部分相同技术的具体解决方案：</span><span class="sxs-lookup"><span data-stu-id="d300b-237">You may wish to review the following [Azure example scenarios](/azure/architecture/example-scenario) that demonstrate specific solutions using some of the same technologies:</span></span>

- [<span data-ttu-id="d300b-238">建筑行业中的 IoT 和数据分析</span><span class="sxs-lookup"><span data-stu-id="d300b-238">IoT and data analytics in the construction industry</span></span>](/azure/architecture/example-scenario/data/big-data-with-iot)
- [<span data-ttu-id="d300b-239">实时欺诈检测</span><span class="sxs-lookup"><span data-stu-id="d300b-239">Real-time fraud detection</span></span>](/azure/architecture/example-scenario/data/fraud-detection)

<!-- links -->

[github]: https://github.com/mspnp/azure-stream-analytics-data-pipeline

