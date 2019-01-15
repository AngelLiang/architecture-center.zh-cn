---
title: 使用 Azure Functions 进行无服务器事件处理
titleSuffix: Azure Reference Architectures
description: 使用 Azure Functions 进行的无服务器事件引入和处理的参考体系结构。
author: MikeWasson
ms.date: 10/16/2018
ms.custom: seodec18
ms.openlocfilehash: 23edc69e52981cfd15717b491875b34ed025958a
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111387"
---
# <a name="serverless-event-processing-using-azure-functions"></a><span data-ttu-id="c0094-103">使用 Azure Functions 进行无服务器事件处理</span><span class="sxs-lookup"><span data-stu-id="c0094-103">Serverless event processing using Azure Functions</span></span>

<span data-ttu-id="c0094-104">本参考体系结构演示一个可以引入数据流、处理数据，并将结果写入后端数据库的事件驱动式[无服务器](https://azure.microsoft.com/solutions/serverless/)体系结构。</span><span class="sxs-lookup"><span data-stu-id="c0094-104">This reference architecture shows a [serverless](https://azure.microsoft.com/solutions/serverless/), event-driven architecture that ingests a stream of data, processes the data, and writes the results to a back-end database.</span></span> <span data-ttu-id="c0094-105">[GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="c0094-105">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![使用 Azure Functions 进行的无服务器事件处理的参考体系结构](./_images/serverless-event-processing.png)

## <a name="architecture"></a><span data-ttu-id="c0094-107">体系结构</span><span class="sxs-lookup"><span data-stu-id="c0094-107">Architecture</span></span>

<span data-ttu-id="c0094-108">**事件中心**引入数据流。</span><span class="sxs-lookup"><span data-stu-id="c0094-108">**Event Hubs** ingests the data stream.</span></span> <span data-ttu-id="c0094-109">[事件中心][eh]面向高吞吐量数据流方案。</span><span class="sxs-lookup"><span data-stu-id="c0094-109">[Event Hubs][eh] is designed for high-throughput data streaming scenarios.</span></span>

> [!NOTE]
> <span data-ttu-id="c0094-110">对于 IoT 方案，我们建议使用 IoT 中心。</span><span class="sxs-lookup"><span data-stu-id="c0094-110">For IoT scenarios, we recommend IoT Hub.</span></span> <span data-ttu-id="c0094-111">IoT 中心包含与 Azure 事件中心 API 兼容的内置终结点，因此，无需对后端处理进行重大更改，就能在本体系结构中使用任一服务。</span><span class="sxs-lookup"><span data-stu-id="c0094-111">IoT Hub has a built-in endpoint that’s compatible with the Azure Event Hubs API, so you can use either service in this architecture with no major changes in the backend processing.</span></span> <span data-ttu-id="c0094-112">有关详细信息，请参阅[将 IoT 设备连接到 Azure：IoT 中心和事件中心][iot]。</span><span class="sxs-lookup"><span data-stu-id="c0094-112">For more information, see [Connecting IoT Devices to Azure: IoT Hub and Event Hubs][iot].</span></span>

<span data-ttu-id="c0094-113">**函数应用**。</span><span class="sxs-lookup"><span data-stu-id="c0094-113">**Function App**.</span></span> <span data-ttu-id="c0094-114">[Azure Functions][functions] 是一个无服务器计算选项。</span><span class="sxs-lookup"><span data-stu-id="c0094-114">[Azure Functions][functions] is a serverless compute option.</span></span> <span data-ttu-id="c0094-115">它使用事件驱动的模型，其中的一段代码（“函数”）由触发器调用。</span><span class="sxs-lookup"><span data-stu-id="c0094-115">It uses an event-driven model, where a piece of code (a “function”) is invoked by a trigger.</span></span> <span data-ttu-id="c0094-116">在本体系结构中，当事件抵达事件中心时，会触发一个函数，该函数处理事件并将结果写入存储。</span><span class="sxs-lookup"><span data-stu-id="c0094-116">In this architecture, when events arrive at Event Hubs, they trigger a function that processes the events and writes the results to storage.</span></span>

<span data-ttu-id="c0094-117">函数应用适合用于处理来自事件中心的单个记录。</span><span class="sxs-lookup"><span data-stu-id="c0094-117">Function Apps are suitable for processing individual records from Event Hubs.</span></span> <span data-ttu-id="c0094-118">对于更复杂的流处理方案，请考虑结合 Azure Databricks 或 Azure 流分析使用 Apache Spark。</span><span class="sxs-lookup"><span data-stu-id="c0094-118">For more complex stream processing scenarios, consider Apache Spark using Azure Databricks, or Azure Stream Analytics.</span></span>

<span data-ttu-id="c0094-119">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="c0094-119">**Cosmos DB**.</span></span> <span data-ttu-id="c0094-120">[Cosmos DB][cosmosdb] 是一个多模型数据库服务。</span><span class="sxs-lookup"><span data-stu-id="c0094-120">[Cosmos DB][cosmosdb] is a multi-model database service.</span></span> <span data-ttu-id="c0094-121">对于此方案，事件处理函数将使用 Cosmos DB [SQL API][cosmosdb-sql] 存储 JSON 记录。</span><span class="sxs-lookup"><span data-stu-id="c0094-121">For this scenario, the event-processing function stores JSON records, using the Cosmos DB [SQL API][cosmosdb-sql].</span></span>

<span data-ttu-id="c0094-122">**队列存储**。</span><span class="sxs-lookup"><span data-stu-id="c0094-122">**Queue storage**.</span></span> <span data-ttu-id="c0094-123">[队列存储][queue]用于将消息加入死信队列。</span><span class="sxs-lookup"><span data-stu-id="c0094-123">[Queue storage][queue] is used for dead letter messages.</span></span> <span data-ttu-id="c0094-124">如果处理事件时出错，函数会将事件数据存储到死信队列供稍后处理。</span><span class="sxs-lookup"><span data-stu-id="c0094-124">If an error occurs while processing an event, the function stores the event data in a dead letter queue for later processing.</span></span> <span data-ttu-id="c0094-125">有关详细信息，请参阅[复原注意事项](#resiliency-considerations)。</span><span class="sxs-lookup"><span data-stu-id="c0094-125">For more information, see [Resiliency Considerations](#resiliency-considerations).</span></span>

<span data-ttu-id="c0094-126">**Azure Monitor**。</span><span class="sxs-lookup"><span data-stu-id="c0094-126">**Azure Monitor**.</span></span> <span data-ttu-id="c0094-127">[Monitor][monitor] 收集解决方案中部署的 Azure 服务的性能指标。</span><span class="sxs-lookup"><span data-stu-id="c0094-127">[Monitor][monitor] collects performance metrics about the Azure services deployed in the solution.</span></span> <span data-ttu-id="c0094-128">在仪表板中可视化这些指标可以洞察解决方案的运行状况。</span><span class="sxs-lookup"><span data-stu-id="c0094-128">By visualizing these in a dashboard, you can get visibility into the health of the solution.</span></span>

<span data-ttu-id="c0094-129">**Azure Pipelines**。</span><span class="sxs-lookup"><span data-stu-id="c0094-129">**Azure Pipelines**.</span></span> <span data-ttu-id="c0094-130">[Pipelines][pipelines] 是用于生成、测试和部署应用程序的持续集成 (CI) 和持续交付 (CD) 服务。</span><span class="sxs-lookup"><span data-stu-id="c0094-130">[Pipelines][pipelines] is a continuous integration (CI) and continuous delivery (CD) service that builds, tests, and deploys the application.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="c0094-131">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="c0094-131">Scalability considerations</span></span>

### <a name="event-hubs"></a><span data-ttu-id="c0094-132">事件中心</span><span class="sxs-lookup"><span data-stu-id="c0094-132">Event Hubs</span></span>

<span data-ttu-id="c0094-133">事件中心的吞吐量容量以[吞吐量单位][eh-throughput]来度量。</span><span class="sxs-lookup"><span data-stu-id="c0094-133">The throughput capacity of Event Hubs is measured in [throughput units][eh-throughput].</span></span> <span data-ttu-id="c0094-134">可以通过启用[自动扩充][eh-autoscale]来自动缩放事件中心。自动扩充可以根据流量，最高将吞吐量单位自动扩展到配置的上限。</span><span class="sxs-lookup"><span data-stu-id="c0094-134">You can autoscale an event hub by enabling [auto-inflate][eh-autoscale], which automatically scales the throughput units based on traffic, up to a configured maximum.</span></span>

<span data-ttu-id="c0094-135">函数应用中的[事件中心触发器][eh-trigger]根据事件中心内的分区数进行缩放。</span><span class="sxs-lookup"><span data-stu-id="c0094-135">The [Event Hub trigger][eh-trigger] in the function app scales according to the number of partitions in the event hub.</span></span> <span data-ttu-id="c0094-136">每次为每个分区分配一个函数实例。</span><span class="sxs-lookup"><span data-stu-id="c0094-136">Each partition is assigned one function instance at a time.</span></span> <span data-ttu-id="c0094-137">若要最大程度地提高吞吐量，请分批接收事件，而不要逐个接收。</span><span class="sxs-lookup"><span data-stu-id="c0094-137">To maximize throughput, receive the events in a batch, instead of one at a time.</span></span>

### <a name="cosmos-db"></a><span data-ttu-id="c0094-138">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="c0094-138">Cosmos DB</span></span>

<span data-ttu-id="c0094-139">Cosmos DB 的吞吐量容量以[请求单位][ru] (RU) 来度量。</span><span class="sxs-lookup"><span data-stu-id="c0094-139">Throughput capacity for Cosmos DB is measured in [Request Units][ru] (RU).</span></span> <span data-ttu-id="c0094-140">若要将某个 Cosmos DB 容器扩展到超过 10,000 RU，必须在创建该容器时指定[分区键][partition-key]，并在创建的每个文档中包含该分区键。</span><span class="sxs-lookup"><span data-stu-id="c0094-140">In order to scale a Cosmos DB container past 10,000 RU, you must specify a [partition key][partition-key] when you create the container, and include the partition key in every document that you create.</span></span>

<span data-ttu-id="c0094-141">下面是适当分区键的一些特征：</span><span class="sxs-lookup"><span data-stu-id="c0094-141">Here are some characteristics of a good partition key:</span></span>

- <span data-ttu-id="c0094-142">键值空间较大。</span><span class="sxs-lookup"><span data-stu-id="c0094-142">The key value space is large.</span></span>
- <span data-ttu-id="c0094-143">为每个键值平均分配读取/写入，避免出现热键。</span><span class="sxs-lookup"><span data-stu-id="c0094-143">There will be an even distribution of reads/writes per key value, avoiding hot keys.</span></span>
- <span data-ttu-id="c0094-144">为任何一个键值存储的数据上限不超过最大物理分区大小 (10 GB)。</span><span class="sxs-lookup"><span data-stu-id="c0094-144">The maximum data stored for any single key value will not exceed the maximum physical partition size (10 GB).</span></span>
- <span data-ttu-id="c0094-145">文档的分区键不会更改。</span><span class="sxs-lookup"><span data-stu-id="c0094-145">The partition key for a document won't change.</span></span> <span data-ttu-id="c0094-146">无法更新现有文档中的分区键。</span><span class="sxs-lookup"><span data-stu-id="c0094-146">You can't update the partition key on an existing document.</span></span>

<span data-ttu-id="c0094-147">在本参考体系结构的方案中，函数针对发送数据的每个设备正好存储一个文档。</span><span class="sxs-lookup"><span data-stu-id="c0094-147">In the scenario for this reference architecture, the function stores exactly one document per device that is sending data.</span></span> <span data-ttu-id="c0094-148">函数使用 upsert 操作持续以最新的设备状态更新文档。</span><span class="sxs-lookup"><span data-stu-id="c0094-148">The function continually updates the documents with latest device status, using an upsert operation.</span></span> <span data-ttu-id="c0094-149">设备 ID 非常适合在此方案中用作分区键，因为写入操作将在不同的键之间平均分配，并且每个分区的大小严格受限，因为每个键值对应单个文档。</span><span class="sxs-lookup"><span data-stu-id="c0094-149">Device ID is a good partition key for this scenario, because writes will be evenly distributed across the keys, and the size of each partition will be strictly bounded, because there is a single document for each key value.</span></span> <span data-ttu-id="c0094-150">有关分区键的详细信息，请参阅 [Azure Cosmos DB 中的分区和缩放][cosmosdb-scale]。</span><span class="sxs-lookup"><span data-stu-id="c0094-150">For more information about partition keys, see [Partition and scale in Azure Cosmos DB][cosmosdb-scale].</span></span>

## <a name="resiliency-considerations"></a><span data-ttu-id="c0094-151">复原注意事项</span><span class="sxs-lookup"><span data-stu-id="c0094-151">Resiliency considerations</span></span>

<span data-ttu-id="c0094-152">配合 Functions 使用事件中心触发器时，可以捕获处理循环中的异常。</span><span class="sxs-lookup"><span data-stu-id="c0094-152">When using the Event Hubs trigger with Functions, catch exceptions within your processing loop.</span></span> <span data-ttu-id="c0094-153">如果发生未经处理的异常，Functions 运行时不会重试消息。</span><span class="sxs-lookup"><span data-stu-id="c0094-153">If an unhandled exception occurs, the Functions runtime does not retry the messages.</span></span> <span data-ttu-id="c0094-154">如果无法处理某个消息，会将该消息放入死信队列。</span><span class="sxs-lookup"><span data-stu-id="c0094-154">If a message cannot be processed, put the message into a dead letter queue.</span></span> <span data-ttu-id="c0094-155">使用带外进程来检查消息并确定纠正措施。</span><span class="sxs-lookup"><span data-stu-id="c0094-155">Use an out-of-band process to examine the messages and determine corrective action.</span></span>

<span data-ttu-id="c0094-156">以下代码演示引入函数如何捕获异常，并将未经处理的消息放入死信队列。</span><span class="sxs-lookup"><span data-stu-id="c0094-156">The following code shows how the ingestion function catches exceptions and puts unprocessed messages onto a dead letter queue.</span></span>

```csharp
[FunctionName("RawTelemetryFunction")]
[StorageAccount("DeadLetterStorage")]
public static async Task RunAsync(
    [EventHubTrigger("%EventHubName%", Connection = "EventHubConnection", ConsumerGroup ="%EventHubConsumerGroup%")]EventData[] messages,
    [Queue("deadletterqueue")] IAsyncCollector<DeadLetterMessage> deadLetterMessages,
    ILogger logger)
{
    foreach (var message in messages)
    {
        DeviceState deviceState = null;

        try
        {
            deviceState = telemetryProcessor.Deserialize(message.Body.Array, logger);
        }
        catch (Exception ex)
        {
            logger.LogError(ex, "Error deserializing message", message.SystemProperties.PartitionKey, message.SystemProperties.SequenceNumber);
            await deadLetterMessages.AddAsync(new DeadLetterMessage { Issue = ex.Message, EventData = message });
        }

        try
        {
            await stateChangeProcessor.UpdateState(deviceState, logger);
        }
        catch (Exception ex)
        {
            logger.LogError(ex, "Error updating status document", deviceState);
            await deadLetterMessages.AddAsync(new DeadLetterMessage { Issue = ex.Message, EventData = message, DeviceState = deviceState });
        }
    }
}
```

<span data-ttu-id="c0094-157">请注意，函数使用[队列存储输出绑定][queue-binding]将项放入队列。</span><span class="sxs-lookup"><span data-stu-id="c0094-157">Notice that the function uses the [Queue storage output binding][queue-binding] to put items in the queue.</span></span>

<span data-ttu-id="c0094-158">上面所示的代码还会将异常记录到 Application Insights。</span><span class="sxs-lookup"><span data-stu-id="c0094-158">The code shown above also logs exceptions to Application Insights.</span></span> <span data-ttu-id="c0094-159">可以使用分区键和序号将死信消息与日志中的异常相关联。</span><span class="sxs-lookup"><span data-stu-id="c0094-159">You can use the partition key and sequence number to correlate dead letter messages with the exceptions in the logs.</span></span>

<span data-ttu-id="c0094-160">死信队列中的消息应该包含足够的信息，使你能够了解错误的上下文。</span><span class="sxs-lookup"><span data-stu-id="c0094-160">Messages in the dead letter queue should have enough information so that you can understand the context of error.</span></span> <span data-ttu-id="c0094-161">在此示例中，`DeadLetterMessage` 类包含异常消息、原始事件数据，以及反序列化的事件消息（如果有）。</span><span class="sxs-lookup"><span data-stu-id="c0094-161">In this example, the `DeadLetterMessage` class contains the exception message, the original event data, and the deserialized event message (if available).</span></span>

```csharp
public class DeadLetterMessage
{
    public string Issue { get; set; }
    public EventData EventData { get; set; }
    public DeviceState DeviceState { get; set; }
}
```

<span data-ttu-id="c0094-162">使用 [Azure Monitor][monitor] 监视事件中心。</span><span class="sxs-lookup"><span data-stu-id="c0094-162">Use [Azure Monitor][monitor] to monitor the event hub.</span></span> <span data-ttu-id="c0094-163">如果显示了输入但未显示输出，则表示消息尚未处理。</span><span class="sxs-lookup"><span data-stu-id="c0094-163">If you see there is input but no output, it means that messages are not being processed.</span></span> <span data-ttu-id="c0094-164">在此情况下，请转到 [Log Analytics][log-analytics] 并查找异常或其他错误。</span><span class="sxs-lookup"><span data-stu-id="c0094-164">In that case, go into [Log Analytics][log-analytics] and look for exceptions or other errors.</span></span>

## <a name="disaster-recovery-considerations"></a><span data-ttu-id="c0094-165">灾难恢复注意事项</span><span class="sxs-lookup"><span data-stu-id="c0094-165">Disaster recovery considerations</span></span>

<span data-ttu-id="c0094-166">下面所示的部署驻留在单个 Azure 区域中。</span><span class="sxs-lookup"><span data-stu-id="c0094-166">The deployment shown here resides in a single Azure region.</span></span> <span data-ttu-id="c0094-167">更具弹性的灾难恢复方法利用不同服务中的异地分发功能：</span><span class="sxs-lookup"><span data-stu-id="c0094-167">For a more resilient approach to disaster-recovery, take advantage of geo-distribution features in the various services:</span></span>

- <span data-ttu-id="c0094-168">**事件中心**。</span><span class="sxs-lookup"><span data-stu-id="c0094-168">**Event Hubs**.</span></span> <span data-ttu-id="c0094-169">创建两个事件中心命名空间：主要（主动）命名空间和次要（被动）命名空间。</span><span class="sxs-lookup"><span data-stu-id="c0094-169">Create two Event Hubs namespaces, a primary (active) namespace and a secondary (passive) namespace.</span></span> <span data-ttu-id="c0094-170">除非故障转移到次要命名空间，否则消息将自动路由到主动命名空间。</span><span class="sxs-lookup"><span data-stu-id="c0094-170">Messages are automatically routed to the active namespace unless you fail over to the secondary namespace.</span></span> <span data-ttu-id="c0094-171">有关详细信息，请参阅 [Azure 事件中心异地灾难恢复][eh-dr]。</span><span class="sxs-lookup"><span data-stu-id="c0094-171">For more information, see [Azure Event Hubs Geo-disaster recovery][eh-dr].</span></span>

- <span data-ttu-id="c0094-172">**函数应用**。</span><span class="sxs-lookup"><span data-stu-id="c0094-172">**Function App**.</span></span> <span data-ttu-id="c0094-173">部署另一个函数应用，用于等待从次要事件中心命名空间读取数据。</span><span class="sxs-lookup"><span data-stu-id="c0094-173">Deploy a second function app that is waiting to read from the secondary Event Hubs namespace.</span></span> <span data-ttu-id="c0094-174">此函数将数据写入死信队列的辅助存储帐户。</span><span class="sxs-lookup"><span data-stu-id="c0094-174">This function writes to a secondary storage account for dead letter queue.</span></span>

- <span data-ttu-id="c0094-175">**Cosmos DB**。</span><span class="sxs-lookup"><span data-stu-id="c0094-175">**Cosmos DB**.</span></span> <span data-ttu-id="c0094-176">Cosmos DB 支持[多主数据库区域][cosmosdb-geo]，因此可以向添加到 Cosmos DB 帐户中的任何区域写入数据。</span><span class="sxs-lookup"><span data-stu-id="c0094-176">Cosmos DB supports [multiple master regions][cosmosdb-geo], which enables writes to any region that you add to your Cosmos DB account.</span></span> <span data-ttu-id="c0094-177">如果未启用多主数据库，仍可以故障转移主要写入区域。</span><span class="sxs-lookup"><span data-stu-id="c0094-177">If you don’t enable multi-master, you can still fail over the primary write region.</span></span> <span data-ttu-id="c0094-178">Cosmos DB 客户端 SDK 和 Azure 函数绑定会自动处理故障转移，因此你无需更新任何应用程序配置设置。</span><span class="sxs-lookup"><span data-stu-id="c0094-178">The Cosmos DB client SDKs and the Azure Function bindings automatically handle the failover, so you don’t need to update any application configuration settings.</span></span>

- <span data-ttu-id="c0094-179">**Azure 存储**。</span><span class="sxs-lookup"><span data-stu-id="c0094-179">**Azure Storage**.</span></span> <span data-ttu-id="c0094-180">将 [RA-GRS][ra-grs] 存储用于死信队列。</span><span class="sxs-lookup"><span data-stu-id="c0094-180">Use [RA-GRS][ra-grs] storage for the dead letter queue.</span></span> <span data-ttu-id="c0094-181">这会在另一个区域中创建只读副本。</span><span class="sxs-lookup"><span data-stu-id="c0094-181">This creates a read-only replica in another region.</span></span> <span data-ttu-id="c0094-182">如果主要区域不可用，可以读取当前位于队列中的项。</span><span class="sxs-lookup"><span data-stu-id="c0094-182">If the primary region becomes unavailable, you can read the items currently in the queue.</span></span> <span data-ttu-id="c0094-183">此外，在次要区域中预配另一个存储帐户，在故障转移后，函数可将数据写入该存储帐户。</span><span class="sxs-lookup"><span data-stu-id="c0094-183">In addition, provision another storage account in the secondary region that the function can write to after a fail-over.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="c0094-184">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="c0094-184">Deploy the solution</span></span>

<span data-ttu-id="c0094-185">若要部署此参考体系结构，请查看 [GitHub 自述文件][readme]。</span><span class="sxs-lookup"><span data-stu-id="c0094-185">To deploy this reference architecture, view the [GitHub readme][readme].</span></span>

<!-- links -->

[cosmosdb]: /azure/cosmos-db/introduction
[cosmosdb-geo]: /azure/cosmos-db/distribute-data-globally
[cosmosdb-scale]: /azure/cosmos-db/partition-data
[cosmosdb-sql]: /azure/cosmos-db/sql-api-introduction
[eh]: /azure/event-hubs/
[eh-autoscale]: /azure/event-hubs/event-hubs-auto-inflate
[eh-dr]: /azure/event-hubs/event-hubs-geo-dr
[eh-throughput]: /azure/event-hubs/event-hubs-features#throughput-units
[eh-trigger]: /azure/azure-functions/functions-bindings-event-hubs
[functions]: /azure/azure-functions/functions-overview
[iot]: /azure/iot-hub/iot-hub-compare-event-hubs
[log-analytics]: /azure/log-analytics/log-analytics-queries
[monitor]: /azure/azure-monitor/overview
[partition-key]: /azure/cosmos-db/partition-data
[pipelines]: /azure/devops/pipelines/index
[queue]: /azure/storage/queues/storage-queues-introduction
[queue-binding]: /azure/azure-functions/functions-bindings-storage-queue#output
[ra-grs]: /azure/storage/common/storage-redundancy-grs
[ru]: /azure/cosmos-db/request-units

[github]: https://github.com/mspnp/serverless-reference-implementation
[readme]: https://github.com/mspnp/serverless-reference-implementation/blob/master/README.md
