---
title: Azure 上新闻源的大量引入和分析
description: 创建一个管道，以便仅使用 Azure 服务（包括 Azure Cosmos DB 和 Azure 认知服务）引入和分析文本、图像、情绪以及来自 RSS 新闻源的其他数据。
author: njray
ms.date: 2/1/2019
ms.custom: azcat-ai, AI
social_image_url: /azure/architecture/example-scenario/ai/media/mass-ingestion-newsfeeds-architecture.png
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.openlocfilehash: e1bc636103753d474b545a6e3e9118b2ef302b34
ms.sourcegitcommit: ea97ac004c38c6b456794c1a8eef29f8d2b77d50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58489249"
---
# <a name="mass-ingestion-and-analysis-of-news-feeds-on-azure"></a><span data-ttu-id="4adbf-103">Azure 上新闻源的大量引入和分析</span><span class="sxs-lookup"><span data-stu-id="4adbf-103">Mass ingestion and analysis of news feeds on Azure</span></span>

<span data-ttu-id="4adbf-104">此示例方案中介绍的批量引入和准实时分析使用公共 RSS 新闻源文档的管道。</span><span class="sxs-lookup"><span data-stu-id="4adbf-104">This example scenario describes a pipeline for mass ingestion and near real-time analysis of documents using public RSS news feeds.</span></span>  <span data-ttu-id="4adbf-105">它使用 Azure 认知服务提供有用的见解，包括文本翻译、 面部识别和情绪检测。</span><span class="sxs-lookup"><span data-stu-id="4adbf-105">It uses Azure Cognitive Services to offer useful insights including text translation, facial recognition, and sentiment detection.</span></span>

<span data-ttu-id="4adbf-106">此方案中包含的示例[英语][english]，[俄语][russian]，并[德语][german]新闻源，但您可以轻松地将其扩展到其他 RSS 源。</span><span class="sxs-lookup"><span data-stu-id="4adbf-106">This scenario contains examples for [English][english], [Russian][russian], and [German][german] news feeds, but you can easily extend it to other RSS feeds.</span></span> <span data-ttu-id="4adbf-107">为了便于部署，数据收集、 处理和分析是完全基于 Azure 服务。</span><span class="sxs-lookup"><span data-stu-id="4adbf-107">For ease of deployment, the data collection, processing, and analysis are based entirely on Azure services.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="4adbf-108">相关用例</span><span class="sxs-lookup"><span data-stu-id="4adbf-108">Relevant use cases</span></span>

<span data-ttu-id="4adbf-109">尽管此方案基于 RSS 源的处理，它是与任何文档、 网站或文章，您需要对相关：</span><span class="sxs-lookup"><span data-stu-id="4adbf-109">While this scenario is based on processing of RSS feeds, it's relevant to any document, website, or article where you would need to:</span></span>

* <span data-ttu-id="4adbf-110">转换为所选的语言的任何文本。</span><span class="sxs-lookup"><span data-stu-id="4adbf-110">Translate any text to the language of choice.</span></span>
* <span data-ttu-id="4adbf-111">在数字内容中查找关键短语、 实体和用户情绪。</span><span class="sxs-lookup"><span data-stu-id="4adbf-111">Find key phrases, entities, and user sentiment in digital content.</span></span>
* <span data-ttu-id="4adbf-112">与数字文章关联的图像中检测对象、 文本和特征点。</span><span class="sxs-lookup"><span data-stu-id="4adbf-112">Detect objects, text, and landmarks in images associated with a digital article.</span></span>
* <span data-ttu-id="4adbf-113">在数字内容与关联任何图像中检测通过其性别和年龄的人。</span><span class="sxs-lookup"><span data-stu-id="4adbf-113">Detect people by their gender and age in any image associated with digital content.</span></span>

## <a name="architecture"></a><span data-ttu-id="4adbf-114">体系结构</span><span class="sxs-lookup"><span data-stu-id="4adbf-114">Architecture</span></span>

![][architecture]

<span data-ttu-id="4adbf-115">数据流经解决方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="4adbf-115">The data flows through the solution as follows:</span></span>

1. <span data-ttu-id="4adbf-116">RSS 新闻源充当从文档或项目中获取数据的生成器。</span><span class="sxs-lookup"><span data-stu-id="4adbf-116">An RSS news feed acts as the generator that obtains data from a document or article.</span></span> <span data-ttu-id="4adbf-117">例如，其中一篇文章，数据通常包含一个标题，新闻项的原始正文的摘要和有时图像。</span><span class="sxs-lookup"><span data-stu-id="4adbf-117">For example, with an article, data typically includes a title, a summary of the original body of the news item, and sometimes images.</span></span>

2. <span data-ttu-id="4adbf-118">生成器或引入过程将项目和任何关联的图像插入到 Azure Cosmos DB[集合][collection]。</span><span class="sxs-lookup"><span data-stu-id="4adbf-118">A generator or ingestion process inserts the article and any associated images into an Azure Cosmos DB [Collection][collection].</span></span>

3. <span data-ttu-id="4adbf-119">通知会触发将文章文本存储在 CosmosDB 和 Azure Blob 存储中的文章图像 （如果有） 的 Azure Functions 中的插入函数。</span><span class="sxs-lookup"><span data-stu-id="4adbf-119">A notification triggers an ingest function in Azure Functions that stores the article text in CosmosDB and the article images (if any) in Azure Blob Storage.</span></span>  <span data-ttu-id="4adbf-120">文章然后传递给下一个队列。</span><span class="sxs-lookup"><span data-stu-id="4adbf-120">The article is then passed to the next queue.</span></span>

4. <span data-ttu-id="4adbf-121">Translate 函数由队列事件触发。</span><span class="sxs-lookup"><span data-stu-id="4adbf-121">A translate function is triggered by the queue event.</span></span> <span data-ttu-id="4adbf-122">它使用[翻译文本 API] [ translate-text] Azure 认知服务为检测语言中，如果有必要，将转换并收集情绪、 关键短语和实体正文和标题。</span><span class="sxs-lookup"><span data-stu-id="4adbf-122">It uses the [Translate Text API][translate-text] of Azure Cognitive Services to detect the language, translate if necessary, and collect the sentiment, key phrases, and entities from the body and the title.</span></span> <span data-ttu-id="4adbf-123">然后它将文章传递给下一个队列。</span><span class="sxs-lookup"><span data-stu-id="4adbf-123">Then it passes the article to the next queue.</span></span>

5. <span data-ttu-id="4adbf-124">排队的文章中触发检测函数。</span><span class="sxs-lookup"><span data-stu-id="4adbf-124">A detect function is triggered from the queued article.</span></span> <span data-ttu-id="4adbf-125">它使用[计算机视觉][ vision]服务能够检测对象、 特征点和手写的文字中的关联的映像，然后将传递项目到下一个队列。</span><span class="sxs-lookup"><span data-stu-id="4adbf-125">It uses the [Computer Vision][vision] service to detect objects, landmarks, and written words in the associated image, then passes the article to the next queue.</span></span>

6. <span data-ttu-id="4adbf-126">人脸函数触发触发排队的文章中的说明。</span><span class="sxs-lookup"><span data-stu-id="4adbf-126">A face function is triggered is triggered from the queued article.</span></span> <span data-ttu-id="4adbf-127">它使用[Azure 人脸 API] [ face]服务来检测人脸的性别和年龄在关联的图像，然后再将文章传递给下一个队列。</span><span class="sxs-lookup"><span data-stu-id="4adbf-127">It uses the [Azure Face API][face] service to detect faces for gender and age in the associated image, then passes the article to the next queue.</span></span>

7. <span data-ttu-id="4adbf-128">在所有函数都都完成后，会触发通知函数。</span><span class="sxs-lookup"><span data-stu-id="4adbf-128">When all functions are complete, the notify function is triggered.</span></span> <span data-ttu-id="4adbf-129">它加载项目的处理的记录，并扫描的所需的任何结果。</span><span class="sxs-lookup"><span data-stu-id="4adbf-129">It loads the processed records for the article and scans them for any results you want.</span></span> <span data-ttu-id="4adbf-130">如果找到，则内容将标记通知发送到所选的系统。</span><span class="sxs-lookup"><span data-stu-id="4adbf-130">If found, the content is flagged and a notification is sent to the system of your choice.</span></span>

<span data-ttu-id="4adbf-131">在每个处理步骤，该函数将结果写入到 Azure Cosmos DB。</span><span class="sxs-lookup"><span data-stu-id="4adbf-131">At each processing step, the function writes the results to Azure Cosmos DB.</span></span> <span data-ttu-id="4adbf-132">从根本上讲，可以使用这些数据按需。</span><span class="sxs-lookup"><span data-stu-id="4adbf-132">Ultimately, the data can be used as desired.</span></span> <span data-ttu-id="4adbf-133">例如，您可以使用它来增强业务流程中，找到新客户，或确定客户满意度问题。</span><span class="sxs-lookup"><span data-stu-id="4adbf-133">For example, you can use it to enhance business processes, locate new customers, or identify customer satisfaction issues.</span></span>

### <a name="components"></a><span data-ttu-id="4adbf-134">组件</span><span class="sxs-lookup"><span data-stu-id="4adbf-134">Components</span></span>

<span data-ttu-id="4adbf-135">此示例中使用以下 Azure 组件的列表。</span><span class="sxs-lookup"><span data-stu-id="4adbf-135">The following list of Azure components is used in this example.</span></span>

* <span data-ttu-id="4adbf-136">[Azure 存储][ storage]用来保存原始图像和视频文件与项目相关联。</span><span class="sxs-lookup"><span data-stu-id="4adbf-136">[Azure Storage][storage] is used to hold raw image and video files associated with an article.</span></span> <span data-ttu-id="4adbf-137">辅助存储帐户使用 Azure 应用服务创建和使用托管的 Azure 函数代码和日志。</span><span class="sxs-lookup"><span data-stu-id="4adbf-137">A secondary storage account is created with Azure App Service and is used to host the Azure Function code and logs.</span></span>

* <span data-ttu-id="4adbf-138">[Azure Cosmos DB] [ cosmos-db]保存项目的文本、 图像和视频跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="4adbf-138">[Azure Cosmos DB][cosmos-db] holds article text, image, and video tracking information.</span></span> <span data-ttu-id="4adbf-139">认知服务步骤的结果也存储在此处。</span><span class="sxs-lookup"><span data-stu-id="4adbf-139">The results of the Cognitive Services steps are also stored here.</span></span>

* <span data-ttu-id="4adbf-140">[Azure Functions] [ functions]执行用于响应队列消息内容，并转换传入的函数代码。</span><span class="sxs-lookup"><span data-stu-id="4adbf-140">[Azure Functions][functions] executes the function code used to respond to queue messages and transform the incoming content.</span></span> <span data-ttu-id="4adbf-141">[Azure 应用服务][ aas]承载函数代码并按顺序处理记录。</span><span class="sxs-lookup"><span data-stu-id="4adbf-141">[Azure App Service][aas] hosts the function code and processes the records serially.</span></span> <span data-ttu-id="4adbf-142">此方案包括五个函数：引入、 转换、 检测对象，人脸，并通知。</span><span class="sxs-lookup"><span data-stu-id="4adbf-142">This scenario includes five functions: Ingest, Transform, Detect Object, Face, and Notify.</span></span>

* <span data-ttu-id="4adbf-143">[Azure 服务总线][ service-bus]托管的函数使用的 Azure 服务总线队列。</span><span class="sxs-lookup"><span data-stu-id="4adbf-143">[Azure Service Bus][service-bus] hosts the Azure Service Bus queues used by the functions.</span></span>

* <span data-ttu-id="4adbf-144">[Azure 认知服务][ acs]管道基于的实现提供 AI[计算机视觉][ vision]服务，[人脸API][face]，和[翻译文本][ translate-text]机器翻译服务。</span><span class="sxs-lookup"><span data-stu-id="4adbf-144">[Azure Cognitive Services][acs] provides the AI for the pipeline based on implementations of the [Computer Vision][vision] service, [Face API][face], and [Translate Text][translate-text] machine translation service.</span></span>

* <span data-ttu-id="4adbf-145">[Azure Application Insights] [ aai]提供分析帮助您诊断问题并了解你的应用程序的功能。</span><span class="sxs-lookup"><span data-stu-id="4adbf-145">[Azure Application Insights][aai] provides analytics to help you diagnose issues and to understand functionality of your application.</span></span>

### <a name="alternatives"></a><span data-ttu-id="4adbf-146">备选项</span><span class="sxs-lookup"><span data-stu-id="4adbf-146">Alternatives</span></span>

* <span data-ttu-id="4adbf-147">而不是使用根据队列通知和 Azure Functions 的模式，为此数据流使用另一种模式。</span><span class="sxs-lookup"><span data-stu-id="4adbf-147">Instead of using a pattern based on queue notification and Azure Functions, use another pattern for this data flow.</span></span> <span data-ttu-id="4adbf-148">例如， [Azure 服务总线主题][ topics]可用于对进程并行情况下，作为本文的各个部分而不是此示例中的串行处理。</span><span class="sxs-lookup"><span data-stu-id="4adbf-148">For example, [Azure Service Bus Topics][topics] can be used to processes the various parts of the article in parallel as opposed to the serial processing done in this example.</span></span> <span data-ttu-id="4adbf-149">有关详细信息，比较[队列和主题][queues-topics]。</span><span class="sxs-lookup"><span data-stu-id="4adbf-149">For more information, compare [queues and topics][queues-topics].</span></span>

* <span data-ttu-id="4adbf-150">使用[Azure 逻辑应用][ logic-app]实现函数代码并实现记录级别如锁定[Redlock] [ redlock] （所需的并行处理 Azure Cosmos DB 支持之前[部分文档更新][partial])。</span><span class="sxs-lookup"><span data-stu-id="4adbf-150">Use [Azure Logic App][logic-app] to implement the function code and implement record-level locking such as [Redlock][redlock] (needed for parallel processing until Azure Cosmos DB supports [partial document updates][partial]).</span></span> <span data-ttu-id="4adbf-151">有关详细信息[比较函数和逻辑应用][compare]。</span><span class="sxs-lookup"><span data-stu-id="4adbf-151">For more information, [compare Functions and Logic Apps][compare].</span></span>

* <span data-ttu-id="4adbf-152">实现此体系结构中使用自定义的 AI 组件，而不是现有 Azure 服务。</span><span class="sxs-lookup"><span data-stu-id="4adbf-152">Implement this architecture using customized AI components rather than existing Azure services.</span></span> <span data-ttu-id="4adbf-153">例如，扩展使用的自定义的模型，而不是一般人计数、 性别、 图像中检测到某些人管道和年龄在此示例中收集的数据。</span><span class="sxs-lookup"><span data-stu-id="4adbf-153">For example, extend the pipeline using a customized model that detects certain people in an image as opposed to the generic people count, gender, and age data collected in this example.</span></span> <span data-ttu-id="4adbf-154">若要使用这种体系结构的自定义的机器学习或 AI 模型，生成模型作为 RESTful 终结点以便可以从 Azure Functions 调用它们。</span><span class="sxs-lookup"><span data-stu-id="4adbf-154">To use customized machine learning or AI models with this architecture, build the models as RESTful endpoints so they can be called from Azure Functions.</span></span>

* <span data-ttu-id="4adbf-155">使用不同的输入的机制而不是 RSS 源。</span><span class="sxs-lookup"><span data-stu-id="4adbf-155">Use a different input mechanism instead of RSS feeds.</span></span> <span data-ttu-id="4adbf-156">使用多个生成器或引入进程源 Azure Cosmos DB 和 Azure 存储。</span><span class="sxs-lookup"><span data-stu-id="4adbf-156">Use multiple generators or ingestion processes to feed Azure Cosmos DB and Azure Storage.</span></span>

## <a name="considerations"></a><span data-ttu-id="4adbf-157">注意事项</span><span class="sxs-lookup"><span data-stu-id="4adbf-157">Considerations</span></span>

<span data-ttu-id="4adbf-158">为简单起见，此示例方案中使用只有几个可用的 Api 和 Azure 认知服务的服务。</span><span class="sxs-lookup"><span data-stu-id="4adbf-158">For simplicity, this example scenario uses only a few of the available APIs and services from Azure Cognitive Services.</span></span> <span data-ttu-id="4adbf-159">例如，使用分析图像中的文本[文本分析 API][text-analytics]。</span><span class="sxs-lookup"><span data-stu-id="4adbf-159">For example, text in images can be analyzed using the [Text Analytics API][text-analytics].</span></span> <span data-ttu-id="4adbf-160">假定在此方案中目标语言为英语，但您可以更改任何输入[受支持的语言][ language]所选。</span><span class="sxs-lookup"><span data-stu-id="4adbf-160">The target language in this scenario is assumed to be English, but you can change the input to any [supported language][language] of your choice.</span></span>

### <a name="scalability"></a><span data-ttu-id="4adbf-161">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="4adbf-161">Scalability</span></span>

<span data-ttu-id="4adbf-162">取决于缩放的 azure 函数[托管计划][ plan]你使用。</span><span class="sxs-lookup"><span data-stu-id="4adbf-162">Azure Functions scaling depends on the [hosting plan][plan] you use.</span></span> <span data-ttu-id="4adbf-163">此解决方案假设[消耗计划][plan-c]中的计算能力将会自动分配到时所需的函数。</span><span class="sxs-lookup"><span data-stu-id="4adbf-163">This solution assumes a [Consumption plan][plan-c], in which compute power is automatically allocated to the functions when required.</span></span> <span data-ttu-id="4adbf-164">仅当函数正在运行时才需付费。</span><span class="sxs-lookup"><span data-stu-id="4adbf-164">You pay only when your functions are running.</span></span> <span data-ttu-id="4adbf-165">另一种方法是使用[Azure 应用服务][ plan-aas]计划，以便您可以分配不同数量的资源的层之间进行缩放。</span><span class="sxs-lookup"><span data-stu-id="4adbf-165">Another option is to use an [Azure App Service][plan-aas] plan, which allows you to scale between tiers to allocate a different amount of resources.</span></span>

<span data-ttu-id="4adbf-166">使用 Azure Cosmos DB 的关键是您的工作负荷大致均匀地分布在足够数量的之间[分区键][keys]。</span><span class="sxs-lookup"><span data-stu-id="4adbf-166">With Azure Cosmos DB, the key is to distribute your workload roughly evenly among a sufficiently large number of [partition keys][keys].</span></span> <span data-ttu-id="4adbf-167">没有任何限制到一个容器可以存储的数据总量或的总量[吞吐量][ throughput]的容器可以支持。</span><span class="sxs-lookup"><span data-stu-id="4adbf-167">There's no limit to the total amount of data that a container can store or to the total amount of [throughput][throughput] that a container can support.</span></span>

### <a name="management-and-logging"></a><span data-ttu-id="4adbf-168">管理和日志记录</span><span class="sxs-lookup"><span data-stu-id="4adbf-168">Management and logging</span></span>

<span data-ttu-id="4adbf-169">此解决方案使用[Application Insights] [ aai]收集性能和日志记录信息。</span><span class="sxs-lookup"><span data-stu-id="4adbf-169">This solution uses [Application Insights][aai] to collect performance and logging information.</span></span> <span data-ttu-id="4adbf-170">使用与此部署所需的其他服务相同的资源组中部署创建的 Application Insights 实例。</span><span class="sxs-lookup"><span data-stu-id="4adbf-170">An instance of Application Insights is created with the deployment in the same resource group as the other services needed for this deployment.</span></span>

<span data-ttu-id="4adbf-171">若要查看解决方案生成的日志：</span><span class="sxs-lookup"><span data-stu-id="4adbf-171">To view the logs generated by the solution:</span></span>

1. <span data-ttu-id="4adbf-172">转到[Azure 门户][ portal]并导航到为部署创建的资源组。</span><span class="sxs-lookup"><span data-stu-id="4adbf-172">Go to [Azure portal][portal] and navigate to the resource group created for the deployment.</span></span>

2. <span data-ttu-id="4adbf-173">单击**Application Insights**实例。</span><span class="sxs-lookup"><span data-stu-id="4adbf-173">Click the **Application Insights** instance.</span></span>

3. <span data-ttu-id="4adbf-174">从**Application Insights**部分中，导航到**调查\\搜索**和搜索数据。</span><span class="sxs-lookup"><span data-stu-id="4adbf-174">From the **Application Insights** section, navigate to **Investigate\\Search** and search the data.</span></span>

### <a name="security"></a><span data-ttu-id="4adbf-175">安全</span><span class="sxs-lookup"><span data-stu-id="4adbf-175">Security</span></span>

<span data-ttu-id="4adbf-176">Azure Cosmos DB 使用受保护的连接和共享的访问签名通过 C\#由 Microsoft 提供的 SDK。</span><span class="sxs-lookup"><span data-stu-id="4adbf-176">Azure Cosmos DB uses a secured connection and shared access signature through the C\# SDK provided by Microsoft.</span></span> <span data-ttu-id="4adbf-177">没有其他面向外部的图面区域。</span><span class="sxs-lookup"><span data-stu-id="4adbf-177">There are no other externally facing surface areas.</span></span> <span data-ttu-id="4adbf-178">了解有关安全性的详细信息[最佳做法][ db-practices]适用于 Azure Cosmos DB。</span><span class="sxs-lookup"><span data-stu-id="4adbf-178">Learn more about security [best practices][db-practices] for Azure Cosmos DB.</span></span>

## <a name="pricing"></a><span data-ttu-id="4adbf-179">定价</span><span class="sxs-lookup"><span data-stu-id="4adbf-179">Pricing</span></span>

<span data-ttu-id="4adbf-180">保留此部署可用的估计每日成本大约为\$20 美国与通过系统移动任何数据。</span><span class="sxs-lookup"><span data-stu-id="4adbf-180">The estimated daily cost to keep this deployment available is approximately \$20 U.S. with no data moving through the system.</span></span>

<span data-ttu-id="4adbf-181">Azure Cosmos DB 是功能强大，但会导致最大[成本][ db-cost]此部署中。</span><span class="sxs-lookup"><span data-stu-id="4adbf-181">Azure Cosmos DB is powerful but incurs the greatest [cost][db-cost] in this deployment.</span></span> <span data-ttu-id="4adbf-182">可以通过重构提供的 Azure 函数代码中使用另一种存储解决方案。</span><span class="sxs-lookup"><span data-stu-id="4adbf-182">You can use another storage solution by refactoring the Azure Functions code provided.</span></span>

<span data-ttu-id="4adbf-183">有关 Azure Functions 会有所不同，具体取决于定价[计划][ function-plan]在中运行。</span><span class="sxs-lookup"><span data-stu-id="4adbf-183">Pricing for Azure Functions varies depending on the [plan][function-plan] it runs in.</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="4adbf-184">部署方案</span><span class="sxs-lookup"><span data-stu-id="4adbf-184">Deploy the scenario</span></span>

> [!NOTE]
> <span data-ttu-id="4adbf-185">必须已经有 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="4adbf-185">You must have an existing Azure account.</span></span> <span data-ttu-id="4adbf-186">如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][free]。</span><span class="sxs-lookup"><span data-stu-id="4adbf-186">If you don't have an Azure subscription, create a [free account][free] before you begin.</span></span>

<span data-ttu-id="4adbf-187">此方案中的所有代码现都已推出[GitHub] [ github]存储库。</span><span class="sxs-lookup"><span data-stu-id="4adbf-187">All the code for this scenario is available in the [GitHub][github] repository.</span></span> <span data-ttu-id="4adbf-188">此存储库包含用于生成馈送对于此演示管道生成器应用程序的源代码。</span><span class="sxs-lookup"><span data-stu-id="4adbf-188">This repository contains the source code used to build the generator application that feeds the pipeline for this demo.</span></span>

[architecture]: ./media/mass-ingestion-newsfeeds-architecture.png
[aai]: /azure/azure-monitor/app/app-insights-overview
[aas]: https://azure.microsoft.com/try/app-service/
[acs]: https://azure.microsoft.com/services/cognitive-services/directory/
[collection]: /rest/api/cosmos-db/collections
[compare]: /azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs#compare-azure-functions-and-azure-logic-apps
[cosmos-db]: /azure/cosmos-db/introduction
[db-cost]: https://azure.microsoft.com/pricing/details/cosmos-db/
[db-practices]: /azure/cosmos-db/database-security
[db-collection]: /azure/cosmos-db/databases-containers-items
[english]: https://www.nasa.gov/rss/dyn/breaking_news.rss
[face]: /azure/cognitive-services/face/overview
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[functions]: /azure/azure-functions/functions-overview
[function-plan]: /azure/azure-functions/functions-scale
[german]: http://www.bamf.de/SiteGlobals/Functions/RSS/DE/Feed/RSSNewsfeed_Meldungen
[github]: https://github.com/Azure/cognitive-services
[keys]: /azure/cosmos-db/partition-data
[language]: /azure/cognitive-services/translator/reference/v3-0-languages
[logic-app]: /azure/logic-apps/logic-apps-overview
[queues-topics]: /azure/service-bus-messaging/service-bus-queues-topics-subscriptions
[partial]: https://feedback.azure.com/forums/263030-azure-cosmos-db/suggestions/6693091-be-able-to-do-partial-updates-on-document
[plan]: /azure/azure-functions/functions-scale
[plan-aas]: /azure/azure-functions/functions-scale#app-service-plan
[plan-c]: /azure/azure-functions/functions-scale#consumption-plan
[portal]: http://portal.azure.com
[redlock]: https://redis.io/topics/distlock
[russian]: http://government.ru/all/rss/
[service-bus]: /azure/service-bus-messaging/
[storage]: /azure/storage/common/storage-account-overview 
[throughput]: /azure/cosmos-db/scaling-throughput
[topics]: /azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions
[text-analytics]: /azure/cognitive-services/text-analytics/
[translate-text]: /azure/cognitive-services/translator/translator-info-overview
[vision]: /azure/cognitive-services/computer-vision/home
