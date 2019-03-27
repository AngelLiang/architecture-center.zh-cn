---
title: 为搜索处理自由格式文本
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: ba1685d12834b04e7d231929c53453b51527cce2
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244068"
---
# <a name="processing-free-form-text-for-search"></a><span data-ttu-id="b070a-102">为搜索处理自由格式文本</span><span class="sxs-lookup"><span data-stu-id="b070a-102">Processing free-form text for search</span></span>

<span data-ttu-id="b070a-103">若要支持搜索，可以对包含文本段落的文档执行自由格式文本处理。</span><span class="sxs-lookup"><span data-stu-id="b070a-103">To support search, free-form text processing can be performed against documents containing paragraphs of text.</span></span>

<span data-ttu-id="b070a-104">文本搜索通过构造针对文档集合预先计算的专用索引进行工作。</span><span class="sxs-lookup"><span data-stu-id="b070a-104">Text search works by constructing a specialized index that is precomputed against a collection of documents.</span></span> <span data-ttu-id="b070a-105">客户端应用程序提交包含搜索词的查询。</span><span class="sxs-lookup"><span data-stu-id="b070a-105">A client application submits a query that contains the search terms.</span></span> <span data-ttu-id="b070a-106">查询返回一个结果集，其中包含根据每个文档与搜索条件的匹配程度排序的文档列表。</span><span class="sxs-lookup"><span data-stu-id="b070a-106">The query returns a result set, consisting of a list of documents sorted by how well each document matches the search criteria.</span></span> <span data-ttu-id="b070a-107">搜索集还可能包括文档与条件进行匹配时所处的上下文，这使得应用程序能够突出显示文档中的匹配段落。</span><span class="sxs-lookup"><span data-stu-id="b070a-107">The result set may also include the context in which the document matches the criteria, which enables the application to highlight the matching phrase in the document.</span></span>

![搜索管道图](./images/search-pipeline.png)

<span data-ttu-id="b070a-109">自由格式文本处理可以基于大量的嘈杂文本数据生成有用的可操作数据。</span><span class="sxs-lookup"><span data-stu-id="b070a-109">Free-form text processing can produce useful, actionable data from large amounts of noisy text data.</span></span> <span data-ttu-id="b070a-110">结果可以为非结构化文档提供定义完善的且可查询的结构。</span><span class="sxs-lookup"><span data-stu-id="b070a-110">The results can give unstructured documents a well-defined and queryable structure.</span></span>

## <a name="challenges"></a><span data-ttu-id="b070a-111">挑战</span><span class="sxs-lookup"><span data-stu-id="b070a-111">Challenges</span></span>

- <span data-ttu-id="b070a-112">处理自由格式文本文档的集合通常需要密集进行计算，而且非常耗时。</span><span class="sxs-lookup"><span data-stu-id="b070a-112">Processing a collection of free-form text documents is typically computationally intensive, as well as time intensive.</span></span>
- <span data-ttu-id="b070a-113">为了高效地搜索自由格式文本，搜索索引应当支持基于具有相似构造的词语进行模糊搜索。</span><span class="sxs-lookup"><span data-stu-id="b070a-113">In order to search free-form text effectively, the search index should support fuzzy search based on terms that have a similar construction.</span></span> <span data-ttu-id="b070a-114">例如，搜索索引是通过词元化和语言词干分解构建的，因此，对“run”的查询将与包含“ran”和“running”的文档匹配。</span><span class="sxs-lookup"><span data-stu-id="b070a-114">For example, search indexes are built with lemmatization and linguistic stemming, so that queries for "run" will match documents that contain "ran" and "running."</span></span>

## <a name="architecture"></a><span data-ttu-id="b070a-115">体系结构</span><span class="sxs-lookup"><span data-stu-id="b070a-115">Architecture</span></span>

<span data-ttu-id="b070a-116">在大多数情况下，源文本文档将加载到对象存储，例如 Azure 存储或 Azure Data Lake Store。</span><span class="sxs-lookup"><span data-stu-id="b070a-116">In most scenarios, the source text documents are loaded into object storage such as Azure Storage or Azure Data Lake Store.</span></span> <span data-ttu-id="b070a-117">例外情况是在 SQL Server 或 Azure SQL 数据库中使用全文搜索。</span><span class="sxs-lookup"><span data-stu-id="b070a-117">An exception is using full text search within SQL Server or Azure SQL Database.</span></span> <span data-ttu-id="b070a-118">在这种情况下，文档数据加载到由数据库管理的表中。</span><span class="sxs-lookup"><span data-stu-id="b070a-118">In this case, the document data is loaded into tables managed by the database.</span></span> <span data-ttu-id="b070a-119">一旦存储，将批量处理文档来创建索引。</span><span class="sxs-lookup"><span data-stu-id="b070a-119">Once stored, the documents are processed in a batch to create the index.</span></span>

## <a name="technology-choices"></a><span data-ttu-id="b070a-120">技术选择</span><span class="sxs-lookup"><span data-stu-id="b070a-120">Technology choices</span></span>

<span data-ttu-id="b070a-121">用于创建搜索索引的选项包括 Azure 搜索、Elasticsearch 和基于 Solr 的 HDInsight。</span><span class="sxs-lookup"><span data-stu-id="b070a-121">Options for creating a search index include Azure Search, Elasticsearch, and HDInsight with Solr.</span></span> <span data-ttu-id="b070a-122">这些技术每一种都可以基于文档集合来填充搜索索引。</span><span class="sxs-lookup"><span data-stu-id="b070a-122">Each of these technologies can populate a search index from a collection of documents.</span></span> <span data-ttu-id="b070a-123">Azure 搜索提供了索引器，它们可以自动针对从纯文本到 Excel 和 PDF 格式的文档填充索引。</span><span class="sxs-lookup"><span data-stu-id="b070a-123">Azure Search provides indexers that can automatically populate the index for documents ranging from plain text to Excel and PDF formats.</span></span> <span data-ttu-id="b070a-124">在 HDInsight 上，Apache Solr 可以为许多类型（包括纯文本、Word 和 PDF）的二进制文件编制索引。</span><span class="sxs-lookup"><span data-stu-id="b070a-124">On HDInsight, Apache Solr can index binary files of many types, including plain text, Word, and PDF.</span></span> <span data-ttu-id="b070a-125">在构造索引后，客户端可以通过 REST API 访问搜索接口。</span><span class="sxs-lookup"><span data-stu-id="b070a-125">Once the index is constructed, clients can access the search interface by means of a REST API.</span></span>

<span data-ttu-id="b070a-126">如果文本数据存储在 SQL Server 或 Azure SQL 数据库中，则可以使用数据库中内置的全文索引。</span><span class="sxs-lookup"><span data-stu-id="b070a-126">If your text data is stored in SQL Server or Azure SQL Database, you can use the full-text search that is built into the database.</span></span> <span data-ttu-id="b070a-127">数据库可以基于同一数据库中存储的文本、二进制或 XML 数据来填充索引。</span><span class="sxs-lookup"><span data-stu-id="b070a-127">The database populates the index from text, binary, or XML data stored within the same database.</span></span> <span data-ttu-id="b070a-128">客户端使用 T-SQL 查询进行搜索。</span><span class="sxs-lookup"><span data-stu-id="b070a-128">Clients search by using T-SQL queries.</span></span>

<span data-ttu-id="b070a-129">有关详细信息，请参阅[搜索数据存储](../technology-choices/search-options.md)。</span><span class="sxs-lookup"><span data-stu-id="b070a-129">For more information, see [Search data stores](../technology-choices/search-options.md).</span></span>
