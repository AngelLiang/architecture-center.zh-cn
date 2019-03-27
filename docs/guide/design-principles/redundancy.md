---
title: 实现全面冗余
titleSuffix: Azure Application Architecture Guide
description: 在应用程序中构建冗余，避免出现单一故障点。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: c8722250b5b5357c9ffcf65eced95b4f2b487c72
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241718"
---
# <a name="make-all-things-redundant"></a><span data-ttu-id="f780e-103">实现全面冗余</span><span class="sxs-lookup"><span data-stu-id="f780e-103">Make all things redundant</span></span>

## <a name="build-redundancy-into-your-application-to-avoid-having-single-points-of-failure"></a><span data-ttu-id="f780e-104">在应用程序中构建冗余，以避免出现单一故障点</span><span class="sxs-lookup"><span data-stu-id="f780e-104">Build redundancy into your application, to avoid having single points of failure</span></span>

<span data-ttu-id="f780e-105">有弹性的应用程序围绕故障路由。</span><span class="sxs-lookup"><span data-stu-id="f780e-105">A resilient application routes around failure.</span></span> <span data-ttu-id="f780e-106">标识应用程序中的关键路径。</span><span class="sxs-lookup"><span data-stu-id="f780e-106">Identify the critical paths in your application.</span></span> <span data-ttu-id="f780e-107">该路径中的每个点是否都存在冗余？</span><span class="sxs-lookup"><span data-stu-id="f780e-107">Is there redundancy at each point in the path?</span></span> <span data-ttu-id="f780e-108">如果子系统出现故障，应用程序是否会故障转移到其他组件？</span><span class="sxs-lookup"><span data-stu-id="f780e-108">If a subsystem fails, will the application fail over to something else?</span></span>

## <a name="recommendations"></a><span data-ttu-id="f780e-109">建议</span><span class="sxs-lookup"><span data-stu-id="f780e-109">Recommendations</span></span>

<span data-ttu-id="f780e-110">**考虑业务需求**。</span><span class="sxs-lookup"><span data-stu-id="f780e-110">**Consider business requirements**.</span></span> <span data-ttu-id="f780e-111">在系统中生成的冗余量会影响成本和复杂度。</span><span class="sxs-lookup"><span data-stu-id="f780e-111">The amount of redundancy built into a system can affect both cost and complexity.</span></span> <span data-ttu-id="f780e-112">体系结构应反映业务需求，如恢复时间目标 (RTO)。</span><span class="sxs-lookup"><span data-stu-id="f780e-112">Your architecture should be informed by your business requirements, such as recovery time objective (RTO).</span></span> <span data-ttu-id="f780e-113">例如，多区域部署比单区域部署昂贵，其管理也更复杂。</span><span class="sxs-lookup"><span data-stu-id="f780e-113">For example, a multi-region deployment is more expensive than a single-region deployment, and is more complicated to manage.</span></span> <span data-ttu-id="f780e-114">需要使用操作过程处理故障转移和故障回复。</span><span class="sxs-lookup"><span data-stu-id="f780e-114">You will need operational procedures to handle failover and failback.</span></span> <span data-ttu-id="f780e-115">可能为一些业务方案（而不是其他方案）考虑额外的成本和复杂性。</span><span class="sxs-lookup"><span data-stu-id="f780e-115">The additional cost and complexity might be justified for some business scenarios and not others.</span></span>

<span data-ttu-id="f780e-116">**将 VM 放在负载均衡器之后**。</span><span class="sxs-lookup"><span data-stu-id="f780e-116">**Place VMs behind a load balancer**.</span></span> <span data-ttu-id="f780e-117">请勿将一个 VM 用于任务关键的工作负载。</span><span class="sxs-lookup"><span data-stu-id="f780e-117">Don't use a single VM for mission-critical workloads.</span></span> <span data-ttu-id="f780e-118">而是将多个 VM 放置于负载均衡器之后。</span><span class="sxs-lookup"><span data-stu-id="f780e-118">Instead, place multiple VMs behind a load balancer.</span></span> <span data-ttu-id="f780e-119">如果任何 VM 变得不可用，负载均衡器会向其余正常运行的 VM 分配流量。</span><span class="sxs-lookup"><span data-stu-id="f780e-119">If any VM becomes unavailable, the load balancer distributes traffic to the remaining healthy VMs.</span></span> <span data-ttu-id="f780e-120">若要了解如何部署此配置，请参阅[用于提高可伸缩性和可用性的多个 VM][multi-vm-blueprint]。</span><span class="sxs-lookup"><span data-stu-id="f780e-120">To learn how to deploy this configuration, see [Multiple VMs for scalability and availability][multi-vm-blueprint].</span></span>

![负载均衡 VM 图](./images/load-balancing.svg)

<span data-ttu-id="f780e-122">**复制数据库**。</span><span class="sxs-lookup"><span data-stu-id="f780e-122">**Replicate databases**.</span></span> <span data-ttu-id="f780e-123">Azure SQL 数据库和 Cosmos DB 自动复制区域内的数据，且可以跨区域启用异地复制。</span><span class="sxs-lookup"><span data-stu-id="f780e-123">Azure SQL Database and Cosmos DB automatically replicate the data within a region, and you can enable geo-replication across regions.</span></span> <span data-ttu-id="f780e-124">如果使用的是 IaaS 数据库解决方案，请选择支持复制和故障转移的解决方案，如 [SQL Server AlwaysOn 可用性组][sql-always-on]。</span><span class="sxs-lookup"><span data-stu-id="f780e-124">If you are using an IaaS database solution, choose one that supports replication and failover, such as [SQL Server Always On Availability Groups][sql-always-on].</span></span>

<span data-ttu-id="f780e-125">**启用异地复制**。</span><span class="sxs-lookup"><span data-stu-id="f780e-125">**Enable geo-replication**.</span></span> <span data-ttu-id="f780e-126">[Azure SQL 数据库][sql-geo-replication]和 [Cosmos DB][cosmosdb-geo-replication] 的异地复制在一个或多个次要区域中创建数据的可读次要副本。</span><span class="sxs-lookup"><span data-stu-id="f780e-126">Geo-replication for [Azure SQL Database][sql-geo-replication] and [Cosmos DB][cosmosdb-geo-replication] creates secondary readable replicas of your data in one or more secondary regions.</span></span> <span data-ttu-id="f780e-127">出现故障时，数据库可以故障转移到供写入的次要区域。</span><span class="sxs-lookup"><span data-stu-id="f780e-127">In the event of an outage, the database can fail over to the secondary region for writes.</span></span>

<span data-ttu-id="f780e-128">**为提高可用性而分区**。</span><span class="sxs-lookup"><span data-stu-id="f780e-128">**Partition for availability**.</span></span> <span data-ttu-id="f780e-129">数据库分区通常用于提高可伸缩性，但它还可以提高可用性。</span><span class="sxs-lookup"><span data-stu-id="f780e-129">Database partitioning is often used to improve scalability, but it can also improve availability.</span></span> <span data-ttu-id="f780e-130">如果一个分片出现故障，仍可以访问其他分片。</span><span class="sxs-lookup"><span data-stu-id="f780e-130">If one shard goes down, the other shards can still be reached.</span></span> <span data-ttu-id="f780e-131">一个分片中的故障仅中断总事务的子集。</span><span class="sxs-lookup"><span data-stu-id="f780e-131">A failure in one shard will only disrupt a subset of the total transactions.</span></span>

<span data-ttu-id="f780e-132">**部署到多个区域**。</span><span class="sxs-lookup"><span data-stu-id="f780e-132">**Deploy to more than one region**.</span></span> <span data-ttu-id="f780e-133">为实现最高可用性，请将应用程序部署到多个区域。</span><span class="sxs-lookup"><span data-stu-id="f780e-133">For the highest availability, deploy the application to more than one region.</span></span> <span data-ttu-id="f780e-134">这样，在极少数情况下，当某个问题影响到整个区域时，应用程序可以故障转移到另一区域。</span><span class="sxs-lookup"><span data-stu-id="f780e-134">That way, in the rare case when a problem affects an entire region, the application can fail over to another region.</span></span> <span data-ttu-id="f780e-135">下图显示了使用 Azure 流量管理器处理故障转移的多区域应用程序。</span><span class="sxs-lookup"><span data-stu-id="f780e-135">The following diagram shows a multi-region application that uses Azure Traffic Manager to handle failover.</span></span>

![使用 Azure 流量管理器来处理故障转移的示意图](./images/failover.svg)

<span data-ttu-id="f780e-137">**同步前端和后端故障转移**。</span><span class="sxs-lookup"><span data-stu-id="f780e-137">**Synchronize front and backend failover**.</span></span> <span data-ttu-id="f780e-138">使用 Azure 流量管理器将前端故障转移。</span><span class="sxs-lookup"><span data-stu-id="f780e-138">Use Azure Traffic Manager to fail over the front end.</span></span> <span data-ttu-id="f780e-139">如果无法访问一个区域中的前端，流量管理器会将新请求路由到次要区域。</span><span class="sxs-lookup"><span data-stu-id="f780e-139">If the front end becomes unreachable in one region, Traffic Manager will route new requests to the secondary region.</span></span> <span data-ttu-id="f780e-140">可能需要协调数据库的故障转移，具体取决于数据库解决方案。</span><span class="sxs-lookup"><span data-stu-id="f780e-140">Depending on your database solution, you may need to coordinate failing over the database.</span></span>

<span data-ttu-id="f780e-141">**使用自动故障转移，但手动进行故障回复**。</span><span class="sxs-lookup"><span data-stu-id="f780e-141">**Use automatic failover but manual failback**.</span></span> <span data-ttu-id="f780e-142">将流量管理器用于自动故障转移，而不用于自动故障回复。</span><span class="sxs-lookup"><span data-stu-id="f780e-142">Use Traffic Manager for automatic failover, but not for automatic failback.</span></span> <span data-ttu-id="f780e-143">自动故障回复存在风险，即可能在区域尚未完全正常之前切换到主要区域。</span><span class="sxs-lookup"><span data-stu-id="f780e-143">Automatic failback carries a risk that you might switch to the primary region before the region is completely healthy.</span></span> <span data-ttu-id="f780e-144">请改为验证所有应用程序子系统均正常运行，然后再手动进行故障回复。</span><span class="sxs-lookup"><span data-stu-id="f780e-144">Instead, verify that all application subsystems are healthy before manually failing back.</span></span> <span data-ttu-id="f780e-145">此外，可能需要在故障回复前检查数据一致性，具体取决于数据库。</span><span class="sxs-lookup"><span data-stu-id="f780e-145">Also, depending on the database, you might need to check data consistency before failing back.</span></span>

<span data-ttu-id="f780e-146">**为流量管理器包括冗余**。</span><span class="sxs-lookup"><span data-stu-id="f780e-146">**Include redundancy for Traffic Manager**.</span></span> <span data-ttu-id="f780e-147">流量管理器是一个潜在的故障点。</span><span class="sxs-lookup"><span data-stu-id="f780e-147">Traffic Manager is a possible failure point.</span></span> <span data-ttu-id="f780e-148">查看流量管理器 SLA，然后决定仅使用流量管理器是否能满足高可用性的业务需求。</span><span class="sxs-lookup"><span data-stu-id="f780e-148">Review the Traffic Manager SLA, and determine whether using Traffic Manager alone meets your business requirements for high availability.</span></span> <span data-ttu-id="f780e-149">如果不能，请考虑添加另一个流量管理解决方案作为故障回复机制。</span><span class="sxs-lookup"><span data-stu-id="f780e-149">If not, consider adding another traffic management solution as a failback.</span></span> <span data-ttu-id="f780e-150">如果 Azure 流量管理器服务失败，请将 DNS 中的 CNAME 记录更改为指向其他流量管理服务。</span><span class="sxs-lookup"><span data-stu-id="f780e-150">If the Azure Traffic Manager service fails, change your CNAME records in DNS to point to the other traffic management service.</span></span>

<!-- links -->

[multi-vm-blueprint]: ../../reference-architectures/virtual-machines-windows/multi-vm.md

[cassandra]: https://cassandra.apache.org/
[cosmosdb-geo-replication]: /azure/cosmos-db/distribute-data-globally
[sql-always-on]: https://msdn.microsoft.com/library/hh510230.aspx
[sql-geo-replication]: /azure/sql-database/sql-database-geo-replication-overview
