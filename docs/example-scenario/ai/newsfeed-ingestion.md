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
# <a name="mass-ingestion-and-analysis-of-news-feeds-on-azure"></a>Azure 上新闻源的大量引入和分析

此示例方案中介绍的批量引入和准实时分析使用公共 RSS 新闻源文档的管道。  它使用 Azure 认知服务提供有用的见解，包括文本翻译、 面部识别和情绪检测。

此方案中包含的示例[英语][english]，[俄语][russian]，并[德语][german]新闻源，但您可以轻松地将其扩展到其他 RSS 源。 为了便于部署，数据收集、 处理和分析是完全基于 Azure 服务。

## <a name="relevant-use-cases"></a>相关用例

尽管此方案基于 RSS 源的处理，它是与任何文档、 网站或文章，您需要对相关：

* 转换为所选的语言的任何文本。
* 在数字内容中查找关键短语、 实体和用户情绪。
* 与数字文章关联的图像中检测对象、 文本和特征点。
* 在数字内容与关联任何图像中检测通过其性别和年龄的人。

## <a name="architecture"></a>体系结构

![][architecture]

数据流经解决方案的情形如下所示：

1. RSS 新闻源充当从文档或项目中获取数据的生成器。 例如，其中一篇文章，数据通常包含一个标题，新闻项的原始正文的摘要和有时图像。

2. 生成器或引入过程将项目和任何关联的图像插入到 Azure Cosmos DB[集合][collection]。

3. 通知会触发将文章文本存储在 CosmosDB 和 Azure Blob 存储中的文章图像 （如果有） 的 Azure Functions 中的插入函数。  文章然后传递给下一个队列。

4. Translate 函数由队列事件触发。 它使用[翻译文本 API] [ translate-text] Azure 认知服务为检测语言中，如果有必要，将转换并收集情绪、 关键短语和实体正文和标题。 然后它将文章传递给下一个队列。

5. 排队的文章中触发检测函数。 它使用[计算机视觉][ vision]服务能够检测对象、 特征点和手写的文字中的关联的映像，然后将传递项目到下一个队列。

6. 人脸函数触发触发排队的文章中的说明。 它使用[Azure 人脸 API] [ face]服务来检测人脸的性别和年龄在关联的图像，然后再将文章传递给下一个队列。

7. 在所有函数都都完成后，会触发通知函数。 它加载项目的处理的记录，并扫描的所需的任何结果。 如果找到，则内容将标记通知发送到所选的系统。

在每个处理步骤，该函数将结果写入到 Azure Cosmos DB。 从根本上讲，可以使用这些数据按需。 例如，您可以使用它来增强业务流程中，找到新客户，或确定客户满意度问题。

### <a name="components"></a>组件

此示例中使用以下 Azure 组件的列表。

* [Azure 存储][ storage]用来保存原始图像和视频文件与项目相关联。 辅助存储帐户使用 Azure 应用服务创建和使用托管的 Azure 函数代码和日志。

* [Azure Cosmos DB] [ cosmos-db]保存项目的文本、 图像和视频跟踪信息。 认知服务步骤的结果也存储在此处。

* [Azure Functions] [ functions]执行用于响应队列消息内容，并转换传入的函数代码。 [Azure 应用服务][ aas]承载函数代码并按顺序处理记录。 此方案包括五个函数：引入、 转换、 检测对象，人脸，并通知。

* [Azure 服务总线][ service-bus]托管的函数使用的 Azure 服务总线队列。

* [Azure 认知服务][ acs]管道基于的实现提供 AI[计算机视觉][ vision]服务，[人脸API][face]，和[翻译文本][ translate-text]机器翻译服务。

* [Azure Application Insights] [ aai]提供分析帮助您诊断问题并了解你的应用程序的功能。

### <a name="alternatives"></a>备选项

* 而不是使用根据队列通知和 Azure Functions 的模式，为此数据流使用另一种模式。 例如， [Azure 服务总线主题][ topics]可用于对进程并行情况下，作为本文的各个部分而不是此示例中的串行处理。 有关详细信息，比较[队列和主题][queues-topics]。

* 使用[Azure 逻辑应用][ logic-app]实现函数代码并实现记录级别如锁定[Redlock] [ redlock] （所需的并行处理 Azure Cosmos DB 支持之前[部分文档更新][partial])。 有关详细信息[比较函数和逻辑应用][compare]。

* 实现此体系结构中使用自定义的 AI 组件，而不是现有 Azure 服务。 例如，扩展使用的自定义的模型，而不是一般人计数、 性别、 图像中检测到某些人管道和年龄在此示例中收集的数据。 若要使用这种体系结构的自定义的机器学习或 AI 模型，生成模型作为 RESTful 终结点以便可以从 Azure Functions 调用它们。

* 使用不同的输入的机制而不是 RSS 源。 使用多个生成器或引入进程源 Azure Cosmos DB 和 Azure 存储。

## <a name="considerations"></a>注意事项

为简单起见，此示例方案中使用只有几个可用的 Api 和 Azure 认知服务的服务。 例如，使用分析图像中的文本[文本分析 API][text-analytics]。 假定在此方案中目标语言为英语，但您可以更改任何输入[受支持的语言][ language]所选。

### <a name="scalability"></a>可伸缩性

取决于缩放的 azure 函数[托管计划][ plan]你使用。 此解决方案假设[消耗计划][plan-c]中的计算能力将会自动分配到时所需的函数。 仅当函数正在运行时才需付费。 另一种方法是使用[Azure 应用服务][ plan-aas]计划，以便您可以分配不同数量的资源的层之间进行缩放。

使用 Azure Cosmos DB 的关键是您的工作负荷大致均匀地分布在足够数量的之间[分区键][keys]。 没有任何限制到一个容器可以存储的数据总量或的总量[吞吐量][ throughput]的容器可以支持。

### <a name="management-and-logging"></a>管理和日志记录

此解决方案使用[Application Insights] [ aai]收集性能和日志记录信息。 使用与此部署所需的其他服务相同的资源组中部署创建的 Application Insights 实例。

若要查看解决方案生成的日志：

1. 转到[Azure 门户][ portal]并导航到为部署创建的资源组。

2. 单击**Application Insights**实例。

3. 从**Application Insights**部分中，导航到**调查\\搜索**和搜索数据。

### <a name="security"></a>安全

Azure Cosmos DB 使用受保护的连接和共享的访问签名通过 C\#由 Microsoft 提供的 SDK。 没有其他面向外部的图面区域。 了解有关安全性的详细信息[最佳做法][ db-practices]适用于 Azure Cosmos DB。

## <a name="pricing"></a>定价

保留此部署可用的估计每日成本大约为\$20 美国与通过系统移动任何数据。

Azure Cosmos DB 是功能强大，但会导致最大[成本][ db-cost]此部署中。 可以通过重构提供的 Azure 函数代码中使用另一种存储解决方案。

有关 Azure Functions 会有所不同，具体取决于定价[计划][ function-plan]在中运行。

## <a name="deploy-the-scenario"></a>部署方案

> [!NOTE]
> 必须已经有 Azure 帐户。 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][free]。

此方案中的所有代码现都已推出[GitHub] [ github]存储库。 此存储库包含用于生成馈送对于此演示管道生成器应用程序的源代码。

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
