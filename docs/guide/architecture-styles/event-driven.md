---
title: 事件驱动的架构样式
titleSuffix: Azure Application Architecture Guide
description: 介绍 Azure 上事件驱动体系结构和 IoT 体系结构的优点、挑战和最佳做法。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: b83a919e4ccd41d20b508b10604365a0b4ca90af
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245638"
---
# <a name="event-driven-architecture-style"></a><span data-ttu-id="73f64-103">事件驱动的架构样式</span><span class="sxs-lookup"><span data-stu-id="73f64-103">Event-driven architecture style</span></span>

<span data-ttu-id="73f64-104">事件驱动的架构由生成事件流的事件生成者和侦听事件的事件使用者组成 。</span><span class="sxs-lookup"><span data-stu-id="73f64-104">An event-driven architecture consists of **event producers** that generate a stream of events, and **event consumers** that listen for the events.</span></span>

![事件驱动体系结构样式图](./images/event-driven.svg)

<span data-ttu-id="73f64-106">事件可几乎实时发送，因此使用者可在事件发生时立即做出响应。</span><span class="sxs-lookup"><span data-stu-id="73f64-106">Events are delivered in near real time, so consumers can respond immediately to events as they occur.</span></span> <span data-ttu-id="73f64-107">生成者脱离使用者 &mdash; 生成者不知道哪个使用者正在侦听。</span><span class="sxs-lookup"><span data-stu-id="73f64-107">Producers are decoupled from consumers &mdash; a producer doesn't know which consumers are listening.</span></span> <span data-ttu-id="73f64-108">使用者之间也能彼此脱离，且每个使用者都能看到所有事件。</span><span class="sxs-lookup"><span data-stu-id="73f64-108">Consumers are also decoupled from each other, and every consumer sees all of the events.</span></span> <span data-ttu-id="73f64-109">这与[使用者竞争][competing-consumers]模式不同，在此模式中，使用者从队列中拉取消息，且消息仅处理一次（假设没有错误）。</span><span class="sxs-lookup"><span data-stu-id="73f64-109">This differs from a [Competing Consumers][competing-consumers] pattern, where consumers pull messages from a queue and a message is processed just once (assuming no errors).</span></span> <span data-ttu-id="73f64-110">在某些系统中（例如 IoT），必须大量引入事件。</span><span class="sxs-lookup"><span data-stu-id="73f64-110">In some systems, such as IoT, events must be ingested at very high volumes.</span></span>

<span data-ttu-id="73f64-111">事件驱动的架构可以使用发布/订阅模式或事件流模式。</span><span class="sxs-lookup"><span data-stu-id="73f64-111">An event driven architecture can use a pub/sub model or an event stream model.</span></span>

- <span data-ttu-id="73f64-112">**发布/订阅**：消息传递基础结构会跟踪订阅。</span><span class="sxs-lookup"><span data-stu-id="73f64-112">**Pub/sub**: The messaging infrastructure keeps track of subscriptions.</span></span> <span data-ttu-id="73f64-113">事件发布后，它会将事件发送给每位订阅者。</span><span class="sxs-lookup"><span data-stu-id="73f64-113">When an event is published, it sends the event to each subscriber.</span></span> <span data-ttu-id="73f64-114">事件在接收后，便无法重播，新订阅者也看不见此事件。</span><span class="sxs-lookup"><span data-stu-id="73f64-114">After an event is received, it cannot be replayed, and new subscribers do not see the event.</span></span>

- <span data-ttu-id="73f64-115">**事件流式处理**：事件会写入日志。</span><span class="sxs-lookup"><span data-stu-id="73f64-115">**Event streaming**: Events are written to a log.</span></span> <span data-ttu-id="73f64-116">事件是（在分区中）经过严格排序的，而且具有持久性。</span><span class="sxs-lookup"><span data-stu-id="73f64-116">Events are strictly ordered (within a partition) and durable.</span></span> <span data-ttu-id="73f64-117">客户端不会订阅流，但是客户端可以读取该流的任何部分。</span><span class="sxs-lookup"><span data-stu-id="73f64-117">Clients don't subscribe to the stream, instead a client can read from any part of the stream.</span></span> <span data-ttu-id="73f64-118">客户端负责提升它在流中的位置。</span><span class="sxs-lookup"><span data-stu-id="73f64-118">The client is responsible for advancing its position in the stream.</span></span> <span data-ttu-id="73f64-119">这意味着客户端可以随时加入，并可以重播事件。</span><span class="sxs-lookup"><span data-stu-id="73f64-119">That means a client can join at any time, and can replay events.</span></span>

<span data-ttu-id="73f64-120">在使用者端，有一些常见的变化：</span><span class="sxs-lookup"><span data-stu-id="73f64-120">On the consumer side, there are some common variations:</span></span>

- <span data-ttu-id="73f64-121">**简单事件处理**。</span><span class="sxs-lookup"><span data-stu-id="73f64-121">**Simple event processing**.</span></span> <span data-ttu-id="73f64-122">事件会立即触发使用者中的某项操作。</span><span class="sxs-lookup"><span data-stu-id="73f64-122">An event immediately triggers an action in the consumer.</span></span> <span data-ttu-id="73f64-123">例如，可以将 Azure Functions 与服务总线触发器配合使用，每当消息发布到服务总线主题后，函数便开始执行。</span><span class="sxs-lookup"><span data-stu-id="73f64-123">For example, you could use Azure Functions with a Service Bus trigger, so that a function executes whenever a message is published to a Service Bus topic.</span></span>

- <span data-ttu-id="73f64-124">**复杂事件处理**。</span><span class="sxs-lookup"><span data-stu-id="73f64-124">**Complex event processing**.</span></span> <span data-ttu-id="73f64-125">使用者使用 Azure 流分析或 Apache Storm 之类的技术处理一系列事件，寻找事件数据中的模式。</span><span class="sxs-lookup"><span data-stu-id="73f64-125">A consumer processes a series of events, looking for patterns in the event data, using a technology such as Azure Stream Analytics or Apache Storm.</span></span> <span data-ttu-id="73f64-126">例如，如果移动平均超过特定阈值，便可聚合在某个时间范围内从嵌入式设备读取的信息，并生成通知。</span><span class="sxs-lookup"><span data-stu-id="73f64-126">For example, you could aggregate readings from an embedded device over a time window, and generate a notification if the moving average crosses a certain threshold.</span></span>

- <span data-ttu-id="73f64-127">**事件流处理**。</span><span class="sxs-lookup"><span data-stu-id="73f64-127">**Event stream processing**.</span></span> <span data-ttu-id="73f64-128">使用 Azure IoT 中心或 Apache Kafka 等数据流平台作为管道引入事件并将其馈送到流处理器。</span><span class="sxs-lookup"><span data-stu-id="73f64-128">Use a data streaming platform, such as Azure IoT Hub or Apache Kafka, as a pipeline to ingest events and feed them to stream processors.</span></span> <span data-ttu-id="73f64-129">此流处理器可处理或转换流。</span><span class="sxs-lookup"><span data-stu-id="73f64-129">The stream processors act to process or transform the stream.</span></span> <span data-ttu-id="73f64-130">不同应用程序子系统可能有多种流处理器。</span><span class="sxs-lookup"><span data-stu-id="73f64-130">There may be multiple stream processors for different subsystems of the application.</span></span> <span data-ttu-id="73f64-131">此方法非常适合 IoT 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="73f64-131">This approach is a good fit for IoT workloads.</span></span>

<span data-ttu-id="73f64-132">事件可能来源于系统之外，例如 IoT 解决方案中的物理设备。</span><span class="sxs-lookup"><span data-stu-id="73f64-132">The source of the events may be external to the system, such as physical devices in an IoT solution.</span></span> <span data-ttu-id="73f64-133">在这种情况下，系统必须能够以数据源需要的容量和吞吐量来引入数据。</span><span class="sxs-lookup"><span data-stu-id="73f64-133">In that case, the system must be able to ingest the data at the volume and throughput that is required by the data source.</span></span>

<span data-ttu-id="73f64-134">在上面的逻辑图中，每种类型的使用者都显示为单个框。</span><span class="sxs-lookup"><span data-stu-id="73f64-134">In the logical diagram above, each type of consumer is shown as a single box.</span></span> <span data-ttu-id="73f64-135">实际情况中通常有多个使用者实例，可避免使用者成为系统中的单点故障。</span><span class="sxs-lookup"><span data-stu-id="73f64-135">In practice, it's common to have multiple instances of a consumer, to avoid having the consumer become a single point of failure in system.</span></span> <span data-ttu-id="73f64-136">处理事件的容量和频率可能还需要多个实例。</span><span class="sxs-lookup"><span data-stu-id="73f64-136">Multiple instances might also be necessary to handle the volume and frequency of events.</span></span> <span data-ttu-id="73f64-137">此外，单个使用者可以处理多个线程上的事件。</span><span class="sxs-lookup"><span data-stu-id="73f64-137">Also, a single consumer might process events on multiple threads.</span></span> <span data-ttu-id="73f64-138">如果必须按照顺序处理事件，或者需要“恰一次”语义，这就会带来一些挑战。</span><span class="sxs-lookup"><span data-stu-id="73f64-138">This can create challenges if events must be processed in order, or require exactly-once semantics.</span></span> <span data-ttu-id="73f64-139">请参阅[尽量减少协调][minimize-coordination]。</span><span class="sxs-lookup"><span data-stu-id="73f64-139">See [Minimize Coordination][minimize-coordination].</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="73f64-140">何时使用此架构</span><span class="sxs-lookup"><span data-stu-id="73f64-140">When to use this architecture</span></span>

- <span data-ttu-id="73f64-141">多个子系统必须处理相同的事件。</span><span class="sxs-lookup"><span data-stu-id="73f64-141">Multiple subsystems must process the same events.</span></span>
- <span data-ttu-id="73f64-142">延迟时间最短的实时处理。</span><span class="sxs-lookup"><span data-stu-id="73f64-142">Real-time processing with minimum time lag.</span></span>
- <span data-ttu-id="73f64-143">复杂事件处理，如模式匹配或时间范围内的聚合。</span><span class="sxs-lookup"><span data-stu-id="73f64-143">Complex event processing, such as pattern matching or aggregation over time windows.</span></span>
- <span data-ttu-id="73f64-144">大量且快速的数据，如 IoT。</span><span class="sxs-lookup"><span data-stu-id="73f64-144">High volume and high velocity of data, such as IoT.</span></span>

## <a name="benefits"></a><span data-ttu-id="73f64-145">优点</span><span class="sxs-lookup"><span data-stu-id="73f64-145">Benefits</span></span>

- <span data-ttu-id="73f64-146">生成者和使用者相脱离。</span><span class="sxs-lookup"><span data-stu-id="73f64-146">Producers and consumers are decoupled.</span></span>
- <span data-ttu-id="73f64-147">没有点到点的集成。</span><span class="sxs-lookup"><span data-stu-id="73f64-147">No point-to point-integrations.</span></span> <span data-ttu-id="73f64-148">容易向系统添加新使用者。</span><span class="sxs-lookup"><span data-stu-id="73f64-148">It's easy to add new consumers to the system.</span></span>
- <span data-ttu-id="73f64-149">使用者在事件发生时便可立即响应。</span><span class="sxs-lookup"><span data-stu-id="73f64-149">Consumers can respond to events immediately as they arrive.</span></span>
- <span data-ttu-id="73f64-150">具有高缩放性，分布广泛。</span><span class="sxs-lookup"><span data-stu-id="73f64-150">Highly scalable and distributed.</span></span>
- <span data-ttu-id="73f64-151">子系统具有独立的事件流视图。</span><span class="sxs-lookup"><span data-stu-id="73f64-151">Subsystems have independent views of the event stream.</span></span>

## <a name="challenges"></a><span data-ttu-id="73f64-152">挑战</span><span class="sxs-lookup"><span data-stu-id="73f64-152">Challenges</span></span>

- <span data-ttu-id="73f64-153">有保证的传递。</span><span class="sxs-lookup"><span data-stu-id="73f64-153">Guaranteed delivery.</span></span> <span data-ttu-id="73f64-154">在某些系统中，尤其是在 IoT 方案中，保证数据传递至关重要。</span><span class="sxs-lookup"><span data-stu-id="73f64-154">In some systems, especially in IoT scenarios, it's crucial to guarantee that events are delivered.</span></span>
- <span data-ttu-id="73f64-155">按顺序或者“恰一次”处理事件。</span><span class="sxs-lookup"><span data-stu-id="73f64-155">Processing events in order or exactly once.</span></span> <span data-ttu-id="73f64-156">每种使用者类型通常都在多个实例中运行，以提供复原能力和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="73f64-156">Each consumer type typically runs in multiple instances, for resiliency and scalability.</span></span> <span data-ttu-id="73f64-157">如果必须按顺序（在使用者类型中）处理事件，或处理逻辑不是幂等的，就会带来挑战。</span><span class="sxs-lookup"><span data-stu-id="73f64-157">This can create a challenge if the events must be processed in order (within a consumer type), or if the processing logic is not idempotent.</span></span>

 <!-- links -->

[competing-consumers]: ../../patterns/competing-consumers.md
[minimize-coordination]: ../design-principles/minimize-coordination.md
