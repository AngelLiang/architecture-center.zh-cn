---
title: 用于电子商务的智能产品搜索引擎
description: 在电子商务应用程序中提供世界一流的搜索体验。
author: jelledruyts
ms.date: 09/14/2018
ms.openlocfilehash: f18e9fd3705c24da71da747c46ab42f263fd06af
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818742"
---
# <a name="intelligent-product-search-engine-for-e-commerce"></a>用于电子商务的智能产品搜索引擎

本示例方案演示如何使用专用搜索服务来大幅提高电子商务客户搜索结果的相关性。

搜索是可让客户找到并最终购买产品的主要机制，因此，至关重要的是，搜索结果必须与搜索查询的意向相关，并且端到端的搜索体验能够提供近实时的结果、语言分析、地理位置匹配、筛选、分面、自动填充和匹配项突出显示等，以此符合巨量信息搜索的预期。

假设某个典型电子商务 Web 应用程序的产品数据存储在 SQL Server 或 Azure SQL 数据库等等关系数据库中。 搜索查询通常是使用 `LIKE` 查询或[全文搜索][docs-sql-fts]功能在数据库内部处理的。 如果改用 [Azure 搜索][docs-search]，则可以解放操作数据库，使其不用执行查询处理，同时，可以轻松开始利用这些难以实现的功能，让它们尽量为客户提供最佳的搜索体验。 此外，由于 Azure 搜索是一个平台即服务 (PaaS) 组件，你无需考虑如何管理基础结构或成为搜索专家。

## <a name="relevant-use-cases"></a>相关用例

其他这些用例采用类似的设计模式：

* 查找靠近用户实际位置的房地产清单或商店。
* 在新闻网站中搜索新闻文章，或查找赛事结果，其中最近的信息排在前面。
* 在大型存储库中搜索决策人和公证人等以文档为中心的组织。

最终，包含某种搜索功能的任何应用程序都可以从专用的搜索服务中受益。

## <a name="architecture"></a>体系结构

![电子商务智能产品搜索引擎中涉及的 Azure 组件的体系结构概况][architecture]

此方案包括一个电子商务解决方案，其中的客户可以搜索产品目录。
1. 客户从任意设备导航到**电子商务 Web 应用程序**。
2. 产品目录在 **Azure SQL 数据库**中维护，以进行事务处理。
3. Azure 搜索使用**搜索索引器**通过集成式更改跟踪来自动保持其搜索索引的最新状态。
4. 客户的搜索查询卸载到 **Azure 搜索**服务，后者处理查询并返回最相关的结果。
5. 作为基于 Web 的搜索体验的替代方案，客户还可以在社交媒体中或者直接从数码助手中使用**聊天机器人**来搜索产品，并逐渐优化其搜索查询和结果。
6. 可以选择性地使用**认知搜索**功能，运用人工智能进行更智能化的处理。

### <a name="components"></a>组件

* [应用服务 - Web 应用][docs-webapps]托管的 Web 应用程序可以实现自动缩放和高可用性，不需管理基础结构。
* [SQL 数据库][docs-sql-database]是 Microsoft Azure 中通用的关系数据库托管服务，支持关系数据、JSON、空间和 XML 等结构。
* [Azure 搜索][docs-search]是一种搜索即服务云解决方案，它可以根据 Web、移动和企业应用程序中的专用异类内容提供丰富的搜索体验。
* [机器人服务][docs-botservice]提供的工具可用于生成、测试、部署和管理智能机器人。
* [认知服务][docs-cognitive]允许你使用智能算法，以自然的沟通方式来观察、倾听、表述、了解和解释用户需求。

### <a name="alternatives"></a>备选项

* 可以使用**数据库中搜索**功能（例如，使用 SQL Server 全文搜索），但随后事务存储也会处理查询（从而需要增大处理能力），并且数据库中的搜索功能存在更大的限制。
* 可将开源 [Apache Lucene][apache-lucene]（Azure 搜索的构建基础）托管在 Azure 虚拟机上，但随后你仍要管理基础结构即服务 (IaaS)，并且无法受益于 Azure 搜索在 Lucene 上提供的许多功能。
* 还可以考虑部署 Azure 市场中提供的 [Elastic Search][elastic-marketplace]，这是第三方供应商提供的支持搜索的替代产品，但在这种情况下，也要运行 IaaS工作负荷。

可用于数据层的其他选项包括：

* [Cosmos DB](/azure/cosmos-db/introduction) - Microsoft 提供的全局分布式多模型数据库。 Costmos DB 提供的平台适合运行其他数据模型，例如 Mongo DB、Cassandra、Graph 数据或简单的表存储。 Azure 搜索还支持直接从 Cosmos DB 为数据编制索引。

## <a name="considerations"></a>注意事项

### <a name="scalability"></a>可伸缩性

Azure 搜索服务的[定价层][search-tier]不会确定可用的功能，主要用于[容量规划][search-capacity]，因为它定义了提供的最大存储量，以及可以预配的分区和副本数目。 使用**分区**可为更多文档编制索引并获取更高的写入吞吐量，而**副本**提供更高的每秒查询次数 (QPS) 和高可用性。

可以动态更改分区和副本数目，但无法更改定价层，因此，应该认真考虑适合目标工作负荷的层。 如果仍然需要更改层，请并列预配一个新服务并将索引重新加载到该服务，此时，可将应用程序指向新服务。

### <a name="availability"></a>可用性

如果至少有两个副本，则 Azure 搜索可为读取（即查询）操作提供 [99.9% 的可用性 SLA][search-sla]；如果至少有三个副本，则可为更新（即，更新搜索索引）操作提供同等的 SLA。 因此，如果希望客户能够可靠地执行搜索，则至少应预配 2 个副本；如果要将索引的实际更改视为高可用性操作，则至少应预配 3 个副本。

如果需要在不停机的情况下对索引进行重大更改（例如，更改数据类型、删除或重命名字段），则需要重建索引。 类似于更改服务层，这意味着要创建新索引、在其中填充数据，然后将应用程序更新为指向新索引。

### <a name="security"></a>安全

Azure 搜索符合许多[安全和数据隐私标准][search-security]，因此可在大多数行业中使用。

为了保护对服务的访问，Azure 搜索使用两种类型的密钥：**管理密钥** - 用于针对服务执行任何任务；**查询密钥** - 只可用于执行查询等只读操作。 通常，执行搜索的应用程序不会更新索引，因此，只应在此类应用程序中配置查询密钥，而不配置管理密钥（尤其是从最终用户设备执行搜索时，例如，通过 Web 浏览器中运行的脚本执行）。

### <a name="search-relevance"></a>搜索相关性

电子商务应用程序是否成功在很大程度上取决于提供给客户的搜索结果的相关性。 根据用户调查精心优化搜索服务以提供最佳结果，或依赖于[搜索流量分析][search-analysis]等内置功能来了解客户的搜索模式，可以基于数据做出决策。

优化搜索服务的典型方法包括：

* 根据与查询匹配的字段、数据的新旧程度、与用户之间的地理距离等条件，使用[评分配置文件][search-scoring]来影响搜索结果
* 借助使用高级自然语言处理 (NLP) 堆栈的 [Microsoft 语言分析器][search-languages]来更好地解释查询
* 使用[自定义分析器][search-analyzers]来确保正确找到产品，尤其是要根据产品品牌和型号等信息执行非语言搜索时。

## <a name="deploy-this-scenario"></a>部署此方案

若要部署此方案的更完整电子商务版本，可以遵循此[分步教程][end-to-end-walkthrough]，其中提供了一个运行简单购票应用程序的 .NET 示例应用程序。 该版本还包含 Azure 搜索，并使用了介绍的许多功能。 此外还有一个资源管理器模板，可以自动部署大多数 Azure 资源。

## <a name="pricing"></a>定价

为方便用户了解运行本方案的成本，成本计算器中预配置了上述所有服务。 若要了解自己的特定用例的定价变化情况，请按预期用法更改相应的变量。

我们已根据你预期接收的流量提供了三个示例性的成本配置文件：

* [小型][small-pricing]：在此配置文件中，我们将使用单个 `Standard S1` Web 应用来托管网站，并使用 Azure 机器人服务的免费层、单个 `Basic` Azure 搜索服务和 `Standard S2` SQL 数据库。
* [中型][medium-pricing]：将 Web 应用扩展为 `Standard S3` 层的两个实例，将搜索服务升级到 `Standard S1` 层，并使用一个 `Standard S6` SQL 数据库。
* [大型][large-pricing]：在最大的配置文件中，我们将使用 `Premium P2V2` Web 应用的四个实例，将 Azure 机器人服务升级到 `Standard S1` 层（高级通道中包含 1,000,000 条消息），使用 2 个单位的 `Standard S3` Azure 搜索服务和一个 `Premium P6` SQL 数据库。

## <a name="related-resources"></a>相关资源

有关 Azure 搜索的详细信息，请访问[文档中心][docs-search]、查看[示例][search-samples]，或查看完全成熟的[演示站点][search-demo]的运作方式。

<!-- links -->
[architecture]: ./media/architecture-ecommerce-search.png
[docs-sql-fts]: /sql/relational-databases/search/query-with-full-text-search
[docs-search]: /azure/search/search-what-is-azure-search
[docs-sql-database]: /azure/sql-database/sql-database-technical-overview
[docs-webapps]: /azure/app-service/app-service-web-overview
[docs-botservice]: /azure/bot-service/
[docs-cognitive]: /azure/cognitive-services/
[apache-lucene]: https://lucene.apache.org/
[elastic-marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/elastic.elasticsearch
[end-to-end-walkthrough]: https://github.com/Azure/fta-customerfacingapps/tree/master/ecommerce/articles
[search-sla]: https://go.microsoft.com/fwlink/?LinkId=716855
[search-tier]: /azure/search/search-sku-tier
[search-capacity]: /azure/search/search-capacity-planning
[search-security]: /azure/search/search-security-overview
[search-analysis]: /azure/search/search-traffic-analytics
[search-languages]: /rest/api/searchservice/language-support
[search-analyzers]: /rest/api/searchservice/custom-analyzers-in-azure-search
[search-scoring]: /rest/api/searchservice/add-scoring-profiles-to-a-search-index
[search-samples]: https://azure.microsoft.com/resources/samples/?service=search&sort=0
[search-demo]: https://azjobsdemo.azurewebsites.net/
[small-pricing]: https://azure.com/e/db2672a55b6b4d768ef0060a8d9759bd
[medium-pricing]: https://azure.com/e/a5ad0706c9e74add811e83ef83766a1c
[large-pricing]: https://azure.com/e/57f95a898daa487795bd305599973ee6
