---
title: 实时欺诈检测
titleSuffix: Azure Example Scenarios
description: 使用 Azure 事件中心和流分析实时检测欺诈性活动。
author: alexbuckgit
ms.date: 07/05/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.openlocfilehash: 392d23ed813a9605392b03de5cb8a7c7638bc4fc
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487214"
---
# <a name="real-time-fraud-detection-on-azure"></a><span data-ttu-id="99e52-103">在 Azure 上实时欺诈检测</span><span class="sxs-lookup"><span data-stu-id="99e52-103">Real-time fraud detection on Azure</span></span>

<span data-ttu-id="99e52-104">本示例方案针对那些需要实时分析数据以检测欺诈性事务或其他异常活动的组织。</span><span class="sxs-lookup"><span data-stu-id="99e52-104">This example scenario is relevant to organizations that need to analyze data in real time to detect fraudulent transactions or other anomalous activity.</span></span>

<span data-ttu-id="99e52-105">可能的应用包括：确定欺诈性信用卡活动或移动电话呼叫。</span><span class="sxs-lookup"><span data-stu-id="99e52-105">Potential applications include identifying fraudulent credit card activity or mobile phone calls.</span></span> <span data-ttu-id="99e52-106">为了确定异常活动，传统的联机分析系统可能需要数小时来转换并分析数据。</span><span class="sxs-lookup"><span data-stu-id="99e52-106">Traditional online analytical systems might take hours to transform and analyze the data to identify anomalous activity.</span></span>

<span data-ttu-id="99e52-107">使用完全托管的 Azure 服务（例如事件中心和流分析），公司不需管理各个服务器，既减少了成本，又可充分利用 Microsoft 在云规模的数据引入和实时分析方面的专业技术。</span><span class="sxs-lookup"><span data-stu-id="99e52-107">By using fully managed Azure services such as Event Hubs and Stream Analytics, companies can eliminate the need to manage individual servers, while reducing costs and leveraging Microsoft's expertise in cloud-scale data ingestion and real-time analytics.</span></span> <span data-ttu-id="99e52-108">本方案专门解决欺诈性活动的检测问题。</span><span class="sxs-lookup"><span data-stu-id="99e52-108">This scenario specifically addresses the detection of fraudulent activity.</span></span> <span data-ttu-id="99e52-109">如果有数据分析的其他需求，则应查看可用 [Azure 分析服务][product-category]的列表。</span><span class="sxs-lookup"><span data-stu-id="99e52-109">If you have other needs for data analytics, you should review the list of available [Azure Analytics services][product-category].</span></span>

<span data-ttu-id="99e52-110">本示例所代表的部分涉及到更广泛的数据处理体系结构和策略。</span><span class="sxs-lookup"><span data-stu-id="99e52-110">This sample represents one part of a broader data processing architecture and strategy.</span></span> <span data-ttu-id="99e52-111">整个体系结构此方面的其他选项将在本文后面讨论。</span><span class="sxs-lookup"><span data-stu-id="99e52-111">Other options for this aspect of an overall architecture are discussed later in this article.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="99e52-112">相关用例</span><span class="sxs-lookup"><span data-stu-id="99e52-112">Relevant use cases</span></span>

<span data-ttu-id="99e52-113">其他相关用例包括：</span><span class="sxs-lookup"><span data-stu-id="99e52-113">Other relevant use cases include:</span></span>

- <span data-ttu-id="99e52-114">在电信场景中检测欺诈性移动电话呼叫。</span><span class="sxs-lookup"><span data-stu-id="99e52-114">Detecting fraudulent mobile-phone calls in telecommunications scenarios.</span></span>
- <span data-ttu-id="99e52-115">为银行机构确定欺诈性信用卡交易。</span><span class="sxs-lookup"><span data-stu-id="99e52-115">Identifying fraudulent credit card transactions for banking institutions.</span></span>
- <span data-ttu-id="99e52-116">在零售或电子商务场景中确定欺诈性购物。</span><span class="sxs-lookup"><span data-stu-id="99e52-116">Identifying fraudulent purchases in retail or e-commerce scenarios.</span></span>

## <a name="architecture"></a><span data-ttu-id="99e52-117">体系结构</span><span class="sxs-lookup"><span data-stu-id="99e52-117">Architecture</span></span>

![从体系结构的角度概要说明实时欺诈检测方案的 Azure 组件][architecture]

<span data-ttu-id="99e52-119">本方案涉及实时分析管道的后端组件。</span><span class="sxs-lookup"><span data-stu-id="99e52-119">This scenario covers the back-end components of a real-time analytics pipeline.</span></span> <span data-ttu-id="99e52-120">数据流经方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="99e52-120">Data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="99e52-121">将移动电话呼叫元数据从源系统发送到 Azure 事件中心实例。</span><span class="sxs-lookup"><span data-stu-id="99e52-121">Mobile phone call metadata is sent from the source system to an Azure Event Hubs instance.</span></span>
2. <span data-ttu-id="99e52-122">启动流分析作业，通过事件中心源接收数据。</span><span class="sxs-lookup"><span data-stu-id="99e52-122">A Stream Analytics job is started, which receives data via the event hub source.</span></span>
3. <span data-ttu-id="99e52-123">流分析作业运行转换输入流所需的预定义查询，并根据欺诈事务算法对其进行分析。</span><span class="sxs-lookup"><span data-stu-id="99e52-123">The Stream Analytics job runs a predefined query to transform the input stream and analyze it based on a fraudulent-transaction algorithm.</span></span> <span data-ttu-id="99e52-124">该查询使用翻转窗口将流分成不同的时间单元。</span><span class="sxs-lookup"><span data-stu-id="99e52-124">This query uses a tumbling window to segment the stream into distinct temporal units.</span></span>
4. <span data-ttu-id="99e52-125">流分析作业将已转换的流（代表检测到的欺诈性呼叫）写入到 Azure Blob 存储中的输出接收器。</span><span class="sxs-lookup"><span data-stu-id="99e52-125">The Stream Analytics job writes the transformed stream representing detected fraudulent calls to an output sink in Azure Blob storage.</span></span>

### <a name="components"></a><span data-ttu-id="99e52-126">组件</span><span class="sxs-lookup"><span data-stu-id="99e52-126">Components</span></span>

- <span data-ttu-id="99e52-127">[Azure 事件中心][docs-event-hubs]是一个实时流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。</span><span class="sxs-lookup"><span data-stu-id="99e52-127">[Azure Event Hubs][docs-event-hubs] is a real-time streaming platform and event ingestion service, capable of receiving and processing millions of events per second.</span></span> <span data-ttu-id="99e52-128">事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。</span><span class="sxs-lookup"><span data-stu-id="99e52-128">Event Hubs can process and store events, data, or telemetry produced by distributed software and devices.</span></span> <span data-ttu-id="99e52-129">在本方案中，事件中心接收需要进行欺诈活动分析的所有电话呼叫元数据。</span><span class="sxs-lookup"><span data-stu-id="99e52-129">In this scenario, Event Hubs receives all phone call metadata to be analyzed for fraudulent activity.</span></span>
- <span data-ttu-id="99e52-130">[Azure 流分析][docs-stream-analytics]是一个事件处理引擎，可以分析从设备和其他数据源流式传输的大量数据。</span><span class="sxs-lookup"><span data-stu-id="99e52-130">[Azure Stream Analytics][docs-stream-analytics] is an event-processing engine that can analyze high volumes of data streaming from devices and other data sources.</span></span> <span data-ttu-id="99e52-131">它还支持从数据流提取信息，以便确定模式和关系。</span><span class="sxs-lookup"><span data-stu-id="99e52-131">It also supports extracting information from data streams to identify patterns and relationships.</span></span> <span data-ttu-id="99e52-132">这些模式可能触发其他下游操作。</span><span class="sxs-lookup"><span data-stu-id="99e52-132">These patterns can trigger other downstream actions.</span></span> <span data-ttu-id="99e52-133">在本方案中，流分析会转换事件中心的输入流，以便确定欺诈性呼叫。</span><span class="sxs-lookup"><span data-stu-id="99e52-133">In this scenario, Stream Analytics transforms the input stream from Event Hubs to identify fraudulent calls.</span></span>
- <span data-ttu-id="99e52-134">[Blob 存储](/azure/storage/blobs/storage-blobs-introduction)在本方案中用于存储流分析作业的结果。</span><span class="sxs-lookup"><span data-stu-id="99e52-134">[Blob storage](/azure/storage/blobs/storage-blobs-introduction) is used in this scenario to store the results of the Stream Analytics job.</span></span>

## <a name="considerations"></a><span data-ttu-id="99e52-135">注意事项</span><span class="sxs-lookup"><span data-stu-id="99e52-135">Considerations</span></span>

### <a name="alternatives"></a><span data-ttu-id="99e52-136">备选项</span><span class="sxs-lookup"><span data-stu-id="99e52-136">Alternatives</span></span>

<span data-ttu-id="99e52-137">进行实时消息引入、数据存储、流处理、分析数据存储以及分析和报告时，有许多技术选择。</span><span class="sxs-lookup"><span data-stu-id="99e52-137">Many technology choices are available for real-time message ingestion, data storage, stream processing, storage of analytical data, and analytics and reporting.</span></span> <span data-ttu-id="99e52-138">有关这些选项及其功能和主要选择标准的概述，请参阅 Azure 数据体系结构指南中的[大数据体系结构：实时处理](/azure/architecture/data-guide/technology-choices/real-time-ingestion)。</span><span class="sxs-lookup"><span data-stu-id="99e52-138">For an overview of these options, their capabilities, and key selection criteria, see [Big data architectures: Real-time processing](/azure/architecture/data-guide/technology-choices/real-time-ingestion) in the Azure Data Architecture Guide.</span></span>

<span data-ttu-id="99e52-139">另外，也可通过 Azure 中的各种机器学习服务生成更复杂的欺诈检测算法。</span><span class="sxs-lookup"><span data-stu-id="99e52-139">Additionally, more complex algorithms for fraud detection can be produced by various machine learning services in Azure.</span></span> <span data-ttu-id="99e52-140">有关这些选项的概述，请参阅 [Azure 数据体系结构指南](../../data-guide/index.md)中的[机器学习的技术选择](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)。</span><span class="sxs-lookup"><span data-stu-id="99e52-140">For an overview of these options, see [Technology choices for machine learning](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning) in the [Azure Data Architecture Guide](../../data-guide/index.md).</span></span>

### <a name="availability"></a><span data-ttu-id="99e52-141">可用性</span><span class="sxs-lookup"><span data-stu-id="99e52-141">Availability</span></span>

<span data-ttu-id="99e52-142">Azure Monitor 提供了统一的用户界面，可用于监视各种 Azure 服务。</span><span class="sxs-lookup"><span data-stu-id="99e52-142">Azure Monitor provides unified user interfaces for monitoring across various Azure services.</span></span> <span data-ttu-id="99e52-143">有关详细信息，请参阅[在 Microsoft Azure 中进行监视](/azure/monitoring-and-diagnostics/monitoring-overview)。</span><span class="sxs-lookup"><span data-stu-id="99e52-143">For more information, see [Monitoring in Microsoft Azure](/azure/monitoring-and-diagnostics/monitoring-overview).</span></span> <span data-ttu-id="99e52-144">事件中心和流分析均与 Azure Monitor 集成在一起。</span><span class="sxs-lookup"><span data-stu-id="99e52-144">Event Hubs and Stream Analytics are both integrated with Azure Monitor.</span></span>

<span data-ttu-id="99e52-145">若要了解其他可用性注意事项，请参阅 Azure 体系结构中心的[可用性核对清单][availability]。</span><span class="sxs-lookup"><span data-stu-id="99e52-145">For other availability considerations, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="99e52-146">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="99e52-146">Scalability</span></span>

<span data-ttu-id="99e52-147">本方案的组件设计用于超大规模的引入和大规模并行实时分析。</span><span class="sxs-lookup"><span data-stu-id="99e52-147">The components of this scenario are designed for hyper-scale ingestion and massively parallel real-time analytics.</span></span> <span data-ttu-id="99e52-148">Azure 事件中心高度可缩放，每秒能够接收和处理数百万个事件且延迟很低。</span><span class="sxs-lookup"><span data-stu-id="99e52-148">Azure Event Hubs is highly scalable, capable of receiving and processing millions of events per second with low latency.</span></span> <span data-ttu-id="99e52-149">事件中心可[自动增加](/azure/event-hubs/event-hubs-auto-inflate)吞吐量单元数，以便满足使用需求。</span><span class="sxs-lookup"><span data-stu-id="99e52-149">Event Hubs can [automatically scale up](/azure/event-hubs/event-hubs-auto-inflate) the number of throughput units to meet usage needs.</span></span> <span data-ttu-id="99e52-150">Azure 流分析可以分析多个源提供的大量流数据。</span><span class="sxs-lookup"><span data-stu-id="99e52-150">Azure Stream Analytics is capable of analyzing high volumes of streaming data from many sources.</span></span> <span data-ttu-id="99e52-151">若要纵向扩展流分析，可以增加分配的用于执行流作业的[流单元](/azure/stream-analytics/stream-analytics-streaming-unit-consumption)数。</span><span class="sxs-lookup"><span data-stu-id="99e52-151">You can scale up Stream Analytics by increasing the number of [streaming units](/azure/stream-analytics/stream-analytics-streaming-unit-consumption) allocated to execute your streaming job.</span></span>

<span data-ttu-id="99e52-152">有关如何设计可缩放解决方案的通用指南，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="99e52-152">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="99e52-153">安全</span><span class="sxs-lookup"><span data-stu-id="99e52-153">Security</span></span>

<span data-ttu-id="99e52-154">Azure 事件中心通过[身份验证和安全模型][docs-event-hubs-security-model]来确保数据安全，该模型基于共享访问签名 (SAS) 令牌与事件发布者的组合。</span><span class="sxs-lookup"><span data-stu-id="99e52-154">Azure Event Hubs secures data through an [authentication and security model][docs-event-hubs-security-model] based on a combination of Shared Access Signature (SAS) tokens and event publishers.</span></span> <span data-ttu-id="99e52-155">事件发布者定义事件中心的虚拟终结点。</span><span class="sxs-lookup"><span data-stu-id="99e52-155">An event publisher defines a virtual endpoint for an event hub.</span></span> <span data-ttu-id="99e52-156">发布者只能用于将消息发送到事件中心。</span><span class="sxs-lookup"><span data-stu-id="99e52-156">The publisher can only be used to send messages to an event hub.</span></span> <span data-ttu-id="99e52-157">无法从发布者接收消息。</span><span class="sxs-lookup"><span data-stu-id="99e52-157">It is not possible to receive messages from a publisher.</span></span>

<span data-ttu-id="99e52-158">若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="99e52-158">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="99e52-159">复原</span><span class="sxs-lookup"><span data-stu-id="99e52-159">Resiliency</span></span>

<span data-ttu-id="99e52-160">若需可复原解决方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="99e52-160">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="99e52-161">部署方案</span><span class="sxs-lookup"><span data-stu-id="99e52-161">Deploy the scenario</span></span>

<span data-ttu-id="99e52-162">若要部署本方案，可以按照此[分步教程][tutorial]的说明操作，手动部署方案的每个组件。</span><span class="sxs-lookup"><span data-stu-id="99e52-162">To deploy this scenario, you can follow this [step-by-step tutorial][tutorial] demonstrating how to manually deploy each component of the scenario.</span></span> <span data-ttu-id="99e52-163">本教程还提供了一个 .NET 客户端应用程序，用于生成示例电话呼叫元数据并将该数据发送到事件中心实例。</span><span class="sxs-lookup"><span data-stu-id="99e52-163">This tutorial also provides a .NET client application to generate sample phone call metadata and send that data to an event hub instance.</span></span>

## <a name="pricing"></a><span data-ttu-id="99e52-164">定价</span><span class="sxs-lookup"><span data-stu-id="99e52-164">Pricing</span></span>

<span data-ttu-id="99e52-165">为了方便用户了解运行本方案的成本，我们已在成本计算器中预配置了所有服务。</span><span class="sxs-lookup"><span data-stu-id="99e52-165">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="99e52-166">若要了解自己的特定用例的定价变化情况，请按预期的数据量更改相应的变量。</span><span class="sxs-lookup"><span data-stu-id="99e52-166">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected data volume.</span></span>

<span data-ttu-id="99e52-167">我们已根据你预期接收的流量提供了三个示例性的成本配置文件：</span><span class="sxs-lookup"><span data-stu-id="99e52-167">We have provided three sample cost profiles based on amount of traffic you expect to get:</span></span>

- <span data-ttu-id="99e52-168">[小][small-pricing]：每月通过一个标准的流单元处理一百万个事件。</span><span class="sxs-lookup"><span data-stu-id="99e52-168">[Small][small-pricing]: process one million events through one standard streaming unit per month.</span></span>
- <span data-ttu-id="99e52-169">[中][medium-pricing]：每月通过五个标准的流单元处理一亿个事件。</span><span class="sxs-lookup"><span data-stu-id="99e52-169">[Medium][medium-pricing]: process 100M events through five standard streaming units per month.</span></span>
- <span data-ttu-id="99e52-170">[大][large-pricing]：每月通过 20 个标准的流单元处理 9.99 亿个事件。</span><span class="sxs-lookup"><span data-stu-id="99e52-170">[Large][large-pricing]: process 999 million events through 20 standard streaming units per month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="99e52-171">相关资源</span><span class="sxs-lookup"><span data-stu-id="99e52-171">Related resources</span></span>

<span data-ttu-id="99e52-172">可以通过机器学习模型生成更复杂的欺诈检测方案。</span><span class="sxs-lookup"><span data-stu-id="99e52-172">More complex fraud detection scenarios can benefit from a machine learning model.</span></span> <span data-ttu-id="99e52-173">若要了解使用 Machine Learning Server 生成的方案，请参阅[使用 Machine Learning Server 进行的欺诈检测][r-server-fraud-detection]。</span><span class="sxs-lookup"><span data-stu-id="99e52-173">For scenarios built using Machine Learning Server, see [Fraud detection using Machine Learning Server][r-server-fraud-detection].</span></span> <span data-ttu-id="99e52-174">若要了解使用 Machine Learning Server 的其他解决方案模板，请参阅[数据科学方案和解决方案模板][docs-r-server-sample-solutions]。</span><span class="sxs-lookup"><span data-stu-id="99e52-174">For other solution templates using Machine Learning Server, see [Data science scenarios and solution templates][docs-r-server-sample-solutions].</span></span> <span data-ttu-id="99e52-175">若要了解使用 Azure Data Lake Analytics 的示例解决方案，请参阅[使用 Azure Data Lake 和 R 进行欺诈检测][technet-fraud-detection]。</span><span class="sxs-lookup"><span data-stu-id="99e52-175">For an example solution using Azure Data Lake Analytics, see [Using Azure Data Lake and R for Fraud Detection][technet-fraud-detection].</span></span>

<!-- links -->
[product-category]: https://azure.microsoft.com/product-categories/analytics/
[tutorial]: /azure/stream-analytics/stream-analytics-real-time-fraud-detection
[small-pricing]: https://azure.com/e/74149ec312c049ccba79bfb3cfa67606
[medium-pricing]: https://azure.com/e/4fc94f7376de484d8ae67a6958cae60a
[large-pricing]: https://azure.com/e/7da8804396f9428a984578700003ba42
[architecture]: ./media/architecture-fraud-detection.png
[docs-event-hubs]: /azure/event-hubs/event-hubs-what-is-event-hubs
[docs-event-hubs-security-model]: /azure/event-hubs/event-hubs-authentication-and-security-model-overview
[docs-stream-analytics]: /azure/stream-analytics/stream-analytics-introduction
[docs-r-server-sample-solutions]: /machine-learning-server/r/sample-solutions
[r-server-fraud-detection]: https://microsoft.github.io/r-server-fraud-detection/
[technet-fraud-detection]: https://blogs.technet.microsoft.com/machinelearning/2017/06/28/using-azure-data-lake-and-r-for-fraud-detection/
[availability]: /azure/architecture/checklist/availability
[scalability]: /azure/architecture/checklist/scalability
[resiliency]: ../../resiliency/index.md
[security]: /azure/security/
