---
title: "选择搜索数据存储"
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 7fe5952c880921984beb30c71458fd1ef72ef239
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-a-search-data-store-in-azure"></a><span data-ttu-id="231d2-102">在 Azure 中选择搜索数据存储</span><span class="sxs-lookup"><span data-stu-id="231d2-102">Choosing a search data store in Azure</span></span>

<span data-ttu-id="231d2-103">本文对 Azure 中用于搜索数据存储的技术选择进行了比较。</span><span class="sxs-lookup"><span data-stu-id="231d2-103">This article compares technology choices for search data stores in Azure.</span></span> <span data-ttu-id="231d2-104">搜索数据存储用来创建和存储用于对自由格式文本执行查询的专用索引。</span><span class="sxs-lookup"><span data-stu-id="231d2-104">A seach data store is used to create and store specialized indexes for performing searches on free-form text.</span></span> <span data-ttu-id="231d2-105">编制了索引的文本可以驻留在单独的数据存储中，例如 blob 存储。</span><span class="sxs-lookup"><span data-stu-id="231d2-105">The text that is indexed may reside in a separate data store, such as blob storage.</span></span> <span data-ttu-id="231d2-106">应用程序将查询提交到搜索数据存储，结果是匹配的文档的列表。</span><span class="sxs-lookup"><span data-stu-id="231d2-106">An application submits a query to the search data store, and the result is a list of matching documents.</span></span> <span data-ttu-id="231d2-107">有关此方案的详细信息，请参阅[为搜索处理自由格式文本](../scenarios/search.md)。</span><span class="sxs-lookup"><span data-stu-id="231d2-107">For more information about this scenario, see [Processing free-form text for search](../scenarios/search.md).</span></span> 

## <a name="what-are-your-options-when-choosing-a-search-data-store"></a><span data-ttu-id="231d2-108">在选择搜索数据存储时有哪些选项？</span><span class="sxs-lookup"><span data-stu-id="231d2-108">What are your options when choosing a search data store?</span></span>
<span data-ttu-id="231d2-109">在 Azure 中，以下所有数据存储都将通过提供搜索索引来满足对自由格式文本数据的核心搜索要求：</span><span class="sxs-lookup"><span data-stu-id="231d2-109">In Azure, all of the following data stores will meet the core requirements for search against free-form text data by providing a search index:</span></span>
- [<span data-ttu-id="231d2-110">Azure 搜索</span><span class="sxs-lookup"><span data-stu-id="231d2-110">Azure Search</span></span>](/azure/search/search-what-is-azure-search)
- [<span data-ttu-id="231d2-111">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="231d2-111">Elasticsearch</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/elastic.elasticsearch?tab=Overview)
- [<span data-ttu-id="231d2-112">基于 Solr 的 HDInsight</span><span class="sxs-lookup"><span data-stu-id="231d2-112">HDInsight with Solr</span></span>](/azure/hdinsight/hdinsight-hadoop-solr-install-linux)
- [<span data-ttu-id="231d2-113">具有全文搜索功能的 Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="231d2-113">Azure SQL Database with full text search</span></span>](/sql/relational-databases/search/full-text-search)


## <a name="key-selection-criteria"></a><span data-ttu-id="231d2-114">关键选择条件</span><span class="sxs-lookup"><span data-stu-id="231d2-114">Key selection criteria</span></span>

<span data-ttu-id="231d2-115">针对搜索方案，通过回答以下问题开始选择合适的满足需求的搜索数据存储：</span><span class="sxs-lookup"><span data-stu-id="231d2-115">For search scenarios, begin choosing the appropriate search data store for your needs by answering these questions:</span></span>

- <span data-ttu-id="231d2-116">你希望使用托管服务还是由你管理自己的服务器？</span><span class="sxs-lookup"><span data-stu-id="231d2-116">Do you want a managed service rather than managing your own servers?</span></span>

- <span data-ttu-id="231d2-117">是否可以在设计时指定索引架构？</span><span class="sxs-lookup"><span data-stu-id="231d2-117">Can you specify your index schema at design time?</span></span> <span data-ttu-id="231d2-118">如果不可以，请选择一个支持可更新架构的选项。</span><span class="sxs-lookup"><span data-stu-id="231d2-118">If not, choose an option that supports updateable schemas.</span></span>

- <span data-ttu-id="231d2-119">只需要为全文搜索使用索引，还是也需要快速聚合数字数据和其他分析？</span><span class="sxs-lookup"><span data-stu-id="231d2-119">Do you need an index only for full-text search, or do you also need rapid aggregation of numeric data and other analytics?</span></span> <span data-ttu-id="231d2-120">如果需要超出全文搜索的功能，请考虑使用支持其他分析的选项。</span><span class="sxs-lookup"><span data-stu-id="231d2-120">If you need functionality beyond full-text search, consider options that support additional analytics.</span></span>

- <span data-ttu-id="231d2-121">是否需要一个支持日志收集、聚合及已索引数据可视化且用于日志分析的搜索索引？</span><span class="sxs-lookup"><span data-stu-id="231d2-121">Do you need a search index for log analytics, with support for log collection, aggregation, and visualizations on indexed data?</span></span> <span data-ttu-id="231d2-122">如果需要，请考虑使用 Elasticsearch，它是日志分析堆栈的一部分。</span><span class="sxs-lookup"><span data-stu-id="231d2-122">If so, consider Elasticsearch, which is part of a log analytics stack.</span></span>

- <span data-ttu-id="231d2-123">是否需要为常见文档格式（例如 PDF、Word、PowerPoint 和 Excel）的数据编制索引？</span><span class="sxs-lookup"><span data-stu-id="231d2-123">Do you need to index data in common document formats such as PDF, Word, PowerPoint, and Excel?</span></span> <span data-ttu-id="231d2-124">如果是，请选择一个提供文档索引器的选项。</span><span class="sxs-lookup"><span data-stu-id="231d2-124">If yes, choose an option that provides document indexers.</span></span>

- <span data-ttu-id="231d2-125">数据库是否有特定的安全需求？</span><span class="sxs-lookup"><span data-stu-id="231d2-125">Does your database have specific security needs?</span></span> <span data-ttu-id="231d2-126">如果是，请考虑使用下面列出的安全功能。</span><span class="sxs-lookup"><span data-stu-id="231d2-126">If yes, consider the security features listed below.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="231d2-127">功能矩阵</span><span class="sxs-lookup"><span data-stu-id="231d2-127">Capability matrix</span></span>

<span data-ttu-id="231d2-128">以下各表汇总了功能上的关键差异。</span><span class="sxs-lookup"><span data-stu-id="231d2-128">The following tables summarize the key differences in capabilities.</span></span>

### <a name="general-capabilities"></a><span data-ttu-id="231d2-129">常规功能</span><span class="sxs-lookup"><span data-stu-id="231d2-129">General capabilities</span></span>
| | <span data-ttu-id="231d2-130">Azure 搜索</span><span class="sxs-lookup"><span data-stu-id="231d2-130">Azure Search</span></span> | <span data-ttu-id="231d2-131">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="231d2-131">Elasticsearch</span></span> | <span data-ttu-id="231d2-132">基于 Solr 的 HDInsight</span><span class="sxs-lookup"><span data-stu-id="231d2-132">HDInsight with Solr</span></span> | <span data-ttu-id="231d2-133">SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="231d2-133">SQL Database</span></span> | 
| --- | --- | --- | --- | --- | 
| <span data-ttu-id="231d2-134">是托管服务</span><span class="sxs-lookup"><span data-stu-id="231d2-134">Is managed service</span></span> | <span data-ttu-id="231d2-135">是</span><span class="sxs-lookup"><span data-stu-id="231d2-135">Yes</span></span> | <span data-ttu-id="231d2-136">否</span><span class="sxs-lookup"><span data-stu-id="231d2-136">No</span></span> | <span data-ttu-id="231d2-137">是</span><span class="sxs-lookup"><span data-stu-id="231d2-137">Yes</span></span> | <span data-ttu-id="231d2-138">是</span><span class="sxs-lookup"><span data-stu-id="231d2-138">Yes</span></span> |  
| <span data-ttu-id="231d2-139">REST API</span><span class="sxs-lookup"><span data-stu-id="231d2-139">REST API</span></span> | <span data-ttu-id="231d2-140">是</span><span class="sxs-lookup"><span data-stu-id="231d2-140">Yes</span></span> | <span data-ttu-id="231d2-141">是</span><span class="sxs-lookup"><span data-stu-id="231d2-141">Yes</span></span> | <span data-ttu-id="231d2-142">是</span><span class="sxs-lookup"><span data-stu-id="231d2-142">Yes</span></span> | <span data-ttu-id="231d2-143">否</span><span class="sxs-lookup"><span data-stu-id="231d2-143">No</span></span> |
| <span data-ttu-id="231d2-144">可编程性</span><span class="sxs-lookup"><span data-stu-id="231d2-144">Programmability</span></span> | <span data-ttu-id="231d2-145">.NET</span><span class="sxs-lookup"><span data-stu-id="231d2-145">.NET</span></span> | <span data-ttu-id="231d2-146">Java</span><span class="sxs-lookup"><span data-stu-id="231d2-146">Java</span></span> | <span data-ttu-id="231d2-147">Java</span><span class="sxs-lookup"><span data-stu-id="231d2-147">Java</span></span> | <span data-ttu-id="231d2-148">T-SQL</span><span class="sxs-lookup"><span data-stu-id="231d2-148">T-SQL</span></span> | 
| <span data-ttu-id="231d2-149">常见文件类型（PDF、DOCX、TXT、等等）的文档索引器</span><span class="sxs-lookup"><span data-stu-id="231d2-149">Document indexers for common file types (PDF, DOCX, TXT, and so on)</span></span> | <span data-ttu-id="231d2-150">是</span><span class="sxs-lookup"><span data-stu-id="231d2-150">Yes</span></span> | <span data-ttu-id="231d2-151">否</span><span class="sxs-lookup"><span data-stu-id="231d2-151">No</span></span> | <span data-ttu-id="231d2-152">是</span><span class="sxs-lookup"><span data-stu-id="231d2-152">Yes</span></span> | <span data-ttu-id="231d2-153">否</span><span class="sxs-lookup"><span data-stu-id="231d2-153">No</span></span> |

### <a name="manageability-capabilities"></a><span data-ttu-id="231d2-154">可管理性功能</span><span class="sxs-lookup"><span data-stu-id="231d2-154">Manageability capabilities</span></span>
| | <span data-ttu-id="231d2-155">Azure 搜索</span><span class="sxs-lookup"><span data-stu-id="231d2-155">Azure Search</span></span> | <span data-ttu-id="231d2-156">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="231d2-156">Elasticsearch</span></span> | <span data-ttu-id="231d2-157">基于 Solr 的 HDInsight</span><span class="sxs-lookup"><span data-stu-id="231d2-157">HDInsight with Solr</span></span> | <span data-ttu-id="231d2-158">SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="231d2-158">SQL Database</span></span> | 
| --- | --- | --- | --- | --- |
| <span data-ttu-id="231d2-159">可更新架构</span><span class="sxs-lookup"><span data-stu-id="231d2-159">Updateable schema</span></span> | <span data-ttu-id="231d2-160">否</span><span class="sxs-lookup"><span data-stu-id="231d2-160">No</span></span> | <span data-ttu-id="231d2-161">是</span><span class="sxs-lookup"><span data-stu-id="231d2-161">Yes</span></span> | <span data-ttu-id="231d2-162">是</span><span class="sxs-lookup"><span data-stu-id="231d2-162">Yes</span></span> | <span data-ttu-id="231d2-163">是</span><span class="sxs-lookup"><span data-stu-id="231d2-163">Yes</span></span> |
| <span data-ttu-id="231d2-164">支持横向扩展</span><span class="sxs-lookup"><span data-stu-id="231d2-164">Supports scale out</span></span>  | <span data-ttu-id="231d2-165">是</span><span class="sxs-lookup"><span data-stu-id="231d2-165">Yes</span></span> | <span data-ttu-id="231d2-166">是</span><span class="sxs-lookup"><span data-stu-id="231d2-166">Yes</span></span> | <span data-ttu-id="231d2-167">是</span><span class="sxs-lookup"><span data-stu-id="231d2-167">Yes</span></span> | <span data-ttu-id="231d2-168">否</span><span class="sxs-lookup"><span data-stu-id="231d2-168">No</span></span> |

### <a name="analytic-workload-capabilities"></a><span data-ttu-id="231d2-169">分析工作负荷功能</span><span class="sxs-lookup"><span data-stu-id="231d2-169">Analytic workload capabilities</span></span>
| | <span data-ttu-id="231d2-170">Azure 搜索</span><span class="sxs-lookup"><span data-stu-id="231d2-170">Azure Search</span></span> | <span data-ttu-id="231d2-171">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="231d2-171">Elasticsearch</span></span> | <span data-ttu-id="231d2-172">基于 Solr 的 HDInsight</span><span class="sxs-lookup"><span data-stu-id="231d2-172">HDInsight with Solr</span></span> | <span data-ttu-id="231d2-173">SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="231d2-173">SQL Databash</span></span> | 
| --- | --- | --- | --- | --- | 
| <span data-ttu-id="231d2-174">支持超出全文搜索的分析</span><span class="sxs-lookup"><span data-stu-id="231d2-174">Supports analytics beyond full text search</span></span> | <span data-ttu-id="231d2-175">否</span><span class="sxs-lookup"><span data-stu-id="231d2-175">No</span></span> | <span data-ttu-id="231d2-176">是</span><span class="sxs-lookup"><span data-stu-id="231d2-176">Yes</span></span> | <span data-ttu-id="231d2-177">是</span><span class="sxs-lookup"><span data-stu-id="231d2-177">Yes</span></span> | <span data-ttu-id="231d2-178">是</span><span class="sxs-lookup"><span data-stu-id="231d2-178">Yes</span></span> |
| <span data-ttu-id="231d2-179">日志分析堆栈的一部分</span><span class="sxs-lookup"><span data-stu-id="231d2-179">Part of a log analytics stack</span></span> | <span data-ttu-id="231d2-180">否</span><span class="sxs-lookup"><span data-stu-id="231d2-180">No</span></span> | <span data-ttu-id="231d2-181">是 (ELK)</span><span class="sxs-lookup"><span data-stu-id="231d2-181">Yes (ELK)</span></span> |  <span data-ttu-id="231d2-182">否</span><span class="sxs-lookup"><span data-stu-id="231d2-182">No</span></span> | <span data-ttu-id="231d2-183">否</span><span class="sxs-lookup"><span data-stu-id="231d2-183">No</span></span> |
| <span data-ttu-id="231d2-184">支持语义搜索</span><span class="sxs-lookup"><span data-stu-id="231d2-184">Supports semantic search</span></span> | <span data-ttu-id="231d2-185">是（仅限查找类似文档）</span><span class="sxs-lookup"><span data-stu-id="231d2-185">Yes (find similar documents only)</span></span> | <span data-ttu-id="231d2-186">是</span><span class="sxs-lookup"><span data-stu-id="231d2-186">Yes</span></span> | <span data-ttu-id="231d2-187">是</span><span class="sxs-lookup"><span data-stu-id="231d2-187">Yes</span></span> | <span data-ttu-id="231d2-188">是</span><span class="sxs-lookup"><span data-stu-id="231d2-188">Yes</span></span> | 

### <a name="security-capabilities"></a><span data-ttu-id="231d2-189">安全功能</span><span class="sxs-lookup"><span data-stu-id="231d2-189">Security capabilities</span></span>
| | <span data-ttu-id="231d2-190">Azure 搜索</span><span class="sxs-lookup"><span data-stu-id="231d2-190">Azure Search</span></span> | <span data-ttu-id="231d2-191">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="231d2-191">Elasticsearch</span></span> | <span data-ttu-id="231d2-192">基于 Solr 的 HDInsight</span><span class="sxs-lookup"><span data-stu-id="231d2-192">HDInsight with Solr</span></span> | <span data-ttu-id="231d2-193">SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="231d2-193">SQL Databash</span></span> | 
| --- | --- | --- | --- | --- | 
| <span data-ttu-id="231d2-194">行级别安全性</span><span class="sxs-lookup"><span data-stu-id="231d2-194">Row-level security</span></span> | <span data-ttu-id="231d2-195">部分（要求应用程序查询按组 id 进行筛选）</span><span class="sxs-lookup"><span data-stu-id="231d2-195">Partial (requires application query to filter by group id)</span></span> | <span data-ttu-id="231d2-196">部分（要求应用程序查询按组 id 进行筛选）</span><span class="sxs-lookup"><span data-stu-id="231d2-196">Partial (requires application query to filter by group id)</span></span> | <span data-ttu-id="231d2-197">是</span><span class="sxs-lookup"><span data-stu-id="231d2-197">Yes</span></span> | <span data-ttu-id="231d2-198">是</span><span class="sxs-lookup"><span data-stu-id="231d2-198">Yes</span></span> | 
| <span data-ttu-id="231d2-199">透明数据加密</span><span class="sxs-lookup"><span data-stu-id="231d2-199">Transparent data encryption</span></span> | <span data-ttu-id="231d2-200">否</span><span class="sxs-lookup"><span data-stu-id="231d2-200">No</span></span> | <span data-ttu-id="231d2-201">否</span><span class="sxs-lookup"><span data-stu-id="231d2-201">No</span></span> | <span data-ttu-id="231d2-202">否</span><span class="sxs-lookup"><span data-stu-id="231d2-202">No</span></span> | <span data-ttu-id="231d2-203">是</span><span class="sxs-lookup"><span data-stu-id="231d2-203">Yes</span></span> |  
| <span data-ttu-id="231d2-204">限制访问，仅限特定 IP 地址进行访问</span><span class="sxs-lookup"><span data-stu-id="231d2-204">Restrict access to specific IP addresses</span></span> | <span data-ttu-id="231d2-205">否</span><span class="sxs-lookup"><span data-stu-id="231d2-205">No</span></span> | <span data-ttu-id="231d2-206">是</span><span class="sxs-lookup"><span data-stu-id="231d2-206">Yes</span></span> | <span data-ttu-id="231d2-207">是</span><span class="sxs-lookup"><span data-stu-id="231d2-207">Yes</span></span> | <span data-ttu-id="231d2-208">是</span><span class="sxs-lookup"><span data-stu-id="231d2-208">Yes</span></span> |   
| <span data-ttu-id="231d2-209">限制访问，仅允许访问虚拟网络</span><span class="sxs-lookup"><span data-stu-id="231d2-209">Restrict access to allow virtual network access only</span></span> | <span data-ttu-id="231d2-210">否</span><span class="sxs-lookup"><span data-stu-id="231d2-210">No</span></span> | <span data-ttu-id="231d2-211">是</span><span class="sxs-lookup"><span data-stu-id="231d2-211">Yes</span></span> | <span data-ttu-id="231d2-212">是</span><span class="sxs-lookup"><span data-stu-id="231d2-212">Yes</span></span> | <span data-ttu-id="231d2-213">是</span><span class="sxs-lookup"><span data-stu-id="231d2-213">Yes</span></span> |  
| <span data-ttu-id="231d2-214">Active Directory 身份验证（集成身份验证）</span><span class="sxs-lookup"><span data-stu-id="231d2-214">Active Directory authentication (integrated authentication)</span></span> | <span data-ttu-id="231d2-215">否</span><span class="sxs-lookup"><span data-stu-id="231d2-215">No</span></span> | <span data-ttu-id="231d2-216">否</span><span class="sxs-lookup"><span data-stu-id="231d2-216">No</span></span> | <span data-ttu-id="231d2-217">否</span><span class="sxs-lookup"><span data-stu-id="231d2-217">No</span></span> | <span data-ttu-id="231d2-218">是</span><span class="sxs-lookup"><span data-stu-id="231d2-218">Yes</span></span> | 

## <a name="see-also"></a><span data-ttu-id="231d2-219">另请参阅</span><span class="sxs-lookup"><span data-stu-id="231d2-219">See also</span></span>

[<span data-ttu-id="231d2-220">为搜索处理自由格式文本</span><span class="sxs-lookup"><span data-stu-id="231d2-220">Processing free-form text for search</span></span>](../scenarios/search.md)