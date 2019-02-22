---
title: CAF：软件定义的网络 - 云原生
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 云原生虚拟网络服务的介绍
author: rotycenh
ms.openlocfilehash: e0ad6803f2ddc982ea0c42c59fdf2486e1710433
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900519"
---
# <a name="software-defined-networks-hub-and-spoke"></a><span data-ttu-id="8d37a-103">软件定义网络：中心和分支</span><span class="sxs-lookup"><span data-stu-id="8d37a-103">Software Defined Networks: Hub and Spoke</span></span>

<span data-ttu-id="8d37a-104">中心和分支网络模型将基于 Azure 的云网络基础结构组织到多个连接的虚拟网络中。</span><span class="sxs-lookup"><span data-stu-id="8d37a-104">The hub and spoke networking model organizes your Azure-based cloud network infrastructure into multiple connected virtual networks.</span></span> <span data-ttu-id="8d37a-105">通过此模型可以更高效地管理常见通信或安全要求，以及应对潜在的订阅限制。</span><span class="sxs-lookup"><span data-stu-id="8d37a-105">This model allows you to more efficiently manage common communication or security requirements and deal with potential subscription limitations.</span></span>

<span data-ttu-id="8d37a-106">在中心和分支模型中，中心是一个虚拟网络，充当用于管理外部连接和托管多个工作负荷所使用的服务的中央位置。</span><span class="sxs-lookup"><span data-stu-id="8d37a-106">In the hub and spoke model, the *hub* is a virtual network that acts as a central location for managing external connectivity and hosting services used by multiple workloads.</span></span> <span data-ttu-id="8d37a-107">分支是托管工作负荷并通过[虚拟网络对等互连](/virtual-network/virtual-network-peering-overview)连接到中央中心的虚拟网络。</span><span class="sxs-lookup"><span data-stu-id="8d37a-107">The *spokes* are virtual networks that host workloads and connect to the central hub through [virtual network peering](/virtual-network/virtual-network-peering-overview).</span></span>

<span data-ttu-id="8d37a-108">传入或传出工作负荷分支网络的所有流量都通过中心网络进行路由，在其中可以通过集中管理的 IT 规则或流程对这些流量进行路由、检查或在其他方面进行管理。</span><span class="sxs-lookup"><span data-stu-id="8d37a-108">All traffic passing in or out of the workload spoke networks is routed through the hub network where it can be routed, inspected, or otherwise managed by centrally managed IT rules or processes.</span></span>

<span data-ttu-id="8d37a-109">此模型旨在解决以下问题：</span><span class="sxs-lookup"><span data-stu-id="8d37a-109">This model aims to address the following issues:</span></span>

- <span data-ttu-id="8d37a-110">成本节省和管理效率。</span><span class="sxs-lookup"><span data-stu-id="8d37a-110">Cost savings and management efficiency.</span></span> <span data-ttu-id="8d37a-111">将可以由多个工作负荷（例如网络虚拟设备 (NVAs) 和 DNS 服务器）共享的服务集中放置在单个位置中使 IT 可以跨多个工作负荷尽量减少冗余资源和管理工作。</span><span class="sxs-lookup"><span data-stu-id="8d37a-111">Centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location allows IT to minimize redundant resources and management effort across multiple workloads.</span></span>
- <span data-ttu-id="8d37a-112">克服订阅限制。</span><span class="sxs-lookup"><span data-stu-id="8d37a-112">Overcoming subscriptions limits.</span></span> <span data-ttu-id="8d37a-113">基于云的大型工作负荷需要使用的资源可能会多于单个 Azure 订阅中允许使用的资源（请参阅[订阅限制](/azure/azure-subscription-service-limits)）。</span><span class="sxs-lookup"><span data-stu-id="8d37a-113">Large cloud-based workloads may require the use of more resources than are allowed within a single Azure subscription (see [subscription limits](/azure/azure-subscription-service-limits)).</span></span> <span data-ttu-id="8d37a-114">将来自不同订阅的工作负荷虚拟网络对等互连到一个中央中心可以克服这些限制。</span><span class="sxs-lookup"><span data-stu-id="8d37a-114">Peering workload virtual networks from different subscriptions to a central hub can overcome these limits.</span></span>
- <span data-ttu-id="8d37a-115">关注点分离。</span><span class="sxs-lookup"><span data-stu-id="8d37a-115">Separation of concerns.</span></span> <span data-ttu-id="8d37a-116">能够在中央 IT 团队与工作负荷团队之间部署单独的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="8d37a-116">The ability to deploy individual workloads between central IT teams and workloads teams.</span></span>

<span data-ttu-id="8d37a-117">下图显示一个示例中心和分支体系结构，其中包括集中管理的混合连接。</span><span class="sxs-lookup"><span data-stu-id="8d37a-117">The following diagram shows an example hub and spoke architecture including centrally managed hybrid connectivity.</span></span>

![中心辐射型网络体系结构](../../../reference-architectures/hybrid-networking/images/hub-spoke.png)

<span data-ttu-id="8d37a-119">中心和分支体系结构通常与混合网络体系结构一起使用，从而提供与多个工作负荷之间共享的本地环境的集中管理连接。</span><span class="sxs-lookup"><span data-stu-id="8d37a-119">The hub and spoke architecture is often used alongside the hybrid networking architecture, providing a centrally managed connection to your on-premises environment shared between multiple workloads.</span></span> <span data-ttu-id="8d37a-120">在此方案中，工作负荷与本地之间传输的所有流量都会经过中心，在其中可以对这些流量进行管理和保护。</span><span class="sxs-lookup"><span data-stu-id="8d37a-120">In this scenario, all traffic traveling between the workloads and on-premises passes through the hub where it can be managed and secured.</span></span>

## <a name="hub-and-spoke-assumptions"></a><span data-ttu-id="8d37a-121">中心和分支假设</span><span class="sxs-lookup"><span data-stu-id="8d37a-121">Hub and spoke assumptions</span></span>

<span data-ttu-id="8d37a-122">实现中心和分支虚拟网络体系结构的假设条件如下：</span><span class="sxs-lookup"><span data-stu-id="8d37a-122">Implementing a hub and spoke virtual networking architecture assumes the following:</span></span>

- <span data-ttu-id="8d37a-123">云部署涉及在单独工作环境（如开发、测试和生产）中托管且全部依赖于一组公用服务（例如 DNS 或目录服务）的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="8d37a-123">Your cloud deployments will involve workloads hosted in separate working environments, such as development, test, and production, that all rely on a set of common services such as DNS or directory services.</span></span>
- <span data-ttu-id="8d37a-124">工作负荷无需相互通信，但具有公用外部通信和共享服务要求。</span><span class="sxs-lookup"><span data-stu-id="8d37a-124">Your workloads do not need to communicate with each other but have common external communications and shared services requirements.</span></span>
- <span data-ttu-id="8d37a-125">工作负荷需要的资源多于单个 Azure 订阅中可用的资源。</span><span class="sxs-lookup"><span data-stu-id="8d37a-125">Your workloads require more resources than are available within a single Azure subscription.</span></span>
- <span data-ttu-id="8d37a-126">需要向工作负荷团队提供对其自己的资源的委托管理权限，同时维护对外部连接的中央安全控制。</span><span class="sxs-lookup"><span data-stu-id="8d37a-126">You need to provide workload teams with delegated management rights over their own resources while maintaining central security control over external connectivity.</span></span>

## <a name="global-hub-and-spoke"></a><span data-ttu-id="8d37a-127">全局中心和分支</span><span class="sxs-lookup"><span data-stu-id="8d37a-127">Global hub and spoke</span></span>

<span data-ttu-id="8d37a-128">中心和分支体系结构通常使用部署到相同 Azure 区域的虚拟网络来实现，以尽量降低网络之间的延迟。</span><span class="sxs-lookup"><span data-stu-id="8d37a-128">Hub and spoke architectures are commonly implemented with virtual networks deployed to the same Azure Region to minimize latency between networks.</span></span> <span data-ttu-id="8d37a-129">但是，遍布全球的大型组织可能需要跨多个区域部署工作负荷，以实现可用性、灾难恢复或法规要求。</span><span class="sxs-lookup"><span data-stu-id="8d37a-129">However, large organizations with global reach may need to deploy workloads across multiple regions for availability, disaster recovery, or regulatory requirements.</span></span> <span data-ttu-id="8d37a-130">通过使用 Azure [全局虚拟网络对等互连](/azure/virtual-network/virtual-network-peering-overview)，中心和分支模型可以跨区域扩展集中管理和共享的服务，以支持分布在全世界的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="8d37a-130">Through the use of Azure [global virtual network peering](/azure/virtual-network/virtual-network-peering-overview), the hub and spoke model can extend centralized management and shared services across regions to support workloads distributed across the world.</span></span>

## <a name="learn-more"></a><span data-ttu-id="8d37a-131">了解详细信息</span><span class="sxs-lookup"><span data-stu-id="8d37a-131">Learn more</span></span>

<span data-ttu-id="8d37a-132">有关如何在 Azure 上实现中心和分支网络的示例，请参阅 Azure 参考体系结构站点上的以下示例：</span><span class="sxs-lookup"><span data-stu-id="8d37a-132">For examples of how to implement hub and spoke networks on Azure, see the following examples on the Azure Reference Architectures site:</span></span>

- [<span data-ttu-id="8d37a-133">在 Azure 中实现中心辐射型网络拓扑</span><span class="sxs-lookup"><span data-stu-id="8d37a-133">Implement a hub-spoke network topology in Azure</span></span>](../../../reference-architectures/hybrid-networking/hub-spoke.md)
- [<span data-ttu-id="8d37a-134">在 Azure 中使用共享服务实现中心辐射型网络拓扑</span><span class="sxs-lookup"><span data-stu-id="8d37a-134">Implement a hub-spoke network topology with shared services in Azure</span></span>](../../../reference-architectures/hybrid-networking/shared-services.md)
