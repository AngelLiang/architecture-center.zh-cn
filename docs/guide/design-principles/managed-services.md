---
title: 使用托管服务
titleSuffix: Azure Application Architecture Guide
description: 如果可能，请使用平台即服务 (PaaS)，而不是基础结构即服务 (IaaS)。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: f08914e1eacc4f02fdb16093fe590f46249e3da3
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249532"
---
# <a name="use-managed-services"></a><span data-ttu-id="aecef-103">使用托管服务</span><span class="sxs-lookup"><span data-stu-id="aecef-103">Use managed services</span></span>

## <a name="when-possible-use-platform-as-a-service-paas-rather-than-infrastructure-as-a-service-iaas"></a><span data-ttu-id="aecef-104">如果可能，请使用平台即为服务 (PaaS)，而不是基础结构即服务 (IaaS)</span><span class="sxs-lookup"><span data-stu-id="aecef-104">When possible, use platform as a service (PaaS) rather than infrastructure as a service (IaaS)</span></span>

<span data-ttu-id="aecef-105">IaaS 就像有一盒零件。</span><span class="sxs-lookup"><span data-stu-id="aecef-105">IaaS is like having a box of parts.</span></span> <span data-ttu-id="aecef-106">你可以构建任何东西，但必须自己组装。</span><span class="sxs-lookup"><span data-stu-id="aecef-106">You can build anything, but you have to assemble it yourself.</span></span> <span data-ttu-id="aecef-107">托管服务更易于配置和管理。</span><span class="sxs-lookup"><span data-stu-id="aecef-107">Managed services are easier to configure and administer.</span></span> <span data-ttu-id="aecef-108">无需提供 VM、设置 VNet、管理补丁和更新，以及与在 VM 上运行软件相关的所有其他开销。</span><span class="sxs-lookup"><span data-stu-id="aecef-108">You don't need to provision VMs, set up VNets, manage patches and updates, and all of the other overhead associated with running software on a VM.</span></span>

<span data-ttu-id="aecef-109">例如，假设应用程序需要一个消息队列。</span><span class="sxs-lookup"><span data-stu-id="aecef-109">For example, suppose your application needs a message queue.</span></span> <span data-ttu-id="aecef-110">可以使用类似 RabbitMQ 的东西在 VM 上设置自己的消息传递服务。</span><span class="sxs-lookup"><span data-stu-id="aecef-110">You could set up your own messaging service on a VM, using something like RabbitMQ.</span></span> <span data-ttu-id="aecef-111">但 Azure 服务总线已经提供了可靠的消息传递作为服务，而且它更易于设置。</span><span class="sxs-lookup"><span data-stu-id="aecef-111">But Azure Service Bus already provides reliable messaging as service, and it's simpler to set up.</span></span> <span data-ttu-id="aecef-112">只需创建一个服务总线命名空间（这可以作为部署脚本的一部分来完成），然后使用客户端 SDK 调用服务总线。</span><span class="sxs-lookup"><span data-stu-id="aecef-112">Just create a Service Bus namespace (which can be done as part of a deployment script) and then call Service Bus using the client SDK.</span></span>

<span data-ttu-id="aecef-113">当然，应用程序可能具有某些特定要求，会使 IaaS 方法更合适。</span><span class="sxs-lookup"><span data-stu-id="aecef-113">Of course, your application may have specific requirements that make an IaaS approach more suitable.</span></span> <span data-ttu-id="aecef-114">但即使应用程序基于 IaaS，也能找到可以自然合并托管服务的位置。</span><span class="sxs-lookup"><span data-stu-id="aecef-114">However, even if your application is based on IaaS, look for places where it may be natural to incorporate managed services.</span></span> <span data-ttu-id="aecef-115">其中包括缓存、队列和数据存储。</span><span class="sxs-lookup"><span data-stu-id="aecef-115">These include cache, queues, and data storage.</span></span>

| <span data-ttu-id="aecef-116">取消运行…</span><span class="sxs-lookup"><span data-stu-id="aecef-116">Instead of running...</span></span> | <span data-ttu-id="aecef-117">考虑使用…</span><span class="sxs-lookup"><span data-stu-id="aecef-117">Consider using...</span></span> |
|-----------------------|-------------|
| <span data-ttu-id="aecef-118">Active Directory</span><span class="sxs-lookup"><span data-stu-id="aecef-118">Active Directory</span></span> | <span data-ttu-id="aecef-119">Azure Active Directory 域服务</span><span class="sxs-lookup"><span data-stu-id="aecef-119">Azure Active Directory Domain Services</span></span> |
| <span data-ttu-id="aecef-120">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="aecef-120">Elasticsearch</span></span> | <span data-ttu-id="aecef-121">Azure 搜索</span><span class="sxs-lookup"><span data-stu-id="aecef-121">Azure Search</span></span> |
| <span data-ttu-id="aecef-122">Hadoop</span><span class="sxs-lookup"><span data-stu-id="aecef-122">Hadoop</span></span> | <span data-ttu-id="aecef-123">HDInsight</span><span class="sxs-lookup"><span data-stu-id="aecef-123">HDInsight</span></span> |
| <span data-ttu-id="aecef-124">IIS</span><span class="sxs-lookup"><span data-stu-id="aecef-124">IIS</span></span> | <span data-ttu-id="aecef-125">应用服务</span><span class="sxs-lookup"><span data-stu-id="aecef-125">App Service</span></span> |
| <span data-ttu-id="aecef-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="aecef-126">MongoDB</span></span> | <span data-ttu-id="aecef-127">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="aecef-127">Cosmos DB</span></span> |
| <span data-ttu-id="aecef-128">Redis</span><span class="sxs-lookup"><span data-stu-id="aecef-128">Redis</span></span> | <span data-ttu-id="aecef-129">Azure Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="aecef-129">Azure Redis Cache</span></span> |
| <span data-ttu-id="aecef-130">SQL Server</span><span class="sxs-lookup"><span data-stu-id="aecef-130">SQL Server</span></span> | <span data-ttu-id="aecef-131">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="aecef-131">Azure SQL Database</span></span> |
