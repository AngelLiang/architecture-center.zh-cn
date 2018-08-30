---
title: 事件驱动的架构样式
description: 介绍 Azure 上事件驱动和 IoT 架构的优点、挑战和最佳做法
author: MikeWasson
ms.openlocfilehash: dbf6be5ed386d06f96c876993ad03e7cb0e3dded
ms.sourcegitcommit: 8ec48a0e2c080c9e2e0abbfdbc463622b28de2f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2018
ms.locfileid: "43016078"
---
# <a name="event-driven-architecture-style"></a><span data-ttu-id="8ca51-103">事件驱动的架构样式</span><span class="sxs-lookup"><span data-stu-id="8ca51-103">Event-driven architecture style</span></span>

<span data-ttu-id="8ca51-104">事件驱动的架构由生成事件流的事件生成者和侦听事件的事件使用者组成 。</span><span class="sxs-lookup"><span data-stu-id="8ca51-104">An event-driven architecture consists of **event producers** that generate a stream of events, and **event consumers** that listen for the events.</span></span> 

![](./images/event-driven.svg)

<span data-ttu-id="8ca51-105">事件可几乎实时发送，因此使用者可在事件发生时立即做出响应。</span><span class="sxs-lookup"><span data-stu-id="8ca51-105">Events are delivered in near real time, so consumers can respond immediately to events as they occur.</span></span> <span data-ttu-id="8ca51-106">生成者脱离使用者 &mdash; 生成者不知道哪个使用者正在侦听。</span><span class="sxs-lookup"><span data-stu-id="8ca51-106">Producers are decoupled from consumers &mdash; a producer doesn't know which consumers are listening.</span></span> <span data-ttu-id="8ca51-107">使用者之间也能彼此脱离，且每个使用者都能看到所有事件。</span><span class="sxs-lookup"><span data-stu-id="8ca51-107">Consumers are also decoupled from each other, and every consumer sees all of the events.</span></span> <span data-ttu-id="8ca51-108">这与[使用者竞争][competing-consumers]模式不同，在此模式中，使用者从队列中拉取消息，且消息仅处理一次（假设没有错误）。</span><span class="sxs-lookup"><span data-stu-id="8ca51-108">This differs from a [Competing Consumers][competing-consumers] pattern, where consumers pull messages from a queue and a message is processed just once (assuming no errors).</span></span> <span data-ttu-id="8ca51-109">在某些系统中（例如 IoT），必须大量引入事件。</span><span class="sxs-lookup"><span data-stu-id="8ca51-109">In some systems, such as IoT, events must be ingested at very high volumes.</span></span>

<span data-ttu-id="8ca51-110">事件驱动的架构可以使用发布/订阅模式或事件流模式。</span><span class="sxs-lookup"><span data-stu-id="8ca51-110">An event driven architecture can use a pub/sub model or an event stream model.</span></span> 

- <span data-ttu-id="8ca51-111">**发布/订阅**：消息传递基础结构会跟踪订阅。</span><span class="sxs-lookup"><span data-stu-id="8ca51-111">**Pub/sub**: The messaging infrastructure keeps track of subscriptions.</span></span> <span data-ttu-id="8ca51-112">事件发布后，它会将事件发送给每位订阅者。</span><span class="sxs-lookup"><span data-stu-id="8ca51-112">When an event is published, it sends the event to each subscriber.</span></span> <span data-ttu-id="8ca51-113">事件在接收后，便无法重播，新订阅者也看不见此事件。</span><span class="sxs-lookup"><span data-stu-id="8ca51-113">After an event is received, it cannot be replayed, and new subscribers do not see the event.</span></span> 

- <span data-ttu-id="8ca51-114">**事件流式处理**：事件会写入日志。</span><span class="sxs-lookup"><span data-stu-id="8ca51-114">**Event streaming**: Events are written to a log.</span></span> <span data-ttu-id="8ca51-115">事件是（在分区中）经过严格排序的，而且具有持久性。</span><span class="sxs-lookup"><span data-stu-id="8ca51-115">Events are strictly ordered (within a partition) and durable.</span></span> <span data-ttu-id="8ca51-116">客户端不会订阅流，但是客户端可以读取该流的任何部分。</span><span class="sxs-lookup"><span data-stu-id="8ca51-116">Clients don't subscribe to the stream, instead a client can read from any part of the stream.</span></span> <span data-ttu-id="8ca51-117">客户端负责提升它在流中的位置。</span><span class="sxs-lookup"><span data-stu-id="8ca51-117">The client is responsible for advancing its position in the stream.</span></span> <span data-ttu-id="8ca51-118">这意味着客户端可以随时加入，并可以重播事件。</span><span class="sxs-lookup"><span data-stu-id="8ca51-118">That means a client can join at any time, and can replay events.</span></span>

<span data-ttu-id="8ca51-119">在使用者端，有一些常见的变化：</span><span class="sxs-lookup"><span data-stu-id="8ca51-119">On the consumer side, there are some common variations:</span></span>

- <span data-ttu-id="8ca51-120">**简单事件处理**。</span><span class="sxs-lookup"><span data-stu-id="8ca51-120">**Simple event processing**.</span></span> <span data-ttu-id="8ca51-121">事件会立即触发使用者中的某项操作。</span><span class="sxs-lookup"><span data-stu-id="8ca51-121">An event immediately triggers an action in the consumer.</span></span> <span data-ttu-id="8ca51-122">例如，可以将 Azure Functions 与服务总线触发器配合使用，每当消息发布到服务总线主题后，函数便开始执行。</span><span class="sxs-lookup"><span data-stu-id="8ca51-122">For example, you could use Azure Functions with a Service Bus trigger, so that a function executes whenever a message is published to a Service Bus topic.</span></span>

- <span data-ttu-id="8ca51-123">**复杂事件处理**。</span><span class="sxs-lookup"><span data-stu-id="8ca51-123">**Complex event processing**.</span></span> <span data-ttu-id="8ca51-124">使用者使用 Azure 流分析或 Apache Storm 之类的技术处理一系列事件，寻找事件数据中的模式。</span><span class="sxs-lookup"><span data-stu-id="8ca51-124">A consumer processes a series of events, looking for patterns in the event data, using a technology such as Azure Stream Analytics or Apache Storm.</span></span> <span data-ttu-id="8ca51-125">例如，如果移动平均超过特定阈值，便可聚合在某个时间范围内从嵌入式设备读取的信息，并生成通知。</span><span class="sxs-lookup"><span data-stu-id="8ca51-125">For example, you could aggregate readings from an embedded device over a time window, and generate a notification if the moving average crosses a certain threshold.</span></span> 

- <span data-ttu-id="8ca51-126">**事件流处理**。</span><span class="sxs-lookup"><span data-stu-id="8ca51-126">**Event stream processing**.</span></span> <span data-ttu-id="8ca51-127">使用 Azure IoT 中心或 Apache Kafka 等数据流平台作为管道引入事件并将其馈送到流处理器。</span><span class="sxs-lookup"><span data-stu-id="8ca51-127">Use a data streaming platform, such as Azure IoT Hub or Apache Kafka, as a pipeline to ingest events and feed them to stream processors.</span></span> <span data-ttu-id="8ca51-128">此流处理器可处理或转换流。</span><span class="sxs-lookup"><span data-stu-id="8ca51-128">The stream processors act to process or transform the stream.</span></span> <span data-ttu-id="8ca51-129">不同应用程序子系统可能有多种流处理器。</span><span class="sxs-lookup"><span data-stu-id="8ca51-129">There may be multiple stream processors for different subsystems of the application.</span></span> <span data-ttu-id="8ca51-130">此方法非常适合 IoT 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="8ca51-130">This approach is a good fit for IoT workloads.</span></span>

<span data-ttu-id="8ca51-131">事件可能来源于系统之外，例如 IoT 解决方案中的物理设备。</span><span class="sxs-lookup"><span data-stu-id="8ca51-131">The source of the events may be external to the system, such as physical devices in an IoT solution.</span></span> <span data-ttu-id="8ca51-132">在这种情况下，系统必须能够以数据源需要的容量和吞吐量来引入数据。</span><span class="sxs-lookup"><span data-stu-id="8ca51-132">In that case, the system must be able to ingest the data at the volume and throughput that is required by the data source.</span></span>

<span data-ttu-id="8ca51-133">在上面的逻辑图中，每种类型的使用者都显示为单个框。</span><span class="sxs-lookup"><span data-stu-id="8ca51-133">In the logical diagram above, each type of consumer is shown as a single box.</span></span> <span data-ttu-id="8ca51-134">实际情况中通常有多个使用者实例，可避免使用者成为系统中的单点故障。</span><span class="sxs-lookup"><span data-stu-id="8ca51-134">In practice, it's common to have multiple instances of a consumer, to avoid having the consumer become a single point of failure in system.</span></span> <span data-ttu-id="8ca51-135">处理事件的容量和频率可能还需要多个实例。</span><span class="sxs-lookup"><span data-stu-id="8ca51-135">Multiple instances might also be necessary to handle the volume and frequency of events.</span></span> <span data-ttu-id="8ca51-136">此外，单个使用者可以处理多个线程上的事件。</span><span class="sxs-lookup"><span data-stu-id="8ca51-136">Also, a single consumer might process events on multiple threads.</span></span> <span data-ttu-id="8ca51-137">如果必须按照顺序处理事件，或者需要“恰一次”语义，这就会带来一些挑战。</span><span class="sxs-lookup"><span data-stu-id="8ca51-137">This can create challenges if events must be processed in order, or require exactly-once semantics.</span></span> <span data-ttu-id="8ca51-138">请参阅[尽量减少协调][minimize-coordination]。</span><span class="sxs-lookup"><span data-stu-id="8ca51-138">See [Minimize Coordination][minimize-coordination].</span></span> 

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="8ca51-139">何时使用此架构</span><span class="sxs-lookup"><span data-stu-id="8ca51-139">When to use this architecture</span></span>

- <span data-ttu-id="8ca51-140">多个子系统必须处理相同的事件。</span><span class="sxs-lookup"><span data-stu-id="8ca51-140">Multiple subsystems must process the same events.</span></span> 
- <span data-ttu-id="8ca51-141">延迟时间最短的实时处理。</span><span class="sxs-lookup"><span data-stu-id="8ca51-141">Real-time processing with minimum time lag.</span></span>
- <span data-ttu-id="8ca51-142">复杂事件处理，如模式匹配或时间范围内的聚合。</span><span class="sxs-lookup"><span data-stu-id="8ca51-142">Complex event processing, such as pattern matching or aggregation over time windows.</span></span>
- <span data-ttu-id="8ca51-143">大量且快速的数据，如 IoT。</span><span class="sxs-lookup"><span data-stu-id="8ca51-143">High volume and high velocity of data, such as IoT.</span></span>

## <a name="benefits"></a><span data-ttu-id="8ca51-144">优点</span><span class="sxs-lookup"><span data-stu-id="8ca51-144">Benefits</span></span>

- <span data-ttu-id="8ca51-145">生成者和使用者相脱离。</span><span class="sxs-lookup"><span data-stu-id="8ca51-145">Producers and consumers are decoupled.</span></span>
- <span data-ttu-id="8ca51-146">没有点到点的集成。</span><span class="sxs-lookup"><span data-stu-id="8ca51-146">No point-to point-integrations.</span></span> <span data-ttu-id="8ca51-147">容易向系统添加新使用者。</span><span class="sxs-lookup"><span data-stu-id="8ca51-147">It's easy to add new consumers to the system.</span></span>
- <span data-ttu-id="8ca51-148">使用者在事件发生时便可立即响应。</span><span class="sxs-lookup"><span data-stu-id="8ca51-148">Consumers can respond to events immediately as they arrive.</span></span> 
- <span data-ttu-id="8ca51-149">具有高缩放性，分布广泛。</span><span class="sxs-lookup"><span data-stu-id="8ca51-149">Highly scalable and distributed.</span></span> 
- <span data-ttu-id="8ca51-150">子系统具有独立的事件流视图。</span><span class="sxs-lookup"><span data-stu-id="8ca51-150">Subsystems have independent views of the event stream.</span></span>

## <a name="challenges"></a><span data-ttu-id="8ca51-151">挑战</span><span class="sxs-lookup"><span data-stu-id="8ca51-151">Challenges</span></span>

- <span data-ttu-id="8ca51-152">有保证的传递。</span><span class="sxs-lookup"><span data-stu-id="8ca51-152">Guaranteed delivery.</span></span> <span data-ttu-id="8ca51-153">在某些系统中，尤其是在 IoT 方案中，保证数据传递至关重要。</span><span class="sxs-lookup"><span data-stu-id="8ca51-153">In some systems, especially in IoT scenarios, it's crucial to guarantee that events are delivered.</span></span>
- <span data-ttu-id="8ca51-154">按顺序或者“恰一次”处理事件。</span><span class="sxs-lookup"><span data-stu-id="8ca51-154">Processing events in order or exactly once.</span></span> <span data-ttu-id="8ca51-155">每种使用者类型通常都在多个实例中运行，以提供复原能力和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="8ca51-155">Each consumer type typically runs in multiple instances, for resiliency and scalability.</span></span> <span data-ttu-id="8ca51-156">如果必须按顺序（在使用者类型中）处理事件，或处理逻辑不是幂等的，就会带来挑战。</span><span class="sxs-lookup"><span data-stu-id="8ca51-156">This can create a challenge if the events must be processed in order (within a consumer type), or if the processing logic is not idempotent.</span></span>

 <!-- links -->

[competing-consumers]: ../../patterns/competing-consumers.md
[minimize-coordination]: ../design-principles/minimize-coordination.md


