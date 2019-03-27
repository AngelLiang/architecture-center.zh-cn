---
title: 在 Azure 中实现中心辐射型网络拓扑
titleSuffix: Azure Reference Architectures
description: 在 Azure 中实现中心辐射型网络拓扑。
author: telmosampaio
ms.date: 10/08/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, networking
ms.openlocfilehash: 4235e5d1bb3b202cff9f7c703f079651982aac59
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246108"
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a><span data-ttu-id="339c8-103">在 Azure 中实现中心辐射型网络拓扑</span><span class="sxs-lookup"><span data-stu-id="339c8-103">Implement a hub-spoke network topology in Azure</span></span>

<span data-ttu-id="339c8-104">此参考体系结构展示了如何在 Azure 中实现中心辐射型拓扑。</span><span class="sxs-lookup"><span data-stu-id="339c8-104">This reference architecture shows how to implement a hub-spoke topology in Azure.</span></span> <span data-ttu-id="339c8-105">*中心*是 Azure 中的一个虚拟网络 (VNet)，充当到本地网络的连接的中心点。</span><span class="sxs-lookup"><span data-stu-id="339c8-105">The *hub* is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="339c8-106">*辐射*是与中心对等互连的 VNet，可用于隔离工作负荷。</span><span class="sxs-lookup"><span data-stu-id="339c8-106">The *spokes* are VNets that peer with the hub, and can be used to isolate workloads.</span></span> <span data-ttu-id="339c8-107">流量通过 ExpressRoute 或 VPN 网关连接在本地数据中心与中心之间流动。</span><span class="sxs-lookup"><span data-stu-id="339c8-107">Traffic flows between the on-premises datacenter and the hub through an ExpressRoute or VPN gateway connection.</span></span> <span data-ttu-id="339c8-108">[**部署此解决方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="339c8-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="339c8-109">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="339c8-109">![[0]][0]</span></span>

<span data-ttu-id="339c8-110">*下载此体系结构的 [Visio 文件][visio-download]*</span><span class="sxs-lookup"><span data-stu-id="339c8-110">*Download a [Visio file][visio-download] of this architecture*</span></span>

<span data-ttu-id="339c8-111">此拓扑的好处包括：</span><span class="sxs-lookup"><span data-stu-id="339c8-111">The benefits of this toplogy include:</span></span>

- <span data-ttu-id="339c8-112">**节省成本** - 通过将可以由多个工作负荷（例如网络虚拟设备 (NVAs) 和 DNS 服务器）共享的服务集中放置在单个位置中。</span><span class="sxs-lookup"><span data-stu-id="339c8-112">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
- <span data-ttu-id="339c8-113">**克服订阅限制** - 通过将不同订阅中的 Vnet 对等互连到中心。</span><span class="sxs-lookup"><span data-stu-id="339c8-113">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
- <span data-ttu-id="339c8-114">**关注点隔离**（在中心 IT（SecOps、InfraOps）与工作负荷 (DevOps) 之间）。</span><span class="sxs-lookup"><span data-stu-id="339c8-114">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="339c8-115">此体系结构的典型用途包括：</span><span class="sxs-lookup"><span data-stu-id="339c8-115">Typical uses for this architecture include:</span></span>

- <span data-ttu-id="339c8-116">在各种环境（例如开发、测试和生产）中部署的需要使用共享服务（例如 DNS、IDS、NTP 或 AD DS）的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="339c8-116">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="339c8-117">共享服务放置在中心 VNet 中，而每个环境都部署到辐射以保持隔离。</span><span class="sxs-lookup"><span data-stu-id="339c8-117">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
- <span data-ttu-id="339c8-118">不需要彼此连接但需要访问共享服务的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="339c8-118">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
- <span data-ttu-id="339c8-119">需要对安全方面进行集中控制（例如作为外围网络的中心内的防火墙），并且需要在每个辐射中对工作负荷进行隔离管理的企业。</span><span class="sxs-lookup"><span data-stu-id="339c8-119">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="339c8-120">体系结构</span><span class="sxs-lookup"><span data-stu-id="339c8-120">Architecture</span></span>

<span data-ttu-id="339c8-121">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="339c8-121">The architecture consists of the following components.</span></span>

- <span data-ttu-id="339c8-122">**本地网络**。</span><span class="sxs-lookup"><span data-stu-id="339c8-122">**On-premises network**.</span></span> <span data-ttu-id="339c8-123">在组织内运行的一个专用局域网。</span><span class="sxs-lookup"><span data-stu-id="339c8-123">A private local-area network running within an organization.</span></span>

- <span data-ttu-id="339c8-124">**VPN 设备**。</span><span class="sxs-lookup"><span data-stu-id="339c8-124">**VPN device**.</span></span> <span data-ttu-id="339c8-125">提供到本地网络的外部连接的设备或服务。</span><span class="sxs-lookup"><span data-stu-id="339c8-125">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="339c8-126">VPN 设备可以是硬件设备，也可以是软件解决方案，例如 Windows Server 2012 中的路由和远程访问服务 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="339c8-126">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="339c8-127">有关受支持 VPN 设备的列表和有关为连接到 Azure 而配置所选 VPN 设备的信息，请参阅 [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance]（关于站点到站点 VPN 网关连接的 VPN 设备）。</span><span class="sxs-lookup"><span data-stu-id="339c8-127">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

- <span data-ttu-id="339c8-128">**VPN 虚拟网络网关或 ExpressRoute 网关**。</span><span class="sxs-lookup"><span data-stu-id="339c8-128">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="339c8-129">虚拟网络网关可以将 VNet 连接到用于本地网络连接的 VPN 设备或 ExpressRoute 线路。</span><span class="sxs-lookup"><span data-stu-id="339c8-129">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="339c8-130">有关详细信息，请参阅[将本地网络连接到 Microsoft Azure 虚拟网络][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="339c8-130">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="339c8-131">此参考体系结构的部署脚本使用 VPN 网关进行连接，使用 Azure 中的 VNet 来模拟本地网络。</span><span class="sxs-lookup"><span data-stu-id="339c8-131">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

- <span data-ttu-id="339c8-132">**中心 VNet**。</span><span class="sxs-lookup"><span data-stu-id="339c8-132">**Hub VNet**.</span></span> <span data-ttu-id="339c8-133">用作中心辐射型拓扑中的中心的 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="339c8-133">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="339c8-134">中心是到本地网络的连接的中心点，它还托管着可以由辐射 VNet 中托管的各种工作负荷使用的服务。</span><span class="sxs-lookup"><span data-stu-id="339c8-134">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

- <span data-ttu-id="339c8-135">**网关子网**。</span><span class="sxs-lookup"><span data-stu-id="339c8-135">**Gateway subnet**.</span></span> <span data-ttu-id="339c8-136">虚拟网络网关保留在同一子网中。</span><span class="sxs-lookup"><span data-stu-id="339c8-136">The virtual network gateways are held in the same subnet.</span></span>

- <span data-ttu-id="339c8-137">**辐射 VNet**。</span><span class="sxs-lookup"><span data-stu-id="339c8-137">**Spoke VNets**.</span></span> <span data-ttu-id="339c8-138">用作中心辐射型拓扑中的辐射的一个或多个 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="339c8-138">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="339c8-139">辐射可以用来隔离其自己的 VNet 中的工作负荷，独立于其他辐射进行管理。</span><span class="sxs-lookup"><span data-stu-id="339c8-139">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="339c8-140">每个工作负荷可以包括多个层，并具有通过 Azure 负载均衡器连接的多个子网。</span><span class="sxs-lookup"><span data-stu-id="339c8-140">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="339c8-141">有关应用程序基础结构的详细信息，请参阅[运行 Windows VM 工作负荷][windows-vm-ra]和[运行 Linux VM 工作负荷][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="339c8-141">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

- <span data-ttu-id="339c8-142">**VNet 对等互连**。</span><span class="sxs-lookup"><span data-stu-id="339c8-142">**VNet peering**.</span></span> <span data-ttu-id="339c8-143">可以使用[对等互连连接][vnet-peering]来连接两个 VNet。</span><span class="sxs-lookup"><span data-stu-id="339c8-143">Two VNets can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="339c8-144">对等互连连接是 VNet 之间的不可传递低延迟连接。</span><span class="sxs-lookup"><span data-stu-id="339c8-144">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="339c8-145">进行对等互连后，VNet 可使用 Azure 主干交换流量，不需要使用路由器。</span><span class="sxs-lookup"><span data-stu-id="339c8-145">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="339c8-146">在中心辐射型网络拓扑中，将使用 VNet 对等互连来将中心连接到每个辐射。</span><span class="sxs-lookup"><span data-stu-id="339c8-146">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span> <span data-ttu-id="339c8-147">可在相同区域或不同区域中的虚拟网络之间建立对等互连。</span><span class="sxs-lookup"><span data-stu-id="339c8-147">You can peer virtual networks in the same region, or different regions.</span></span> <span data-ttu-id="339c8-148">有关详细信息，请参阅[要求和约束][vnet-peering-requirements]。</span><span class="sxs-lookup"><span data-stu-id="339c8-148">For more information, see [Requirements and constraints][vnet-peering-requirements].</span></span>

> [!NOTE]
> <span data-ttu-id="339c8-149">文本仅涵盖了[资源管理器](/azure/azure-resource-manager/resource-group-overview)部署，但也可以将经典 VNet 连接到同一订阅中的资源管理器 VNet。</span><span class="sxs-lookup"><span data-stu-id="339c8-149">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="339c8-150">这样，辐射将可以托管经典部署，并且仍然可以从中心内共享的各种服务受益。</span><span class="sxs-lookup"><span data-stu-id="339c8-150">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="339c8-151">建议</span><span class="sxs-lookup"><span data-stu-id="339c8-151">Recommendations</span></span>

<span data-ttu-id="339c8-152">以下建议适用于大多数方案。</span><span class="sxs-lookup"><span data-stu-id="339c8-152">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="339c8-153">除非有优先于这些建议的特定要求，否则请遵循这些建议。</span><span class="sxs-lookup"><span data-stu-id="339c8-153">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="339c8-154">资源组</span><span class="sxs-lookup"><span data-stu-id="339c8-154">Resource groups</span></span>

<span data-ttu-id="339c8-155">中心 VNet 和每个辐射 VNet 可以在不同的资源组中实现，甚至可以在不同的订阅中实现。</span><span class="sxs-lookup"><span data-stu-id="339c8-155">The hub VNet, and each spoke VNet, can be implemented in different resource groups, and even different subscriptions.</span></span> <span data-ttu-id="339c8-156">如果对等虚拟网络位于不同的订阅中，两个订阅可关联到同一个或不同的 Azure Active Directory 租户。</span><span class="sxs-lookup"><span data-stu-id="339c8-156">When you peer virtual networks in different subscriptions, both subscriptions can be associated to the same or different Azure Active Directory tenant.</span></span> <span data-ttu-id="339c8-157">这样，可以对各个工作负荷进行非集中管理，同时在中心 VNet 内维护共享服务。</span><span class="sxs-lookup"><span data-stu-id="339c8-157">This allows for a decentralized management of each workload, while sharing services maintained in the hub VNet.</span></span>

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="339c8-158">VNet 和 GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="339c8-158">VNet and GatewaySubnet</span></span>

<span data-ttu-id="339c8-159">创建一个名为 *GatewaySubnet* 的子网，使其地址范围为 /27。</span><span class="sxs-lookup"><span data-stu-id="339c8-159">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="339c8-160">此子网是虚拟网络网关所必需的。</span><span class="sxs-lookup"><span data-stu-id="339c8-160">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="339c8-161">向此子网分配 32 个地址将有助于防止将来达到网关大小限制。</span><span class="sxs-lookup"><span data-stu-id="339c8-161">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span>

<span data-ttu-id="339c8-162">有关设置网关的详细信息，请根据你的连接类型参阅以下参考体系结构：</span><span class="sxs-lookup"><span data-stu-id="339c8-162">For more information about setting up the gateway, see the following reference architectures, depending on your connection type:</span></span>

- <span data-ttu-id="339c8-163">[使用 ExpressRoute 的混合网络][guidance-expressroute]</span><span class="sxs-lookup"><span data-stu-id="339c8-163">[Hybrid network using ExpressRoute][guidance-expressroute]</span></span>
- <span data-ttu-id="339c8-164">[使用 VPN 网关的混合网络][guidance-vpn]</span><span class="sxs-lookup"><span data-stu-id="339c8-164">[Hybrid network using a VPN gateway][guidance-vpn]</span></span>

<span data-ttu-id="339c8-165">要实现更高的可用性，可以将 ExpressRoute 外加 VPN 用于故障转移。</span><span class="sxs-lookup"><span data-stu-id="339c8-165">For higher availability, you can use ExpressRoute plus a VPN for failover.</span></span> <span data-ttu-id="339c8-166">请参阅[将本地网络连接到 Azure 并将 ExpressRoute 和 VPN 用于故障转移][hybrid-ha]。</span><span class="sxs-lookup"><span data-stu-id="339c8-166">See [Connect an on-premises network to Azure using ExpressRoute with VPN failover][hybrid-ha].</span></span>

<span data-ttu-id="339c8-167">如果不需要与本地网络的连接，还可以在不使用网关的情况下使用中心辐射型拓扑。</span><span class="sxs-lookup"><span data-stu-id="339c8-167">A hub-spoke topology can also be used without a gateway, if you don't need connectivity with your on-premises network.</span></span>

### <a name="vnet-peering"></a><span data-ttu-id="339c8-168">VNet 对等互连</span><span class="sxs-lookup"><span data-stu-id="339c8-168">VNet peering</span></span>

<span data-ttu-id="339c8-169">VNet 对等互连是两个 VNet 之间的不可传递关系。</span><span class="sxs-lookup"><span data-stu-id="339c8-169">VNet peering is a non-transitive relationship between two VNets.</span></span> <span data-ttu-id="339c8-170">如果需要将各个辐射彼此连接，请考虑在这些辐射之间添加一个单独的对等互连连接。</span><span class="sxs-lookup"><span data-stu-id="339c8-170">If you require spokes to connect to each other, consider adding a separate peering connection between those spokes.</span></span>

<span data-ttu-id="339c8-171">不过，如果有多个辐射需要彼此连接，则你可能会由于[每个 VNet 的 VNet 对等互连数限制][vnet-peering-limit]而很快耗尽可能的对等互连连接。</span><span class="sxs-lookup"><span data-stu-id="339c8-171">However, if you have several spokes that need to connect with each other, you will run out of possible peering connections very quickly due to the [limitation on number of VNets peerings per VNet][vnet-peering-limit].</span></span> <span data-ttu-id="339c8-172">在这种情况下，请考虑使用用户定义的路由 (UDR) 强制将目的地为辐射的流量发送到在中心 VNet 中充当路由器的 NVA。</span><span class="sxs-lookup"><span data-stu-id="339c8-172">In this scenario, consider using user defined routes (UDRs) to force traffic destined to a spoke to be sent to an NVA acting as a router at the hub VNet.</span></span> <span data-ttu-id="339c8-173">这将允许各个辐射彼此连接。</span><span class="sxs-lookup"><span data-stu-id="339c8-173">This will allow the spokes to connect to each other.</span></span>

<span data-ttu-id="339c8-174">还可以将辐射配置为使用中心 VNet 网关与远程网络进行通信。</span><span class="sxs-lookup"><span data-stu-id="339c8-174">You can also configure spokes to use the hub VNet gateway to communicate with remote networks.</span></span> <span data-ttu-id="339c8-175">若要允许网关流量从辐射流动到中心，以及允许连接到远程网络，必须：</span><span class="sxs-lookup"><span data-stu-id="339c8-175">To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:</span></span>

- <span data-ttu-id="339c8-176">在中心内配置 VNet 对等互连连接以**允许网关中转**。</span><span class="sxs-lookup"><span data-stu-id="339c8-176">Configure the VNet peering connection in the hub to **allow gateway transit**.</span></span>
- <span data-ttu-id="339c8-177">在每个辐射中配置 VNet 对等互连连接以**使用远程网关**。</span><span class="sxs-lookup"><span data-stu-id="339c8-177">Configure the VNet peering connection in each spoke to **use remote gateways**.</span></span>
- <span data-ttu-id="339c8-178">配置所有 VNet 对等互连连接以**允许转发的流量**。</span><span class="sxs-lookup"><span data-stu-id="339c8-178">Configure all VNet peering connections to **allow forwarded traffic**.</span></span>

## <a name="considerations"></a><span data-ttu-id="339c8-179">注意事项</span><span class="sxs-lookup"><span data-stu-id="339c8-179">Considerations</span></span>

### <a name="spoke-connectivity"></a><span data-ttu-id="339c8-180">辐射连接</span><span class="sxs-lookup"><span data-stu-id="339c8-180">Spoke connectivity</span></span>

<span data-ttu-id="339c8-181">如果辐射之间需要存在连接，请考虑在中心内实现一个用于路由的 NVA，并在辐射中使用 UDR 将流量转发到中心。</span><span class="sxs-lookup"><span data-stu-id="339c8-181">If you require connectivity between spokes, consider implementing an NVA for routing in the hub, and using UDRs in the spoke to forward traffic to the hub.</span></span>

<span data-ttu-id="339c8-182">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="339c8-182">![[2]][2]</span></span>

<span data-ttu-id="339c8-183">在这种情况下，必须配置对等互连连接以**允许转发的流量**。</span><span class="sxs-lookup"><span data-stu-id="339c8-183">In this scenario, you must configure the peering connections to **allow forwarded traffic**.</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="339c8-184">克服 VNet 对等互连限制</span><span class="sxs-lookup"><span data-stu-id="339c8-184">Overcoming VNet peering limits</span></span>

<span data-ttu-id="339c8-185">请务必考虑 Azure 中[每个 VNet 的 VNet 对等互连数限制][vnet-peering-limit]。</span><span class="sxs-lookup"><span data-stu-id="339c8-185">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="339c8-186">如果你确定所需的辐射多于限制将允许的数量，请考虑创建一个中心-辐射-中心-辐射型拓扑，其中的第一级辐射还充当中心。</span><span class="sxs-lookup"><span data-stu-id="339c8-186">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="339c8-187">下图显示了此方式。</span><span class="sxs-lookup"><span data-stu-id="339c8-187">The following diagram shows this approach.</span></span>

<span data-ttu-id="339c8-188">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="339c8-188">![[3]][3]</span></span>

<span data-ttu-id="339c8-189">另外，请考虑要在中心内共享哪些服务，以确保中心能够针对大量辐射进行缩放。</span><span class="sxs-lookup"><span data-stu-id="339c8-189">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="339c8-190">例如，如果中心提供防火墙服务，则在添加多个辐射时请考虑防火墙解决方案的带宽限制。</span><span class="sxs-lookup"><span data-stu-id="339c8-190">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="339c8-191">你可能希望将这些共享服务中的某一些移动到二级中心内。</span><span class="sxs-lookup"><span data-stu-id="339c8-191">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="339c8-192">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="339c8-192">Deploy the solution</span></span>

<span data-ttu-id="339c8-193">[GitHub][ref-arch-repo] 上提供了此体系结构的部署。</span><span class="sxs-lookup"><span data-stu-id="339c8-193">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="339c8-194">该部署使用每个 VNet 中的 VM 来测试连接。</span><span class="sxs-lookup"><span data-stu-id="339c8-194">It uses VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="339c8-195">没有实际服务托管在 **中心 VNet** 内的**共享服务**中。</span><span class="sxs-lookup"><span data-stu-id="339c8-195">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

<span data-ttu-id="339c8-196">该部署在订阅中创建以下资源组：</span><span class="sxs-lookup"><span data-stu-id="339c8-196">The deployment creates the following resource groups in your subscription:</span></span>

- <span data-ttu-id="339c8-197">hub-nva-rg</span><span class="sxs-lookup"><span data-stu-id="339c8-197">hub-nva-rg</span></span>
- <span data-ttu-id="339c8-198">hub-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="339c8-198">hub-vnet-rg</span></span>
- <span data-ttu-id="339c8-199">onprem-jb-rg</span><span class="sxs-lookup"><span data-stu-id="339c8-199">onprem-jb-rg</span></span>
- <span data-ttu-id="339c8-200">onprem-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="339c8-200">onprem-vnet-rg</span></span>
- <span data-ttu-id="339c8-201">spoke1-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="339c8-201">spoke1-vnet-rg</span></span>
- <span data-ttu-id="339c8-202">spoke2-vent-rg</span><span class="sxs-lookup"><span data-stu-id="339c8-202">spoke2-vent-rg</span></span>

<span data-ttu-id="339c8-203">模板参数文件将引用这些名称，因此，如果更改了这些名称，请相应地更新参数文件。</span><span class="sxs-lookup"><span data-stu-id="339c8-203">The template parameter files refer to these names, so if you change them, update the parameter files to match.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="339c8-204">先决条件</span><span class="sxs-lookup"><span data-stu-id="339c8-204">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-simulated-on-premises-datacenter"></a><span data-ttu-id="339c8-205">部署模拟的本地数据中心</span><span class="sxs-lookup"><span data-stu-id="339c8-205">Deploy the simulated on-premises datacenter</span></span>

<span data-ttu-id="339c8-206">若要将模拟的本地数据中心部署为 Azure VNet，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="339c8-206">To deploy the simulated on-premises datacenter as an Azure VNet, follow these steps:</span></span>

1. <span data-ttu-id="339c8-207">导航到参考体系结构存储库的 `hybrid-networking/hub-spoke` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="339c8-207">Navigate to the `hybrid-networking/hub-spoke` folder of the reference architectures repository.</span></span>

2. <span data-ttu-id="339c8-208">打开 `onprem.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="339c8-208">Open the `onprem.json` file.</span></span> <span data-ttu-id="339c8-209">替换 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="339c8-209">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```json
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

3. <span data-ttu-id="339c8-210">（可选）对于 Linux 部署，请将 `osType` 设置为 `Linux`。</span><span class="sxs-lookup"><span data-stu-id="339c8-210">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

4. <span data-ttu-id="339c8-211">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="339c8-211">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g onprem-vnet-rg -l <location> -p onprem.json --deploy
    ```

5. <span data-ttu-id="339c8-212">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="339c8-212">Wait for the deployment to finish.</span></span> <span data-ttu-id="339c8-213">此部署创建虚拟网络、虚拟机和 VPN 网关。</span><span class="sxs-lookup"><span data-stu-id="339c8-213">This deployment creates a virtual network, a virtual machine, and a VPN gateway.</span></span> <span data-ttu-id="339c8-214">创建 VPN 网关可能需要花费大约 40 分钟。</span><span class="sxs-lookup"><span data-stu-id="339c8-214">It can take about 40 minutes to create the VPN gateway.</span></span>

### <a name="deploy-the-hub-vnet"></a><span data-ttu-id="339c8-215">部署中心 VNet</span><span class="sxs-lookup"><span data-stu-id="339c8-215">Deploy the hub VNet</span></span>

<span data-ttu-id="339c8-216">若要部署中心 VNet，请执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="339c8-216">To deploy the hub VNet, perform the following steps.</span></span>

1. <span data-ttu-id="339c8-217">打开 `hub-vnet.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="339c8-217">Open the `hub-vnet.json` file.</span></span> <span data-ttu-id="339c8-218">替换 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="339c8-218">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```json
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="339c8-219">（可选）对于 Linux 部署，请将 `osType` 设置为 `Linux`。</span><span class="sxs-lookup"><span data-stu-id="339c8-219">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

3. <span data-ttu-id="339c8-220">找到 `sharedKey` 的两个实例，并输入 VPN 连接的共享密钥。</span><span class="sxs-lookup"><span data-stu-id="339c8-220">Find both instances of `sharedKey` and enter a shared key for the VPN connection.</span></span> <span data-ttu-id="339c8-221">值必须匹配。</span><span class="sxs-lookup"><span data-stu-id="339c8-221">The values must match.</span></span>

    ```json
    "sharedKey": "",
    ```

4. <span data-ttu-id="339c8-222">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="339c8-222">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet.json --deploy
    ```

5. <span data-ttu-id="339c8-223">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="339c8-223">Wait for the deployment to finish.</span></span> <span data-ttu-id="339c8-224">此部署创建虚拟网络、虚拟机、VPN 网关和网关连接。</span><span class="sxs-lookup"><span data-stu-id="339c8-224">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway.</span></span>  <span data-ttu-id="339c8-225">创建 VPN 网关可能需要花费大约 40 分钟。</span><span class="sxs-lookup"><span data-stu-id="339c8-225">It can take about 40 minutes to create the VPN gateway.</span></span>

### <a name="test-connectivity-to-the-hub-vnet-mdash-windows-deployment"></a><span data-ttu-id="339c8-226">测试到中心 VNet 的连接 &mdash; Windows 部署</span><span class="sxs-lookup"><span data-stu-id="339c8-226">Test connectivity to the hub VNet &mdash; Windows deployment</span></span>

<span data-ttu-id="339c8-227">若要使用 Windows VM 测试从模拟的本地环境到中心 VNet 的连接，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="339c8-227">To test conectivity from the simulated on-premises environment to the hub VNet using Windows VMs, follow these steps:</span></span>

1. <span data-ttu-id="339c8-228">使用 Azure 门户在 `onprem-jb-rg` 资源组中找到名为 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="339c8-228">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="339c8-229">单击 `Connect` 来与 VM 建立远程桌面会话。</span><span class="sxs-lookup"><span data-stu-id="339c8-229">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="339c8-230">使用 `onprem.json` 参数文件中指定的密码。</span><span class="sxs-lookup"><span data-stu-id="339c8-230">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="339c8-231">在 VM 中打开 PowerShell 控制台，使用 `Test-NetConnection` cmdlet 验证能否连接到中心 VNet 中的 Jumpbox VM。</span><span class="sxs-lookup"><span data-stu-id="339c8-231">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VM in the hub VNet.</span></span>

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```

<span data-ttu-id="339c8-232">输出应如下所示：</span><span class="sxs-lookup"><span data-stu-id="339c8-232">The output should look similar to the following:</span></span>

```powershell
ComputerName     : 10.0.0.68
RemoteAddress    : 10.0.0.68
RemotePort       : 3389
InterfaceAlias   : Ethernet 2
SourceAddress    : 192.168.1.000
TcpTestSucceeded : True
```

> [!NOTE]
> <span data-ttu-id="339c8-233">默认情况下，Windows Server VM 不允许 Azure 中的 ICMP 响应。</span><span class="sxs-lookup"><span data-stu-id="339c8-233">By default, Windows Server VMs do not allow ICMP responses in Azure.</span></span> <span data-ttu-id="339c8-234">若要使用 `ping` 来测试连接，需在“Windows 高级防火墙”中为每个 VM 启用 ICMP 流量。</span><span class="sxs-lookup"><span data-stu-id="339c8-234">If you want to use `ping` to test connectivity, you need to enable ICMP traffic in the Windows Advanced Firewall for each VM.</span></span>

### <a name="test-connectivity-to-the-hub-vnet-mdash-linux-deployment"></a><span data-ttu-id="339c8-235">测试到中心 VNet 的连接 &mdash; Linux 部署</span><span class="sxs-lookup"><span data-stu-id="339c8-235">Test connectivity to the hub VNet &mdash; Linux deployment</span></span>

<span data-ttu-id="339c8-236">若要使用 Linux VM 测试从模拟的本地环境到中心 VNet 的连接，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="339c8-236">To test conectivity from the simulated on-premises environment to the hub VNet using Linux VMs, follow these steps:</span></span>

1. <span data-ttu-id="339c8-237">使用 Azure 门户在 `onprem-jb-rg` 资源组中找到名为 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="339c8-237">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="339c8-238">单击 `Connect`，并复制门户中显示的 `ssh` 命令。</span><span class="sxs-lookup"><span data-stu-id="339c8-238">Click `Connect` and copy the `ssh` command shown in the portal.</span></span>

3. <span data-ttu-id="339c8-239">在 Linux 提示符下，运行 `ssh` 连接到模拟本地环境。</span><span class="sxs-lookup"><span data-stu-id="339c8-239">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment.</span></span> <span data-ttu-id="339c8-240">使用 `onprem.json` 参数文件中指定的密码。</span><span class="sxs-lookup"><span data-stu-id="339c8-240">Use the password that you specified in the `onprem.json` parameter file.</span></span>

4. <span data-ttu-id="339c8-241">使用 `ping` 命令测试与中心 VNet 中 Jumpbox VM 连接。</span><span class="sxs-lookup"><span data-stu-id="339c8-241">Use the `ping` command to test connectivity to the jumpbox VM in the hub VNet:</span></span>

   ```shell
   ping 10.0.0.68
   ```

### <a name="deploy-the-spoke-vnets"></a><span data-ttu-id="339c8-242">部署辐射 VNet</span><span class="sxs-lookup"><span data-stu-id="339c8-242">Deploy the spoke VNets</span></span>

<span data-ttu-id="339c8-243">若要部署辐射 VNet，请执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="339c8-243">To deploy the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="339c8-244">打开 `spoke1.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="339c8-244">Open the `spoke1.json` file.</span></span> <span data-ttu-id="339c8-245">替换 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="339c8-245">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```json
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="339c8-246">（可选）对于 Linux 部署，请将 `osType` 设置为 `Linux`。</span><span class="sxs-lookup"><span data-stu-id="339c8-246">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

3. <span data-ttu-id="339c8-247">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="339c8-247">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg -l <location> -p spoke1.json --deploy
   ```

4. <span data-ttu-id="339c8-248">针对 `spoke2.json` 文件重复步骤 1-2。</span><span class="sxs-lookup"><span data-stu-id="339c8-248">Repeat steps 1-2 for the `spoke2.json` file.</span></span>

5. <span data-ttu-id="339c8-249">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="339c8-249">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg -l <location> -p spoke2.json --deploy
   ```

6. <span data-ttu-id="339c8-250">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="339c8-250">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet-peering.json --deploy
   ```

### <a name="test-connectivity-to-the-spoke-vnets-mdash-windows-deployment"></a><span data-ttu-id="339c8-251">测试到分支 VNet 的连接 &mdash; Windows 部署</span><span class="sxs-lookup"><span data-stu-id="339c8-251">Test connectivity to the spoke VNets &mdash; Windows deployment</span></span>

<span data-ttu-id="339c8-252">若要使用 Windows VM 测试从模拟的本地环境到分支 VNet 的连接，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="339c8-252">To test conectivity from the simulated on-premises environment to the spoke VNets using Windows VMs, perform the following steps:</span></span>

1. <span data-ttu-id="339c8-253">使用 Azure 门户在 `onprem-jb-rg` 资源组中找到名为 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="339c8-253">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="339c8-254">单击 `Connect` 来与 VM 建立远程桌面会话。</span><span class="sxs-lookup"><span data-stu-id="339c8-254">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="339c8-255">使用 `onprem.json` 参数文件中指定的密码。</span><span class="sxs-lookup"><span data-stu-id="339c8-255">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="339c8-256">在 VM 中打开 PowerShell 控制台，使用 `Test-NetConnection` cmdlet 验证能否连接到辐射 VNet 中的 Jumpbox VM。</span><span class="sxs-lookup"><span data-stu-id="339c8-256">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VMs in the spoke VNets.</span></span>

   ```powershell
   Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
   Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
   ```

### <a name="test-connectivity-to-the-spoke-vnets-mdash-linux-deployment"></a><span data-ttu-id="339c8-257">测试到分支 VNet 的连接 &mdash; Linux 部署</span><span class="sxs-lookup"><span data-stu-id="339c8-257">Test connectivity to the spoke VNets &mdash; Linux deployment</span></span>

<span data-ttu-id="339c8-258">若要使用 Linux VM 测试从模拟的本地环境到辐射 VNet 的连接，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="339c8-258">To test conectivity from the simulated on-premises environment to the spoke VNets using Linux VMs, perform the following steps:</span></span>

1. <span data-ttu-id="339c8-259">使用 Azure 门户在 `onprem-jb-rg` 资源组中找到名为 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="339c8-259">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="339c8-260">单击 `Connect`，并复制门户中显示的 `ssh` 命令。</span><span class="sxs-lookup"><span data-stu-id="339c8-260">Click `Connect` and copy the `ssh` command shown in the portal.</span></span>

3. <span data-ttu-id="339c8-261">在 Linux 提示符下，运行 `ssh` 连接到模拟本地环境。</span><span class="sxs-lookup"><span data-stu-id="339c8-261">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment.</span></span> <span data-ttu-id="339c8-262">使用 `onprem.json` 参数文件中指定的密码。</span><span class="sxs-lookup"><span data-stu-id="339c8-262">Use the password that you specified in the `onprem.json` parameter file.</span></span>

4. <span data-ttu-id="339c8-263">使用 `ping` 命令测试与每个辐射中 Jumpbox VM 的连接。</span><span class="sxs-lookup"><span data-stu-id="339c8-263">Use the `ping` command to test connectivity to the jumpbox VMs in each spoke:</span></span>

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

### <a name="add-connectivity-between-spokes"></a><span data-ttu-id="339c8-264">添加辐射之间的连接</span><span class="sxs-lookup"><span data-stu-id="339c8-264">Add connectivity between spokes</span></span>

<span data-ttu-id="339c8-265">此步骤是可选的。</span><span class="sxs-lookup"><span data-stu-id="339c8-265">This step is optional.</span></span> <span data-ttu-id="339c8-266">如果需要允许辐射 VNet 互相进行连接，则必须使用网络虚拟设备 (NVA) 作为中心 VNet 中的路由器，并在尝试连接到另一辐射 VNet 时强制流量从辐射 VNet 流向路由器。</span><span class="sxs-lookup"><span data-stu-id="339c8-266">If you want to allow spokes to connect to each other, you must use a network virtual appliance (NVA) as a router in the hub VNet, and force traffic from spokes to the router when trying to connect to another spoke.</span></span> <span data-ttu-id="339c8-267">若要将基本的示例 NVA 部署为单个 VM 并部署用户定义的路由 (UDR) 以允许这两个辐射 VNet 建立连接，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="339c8-267">To deploy a basic sample NVA as a single VM, along with user-defined routes (UDRs) to allow the two spoke VNets to connect, perform the following steps:</span></span>

1. <span data-ttu-id="339c8-268">打开 `hub-nva.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="339c8-268">Open the `hub-nva.json` file.</span></span> <span data-ttu-id="339c8-269">替换 `adminUsername` 和 `adminPassword` 的值。</span><span class="sxs-lookup"><span data-stu-id="339c8-269">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```json
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="339c8-270">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="339c8-270">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg -l <location> -p hub-nva.json --deploy
   ```

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vnet-peering-requirements]: /azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures

[0]: ./images/hub-spoke.png "Azure 中的中心辐射型拓扑"
[1]: ./images/hub-spoke-gateway-routing.svg "Azure 中的具有可传递路由的中心辐射型拓扑"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Azure 中的具有使用 NVA 的可传递路由的中心辐射型拓扑"
[3]: ./images/hub-spokehub-spoke.svg "Azure 中的中心-辐射-中心-辐射型拓扑"
