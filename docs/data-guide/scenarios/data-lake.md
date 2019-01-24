---
title: Data Lake
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: d433867886ce0afc219fcc9f35eb7f8b3ce6bee1
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484697"
---
# <a name="data-lakes"></a><span data-ttu-id="5a36d-102">Data Lake</span><span class="sxs-lookup"><span data-stu-id="5a36d-102">Data lakes</span></span>

<span data-ttu-id="5a36d-103">Data Lake 是一种存储库，可以按数据本来的原始格式存储大量的数据。</span><span class="sxs-lookup"><span data-stu-id="5a36d-103">A data lake is a storage repository that holds a large amount of data in its native, raw format.</span></span> <span data-ttu-id="5a36d-104">Data Lake 存储经过优化，数据规模可以达到数 TB 甚至数 PB。</span><span class="sxs-lookup"><span data-stu-id="5a36d-104">Data lake stores are optimized for scaling to terabytes and petabytes of data.</span></span> <span data-ttu-id="5a36d-105">这些数据通常来自多个异类源，可以是结构化的、半结构化的或非结构化的。</span><span class="sxs-lookup"><span data-stu-id="5a36d-105">The data typically comes from multiple heterogeneous sources, and may be structured, semi-structured, or unstructured.</span></span> <span data-ttu-id="5a36d-106">Data Lake 的理念是指以原始的非转换状态存储一切内容。</span><span class="sxs-lookup"><span data-stu-id="5a36d-106">The idea with a data lake is to store everything in its original, untransformed state.</span></span> <span data-ttu-id="5a36d-107">此方法不同于传统的[数据仓库](../relational-data/data-warehousing.md)，后者在引入数据时对数据进行转换和处理。</span><span class="sxs-lookup"><span data-stu-id="5a36d-107">This approach differs from a traditional [data warehouse](../relational-data/data-warehousing.md), which transforms and processes the data at the time of ingestion.</span></span>

<span data-ttu-id="5a36d-108">Data Lake 的优点：</span><span class="sxs-lookup"><span data-stu-id="5a36d-108">Advantages of a data lake:</span></span>

- <span data-ttu-id="5a36d-109">绝不会丢弃数据，因为数据是以原始格式存储的。</span><span class="sxs-lookup"><span data-stu-id="5a36d-109">Data is never thrown away, because the data is stored in its raw format.</span></span> <span data-ttu-id="5a36d-110">这在无法事先知道可以从数据中获得何种见解的大数据 环境中特别有用。</span><span class="sxs-lookup"><span data-stu-id="5a36d-110">This is especially useful in a big data environment, when you may not know in advance what insights are available from the data.</span></span>
- <span data-ttu-id="5a36d-111">用户可以浏览数据并创建自己的查询。</span><span class="sxs-lookup"><span data-stu-id="5a36d-111">Users can explore the data and create their own queries.</span></span>
- <span data-ttu-id="5a36d-112">可能比传统的 ETL 工具更快。</span><span class="sxs-lookup"><span data-stu-id="5a36d-112">May be faster than traditional ETL tools.</span></span>
- <span data-ttu-id="5a36d-113">比数据仓库更灵活，因为它可以存储非结构化和半结构化数据。</span><span class="sxs-lookup"><span data-stu-id="5a36d-113">More flexible than a data warehouse, because it can store unstructured and semi-structured data.</span></span>

<span data-ttu-id="5a36d-114">完整的 Data Lake 解决方案由存储和处理两部分组成。</span><span class="sxs-lookup"><span data-stu-id="5a36d-114">A complete data lake solution consists of both storage and processing.</span></span> <span data-ttu-id="5a36d-115">Data Lake 存储的设计用途包括：容错、确保无限可伸缩性，以及在引入不同形状和大小的数据时实现高吞吐量。</span><span class="sxs-lookup"><span data-stu-id="5a36d-115">Data lake storage is designed for fault-tolerance, infinite scalability, and high-throughput ingestion of data with varying shapes and sizes.</span></span> <span data-ttu-id="5a36d-116">Data Lake 处理涉及生成一个或多个用于实现此类目的的处理引擎，可以对存储在 Data Lake 中的数据进行大规模的处理。</span><span class="sxs-lookup"><span data-stu-id="5a36d-116">Data lake processing involves one or more processing engines built with these goals in mind, and can operate on data stored in a data lake at scale.</span></span>

## <a name="when-to-use-a-data-lake"></a><span data-ttu-id="5a36d-117">何时使用 Data Lake</span><span class="sxs-lookup"><span data-stu-id="5a36d-117">When to use a data lake</span></span>

<span data-ttu-id="5a36d-118">Data Lake 的典型用途包括[数据浏览](./interactive-data-exploration.md)、数据分析和机器学习。</span><span class="sxs-lookup"><span data-stu-id="5a36d-118">Typical uses for a data lake include [data exploration](./interactive-data-exploration.md), data analytics, and machine learning.</span></span>

<span data-ttu-id="5a36d-119">Data Lake 也可充当数据仓库的数据源。</span><span class="sxs-lookup"><span data-stu-id="5a36d-119">A data lake can also act as the data source for a data warehouse.</span></span> <span data-ttu-id="5a36d-120">使用此方法时，原始数据先引入到 Data Lake 中，然后转换为结构化的可查询格式。</span><span class="sxs-lookup"><span data-stu-id="5a36d-120">With this approach, the raw data is ingested into the data lake and then transformed into a structured queryable format.</span></span> <span data-ttu-id="5a36d-121">此转换通常使用 [ELT](../relational-data/etl.md#extract-load-and-transform-elt)（提取-加载-转换）管道，就地对数据进行引入和转换操作。</span><span class="sxs-lookup"><span data-stu-id="5a36d-121">Typically this transformation uses an [ELT](../relational-data/etl.md#extract-load-and-transform-elt) (extract-load-transform) pipeline, where the data is ingested and transformed in place.</span></span> <span data-ttu-id="5a36d-122">已经是关系数据的源数据可以通过 ETL 过程直接进入数据仓库，跳过 Data Lake。</span><span class="sxs-lookup"><span data-stu-id="5a36d-122">Source data that is already relational may go directly into the data warehouse, using an ETL process, skipping the data lake.</span></span>

<span data-ttu-id="5a36d-123">Data Lake 存储通常用于事件流式处理或 IoT 方案，因为此类存储可以保存大量的关系数据和非关系数据，不需进行转换，也不需架构定义。</span><span class="sxs-lookup"><span data-stu-id="5a36d-123">Data lake stores are often used in event streaming or IoT scenarios, because they can persist large amounts of relational and nonrelational data without transformation or schema definition.</span></span> <span data-ttu-id="5a36d-124">此类存储可以在低延迟的状况下处理大量的小型写入数据，并已针对大规模的吞吐量进行了优化。</span><span class="sxs-lookup"><span data-stu-id="5a36d-124">They are built to handle high volumes of small writes at low latency, and are optimized for massive throughput.</span></span>

## <a name="challenges"></a><span data-ttu-id="5a36d-125">挑战</span><span class="sxs-lookup"><span data-stu-id="5a36d-125">Challenges</span></span>

- <span data-ttu-id="5a36d-126">缺少架构或描述性元数据可能使得数据难以使用或查询。</span><span class="sxs-lookup"><span data-stu-id="5a36d-126">Lack of a schema or descriptive metadata can make the data hard to consume or query.</span></span>
- <span data-ttu-id="5a36d-127">整个数据缺少语义一致性可能导致数据难以分析，除非用户精于数据分析。</span><span class="sxs-lookup"><span data-stu-id="5a36d-127">Lack of semantic consistency across the data can make it challenging to perform analysis on the data, unless users are highly skilled at data analytics.</span></span>
- <span data-ttu-id="5a36d-128">可能难以保证进入 Data Lake 的数据的质量。</span><span class="sxs-lookup"><span data-stu-id="5a36d-128">It can be hard to guarantee the quality of the data going into the data lake.</span></span>
- <span data-ttu-id="5a36d-129">如果没有正确的监管，可能造成访问控制和隐私方面的问题。</span><span class="sxs-lookup"><span data-stu-id="5a36d-129">Without proper governance, access control and privacy issues can be problems.</span></span> <span data-ttu-id="5a36d-130">什么信息会进入 Data Lake？谁可以访问该数据？该数据的用途是什么？</span><span class="sxs-lookup"><span data-stu-id="5a36d-130">What information is going into the data lake, who can access that data, and for what uses?</span></span>
- <span data-ttu-id="5a36d-131">若要集成已经是关系数据的数据，Data Lake 可能不是最佳方式。</span><span class="sxs-lookup"><span data-stu-id="5a36d-131">A data lake may not be the best way to integrate data that is already relational.</span></span>
- <span data-ttu-id="5a36d-132">Data Lake 本身不提供跨组织的集成视图或整体视图。</span><span class="sxs-lookup"><span data-stu-id="5a36d-132">By itself, a data lake does not provide integrated or holistic views across the organization.</span></span>
- <span data-ttu-id="5a36d-133">Data Lake 可能会成为那些从来没有进行过实际分析或见解挖掘的数据的转储之地。</span><span class="sxs-lookup"><span data-stu-id="5a36d-133">A data lake may become a dumping ground for data that is never actually analyzed or mined for insights.</span></span>

## <a name="relevant-azure-services"></a><span data-ttu-id="5a36d-134">相关的 Azure 服务</span><span class="sxs-lookup"><span data-stu-id="5a36d-134">Relevant Azure services</span></span>

- <span data-ttu-id="5a36d-135">[Data Lake Store](/azure/data-lake-store/) 是一种兼容 Hadoop 的超大规模存储库。</span><span class="sxs-lookup"><span data-stu-id="5a36d-135">[Data Lake Store](/azure/data-lake-store/) is a hyper-scale, Hadoop-compatible repository.</span></span>
- <span data-ttu-id="5a36d-136">[Data Lake Analytics](/azure/data-lake-analytics/) 是一项按需分析作业服务，用于简化大数据分析。</span><span class="sxs-lookup"><span data-stu-id="5a36d-136">[Data Lake Analytics](/azure/data-lake-analytics/) is an on-demand analytics job service to simplify big data analytics.</span></span>
