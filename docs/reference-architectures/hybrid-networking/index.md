---
title: 将本地网络连接到 Azure
titleSuffix: Azure Reference Architectures
description: 比较用于将本地网络连接到 Azure 的参考体系结构。
author: telmosampaio
ms.date: 07/02/2018
ms.openlocfilehash: de509b6d95805f4fc871f6dbd76a87d2c0bec6f1
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53119908"
---
# <a name="choose-a-solution-for-connecting-an-on-premises-network-to-azure"></a><span data-ttu-id="f1c4f-103">选择用于将本地网络连接到 Azure 的解决方案</span><span class="sxs-lookup"><span data-stu-id="f1c4f-103">Choose a solution for connecting an on-premises network to Azure</span></span>

<span data-ttu-id="f1c4f-104">本文比较了用于将本地网络连接到 Azure 虚拟网络 (VNet) 的选项。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-104">This article compares options for connecting an on-premises network to an Azure Virtual Network (VNet).</span></span> <span data-ttu-id="f1c4f-105">每个选项都有可用的更详细的参考体系结构。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-105">For each option, a more detailed reference architecture is available.</span></span>

## <a name="vpn-connection"></a><span data-ttu-id="f1c4f-106">VPN 连接</span><span class="sxs-lookup"><span data-stu-id="f1c4f-106">VPN connection</span></span>

<span data-ttu-id="f1c4f-107">[VPN 网关](/azure/vpn-gateway/vpn-gateway-about-vpngateways)是一种虚拟网关，可在 Azure 虚拟网络和本地位置之间发送加密的流量。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-107">A [VPN gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways) is a type of virtual network gateway that sends encrypted traffic between an Azure virtual network and an on-premises location.</span></span> <span data-ttu-id="f1c4f-108">加密的流量流经公共 Internet。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-108">The encrypted traffic goes over the public Internet.</span></span>

<span data-ttu-id="f1c4f-109">此体系结构适合满足以下条件的混合应用程序：本地硬件与云之间的流量可能较小，或者用户愿意用略微延长的延迟换得云的灵活性和处理能力。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-109">This architecture is suitable for hybrid applications where the traffic between on-premises hardware and the cloud is likely to be light, or you are willing to trade slightly extended latency for the flexibility and processing power of the cloud.</span></span>

<span data-ttu-id="f1c4f-110">**优点**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-110">**Benefits**</span></span>

- <span data-ttu-id="f1c4f-111">易于配置。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-111">Simple to configure.</span></span>

<span data-ttu-id="f1c4f-112">**挑战**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-112">**Challenges**</span></span>

- <span data-ttu-id="f1c4f-113">需要本地 VPN 设备。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-113">Requires an on-premises VPN device.</span></span>
- <span data-ttu-id="f1c4f-114">尽管 Microsoft 保证每个 VPN 网关 99.9% 的可用性，但此 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 仅涵盖 VPN 网关，并不涉及与网关之间的网络连接。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-114">Although Microsoft guarantees 99.9% availability for each VPN Gateway, this [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) only covers the VPN gateway, and not your network connection to the gateway.</span></span>
- <span data-ttu-id="f1c4f-115">通过 Azure VPN 网关的 VPN 连接当前最多支持 1.25 Gbps 的带宽。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-115">A VPN connection over Azure VPN Gateway currently supports a maximum of 1.25 Gbps bandwidth.</span></span> <span data-ttu-id="f1c4f-116">如果预期会超过此吞吐量，可能需要跨多个 VPN 连接对 Azure 虚拟网络进行分区。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-116">You may need to partition your Azure virtual network across multiple VPN connections if you expect to exceed this throughput.</span></span>

<span data-ttu-id="f1c4f-117">**参考体系结构**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-117">**Reference architecture**</span></span>

- [<span data-ttu-id="f1c4f-118">使用 VPN 网关的混合网络</span><span class="sxs-lookup"><span data-stu-id="f1c4f-118">Hybrid network with VPN gateway</span></span>](./vpn.md)

## <a name="azure-expressroute-connection"></a><span data-ttu-id="f1c4f-119">Azure ExpressRoute 连接</span><span class="sxs-lookup"><span data-stu-id="f1c4f-119">Azure ExpressRoute connection</span></span>

<span data-ttu-id="f1c4f-120">[ExpressRoute](/azure/expressroute/) 连接通过第三方连接提供商使用专用连接。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-120">[ExpressRoute](/azure/expressroute/) connections use a private, dedicated connection through a third-party connectivity provider.</span></span> <span data-ttu-id="f1c4f-121">该专用连接将本地网络扩展到 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-121">The private connection extends your on-premises network into Azure.</span></span> 

<span data-ttu-id="f1c4f-122">此体系结构适合满足以下条件的混合应用程序：运行需要较高程度可伸缩性的大规模、任务关键型工作负荷。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-122">This architecture is suitable for hybrid applications running large-scale, mission-critical workloads that require a high degree of scalability.</span></span> 

<span data-ttu-id="f1c4f-123">**优点**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-123">**Benefits**</span></span>

- <span data-ttu-id="f1c4f-124">有很高的带宽可用；最高可达 10 Gbps，具体取决于连接提供商。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-124">Much higher bandwidth available; up to 10 Gbps depending on the connectivity provider.</span></span>
- <span data-ttu-id="f1c4f-125">支持动态缩放带宽以帮助在需求较低的时段降低成本。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-125">Supports dynamic scaling of bandwidth to help reduce costs during periods of lower demand.</span></span> <span data-ttu-id="f1c4f-126">但是，并非所有连接提供商都提供此选项。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-126">However, not all connectivity providers have this option.</span></span>
- <span data-ttu-id="f1c4f-127">可能会允许组织直接访问国家/地区云，具体取决于连接提供商。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-127">May allow your organization direct access to national clouds, depending on the connectivity provider.</span></span>
- <span data-ttu-id="f1c4f-128">跨整个连接的 99.9% 可用性 SLA。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-128">99.9% availability SLA across the entire connection.</span></span>

<span data-ttu-id="f1c4f-129">**挑战**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-129">**Challenges**</span></span>

- <span data-ttu-id="f1c4f-130">设置可能复杂。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-130">Can be complex to set up.</span></span> <span data-ttu-id="f1c4f-131">创建 ExpressRoute 连接需要使用第三方连接提供商。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-131">Creating an ExpressRoute connection requires working with a third-party connectivity provider.</span></span> <span data-ttu-id="f1c4f-132">该提供商负责预配网络连接。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-132">The provider is responsible for provisioning the network connection.</span></span>
- <span data-ttu-id="f1c4f-133">需要本地高带宽路由器。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-133">Requires high-bandwidth routers on-premises.</span></span>

<span data-ttu-id="f1c4f-134">**参考体系结构**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-134">**Reference architecture**</span></span>

- [<span data-ttu-id="f1c4f-135">使用 ExpressRoute 的混合网络</span><span class="sxs-lookup"><span data-stu-id="f1c4f-135">Hybrid network with ExpressRoute</span></span>](./expressroute.md)

## <a name="expressroute-with-vpn-failover"></a><span data-ttu-id="f1c4f-136">使用 ExpressRoute 和 VPN 实现故障转移</span><span class="sxs-lookup"><span data-stu-id="f1c4f-136">ExpressRoute with VPN failover</span></span>

<span data-ttu-id="f1c4f-137">此选项合并了前面两个选项，在正常情况下使用 ExpressRoute，但在 ExpressRoute 线路中发生连接丢失时故障转移到 VPN 连接。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-137">This options combines the previous two, using ExpressRoute in normal conditions, but failing over to a VPN connection if there is a loss of connectivity in the ExpressRoute circuit.</span></span>

<span data-ttu-id="f1c4f-138">此体系结构适合需要 ExpressRoute 的较高带宽并且还需要高度可用的网络连接的混合应用程序。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-138">This architecture is suitable for hybrid applications that need the higher bandwidth of ExpressRoute, and also require highly available network connectivity.</span></span> 

<span data-ttu-id="f1c4f-139">**优点**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-139">**Benefits**</span></span>

- <span data-ttu-id="f1c4f-140">在 ExpressRoute 线路出现故障时具有高可用性，虽然回退连接位于带宽较低的网络上。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-140">High availability if the ExpressRoute circuit fails, although the fallback connection is on a lower bandwidth network.</span></span>

<span data-ttu-id="f1c4f-141">**挑战**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-141">**Challenges**</span></span>

- <span data-ttu-id="f1c4f-142">配置复杂。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-142">Complex to configure.</span></span> <span data-ttu-id="f1c4f-143">需要设置 VPN 连接和 ExpressRoute 线路。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-143">You need to set up both a VPN connection and an ExpressRoute circuit.</span></span>
- <span data-ttu-id="f1c4f-144">需要冗余硬件（VPN 设备），以及你需要为其付费的冗余 Azure VPN 网关连接。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-144">Requires redundant hardware (VPN appliances), and a redundant Azure VPN Gateway connection for which you pay charges.</span></span>

<span data-ttu-id="f1c4f-145">**参考体系结构**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-145">**Reference architecture**</span></span>

- [<span data-ttu-id="f1c4f-146">使用 ExpressRoute 和 VPN 故障转移的混合网络</span><span class="sxs-lookup"><span data-stu-id="f1c4f-146">Hybrid network with ExpressRoute and VPN failover</span></span>](./expressroute-vpn-failover.md)

## <a name="hub-spoke-network-topology"></a><span data-ttu-id="f1c4f-147">中心辐射型网络拓扑</span><span class="sxs-lookup"><span data-stu-id="f1c4f-147">Hub-spoke network topology</span></span>

<span data-ttu-id="f1c4f-148">可以使用中心辐射型网络拓扑，在隔离工作负荷的同时共享标识和安全性之类的服务。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-148">A hub-spoke network topology is a way to isolate workloads while sharing services such as identity and security.</span></span> <span data-ttu-id="f1c4f-149">中心是 Azure 中的一个虚拟网络 (VNet)，充当到本地网络的连接的中心点。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-149">The hub is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="f1c4f-150">辐射是与中心对等互连的 VNet。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-150">The spokes are VNets that peer with the hub.</span></span> <span data-ttu-id="f1c4f-151">共享服务部署在中心，而各个工作负荷则以辐射的形式部署。</span><span class="sxs-lookup"><span data-stu-id="f1c4f-151">Shared services are deployed in the hub, while individual workloads are deployed as spokes.</span></span>

<span data-ttu-id="f1c4f-152">**参考体系结构**</span><span class="sxs-lookup"><span data-stu-id="f1c4f-152">**Reference architectures**</span></span>

- [<span data-ttu-id="f1c4f-153">中心辐射型拓扑</span><span class="sxs-lookup"><span data-stu-id="f1c4f-153">Hub-spoke topology</span></span>](./hub-spoke.md)
- [<span data-ttu-id="f1c4f-154">带共享服务的中心辐射</span><span class="sxs-lookup"><span data-stu-id="f1c4f-154">Hub-spoke with shared services</span></span>](./shared-services.md)
