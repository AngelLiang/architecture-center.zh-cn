---
title: 用于电子商务的智能产品搜索引擎
titleSuffix: Azure Example Scenarios
description: 在电子商务应用程序中提供世界一流的搜索体验。
author: jelledruyts
ms.date: 09/14/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-ecommerce-search.png
ms.openlocfilehash: 12829c0b765831efdd08c8116a8fd847bd4e6abd
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245808"
---
# <a name="intelligent-product-search-engine-for-e-commerce"></a><span data-ttu-id="23bf4-103">用于电子商务的智能产品搜索引擎</span><span class="sxs-lookup"><span data-stu-id="23bf4-103">Intelligent product search engine for e-commerce</span></span>

<span data-ttu-id="23bf4-104">本示例方案演示如何使用专用搜索服务来大幅提高电子商务客户搜索结果的相关性。</span><span class="sxs-lookup"><span data-stu-id="23bf4-104">This example scenario shows how using a dedicated search service can dramatically increase the relevance of search results for your e-commerce customers.</span></span>

<span data-ttu-id="23bf4-105">搜索是可让客户找到并最终购买产品的主要机制，因此，至关重要的是，搜索结果必须与搜索查询的意向相关，并且端到端的搜索体验能够提供近实时的结果、语言分析、地理位置匹配、筛选、分面、自动填充和匹配项突出显示等，藉此符合巨量信息搜索的预期。</span><span class="sxs-lookup"><span data-stu-id="23bf4-105">Search is the primary mechanism through which customers find and ultimately purchase products, making it essential that search results are relevant to the _intent_ of the search query, and that the end-to-end search experience matches that of search giants by providing near-instant results, linguistic analysis, geo-location matching, filtering, faceting, autocomplete, hit highlighting, etc.</span></span>

<span data-ttu-id="23bf4-106">假设某个典型电子商务 Web 应用程序的产品数据存储在 SQL Server 或 Azure SQL 数据库等等关系数据库中。</span><span class="sxs-lookup"><span data-stu-id="23bf4-106">Imagine a typical e-commerce web application with product data stored in a relational database like SQL Server or Azure SQL Database.</span></span> <span data-ttu-id="23bf4-107">搜索查询通常是使用 `LIKE` 查询或[全文搜索][docs-sql-fts]功能在数据库内部处理的。</span><span class="sxs-lookup"><span data-stu-id="23bf4-107">Search queries are often handled inside the database using `LIKE` queries or [Full-Text Search][docs-sql-fts] features.</span></span> <span data-ttu-id="23bf4-108">如果改用 [Azure 搜索][docs-search]，则可以解放操作数据库，使其不用执行查询处理，同时，可以轻松开始利用这些难以实现的功能，让它们尽量为客户提供最佳的搜索体验。</span><span class="sxs-lookup"><span data-stu-id="23bf4-108">By using [Azure Search][docs-search] instead, you free up your operational database from the query processing and you can easily start taking advantage of those hard-to-implement features that provide your customers with the best possible search experience.</span></span> <span data-ttu-id="23bf4-109">此外，由于 Azure 搜索是一个平台即服务 (PaaS) 组件，你无需考虑如何管理基础结构或成为搜索专家。</span><span class="sxs-lookup"><span data-stu-id="23bf4-109">Also, because Azure Search is a platform as a service (PaaS) component, you don't have to worry about managing infrastructure or becoming a search expert.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="23bf4-110">相关用例</span><span class="sxs-lookup"><span data-stu-id="23bf4-110">Relevant use cases</span></span>

<span data-ttu-id="23bf4-111">其他相关用例包括：</span><span class="sxs-lookup"><span data-stu-id="23bf4-111">Other relevant use cases include:</span></span>

- <span data-ttu-id="23bf4-112">查找靠近用户实际位置的房地产清单或商店。</span><span class="sxs-lookup"><span data-stu-id="23bf4-112">Finding real estate listings or stores near the user's physical location.</span></span>
- <span data-ttu-id="23bf4-113">在新闻网站中搜索新闻文章，或查找赛事结果，其中最近的信息排在前面。</span><span class="sxs-lookup"><span data-stu-id="23bf4-113">Searching for articles in a news site or looking for sports results, with a higher preference for more _recent_ information.</span></span>
- <span data-ttu-id="23bf4-114">在大型存储库中搜索决策人和公证人等以文档为中心的组织。</span><span class="sxs-lookup"><span data-stu-id="23bf4-114">Searching through large repositories for _document-centric_ organizations like policy makers and notaries.</span></span>

<span data-ttu-id="23bf4-115">最终，包含某种搜索功能的任何应用程序都可以从专用的搜索服务中受益。</span><span class="sxs-lookup"><span data-stu-id="23bf4-115">Ultimately, _any_ application that has some form of search functionality can benefit from a dedicated search service.</span></span>

## <a name="architecture"></a><span data-ttu-id="23bf4-116">体系结构</span><span class="sxs-lookup"><span data-stu-id="23bf4-116">Architecture</span></span>

![电子商务智能产品搜索引擎中涉及的 Azure 组件的体系结构概况][architecture]

<span data-ttu-id="23bf4-118">此方案包括一个电子商务解决方案，其中的客户可以搜索产品目录。</span><span class="sxs-lookup"><span data-stu-id="23bf4-118">This scenario covers an e-commerce solution where customers can search through a product catalog.</span></span>

1. <span data-ttu-id="23bf4-119">客户从任意设备导航到**电子商务 Web 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="23bf4-119">Customers navigate to the **e-commerce web application** from any device.</span></span>
2. <span data-ttu-id="23bf4-120">产品目录在 **Azure SQL 数据库**中维护，以进行事务处理。</span><span class="sxs-lookup"><span data-stu-id="23bf4-120">The product catalog is maintained in an **Azure SQL Database** for transactional processing.</span></span>
3. <span data-ttu-id="23bf4-121">Azure 搜索使用**搜索索引器**通过集成式更改跟踪来自动保持其搜索索引的最新状态。</span><span class="sxs-lookup"><span data-stu-id="23bf4-121">Azure Search uses a **search indexer** to automatically keep its search index up-to-date through integrated change tracking.</span></span>
4. <span data-ttu-id="23bf4-122">客户的搜索查询卸载到 **Azure 搜索**服务，后者处理查询并返回最相关的结果。</span><span class="sxs-lookup"><span data-stu-id="23bf4-122">Customer's search queries are offloaded to the **Azure Search** service, which processes the query and returns the most relevant results.</span></span>
5. <span data-ttu-id="23bf4-123">作为基于 Web 的搜索体验的替代方案，客户还可以在社交媒体中或者直接从数码助手中使用**聊天机器人**来搜索产品，并逐渐优化其搜索查询和结果。</span><span class="sxs-lookup"><span data-stu-id="23bf4-123">As an alternative to a web-based search experience, customers can also use a **conversational bot** in social media or straight from digital assistants to search for products and incrementally refine their search query and results.</span></span>
6. <span data-ttu-id="23bf4-124">可以选择性地使用**认知搜索**功能，运用人工智能进行更智能化的处理。</span><span class="sxs-lookup"><span data-stu-id="23bf4-124">Optionally, the **Cognitive Search** feature can be used to apply artificial intelligence for even smarter processing.</span></span>

### <a name="components"></a><span data-ttu-id="23bf4-125">组件</span><span class="sxs-lookup"><span data-stu-id="23bf4-125">Components</span></span>

- <span data-ttu-id="23bf4-126">[应用服务 - Web 应用][docs-webapps]托管的 Web 应用程序可以实现自动缩放和高可用性，不需管理基础结构。</span><span class="sxs-lookup"><span data-stu-id="23bf4-126">[App Services - Web Apps][docs-webapps] hosts web applications allowing autoscale and high availability without having to manage infrastructure.</span></span>
- <span data-ttu-id="23bf4-127">[SQL 数据库][docs-sql-database]是 Microsoft Azure 中通用的关系数据库托管服务，支持关系数据、JSON、空间和 XML 等结构。</span><span class="sxs-lookup"><span data-stu-id="23bf4-127">[SQL Database][docs-sql-database] is a general-purpose relational database-managed service in Microsoft Azure that supports structures such as relational data, JSON, spatial, and XML.</span></span>
- <span data-ttu-id="23bf4-128">[Azure 搜索][docs-search]是一种搜索即服务云解决方案，它可以根据 Web、移动和企业应用程序中的专用异类内容提供丰富的搜索体验。</span><span class="sxs-lookup"><span data-stu-id="23bf4-128">[Azure Search][docs-search] is a search-as-a-service cloud solution that provides a rich search experience over private, heterogenous content in web, mobile, and enterprise applications.</span></span>
- <span data-ttu-id="23bf4-129">[机器人服务][docs-botservice]提供的工具可用于生成、测试、部署和管理智能机器人。</span><span class="sxs-lookup"><span data-stu-id="23bf4-129">[Bot Service][docs-botservice] provides tools to build, test, deploy, and manage intelligent bots.</span></span>
- <span data-ttu-id="23bf4-130">[认知服务][docs-cognitive]允许你使用智能算法，以自然的沟通方式来观察、倾听、表述、了解和解释用户需求。</span><span class="sxs-lookup"><span data-stu-id="23bf4-130">[Cognitive Services][docs-cognitive] lets you use intelligent algorithms to see, hear, speak, understand, and interpret your user needs through natural methods of communication.</span></span>

### <a name="alternatives"></a><span data-ttu-id="23bf4-131">备选项</span><span class="sxs-lookup"><span data-stu-id="23bf4-131">Alternatives</span></span>

- <span data-ttu-id="23bf4-132">可以使用**数据库中搜索**功能（例如，使用 SQL Server 全文搜索），但随后事务存储也会处理查询（从而需要增大处理能力），并且数据库中的搜索功能存在更大的限制。</span><span class="sxs-lookup"><span data-stu-id="23bf4-132">You could use **in-database search** capabilities, for example, through SQL Server full-text search, but then your transactional store also processes queries (increasing the need for processing power) and the search capabilities inside the database are more limited.</span></span>
- <span data-ttu-id="23bf4-133">可将开源 [Apache Lucene][apache-lucene]（Azure 搜索的构建基础）托管在 Azure 虚拟机上，但随后你仍要管理基础结构即服务 (IaaS)，并且无法受益于 Azure 搜索在 Lucene 上提供的许多功能。</span><span class="sxs-lookup"><span data-stu-id="23bf4-133">You could host the open-source [Apache Lucene][apache-lucene] (on which Azure Search is built upon) on Azure Virtual Machines, but then you are back to managing Infrastructure-as-a-Service (IaaS) and don't benefit from the many features that Azure Search provides on top of Lucene.</span></span>
- <span data-ttu-id="23bf4-134">还可以考虑部署 Azure 市场中提供的 [Elastic Search][elastic-marketplace]，这是第三方供应商提供的支持搜索的替代产品，但在这种情况下，也要运行 IaaS工作负荷。</span><span class="sxs-lookup"><span data-stu-id="23bf4-134">You could also consider deploying [Elastic Search][elastic-marketplace] from the Azure Marketplace, which is an alternative and capable search product from a third-party vendor, but also in this case you are running an IaaS workload.</span></span>

<span data-ttu-id="23bf4-135">可用于数据层的其他选项包括：</span><span class="sxs-lookup"><span data-stu-id="23bf4-135">Other options for the data tier include:</span></span>

- <span data-ttu-id="23bf4-136">[Cosmos DB](/azure/cosmos-db/introduction) - Microsoft 提供的全局分布式多模型数据库。</span><span class="sxs-lookup"><span data-stu-id="23bf4-136">[Cosmos DB](/azure/cosmos-db/introduction) - Microsoft's globally distributed, multi-model database.</span></span> <span data-ttu-id="23bf4-137">Costmos DB 提供的平台适合运行其他数据模型，例如 Mongo DB、Cassandra、Graph 数据或简单的表存储。</span><span class="sxs-lookup"><span data-stu-id="23bf4-137">Costmos DB provides a platform to run other data models such as Mongo DB, Cassandra, Graph data, or simple table storage.</span></span> <span data-ttu-id="23bf4-138">Azure 搜索还支持直接从 Cosmos DB 为数据编制索引。</span><span class="sxs-lookup"><span data-stu-id="23bf4-138">Azure Search also supports indexing the data from Cosmos DB directly.</span></span>

## <a name="considerations"></a><span data-ttu-id="23bf4-139">注意事项</span><span class="sxs-lookup"><span data-stu-id="23bf4-139">Considerations</span></span>

### <a name="scalability"></a><span data-ttu-id="23bf4-140">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="23bf4-140">Scalability</span></span>

<span data-ttu-id="23bf4-141">Azure 搜索服务的[定价层][search-tier]不会确定可用的功能，主要用于[容量规划][search-capacity]，因为它定义了提供的最大存储量，以及可以预配的分区和副本数目。</span><span class="sxs-lookup"><span data-stu-id="23bf4-141">The [pricing tier][search-tier] of the Azure Search service doesn't determine the available features but is used mainly for [capacity planning][search-capacity] as it defines the maximum storage you get and how many partitions and replicas you can provision.</span></span> <span data-ttu-id="23bf4-142">使用**分区**可为更多文档编制索引并获取更高的写入吞吐量，而**副本**提供更高的每秒查询次数 (QPS) 和高可用性。</span><span class="sxs-lookup"><span data-stu-id="23bf4-142">**Partitions** allow you to index more documents and get higher write throughputs, whereas **replicas** provide more Queries-Per-Second (QPS) and High Availability.</span></span>

<span data-ttu-id="23bf4-143">可以动态更改分区和副本数目，但无法更改定价层，因此，应该认真考虑适合目标工作负荷的层。</span><span class="sxs-lookup"><span data-stu-id="23bf4-143">You can dynamically change the number of partitions and replicas but it's not possible to change the pricing tier, so you should carefully consider the right tier for your target workload.</span></span> <span data-ttu-id="23bf4-144">如果仍然需要更改层，请并列预配一个新服务并将索引重新加载到该服务，此时，可将应用程序指向新服务。</span><span class="sxs-lookup"><span data-stu-id="23bf4-144">If you need to change the tier anyway, you will need to provision a new service side by side and reload your indexes there, at which point you can point your applications at the new service.</span></span>

### <a name="availability"></a><span data-ttu-id="23bf4-145">可用性</span><span class="sxs-lookup"><span data-stu-id="23bf4-145">Availability</span></span>

<span data-ttu-id="23bf4-146">如果至少有两个副本，则 Azure 搜索可为读取（即查询）操作提供 [99.9% 的可用性 SLA][search-sla]；如果至少有三个副本，则可为更新（即，更新搜索索引）操作提供同等的 SLA。</span><span class="sxs-lookup"><span data-stu-id="23bf4-146">Azure Search provides a [99.9% availability SLA][search-sla] for _reads_ (that is, querying) if you have at least two replicas, and for _updates_ (that is, updating the search indexes) if you have at least three replicas.</span></span> <span data-ttu-id="23bf4-147">因此，如果希望客户能够可靠地执行搜索，则至少应预配 2 个副本；如果要将索引的实际更改视为高可用性操作，则至少应预配 3 个副本。</span><span class="sxs-lookup"><span data-stu-id="23bf4-147">Therefore you should provision at least two replicas if you want your customers to be able to _search_ reliably, and 3 if actual _changes to the index_ should also be considered high availability operations.</span></span>

<span data-ttu-id="23bf4-148">如果需要在不停机的情况下对索引进行重大更改（例如，更改数据类型、删除或重命名字段），则需要重建索引。</span><span class="sxs-lookup"><span data-stu-id="23bf4-148">If there is a need to make breaking changes to the index without downtime (for example, changing data types, deleting or renaming fields), the index will need to be rebuilt.</span></span> <span data-ttu-id="23bf4-149">类似于更改服务层，这意味着要创建新索引、在其中填充数据，然后将应用程序更新为指向新索引。</span><span class="sxs-lookup"><span data-stu-id="23bf4-149">Similar to changing service tier, this means creating a new index, repopulating it with the data, and then updating your applications to point at the new index.</span></span>

### <a name="security"></a><span data-ttu-id="23bf4-150">安全</span><span class="sxs-lookup"><span data-stu-id="23bf4-150">Security</span></span>

<span data-ttu-id="23bf4-151">Azure 搜索符合许多[安全和数据隐私标准][search-security]，因此可在大多数行业中使用。</span><span class="sxs-lookup"><span data-stu-id="23bf4-151">Azure Search is compliant with many [security and data privacy standards][search-security], which makes it possible to be used in most industries.</span></span>

<span data-ttu-id="23bf4-152">为了保护对服务的访问，Azure 搜索使用两种类型的密钥：**管理密钥** - 用于针对服务执行任何任务；**查询密钥** - 只可用于执行查询等只读操作。</span><span class="sxs-lookup"><span data-stu-id="23bf4-152">For securing access to the service, Azure Search uses two types of keys: **admin keys**, which allow you to perform _any_ task against the service, and **query keys**, which can only be used for read-only operations like querying.</span></span> <span data-ttu-id="23bf4-153">通常，执行搜索的应用程序不会更新索引，因此，只应在此类应用程序中配置查询密钥，而不配置管理密钥（尤其是从最终用户设备执行搜索时，例如，通过 Web 浏览器中运行的脚本执行）。</span><span class="sxs-lookup"><span data-stu-id="23bf4-153">Typically, the application that performs the search does not update the index, so it should only be configured with a query key and not an admin key (especially if the search is performed from an end-user device like script running in a web browser).</span></span>

### <a name="search-relevance"></a><span data-ttu-id="23bf4-154">搜索相关性</span><span class="sxs-lookup"><span data-stu-id="23bf4-154">Search Relevance</span></span>

<span data-ttu-id="23bf4-155">电子商务应用程序是否成功在很大程度上取决于提供给客户的搜索结果的相关性。</span><span class="sxs-lookup"><span data-stu-id="23bf4-155">How successful your e-commerce application is depends largely on the relevance of the search results to your customers.</span></span> <span data-ttu-id="23bf4-156">根据用户调查精心优化搜索服务以提供最佳结果，或依赖于[搜索流量分析][search-analysis]等内置功能来了解客户的搜索模式，可以基于数据做出决策。</span><span class="sxs-lookup"><span data-stu-id="23bf4-156">Carefully tuning your search service to provide optimal results based on user research, or relying on built-in features such as [search traffic analysis][search-analysis] to understand your customer's search patterns allows you to make decisions based on data.</span></span>

<span data-ttu-id="23bf4-157">优化搜索服务的典型方法包括：</span><span class="sxs-lookup"><span data-stu-id="23bf4-157">Typical ways to tune your search service include:</span></span>

- <span data-ttu-id="23bf4-158">根据与查询匹配的字段、数据的新旧程度、与用户之间的地理距离等条件，使用[评分配置文件][search-scoring]来影响搜索结果</span><span class="sxs-lookup"><span data-stu-id="23bf4-158">Using [scoring profiles][search-scoring] to influence the relevance of search results, for example, based on which field matched the query, how recent the data is, the geographical distance to the user, ...</span></span>
- <span data-ttu-id="23bf4-159">借助使用高级自然语言处理 (NLP) 堆栈的 [Microsoft 语言分析器][search-languages]来更好地解释查询</span><span class="sxs-lookup"><span data-stu-id="23bf4-159">Using [Microsoft provided language analyzers][search-languages] that use an advanced Natural Language Processing (NLP) stack to better interpret queries</span></span>
- <span data-ttu-id="23bf4-160">使用[自定义分析器][search-analyzers]来确保正确找到产品，尤其是要根据产品品牌和型号等信息执行非语言搜索时。</span><span class="sxs-lookup"><span data-stu-id="23bf4-160">Using [custom analyzers][search-analyzers] to ensure your products are found correctly, especially if you want to search on non-language based information like a product's make and model.</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="23bf4-161">部署方案</span><span class="sxs-lookup"><span data-stu-id="23bf4-161">Deploy the scenario</span></span>

<span data-ttu-id="23bf4-162">若要部署此方案的更完整电子商务版本，可以遵循此[分步教程][end-to-end-walkthrough]，其中提供了一个运行简单购票应用程序的 .NET 示例应用程序。</span><span class="sxs-lookup"><span data-stu-id="23bf4-162">To deploy a more complete e-commerce version of this scenario, you can follow this [step-by-step tutorial][end-to-end-walkthrough] that provides a .NET sample application that runs a simple ticket purchasing application.</span></span> <span data-ttu-id="23bf4-163">该版本还包含 Azure 搜索，并使用了介绍的许多功能。</span><span class="sxs-lookup"><span data-stu-id="23bf4-163">It also includes Azure Search and uses many of the features discussed.</span></span> <span data-ttu-id="23bf4-164">此外还有一个资源管理器模板，可以自动部署大多数 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="23bf4-164">Additionally, there is a Resource Manager template to automate the deployment of most of the Azure resources.</span></span>

## <a name="pricing"></a><span data-ttu-id="23bf4-165">定价</span><span class="sxs-lookup"><span data-stu-id="23bf4-165">Pricing</span></span>

<span data-ttu-id="23bf4-166">为方便用户了解运行本方案的成本，成本计算器中预配置了上述所有服务。</span><span class="sxs-lookup"><span data-stu-id="23bf4-166">To explore the cost of running this scenario, all the services mentioned above are pre-configured in the cost calculator.</span></span> <span data-ttu-id="23bf4-167">若要了解自己的特定用例的定价变化情况，请按预期用法更改相应的变量。</span><span class="sxs-lookup"><span data-stu-id="23bf4-167">To see how the pricing would change for your particular use case change the appropriate variables to match your expected usage.</span></span>

<span data-ttu-id="23bf4-168">我们已根据你预期接收的流量提供了三个示例性的成本配置文件：</span><span class="sxs-lookup"><span data-stu-id="23bf4-168">We have provided three sample cost profiles based on amount of traffic you expect to get:</span></span>

- <span data-ttu-id="23bf4-169">[小型][small-pricing]：在此配置文件中，我们将使用单个 `Standard S1` Web 应用来托管网站，并使用 Azure 机器人服务的免费层、单个 `Basic` Azure 搜索服务和 `Standard S2` SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="23bf4-169">[Small][small-pricing]: In this profile, we're using a single `Standard S1` Web App to host the website, the free tier of the Azure Bot service, a single `Basic` Azure Search service, and a `Standard S2` SQL Database.</span></span>
- <span data-ttu-id="23bf4-170">[中型][medium-pricing]：将 Web 应用扩展为 `Standard S3` 层的两个实例，将搜索服务升级到 `Standard S1` 层，并使用一个 `Standard S6` SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="23bf4-170">[Medium][medium-pricing]: Here we are scaling up the Web App to two instances of the `Standard S3` tier, upgrading the Search Service to a `Standard S1` tier, and using a `Standard S6` SQL Database.</span></span>
- <span data-ttu-id="23bf4-171">[大型][large-pricing]：在最大的配置文件中，我们将使用 `Premium P2V2` Web 应用的四个实例，将 Azure 机器人服务升级到 `Standard S1` 层（高级通道中包含 1,000,000 条消息），使用 2 个单位的 `Standard S3` Azure 搜索服务和一个 `Premium P6` SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="23bf4-171">[Large][large-pricing]: In the largest profile, we use four instances of a `Premium P2V2` Web App, upgrade the Azure Bot service to the `Standard S1` tier (with 1.000.000 messages in Premium channels), use 2 units of the `Standard S3` Azure Search service, and a `Premium P6` SQL Database.</span></span>

## <a name="related-resources"></a><span data-ttu-id="23bf4-172">相关资源</span><span class="sxs-lookup"><span data-stu-id="23bf4-172">Related resources</span></span>

<span data-ttu-id="23bf4-173">有关 Azure 搜索的详细信息，请访问[文档中心][docs-search]、查看[示例][search-samples]，或查看完全成熟的[演示站点][search-demo]的运作方式。</span><span class="sxs-lookup"><span data-stu-id="23bf4-173">To learn more about Azure Search, visit the [documentation center][docs-search], check out the [samples][search-samples], or see a full fledged [demo site][search-demo] in action.</span></span>

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
