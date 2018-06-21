---
title: 通过分区解决限制
description: 使用分区来解决数据库、网络和计算限制
author: MikeWasson
ms.openlocfilehash: 86306c6c33ea7a93c4c1f868d820cc522095a8b7
ms.sourcegitcommit: 26b04f138a860979aea5d253ba7fecffc654841e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36206528"
---
# <a name="partition-around-limits"></a><span data-ttu-id="c9ca3-103">通过分区解决限制</span><span class="sxs-lookup"><span data-stu-id="c9ca3-103">Partition around limits</span></span>

## <a name="use-partitioning-to-work-around-database-network-and-compute-limits"></a><span data-ttu-id="c9ca3-104">使用分区来解决数据库、网络和计算限制</span><span class="sxs-lookup"><span data-stu-id="c9ca3-104">Use partitioning to work around database, network, and compute limits</span></span>

<span data-ttu-id="c9ca3-105">在云中，所有服务都有纵向扩展的限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-105">In the cloud, all services have limits in their ability to scale up.</span></span> <span data-ttu-id="c9ca3-106">[Azure 订阅和服务限制、配额和约束][azure-limits]中介绍了 Azure 服务限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-106">Azure service limits are documented in [Azure subscription and service limits, quotas, and constraints][azure-limits].</span></span> <span data-ttu-id="c9ca3-107">限制包括内核数、数据库大小、查询吞吐量和网络吞吐量。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-107">Limits include number of cores, database size, query throughput, and network throughput.</span></span> <span data-ttu-id="c9ca3-108">如果系统增长到足够大，可能会命中一个或多个限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-108">If your system grows sufficiently large, you may hit one or more of these limits.</span></span> <span data-ttu-id="c9ca3-109">使用分区来解决这些限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-109">Use partitioning to work around these limits.</span></span>

<span data-ttu-id="c9ca3-110">有很多方法可用于分区系统，例如：</span><span class="sxs-lookup"><span data-stu-id="c9ca3-110">There are many ways to partition a system, such as:</span></span>

- <span data-ttu-id="c9ca3-111">分区数据库，以避免对数据库大小、数据 I/O 或并发会话数的限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-111">Partition a database to avoid limits on database size, data I/O, or number of concurrent sessions.</span></span>

- <span data-ttu-id="c9ca3-112">分区队列或消息总线，以避免对请求数或并发连接数的限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-112">Partition a queue or message bus to avoid limits on the number of requests or the number of concurrent connections.</span></span>

- <span data-ttu-id="c9ca3-113">分区应用服务 Web 应用，以避免对每个应用服务计划的实例数的限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-113">Partition an App Service web app to avoid limits on the number of instances per App Service plan.</span></span> 

<span data-ttu-id="c9ca3-114">可以水平、垂直、或按功能分区数据库。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-114">A database can be partitioned *horizontally*, *vertically*, or *functionally*.</span></span>

- <span data-ttu-id="c9ca3-115">在水平分区（也称为分片）中，每个分区保存总数据集的子集的数据。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-115">In horizontal partitioning, also called sharding, each partition holds data for a subset of the total data set.</span></span> <span data-ttu-id="c9ca3-116">这些分区共享相同的数据架构。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-116">The partitions share the same data schema.</span></span> <span data-ttu-id="c9ca3-117">例如，名称以 A&ndash;M 开头的客户进入一个分区，以 N&ndash;Z 开头的进入另一个分区。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-117">For example, customers whose names start with A&ndash;M go into one partition, N&ndash;Z into another partition.</span></span>

- <span data-ttu-id="c9ca3-118">在垂直分区中，每个分区保存数据存储中项字段的子集。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-118">In vertical partitioning, each partition holds a subset of the fields for the items in the data store.</span></span> <span data-ttu-id="c9ca3-119">例如，将经常访问的字段放在一个分区中，将较不经常访问的字段放在另一个分区。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-119">For example, put frequently accessed fields in one partition, and less frequently accessed fields in another.</span></span>

- <span data-ttu-id="c9ca3-120">在功能分区中，根据系统中每个界限上下文使用数据的方式对数据进行分区。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-120">In functional partitioning, data is partitioned according to how it is used by each bounded context in the system.</span></span> <span data-ttu-id="c9ca3-121">例如，在一个分区中存储发票数据，在另一个分区中存储产品库存数据。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-121">For example, store invoice data in one partition and product inventory data in another.</span></span> <span data-ttu-id="c9ca3-122">架构是独立的。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-122">The schemas are independent.</span></span>

<span data-ttu-id="c9ca3-123">有关更多详细指南，请参阅[数据分区][data-partitioning-guidance]。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-123">For more detailed guidance, see [Data partitioning][data-partitioning-guidance].</span></span>

## <a name="recommendations"></a><span data-ttu-id="c9ca3-124">建议</span><span class="sxs-lookup"><span data-stu-id="c9ca3-124">Recommendations</span></span>

<span data-ttu-id="c9ca3-125">**分区应用程序的不同部分**。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-125">**Partition different parts of the application**.</span></span> <span data-ttu-id="c9ca3-126">数据库显然很适合用于分区，但也需考虑存储、缓存、队列和计算实例。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-126">Databases are one obvious candidate for partitioning, but also consider storage, cache, queues, and compute instances.</span></span>

<span data-ttu-id="c9ca3-127">**设计分区键以避免产生热点**。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-127">**Design the partition key to avoid hot spots**.</span></span> <span data-ttu-id="c9ca3-128">如果分区数据库，但一个分片仍然获取大多数请求，那么问题还未解决。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-128">If you partition a database, but one shard still gets the majority of the requests, then you haven't solved your problem.</span></span> <span data-ttu-id="c9ca3-129">理想情况下，负载会在所有分区中均匀分布。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-129">Ideally, load gets distributed evenly across all the partitions.</span></span> <span data-ttu-id="c9ca3-130">例如，按客户 ID 而不是客户名称的首字母进行哈希分区，因为某些首字母会更集中。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-130">For example, hash by customer ID and not the first letter of the customer name, because some letters are more frequent.</span></span> <span data-ttu-id="c9ca3-131">分区消息队列时，该原则也同样适用。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-131">The same principle applies when partitioning a message queue.</span></span> <span data-ttu-id="c9ca3-132">选择一个可以在队列集中平均分布消息的分区键。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-132">Pick a partition key that leads to an even distribution of messages across the set of queues.</span></span> <span data-ttu-id="c9ca3-133">有关更多信息，请参阅[分片][sharding]。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-133">For more information, see [Sharding][sharding].</span></span>

<span data-ttu-id="c9ca3-134">**通过分区解决 Azure 订阅和服务限制**。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-134">**Partition around Azure subscription and service limits**.</span></span> <span data-ttu-id="c9ca3-135">单个组件和服务有限制，但订阅和资源组也有限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-135">Individual components and services have limits, but there are also limits for subscriptions and resource groups.</span></span> <span data-ttu-id="c9ca3-136">对于非常大的应用程序，可能需要进行分区来解决这些限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-136">For very large applications, you might need to partition around those limits.</span></span>  

<span data-ttu-id="c9ca3-137">**在不同级别分区**。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-137">**Partition at different levels**.</span></span> <span data-ttu-id="c9ca3-138">请考虑部署在 VM 上的数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-138">Consider a database server deployed on a VM.</span></span> <span data-ttu-id="c9ca3-139">VM 有一个由 Azure 存储支持的 VHD。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-139">The VM has a VHD that is backed by Azure Storage.</span></span> <span data-ttu-id="c9ca3-140">存储帐户属于 Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-140">The storage account belongs to an Azure subscription.</span></span> <span data-ttu-id="c9ca3-141">请注意，层次结构中的每个步骤都有限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-141">Notice that each step in the hierarchy has limits.</span></span> <span data-ttu-id="c9ca3-142">数据库服务器可能有连接池限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-142">The database server may have a connection pool limit.</span></span> <span data-ttu-id="c9ca3-143">VM 有 CPU 和网络限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-143">VMs have CPU and network limits.</span></span> <span data-ttu-id="c9ca3-144">存储有 IOPS 限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-144">Storage has IOPS limits.</span></span> <span data-ttu-id="c9ca3-145">订阅有 VM 内核数的限制。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-145">The subscription has limits on the number of VM cores.</span></span> <span data-ttu-id="c9ca3-146">一般来说，在较低的层次结构更容易分区。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-146">Generally, it's easier to partition lower in the hierarchy.</span></span> <span data-ttu-id="c9ca3-147">仅大型应用程序需要在订阅级别进行分区。</span><span class="sxs-lookup"><span data-stu-id="c9ca3-147">Only large applications should need to partition at the subscription level.</span></span> 

<!-- links -->

[azure-limits]: /azure/azure-subscription-service-limits
[data-partitioning-guidance]: ../../best-practices/data-partitioning.md
[sharding]: ../../patterns/sharding.md

 