---
title: 使用工作的最佳数据存储
titleSuffix: Azure Application Architecture Guide
description: 选择最适合数据的存储技术，并了解如何使用该技术。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 2f83e7a184e8fa94f49da4e8fd5252396ecfd632
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242008"
---
# <a name="use-the-best-data-store-for-the-job"></a><span data-ttu-id="1754a-103">使用工作的最佳数据存储</span><span class="sxs-lookup"><span data-stu-id="1754a-103">Use the best data store for the job</span></span>

## <a name="pick-the-storage-technology-that-is-the-best-fit-for-your-data-and-how-it-will-be-used"></a><span data-ttu-id="1754a-104">选择最适合数据的存储技术，并了解如何使用该技术</span><span class="sxs-lookup"><span data-stu-id="1754a-104">Pick the storage technology that is the best fit for your data and how it will be used</span></span>

<span data-ttu-id="1754a-105">现在再也无需将所有数据粘贴到大型 SQL 关系数据库。</span><span class="sxs-lookup"><span data-stu-id="1754a-105">Gone are the days when you would just stick all of your data into a big relational SQL database.</span></span> <span data-ttu-id="1754a-106">关系数据库非常擅长通过关系数据为事务提供 ACID 保证。</span><span class="sxs-lookup"><span data-stu-id="1754a-106">Relational databases are very good at what they do &mdash; providing ACID guarantees for transactions over relational data.</span></span> <span data-ttu-id="1754a-107">但这会产生一些费用：</span><span class="sxs-lookup"><span data-stu-id="1754a-107">But they come with some costs:</span></span>

- <span data-ttu-id="1754a-108">查询可能需要高成本联接。</span><span class="sxs-lookup"><span data-stu-id="1754a-108">Queries may require expensive joins.</span></span>
- <span data-ttu-id="1754a-109">数据必须规范化，且符合预定义架构（写入架构）。</span><span class="sxs-lookup"><span data-stu-id="1754a-109">Data must be normalized and conform to a predefined schema (schema on write).</span></span>
- <span data-ttu-id="1754a-110">锁定争用可能会对性能产生影响。</span><span class="sxs-lookup"><span data-stu-id="1754a-110">Lock contention may impact performance.</span></span>

<span data-ttu-id="1754a-111">在任何大型解决方案中，单个数据存储技术很可能满足不了所有需求。</span><span class="sxs-lookup"><span data-stu-id="1754a-111">In any large solution, it's likely that a single data store technology won't fill all your needs.</span></span> <span data-ttu-id="1754a-112">关系数据库的替代方案包括键/值存储、文档数据库、搜索引擎数据库、时间序列数据库、列系列数据库和图形数据库。</span><span class="sxs-lookup"><span data-stu-id="1754a-112">Alternatives to relational databases include key/value stores, document databases, search engine databases, time series databases, column family databases, and graph databases.</span></span> <span data-ttu-id="1754a-113">每个方案都有其优缺点，不同类型的数据适合其中不同的方案。</span><span class="sxs-lookup"><span data-stu-id="1754a-113">Each has pros and cons, and different types of data fit more naturally into one or another.</span></span>

<span data-ttu-id="1754a-114">例如，可将产品目录存储在文档数据库（如 Cosmos DB）中，这可实现灵活架构。</span><span class="sxs-lookup"><span data-stu-id="1754a-114">For example, you might store a product catalog in a document database, such as Cosmos DB, which allows for a flexible schema.</span></span> <span data-ttu-id="1754a-115">在这种情况下，每个产品描述是一个自包含文档。</span><span class="sxs-lookup"><span data-stu-id="1754a-115">In that case, each product description is a self-contained document.</span></span> <span data-ttu-id="1754a-116">若要查询整个目录，可以索引目录并将索引存储在 Azure 搜索中。</span><span class="sxs-lookup"><span data-stu-id="1754a-116">For queries over the entire catalog, you might index the catalog and store the index in Azure Search.</span></span> <span data-ttu-id="1754a-117">产品清单可以存储在 SQL 数据库中，因为这类数据需要 ACID 保证。</span><span class="sxs-lookup"><span data-stu-id="1754a-117">Product inventory might go into a SQL database, because that data requires ACID guarantees.</span></span>

<span data-ttu-id="1754a-118">请记住，数据不仅仅包括持久化的应用程序数据。</span><span class="sxs-lookup"><span data-stu-id="1754a-118">Remember that data includes more than just the persisted application data.</span></span> <span data-ttu-id="1754a-119">还包括应用程序日志、事件、消息和缓存。</span><span class="sxs-lookup"><span data-stu-id="1754a-119">It also includes application logs, events, messages, and caches.</span></span>

## <a name="recommendations"></a><span data-ttu-id="1754a-120">建议</span><span class="sxs-lookup"><span data-stu-id="1754a-120">Recommendations</span></span>

<span data-ttu-id="1754a-121">请勿在任何情况下都使用关系数据库。</span><span class="sxs-lookup"><span data-stu-id="1754a-121">**Don't use a relational database for everything**.</span></span> <span data-ttu-id="1754a-122">请考虑在适当的时候使用其他数据存储。</span><span class="sxs-lookup"><span data-stu-id="1754a-122">Consider other data stores when appropriate.</span></span> <span data-ttu-id="1754a-123">请参阅[选择正确的数据存储][data-store-overview]。</span><span class="sxs-lookup"><span data-stu-id="1754a-123">See [Choose the right data store][data-store-overview].</span></span>

<span data-ttu-id="1754a-124">采用混合持久性。</span><span class="sxs-lookup"><span data-stu-id="1754a-124">**Embrace polyglot persistence**.</span></span> <span data-ttu-id="1754a-125">在任何大型解决方案中，单个数据存储技术很可能满足不了所有需求。</span><span class="sxs-lookup"><span data-stu-id="1754a-125">In any large solution, it's likely that a single data store technology won't fill all your needs.</span></span>

<span data-ttu-id="1754a-126">请考虑数据的类型。</span><span class="sxs-lookup"><span data-stu-id="1754a-126">**Consider the type of data**.</span></span> <span data-ttu-id="1754a-127">例如，将事务数据存储在 SQL 中，将 JSON 文档存储在文档数据库中，将遥测数据存储在时间序列数据库中，将应用程序日志存储在 Elasticsearch 中，并将 Blob 存储在 Azure Blob 存储中。</span><span class="sxs-lookup"><span data-stu-id="1754a-127">For example, put transactional data into SQL, put JSON documents into a document database, put telemetry data into a time series data base, put application logs in Elasticsearch, and put blobs in Azure Blob Storage.</span></span>

<span data-ttu-id="1754a-128">优先考虑可用性而非（强）一致性。</span><span class="sxs-lookup"><span data-stu-id="1754a-128">**Prefer availability over (strong) consistency**.</span></span> <span data-ttu-id="1754a-129">CAP 定理意味着分布式系统须权衡可用性和一致性。</span><span class="sxs-lookup"><span data-stu-id="1754a-129">The CAP theorem implies that a distributed system must make trade-offs between availability and consistency.</span></span> <span data-ttu-id="1754a-130">（网络分区，是 CAP 定理中不能完全避免的另一方面。）通常情况下，可以通过采用最终一致性模型来获得更高的可用性。</span><span class="sxs-lookup"><span data-stu-id="1754a-130">(Network partitions, the other leg of the CAP theorem, can never be completely avoided.) Often, you can achieve higher availability by adopting an *eventual consistency* model.</span></span>

<span data-ttu-id="1754a-131">请考虑开发团队的技能组合。</span><span class="sxs-lookup"><span data-stu-id="1754a-131">**Consider the skill set of the development team**.</span></span> <span data-ttu-id="1754a-132">使用混合持久性有一些好处，但也有可能适得其反。</span><span class="sxs-lookup"><span data-stu-id="1754a-132">There are advantages to using polyglot persistence, but it's possible to go overboard.</span></span> <span data-ttu-id="1754a-133">采用新的数据存储技术需要新的技能组合。</span><span class="sxs-lookup"><span data-stu-id="1754a-133">Adopting a new data storage technology requires a new set of skills.</span></span> <span data-ttu-id="1754a-134">开发团队须了解如何充分利用此技术。</span><span class="sxs-lookup"><span data-stu-id="1754a-134">The development team must understand how to get the most out of the technology.</span></span> <span data-ttu-id="1754a-135">他们必须了解恰当的使用模式、如何优化查询、调整性能等。</span><span class="sxs-lookup"><span data-stu-id="1754a-135">They must understand appropriate usage patterns, how to optimize queries, tune for performance, and so on.</span></span> <span data-ttu-id="1754a-136">请在考虑存储技术时将此因素考虑在内。</span><span class="sxs-lookup"><span data-stu-id="1754a-136">Factor this in when considering storage technologies.</span></span>

<span data-ttu-id="1754a-137">使用补偿事务。</span><span class="sxs-lookup"><span data-stu-id="1754a-137">**Use compensating transactions**.</span></span> <span data-ttu-id="1754a-138">混合持久性的副作用是单个事务可能会将数据写入多个存储。</span><span class="sxs-lookup"><span data-stu-id="1754a-138">A side effect of polyglot persistence is that single transaction might write data to multiple stores.</span></span> <span data-ttu-id="1754a-139">如果出现故障，使用补偿事务来撤消任何已完成的步骤。</span><span class="sxs-lookup"><span data-stu-id="1754a-139">If something fails, use compensating transactions to undo any steps that already completed.</span></span>

<span data-ttu-id="1754a-140">查看界限上下文。</span><span class="sxs-lookup"><span data-stu-id="1754a-140">**Look at bounded contexts**.</span></span> <span data-ttu-id="1754a-141">界限上下文是域驱动设计中的一个术语。</span><span class="sxs-lookup"><span data-stu-id="1754a-141">*Bounded context* is a term from domain driven design.</span></span> <span data-ttu-id="1754a-142">界限上下文是域模型的显式边界，它定义模型适用于域的哪个部分。</span><span class="sxs-lookup"><span data-stu-id="1754a-142">A bounded context is an explicit boundary around a domain model, and defines which parts of the domain the model applies to.</span></span> <span data-ttu-id="1754a-143">理想情况下，界限上下文将映射到业务域的子域。</span><span class="sxs-lookup"><span data-stu-id="1754a-143">Ideally, a bounded context maps to a subdomain of the business domain.</span></span> <span data-ttu-id="1754a-144">系统中的界线上下文是考虑混合持久性的自然位置。</span><span class="sxs-lookup"><span data-stu-id="1754a-144">The bounded contexts in your system are a natural place to consider polyglot persistence.</span></span> <span data-ttu-id="1754a-145">例如，“产品”可能会出现在产品目录子域和产品清单子域，但这两个子域很可能对存储、更新、查询产品的要求不同。</span><span class="sxs-lookup"><span data-stu-id="1754a-145">For example, "products" may appear in both the Product Catalog subdomain and the Product Inventory subdomain, but it's very likely that these two subdomains have different requirements for storing, updating, and querying products.</span></span>

[data-store-overview]: ../technology-choices/data-store-overview.md
