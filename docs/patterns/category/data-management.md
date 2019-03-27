---
title: 数据管理模式
titleSuffix: Cloud Design Patterns
description: 数据管理是云应用程序的关键元素，它影响着大部分质量属性。 出于性能、可伸缩性或可用性等方面的原因，数据通常托管在不同的位置并跨多个服务器，这可能会带来一系列的挑战。 例如，必须维护数据一致性，通常需要跨不同的位置同步数据。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 25571a431836656856ed3f299455dfdb94ae3477
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243048"
---
# <a name="data-management-patterns"></a><span data-ttu-id="ba74f-106">数据管理模式</span><span class="sxs-lookup"><span data-stu-id="ba74f-106">Data Management patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="ba74f-107">数据管理是云应用程序的关键元素，它影响着大部分质量属性。</span><span class="sxs-lookup"><span data-stu-id="ba74f-107">Data management is the key element of cloud applications, and influences most of the quality attributes.</span></span> <span data-ttu-id="ba74f-108">出于性能、可伸缩性或可用性等方面的原因，数据通常托管在不同的位置并跨多个服务器，这可能会带来一系列的挑战。</span><span class="sxs-lookup"><span data-stu-id="ba74f-108">Data is typically hosted in different locations and across multiple servers for reasons such as performance, scalability or availability, and this can present a range of challenges.</span></span> <span data-ttu-id="ba74f-109">例如，必须维护数据一致性，通常需要跨不同的位置同步数据。</span><span class="sxs-lookup"><span data-stu-id="ba74f-109">For example, data consistency must be maintained, and data will typically need to be synchronized across different locations.</span></span>

|                        <span data-ttu-id="ba74f-110">模式</span><span class="sxs-lookup"><span data-stu-id="ba74f-110">Pattern</span></span>                         |                                                                  <span data-ttu-id="ba74f-111">摘要</span><span class="sxs-lookup"><span data-stu-id="ba74f-111">Summary</span></span>                                                                  |
|--------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
|            [<span data-ttu-id="ba74f-112">缓存端</span><span class="sxs-lookup"><span data-stu-id="ba74f-112">Cache-Aside</span></span>](../cache-aside.md)            |                                            <span data-ttu-id="ba74f-113">将数据按需从数据存储加载到缓存中</span><span class="sxs-lookup"><span data-stu-id="ba74f-113">Load data on demand into a cache from a data store</span></span>                                             |
|                   [<span data-ttu-id="ba74f-114">CQRS</span><span class="sxs-lookup"><span data-stu-id="ba74f-114">CQRS</span></span>](../cqrs.md)                   |                    <span data-ttu-id="ba74f-115">使用独立接口将读取数据的操作与更新数据的操作分离。</span><span class="sxs-lookup"><span data-stu-id="ba74f-115">Segregate operations that read data from operations that update data by using separate interfaces.</span></span>                     |
|         [<span data-ttu-id="ba74f-116">事件溯源</span><span class="sxs-lookup"><span data-stu-id="ba74f-116">Event Sourcing</span></span>](../event-sourcing.md)         |               <span data-ttu-id="ba74f-117">使用只追加存储来记录描述域中数据采取的操作的完整系列事件。</span><span class="sxs-lookup"><span data-stu-id="ba74f-117">Use an append-only store to record the full series of events that describe actions taken on data in a domain.</span></span>               |
|            [<span data-ttu-id="ba74f-118">索引表</span><span class="sxs-lookup"><span data-stu-id="ba74f-118">Index Table</span></span>](../index-table.md)            |                         <span data-ttu-id="ba74f-119">在数据存储中对查询经常引用的字段创建索引。</span><span class="sxs-lookup"><span data-stu-id="ba74f-119">Create indexes over the fields in data stores that are frequently referenced by queries.</span></span>                          |
|      [<span data-ttu-id="ba74f-120">具体化视图</span><span class="sxs-lookup"><span data-stu-id="ba74f-120">Materialized View</span></span>](../materialized-view.md)      | <span data-ttu-id="ba74f-121">当未针对所需的查询操作完美设置数据的格式时，在一个或多个数据存储中基于数据生成预填充的视图。</span><span class="sxs-lookup"><span data-stu-id="ba74f-121">Generate prepopulated views over the data in one or more data stores when the data isn't ideally formatted for required query operations.</span></span> |
|               [<span data-ttu-id="ba74f-122">分片</span><span class="sxs-lookup"><span data-stu-id="ba74f-122">Sharding</span></span>](../sharding.md)               |                                    <span data-ttu-id="ba74f-123">将数据存储划分为一组水平分区或分片。</span><span class="sxs-lookup"><span data-stu-id="ba74f-123">Divide a data store into a set of horizontal partitions or shards.</span></span>                                     |
| [<span data-ttu-id="ba74f-124">静态内容托管</span><span class="sxs-lookup"><span data-stu-id="ba74f-124">Static Content Hosting</span></span>](../static-content-hosting.md) |                   <span data-ttu-id="ba74f-125">将静态内容部署到基于云的存储服务，再由后者将它们直接传送给客户端。</span><span class="sxs-lookup"><span data-stu-id="ba74f-125">Deploy static content to a cloud-based storage service that can deliver them directly to the client.</span></span>                    |
|              [<span data-ttu-id="ba74f-126">附属密钥</span><span class="sxs-lookup"><span data-stu-id="ba74f-126">Valet Key</span></span>](../valet-key.md)              |                 <span data-ttu-id="ba74f-127">使用令牌或密钥，向客户端授予对特定资源或服务的受限直接访问权限。</span><span class="sxs-lookup"><span data-stu-id="ba74f-127">Use a token or key that provides clients with restricted direct access to a specific resource or service.</span></span>                 |
