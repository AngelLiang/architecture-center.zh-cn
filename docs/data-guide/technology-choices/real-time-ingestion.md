---
title: 选择实时消息引入技术
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 9f787a0de5db97f5c0a5651b510e49762fbc44b9
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244878"
---
# <a name="choosing-a-real-time-message-ingestion-technology-in-azure"></a><span data-ttu-id="73c85-102">在 Azure 中选择实时消息引入技术</span><span class="sxs-lookup"><span data-stu-id="73c85-102">Choosing a real-time message ingestion technology in Azure</span></span>

<span data-ttu-id="73c85-103">实时处理对实时捕获的数据流进行处理并且以最低延迟对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="73c85-103">Real time processing deals with streams of data that are captured in real-time and processed with minimal latency.</span></span> <span data-ttu-id="73c85-104">许多实时处理解决方案都需要一个消息引入存储来充当消息缓冲区，以及支持横向扩展处理、可靠传递和其他消息队列语义。</span><span class="sxs-lookup"><span data-stu-id="73c85-104">Many real-time processing solutions need a message ingestion store to act as a buffer for messages, and to support scale-out processing, reliable delivery, and other message queuing semantics.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-for-real-time-message-ingestion"></a><span data-ttu-id="73c85-105">用于实时消息引入的选项有哪些？</span><span class="sxs-lookup"><span data-stu-id="73c85-105">What are your options for real-time message ingestion?</span></span>

<!-- markdownlint-enable MD026 -->

- [<span data-ttu-id="73c85-106">Azure 事件中心</span><span class="sxs-lookup"><span data-stu-id="73c85-106">Azure Event Hubs</span></span>](/azure/event-hubs/)
- [<span data-ttu-id="73c85-107">Azure IoT 中心</span><span class="sxs-lookup"><span data-stu-id="73c85-107">Azure IoT Hub</span></span>](/azure/iot-hub/)
- [<span data-ttu-id="73c85-108">Kafka on HDInsight</span><span class="sxs-lookup"><span data-stu-id="73c85-108">Kafka on HDInsight</span></span>](/azure/hdinsight/kafka/apache-kafka-get-started)

## <a name="azure-event-hubs"></a><span data-ttu-id="73c85-109">Azure 事件中心</span><span class="sxs-lookup"><span data-stu-id="73c85-109">Azure Event Hubs</span></span>

<span data-ttu-id="73c85-110">[Azure 事件中心](/azure/event-hubs/)是高度可缩放的数据流式处理平台和事件引入服务，能够每秒接收和处理数百万事件。</span><span class="sxs-lookup"><span data-stu-id="73c85-110">[Azure Event Hubs](/azure/event-hubs/) is a highly scalable data streaming platform and event ingestion service, capable of receiving and processing millions of events per second.</span></span> <span data-ttu-id="73c85-111">事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。</span><span class="sxs-lookup"><span data-stu-id="73c85-111">Event Hubs can process and store events, data, or telemetry produced by distributed software and devices.</span></span> <span data-ttu-id="73c85-112">可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。</span><span class="sxs-lookup"><span data-stu-id="73c85-112">Data sent to an event hub can be transformed and stored using any real-time analytics provider or batching/storage adapters.</span></span> <span data-ttu-id="73c85-113">事件中心以低延迟大规模提供发布-订阅功能，这使得它适用于大数据方案。</span><span class="sxs-lookup"><span data-stu-id="73c85-113">Event Hubs provides publish-subscribe capabilities with low latency at massive scale, which makes it appropriate for big data scenarios.</span></span>

## <a name="azure-iot-hub"></a><span data-ttu-id="73c85-114">Azure IoT 中心</span><span class="sxs-lookup"><span data-stu-id="73c85-114">Azure IoT Hub</span></span>

<span data-ttu-id="73c85-115">[Azure IoT 中心](/azure/iot-hub/)是一项托管的服务，可在数百万个 IoT 设备和一个基于云的后端之间实现安全可靠的双向通信。</span><span class="sxs-lookup"><span data-stu-id="73c85-115">[Azure IoT Hub](/azure/iot-hub/) is a managed service that enables reliable and secure bidirectional communications between millions of IoT devices and a cloud-based back end.</span></span>

<span data-ttu-id="73c85-116">IoT 中心的功能包括：</span><span class="sxs-lookup"><span data-stu-id="73c85-116">Feature of IoT Hub include:</span></span>

- <span data-ttu-id="73c85-117">多个针对设备到云和云到设备通信的选项。</span><span class="sxs-lookup"><span data-stu-id="73c85-117">Multiple options for device-to-cloud and cloud-to-device communication.</span></span> <span data-ttu-id="73c85-118">这些选项包括单向消息传递、文件传输以及请求-答复方法。</span><span class="sxs-lookup"><span data-stu-id="73c85-118">These options include one-way messaging, file transfer, and request-reply methods.</span></span>
- <span data-ttu-id="73c85-119">将消息传送到其他 Azure 服务。</span><span class="sxs-lookup"><span data-stu-id="73c85-119">Message routing to other Azure services.</span></span>
- <span data-ttu-id="73c85-120">为设备元数据和已同步的状态信息提供可查询存储。</span><span class="sxs-lookup"><span data-stu-id="73c85-120">Queryable store for device metadata and synchronized state information.</span></span>
- <span data-ttu-id="73c85-121">使用每个设备的安全密钥或 X.509 证书来实现安全的通信和访问控制。</span><span class="sxs-lookup"><span data-stu-id="73c85-121">Secure communications and access control using per-device security keys or X.509 certificates.</span></span>
- <span data-ttu-id="73c85-122">监视设备连接和设备标识管理事件。</span><span class="sxs-lookup"><span data-stu-id="73c85-122">Monitoring of device connectivity and device identity management events.</span></span>

<span data-ttu-id="73c85-123">在消息引入方面，IoT 中心类似于事件中心。</span><span class="sxs-lookup"><span data-stu-id="73c85-123">In terms of message ingestion, IoT Hub is similar to Event Hubs.</span></span> <span data-ttu-id="73c85-124">但是，它专门设计用于管理 IoT 设备连接，而不仅仅是用于消息引入。</span><span class="sxs-lookup"><span data-stu-id="73c85-124">However, it was specifically designed for managing IoT device connectivity, not just message ingestion.</span></span> <span data-ttu-id="73c85-125">有关详细信息，请参阅 [Azure IoT 中心与 Azure 事件中心的比较](/azure/iot-hub/iot-hub-compare-event-hubs)。</span><span class="sxs-lookup"><span data-stu-id="73c85-125">For more information, see [Comparison of Azure IoT Hub and Azure Event Hubs](/azure/iot-hub/iot-hub-compare-event-hubs).</span></span>

## <a name="kafka-on-hdinsight"></a><span data-ttu-id="73c85-126">Kafka on HDInsight</span><span class="sxs-lookup"><span data-stu-id="73c85-126">Kafka on HDInsight</span></span>

<span data-ttu-id="73c85-127">[Apache Kafka](https://kafka.apache.org/) 是一个开源的分布式流式处理平台，可用于构建实时数据管道和流式处理应用程序。</span><span class="sxs-lookup"><span data-stu-id="73c85-127">[Apache Kafka](https://kafka.apache.org/) is an open-source distributed streaming platform that can be used to build real-time data pipelines and streaming applications.</span></span> <span data-ttu-id="73c85-128">Kafka 还提供了类似于消息队列的消息中转站，可在其中发布和订阅命名数据流。</span><span class="sxs-lookup"><span data-stu-id="73c85-128">Kafka also provides message broker functionality similar to a message queue, where you can publish and subscribe to named data streams.</span></span> <span data-ttu-id="73c85-129">它可水平缩放，具有容错能力，而且速度非常快。</span><span class="sxs-lookup"><span data-stu-id="73c85-129">It is horizontally scalable, fault-tolerant, and extremely fast.</span></span> <span data-ttu-id="73c85-130">[Kafka on HDInsight](/azure/hdinsight/kafka/apache-kafka-get-started) 将 Kafka 提供为 Azure 中的一项可高度扩展且高度可用的托管服务。</span><span class="sxs-lookup"><span data-stu-id="73c85-130">[Kafka on HDInsight](/azure/hdinsight/kafka/apache-kafka-get-started) provides a Kafka as a managed, highly scalable, and highly available service in Azure.</span></span>

<span data-ttu-id="73c85-131">Kafka 的一些常见用例包括：</span><span class="sxs-lookup"><span data-stu-id="73c85-131">Some common use cases for Kafka are:</span></span>

- <span data-ttu-id="73c85-132">**消息**。</span><span class="sxs-lookup"><span data-stu-id="73c85-132">**Messaging**.</span></span> <span data-ttu-id="73c85-133">由于 Kafka 支持发布-订阅消息模式，因此它经常用作消息中转站。</span><span class="sxs-lookup"><span data-stu-id="73c85-133">Because it supports the publish-subscribe message pattern, Kafka is often used as a message broker.</span></span>
- <span data-ttu-id="73c85-134">**活动跟踪**。</span><span class="sxs-lookup"><span data-stu-id="73c85-134">**Activity tracking**.</span></span> <span data-ttu-id="73c85-135">由于 Kafka 提供了按顺序将记录记入日志的功能，因此它还可用来跟踪和重新创建活动。</span><span class="sxs-lookup"><span data-stu-id="73c85-135">Because Kafka provides in-order logging of records, it can be used to track and re-create activities, such as user actions on a web site.</span></span>
- <span data-ttu-id="73c85-136">**聚合**。</span><span class="sxs-lookup"><span data-stu-id="73c85-136">**Aggregation**.</span></span> <span data-ttu-id="73c85-137">使用流处理可从不同的流中聚合信息，将信息合并和集中到运营数据中。</span><span class="sxs-lookup"><span data-stu-id="73c85-137">Using stream processing, you can aggregate information from different streams to combine and centralize the information into operational data.</span></span>
- <span data-ttu-id="73c85-138">**转换**。</span><span class="sxs-lookup"><span data-stu-id="73c85-138">**Transformation**.</span></span> <span data-ttu-id="73c85-139">使用流处理可将多个输入主题中的数据合并到一个或多个输出主题中，丰富其内容。</span><span class="sxs-lookup"><span data-stu-id="73c85-139">Using stream processing, you can combine and enrich data from multiple input topics into one or more output topics.</span></span>

## <a name="key-selection-criteria"></a><span data-ttu-id="73c85-140">关键选择条件</span><span class="sxs-lookup"><span data-stu-id="73c85-140">Key selection criteria</span></span>

<span data-ttu-id="73c85-141">若要缩小选择范围，请先回答以下问题：</span><span class="sxs-lookup"><span data-stu-id="73c85-141">To narrow the choices, start by answering these questions:</span></span>

- <span data-ttu-id="73c85-142">IoT 设备与 Azure 之间是否需要进行双向通信？</span><span class="sxs-lookup"><span data-stu-id="73c85-142">Do you need two-way communication between your IoT devices and Azure?</span></span> <span data-ttu-id="73c85-143">如果是，请选择 IoT 中心。</span><span class="sxs-lookup"><span data-stu-id="73c85-143">If so, choose IoT Hub.</span></span>

- <span data-ttu-id="73c85-144">是否需要管理对各个设备的访问权限，并且必须能够撤销对特定设备的访问权限？</span><span class="sxs-lookup"><span data-stu-id="73c85-144">Do you need to manage access for individual devices and be able to revoke access to a specific device?</span></span> <span data-ttu-id="73c85-145">如果是，请选择 IoT 中心。</span><span class="sxs-lookup"><span data-stu-id="73c85-145">If yes, choose IoT Hub.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="73c85-146">功能矩阵</span><span class="sxs-lookup"><span data-stu-id="73c85-146">Capability matrix</span></span>

<span data-ttu-id="73c85-147">以下各表汇总了功能上的关键差异。</span><span class="sxs-lookup"><span data-stu-id="73c85-147">The following tables summarize the key differences in capabilities.</span></span>

<!-- markdownlint-disable MD033 -->

| | <span data-ttu-id="73c85-148">IoT 中心</span><span class="sxs-lookup"><span data-stu-id="73c85-148">IoT Hub</span></span> | <span data-ttu-id="73c85-149">事件中心</span><span class="sxs-lookup"><span data-stu-id="73c85-149">Event Hubs</span></span> | <span data-ttu-id="73c85-150">Kafka on HDInsight</span><span class="sxs-lookup"><span data-stu-id="73c85-150">Kafka on HDInsight</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="73c85-151">云到设备的通信</span><span class="sxs-lookup"><span data-stu-id="73c85-151">Cloud-to-device communications</span></span> | <span data-ttu-id="73c85-152">是</span><span class="sxs-lookup"><span data-stu-id="73c85-152">Yes</span></span> | <span data-ttu-id="73c85-153">否</span><span class="sxs-lookup"><span data-stu-id="73c85-153">No</span></span> | <span data-ttu-id="73c85-154">否</span><span class="sxs-lookup"><span data-stu-id="73c85-154">No</span></span> |
| <span data-ttu-id="73c85-155">设备启动的文件上传</span><span class="sxs-lookup"><span data-stu-id="73c85-155">Device-initiated file upload</span></span> | <span data-ttu-id="73c85-156">是</span><span class="sxs-lookup"><span data-stu-id="73c85-156">Yes</span></span> | <span data-ttu-id="73c85-157">否</span><span class="sxs-lookup"><span data-stu-id="73c85-157">No</span></span> | <span data-ttu-id="73c85-158">否</span><span class="sxs-lookup"><span data-stu-id="73c85-158">No</span></span> |
| <span data-ttu-id="73c85-159">设备状态信息</span><span class="sxs-lookup"><span data-stu-id="73c85-159">Device state information</span></span> | [<span data-ttu-id="73c85-160">设备孪生</span><span class="sxs-lookup"><span data-stu-id="73c85-160">Device twins</span></span>](/azure/iot-hub/iot-hub-devguide-device-twins) | <span data-ttu-id="73c85-161">否</span><span class="sxs-lookup"><span data-stu-id="73c85-161">No</span></span> | <span data-ttu-id="73c85-162">否</span><span class="sxs-lookup"><span data-stu-id="73c85-162">No</span></span> |
| <span data-ttu-id="73c85-163">协议支持</span><span class="sxs-lookup"><span data-stu-id="73c85-163">Protocol support</span></span> | <span data-ttu-id="73c85-164">MQTT、AMQP、HTTPS <sup>1</sup></span><span class="sxs-lookup"><span data-stu-id="73c85-164">MQTT, AMQP, HTTPS <sup>1</sup></span></span> | <span data-ttu-id="73c85-165">AMQP、HTTPS</span><span class="sxs-lookup"><span data-stu-id="73c85-165">AMQP, HTTPS</span></span> | [<span data-ttu-id="73c85-166">Kafka 协议</span><span class="sxs-lookup"><span data-stu-id="73c85-166">Kafka Protocol</span></span>](https://cwiki.apache.org/confluence/display/KAFKA/A+Guide+To+The+Kafka+Protocol) |
| <span data-ttu-id="73c85-167">安全</span><span class="sxs-lookup"><span data-stu-id="73c85-167">Security</span></span> | <span data-ttu-id="73c85-168">每设备标识；可撤销的访问控制权限。</span><span class="sxs-lookup"><span data-stu-id="73c85-168">Per-device identity; revocable access control.</span></span> | <span data-ttu-id="73c85-169">共享访问策略；通过发布者策略实现有限的撤销。</span><span class="sxs-lookup"><span data-stu-id="73c85-169">Shared access policies; limited revocation through publisher policies.</span></span> | <span data-ttu-id="73c85-170">使用 SASL 的身份验证；可插拔身份验证；与受支持的外部身份验证服务集成。</span><span class="sxs-lookup"><span data-stu-id="73c85-170">Authentication using SASL; pluggable authorization; integration with external authentication services supported.</span></span> |

<!-- markdownlint-enable MD026 -->

<span data-ttu-id="73c85-171">[1] 还可以使用 [Azure IoT 协议网关](/azure/iot-hub/iot-hub-protocol-gateway)作为自定义网关，来为 IoT 中心启用协议自适应。</span><span class="sxs-lookup"><span data-stu-id="73c85-171">[1] You can also use [Azure IoT protocol gateway](/azure/iot-hub/iot-hub-protocol-gateway) as a custom gateway to enable protocol adaptation for IoT Hub.</span></span>

<span data-ttu-id="73c85-172">有关详细信息，请参阅 [Azure IoT 中心与 Azure 事件中心的比较](/azure/iot-hub/iot-hub-compare-event-hubs)。</span><span class="sxs-lookup"><span data-stu-id="73c85-172">For more information, see [Comparison of Azure IoT Hub and Azure Event Hubs](/azure/iot-hub/iot-hub-compare-event-hubs).</span></span>
