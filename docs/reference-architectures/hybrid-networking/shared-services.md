---
title: 实现中心辐射型网络拓扑
titleSuffix: Azure Reference Architectures
description: 在 Azure 中使用共享服务实现中心辐射型网络拓扑。
author: telmosampaio
ms.date: 10/09/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, networking
ms.openlocfilehash: dd7632c3a84f6a0cee5d8b35e6a943ab8c52caf8
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54488301"
---
# <a name="implement-a-hub-spoke-network-topology-with-shared-services-in-azure"></a><span data-ttu-id="bcbb4-103">在 Azure 中使用共享服务实现中心辐射型网络拓扑</span><span class="sxs-lookup"><span data-stu-id="bcbb4-103">Implement a hub-spoke network topology with shared services in Azure</span></span>

<span data-ttu-id="bcbb4-104">此参考体系结构在[中心辐射型][guidance-hub-spoke]参考体系结构的基础上生成，在中心包括了可供所有辐射 VNet 使用的共享服务。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-104">This reference architecture builds on the [hub-spoke][guidance-hub-spoke] reference architecture to include shared services in the hub that can be consumed by all spokes.</span></span> <span data-ttu-id="bcbb4-105">首先需要共享第一批服务，即标识和安全性，然后才能将数据中心迁移到云并生成[虚拟数据中心]。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-105">As a first step toward migrating a datacenter to the cloud, and building a [virtual datacenter], the first services you need to share are identity and security.</span></span> <span data-ttu-id="bcbb4-106">此参考体系结构显示了如何将 Active Directory 服务从本地数据中心扩展到 Azure，以及如何在中心辐射型拓扑中添加可以充当防火墙的网络虚拟设备 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-106">This reference architecture shows you how to extend your Active Directory services from your on-premises datacenter to Azure, and how to add a network virtual appliance (NVA) that can act as a firewall, in a hub-spoke topology.</span></span>  <span data-ttu-id="bcbb4-107">[**部署此解决方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-107">[**Deploy this solution**](#deploy-the-solution).</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb4-108">还可以使用 [Azure 防火墙](/azure/firewall/)（基于云的网络安全服务）实现此方案。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-108">This scenario can also be accomplished using [Azure Firewall](/azure/firewall/), a cloud-based network security service.</span></span>

![Azure 中的共享服务拓扑](./images/shared-services.png)

<span data-ttu-id="bcbb4-110">*下载此体系结构的 [Visio 文件][visio-download]*</span><span class="sxs-lookup"><span data-stu-id="bcbb4-110">*Download a [Visio file][visio-download] of this architecture*</span></span>

<span data-ttu-id="bcbb4-111">此拓扑的好处包括：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-111">The benefits of this topology include:</span></span>

- <span data-ttu-id="bcbb4-112">**节省成本** - 通过将可以由多个工作负荷（例如网络虚拟设备 (NVAs) 和 DNS 服务器）共享的服务集中放置在单个位置中。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-112">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
- <span data-ttu-id="bcbb4-113">**克服订阅限制** - 通过将不同订阅中的 Vnet 对等互连到中心。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-113">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
- <span data-ttu-id="bcbb4-114">**关注点隔离**（在中心 IT（SecOps、InfraOps）与工作负荷 (DevOps) 之间）。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-114">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="bcbb4-115">此体系结构的典型用途包括：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-115">Typical uses for this architecture include:</span></span>

- <span data-ttu-id="bcbb4-116">在各种环境（例如开发、测试和生产）中部署的需要使用共享服务（例如 DNS、IDS、NTP 或 AD DS）的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-116">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="bcbb4-117">共享服务放置在中心 VNet 中，而每个环境都部署到辐射以保持隔离。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-117">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
- <span data-ttu-id="bcbb4-118">不需要彼此连接但需要访问共享服务的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-118">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
- <span data-ttu-id="bcbb4-119">需要对安全方面进行集中控制（例如作为外围网络的中心内的防火墙），并且需要在每个辐射中对工作负荷进行隔离管理的企业。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-119">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="bcbb4-120">体系结构</span><span class="sxs-lookup"><span data-stu-id="bcbb4-120">Architecture</span></span>

<span data-ttu-id="bcbb4-121">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-121">The architecture consists of the following components.</span></span>

- <span data-ttu-id="bcbb4-122">**本地网络**。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-122">**On-premises network**.</span></span> <span data-ttu-id="bcbb4-123">在组织内运行的一个专用局域网。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-123">A private local-area network running within an organization.</span></span>

- <span data-ttu-id="bcbb4-124">**VPN 设备**。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-124">**VPN device**.</span></span> <span data-ttu-id="bcbb4-125">提供到本地网络的外部连接的设备或服务。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-125">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="bcbb4-126">VPN 设备可以是硬件设备，也可以是软件解决方案，例如 Windows Server 2012 中的路由和远程访问服务 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-126">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="bcbb4-127">有关受支持 VPN 设备的列表和有关为连接到 Azure 而配置所选 VPN 设备的信息，请参阅 [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance]（关于站点到站点 VPN 网关连接的 VPN 设备）。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-127">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

- <span data-ttu-id="bcbb4-128">**VPN 虚拟网络网关或 ExpressRoute 网关**。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-128">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="bcbb4-129">虚拟网络网关可以将 VNet 连接到用于本地网络连接的 VPN 设备或 ExpressRoute 线路。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-129">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="bcbb4-130">有关详细信息，请参阅[将本地网络连接到 Microsoft Azure 虚拟网络][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-130">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb4-131">此参考体系结构的部署脚本使用 VPN 网关进行连接，使用 Azure 中的 VNet 来模拟本地网络。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-131">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

- <span data-ttu-id="bcbb4-132">**中心 VNet**。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-132">**Hub VNet**.</span></span> <span data-ttu-id="bcbb4-133">用作中心辐射型拓扑中的中心的 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-133">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="bcbb4-134">中心是到本地网络的连接的中心点，它还托管着可以由辐射 VNet 中托管的各种工作负荷使用的服务。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-134">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

- <span data-ttu-id="bcbb4-135">**网关子网**。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-135">**Gateway subnet**.</span></span> <span data-ttu-id="bcbb4-136">各个虚拟网络网关都放在同一子网中。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-136">The virtual network gateways are held in the same subnet.</span></span>

- <span data-ttu-id="bcbb4-137">**共享服务子网**。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-137">**Shared services subnet**.</span></span> <span data-ttu-id="bcbb4-138">中心 VNet 中的子网，用来托管可以在所有辐射之间共享的服务，例如 DNS 或 AD DS。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-138">A subnet in the hub VNet used to host services that can be shared among all spokes, such as DNS or AD DS.</span></span>

- <span data-ttu-id="bcbb4-139">**外围网络子网**。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-139">**DMZ subnet**.</span></span> <span data-ttu-id="bcbb4-140">中心 VNet 中的子网，用于托管的 NVA 可以充当安全设备，例如防火墙。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-140">A subnet in the hub VNet used to host NVAs that can act as security appliances, such as firewalls.</span></span>

- <span data-ttu-id="bcbb4-141">**辐射 VNet**。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-141">**Spoke VNets**.</span></span> <span data-ttu-id="bcbb4-142">用作中心辐射型拓扑中的辐射的一个或多个 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-142">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="bcbb4-143">辐射可以用来隔离其自己的 VNet 中的工作负荷，独立于其他辐射进行管理。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-143">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="bcbb4-144">每个工作负荷可以包括多个层，并具有通过 Azure 负载均衡器连接的多个子网。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-144">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="bcbb4-145">有关应用程序基础结构的详细信息，请参阅[运行 Windows VM 工作负荷][windows-vm-ra]和[运行 Linux VM 工作负荷][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-145">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

- <span data-ttu-id="bcbb4-146">**VNet 对等互连**。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-146">**VNet peering**.</span></span> <span data-ttu-id="bcbb4-147">可以使用[对等互连连接][vnet-peering]来连接同一 Azure 区域中的两个 VNet。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-147">Two VNets in the same Azure region can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="bcbb4-148">对等互连连接是 VNet 之间的不可传递低延迟连接。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-148">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="bcbb4-149">进行对等互连后，VNet 可使用 Azure 主干交换流量，不需要使用路由器。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-149">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="bcbb4-150">在中心辐射型网络拓扑中，将使用 VNet 对等互连来将中心连接到每个辐射。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-150">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb4-151">文本仅涵盖了[资源管理器](/azure/azure-resource-manager/resource-group-overview)部署，但也可以将经典 VNet 连接到同一订阅中的资源管理器 VNet。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-151">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="bcbb4-152">这样，辐射将可以托管经典部署，并且仍然可以从中心内共享的各种服务受益。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-152">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="bcbb4-153">建议</span><span class="sxs-lookup"><span data-stu-id="bcbb4-153">Recommendations</span></span>

<span data-ttu-id="bcbb4-154">针对[中心辐射型][guidance-hub-spoke]参考体系结构的所有建议也适用于共享服务参考体系结构。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-154">All the recommendations for the [hub-spoke][guidance-hub-spoke] reference architecture also apply to the shared services reference architecture.</span></span>

<span data-ttu-id="bcbb4-155">另外，以下建议适用于共享服务中的大多数方案。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-155">Also, the following recommendations apply for most scenarios under shared services.</span></span> <span data-ttu-id="bcbb4-156">除非有优先于这些建议的特定要求，否则请遵循这些建议。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-156">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="identity"></a><span data-ttu-id="bcbb4-157">标识</span><span class="sxs-lookup"><span data-stu-id="bcbb4-157">Identity</span></span>

<span data-ttu-id="bcbb4-158">大多数企业组织在其本地数据中心都有 Active Directory 目录服务 (ADDS) 环境。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-158">Most enterprise organizations have an Active Directory Directory Services (ADDS) environment in their on-premises datacenter.</span></span> <span data-ttu-id="bcbb4-159">为了便于管理从本地网络移到 Azure 且依赖于 ADDS 的资产，建议将 ADDS 域控制器托管在 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-159">To facilitate management of assets moved to Azure from your on-premises network that depend on ADDS, it is recommended to host ADDS domain controllers in Azure.</span></span>

<span data-ttu-id="bcbb4-160">如果使用需要针对 Azure 和本地环境分开进行控制的组策略对象，请针对每个 Azure 区域使用不同的 AD 站点。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-160">If you make use of Group Policy Objects, that you want to control separately for Azure and your on-premises environment, use a different AD site for each Azure region.</span></span> <span data-ttu-id="bcbb4-161">将域控制器置于可供依赖性工作负荷访问的中心 VNet（简称“中心”）。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-161">Place your domain controllers in a central VNet (hub) that dependent workloads can access.</span></span>

### <a name="security"></a><span data-ttu-id="bcbb4-162">安全</span><span class="sxs-lookup"><span data-stu-id="bcbb4-162">Security</span></span>

<span data-ttu-id="bcbb4-163">将工作负荷从本地环境移到 Azure 时，其中的某些工作负荷将需要托管在 VM 中。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-163">As you move workloads from your on-premises environment to Azure, some of these workloads will require to be hosted in VMs.</span></span> <span data-ttu-id="bcbb4-164">出于符合性考虑，可能需要对流经这些工作负荷的流量强制实施限制。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-164">For compliance reasons, you may need to enforce restrictions on traffic traversing those workloads.</span></span>

<span data-ttu-id="bcbb4-165">可以在 Azure 中使用网络虚拟设备 (NVA) 来托管各类安全和性能服务。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-165">You can use network virtual appliances (NVAs) in Azure to host different types of security and performance services.</span></span> <span data-ttu-id="bcbb4-166">如果熟悉当前在本地使用的特定设备组，建议在 Azure 中也使用相同的虚拟化设备，如果适用的话。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-166">If you are familiar with a given set of appliances on-premises today, it is recommended to use the same virtualized appliances in Azure, where applicable.</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb4-167">针对此参考体系结构的部署脚本使用已启用 IP 转发功能的 Ubuntu VM，以便模拟网络虚拟设备。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-167">The deployment scripts for this reference architecture use an Ubuntu VM with IP forwarding enabled to mimic a network virtual appliance.</span></span>

## <a name="considerations"></a><span data-ttu-id="bcbb4-168">注意事项</span><span class="sxs-lookup"><span data-stu-id="bcbb4-168">Considerations</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="bcbb4-169">克服 VNet 对等互连限制</span><span class="sxs-lookup"><span data-stu-id="bcbb4-169">Overcoming VNet peering limits</span></span>

<span data-ttu-id="bcbb4-170">请务必考虑 Azure 中[每个 VNet 的 VNet 对等互连数限制][vnet-peering-limit]。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-170">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="bcbb4-171">如果你确定所需的辐射多于限制将允许的数量，请考虑创建一个中心-辐射-中心-辐射型拓扑，其中的第一级辐射还充当中心。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-171">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="bcbb4-172">下图显示了此方式。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-172">The following diagram shows this approach.</span></span>

![Azure 中的中心-辐射-中心-辐射型拓扑](./images/hub-spokehub-spoke.svg)

<span data-ttu-id="bcbb4-174">另外，请考虑要在中心内共享哪些服务，以确保中心能够针对大量辐射进行缩放。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-174">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="bcbb4-175">例如，如果中心提供防火墙服务，则在添加多个辐射时请考虑防火墙解决方案的带宽限制。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-175">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="bcbb4-176">你可能希望将这些共享服务中的某一些移动到二级中心内。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-176">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="bcbb4-177">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="bcbb4-177">Deploy the solution</span></span>

<span data-ttu-id="bcbb4-178">[GitHub][ref-arch-repo] 上提供了此体系结构的部署。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-178">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="bcbb4-179">该部署在订阅中创建以下资源组：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-179">The deployment creates the following resource groups in your subscription:</span></span>

- <span data-ttu-id="bcbb4-180">hub-adds-rg</span><span class="sxs-lookup"><span data-stu-id="bcbb4-180">hub-adds-rg</span></span>
- <span data-ttu-id="bcbb4-181">hub-nva-rg</span><span class="sxs-lookup"><span data-stu-id="bcbb4-181">hub-nva-rg</span></span>
- <span data-ttu-id="bcbb4-182">hub-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="bcbb4-182">hub-vnet-rg</span></span>
- <span data-ttu-id="bcbb4-183">onprem-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="bcbb4-183">onprem-vnet-rg</span></span>
- <span data-ttu-id="bcbb4-184">spoke1-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="bcbb4-184">spoke1-vnet-rg</span></span>
- <span data-ttu-id="bcbb4-185">spoke2-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="bcbb4-185">spoke2-vnet-rg</span></span>

<span data-ttu-id="bcbb4-186">模板参数文件将引用这些名称，因此，如果更改了这些名称，请相应地更新参数文件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-186">The template parameter files refer to these names, so if you change them, update the parameter files to match.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="bcbb4-187">先决条件</span><span class="sxs-lookup"><span data-stu-id="bcbb4-187">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a><span data-ttu-id="bcbb4-188">使用 azbb 部署模拟的本地数据中心</span><span class="sxs-lookup"><span data-stu-id="bcbb4-188">Deploy the simulated on-premises datacenter using azbb</span></span>

<span data-ttu-id="bcbb4-189">此步骤将模拟的本地数据中心部署为 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-189">This step deploys the simulated on-premises datacenter as an Azure VNet.</span></span>

1. <span data-ttu-id="bcbb4-190">导航到 GitHub 存储库的 `hybrid-networking\shared-services-stack\` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-190">Navigate to the `hybrid-networking\shared-services-stack\` folder of the GitHub repository.</span></span>

2. <span data-ttu-id="bcbb4-191">打开 `onprem.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-191">Open the `onprem.json` file.</span></span>

3. <span data-ttu-id="bcbb4-192">搜索 `UserName`、`adminUserName`、`Password`、`adminPassword` 的所有实例。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-192">Search for all instances of `UserName`, `adminUserName`,`Password`, and `adminPassword`.</span></span> <span data-ttu-id="bcbb4-193">在参数中输入用户名和密码的值，然后保存文件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-193">Enter values for the user name and password in the parameters and save the file.</span></span>

4. <span data-ttu-id="bcbb4-194">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-194">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g onprem-vnet-rg -l <location> -p onprem.json --deploy
   ```

5. <span data-ttu-id="bcbb4-195">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-195">Wait for the deployment to finish.</span></span> <span data-ttu-id="bcbb4-196">此部署创建虚拟网络、运行 Windows 的虚拟机和 VPN 网关。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-196">This deployment creates a virtual network, a virtual machine running Windows, and a VPN gateway.</span></span> <span data-ttu-id="bcbb4-197">VPN 网关创建可能需要 40 多分钟才能完成。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-197">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="deploy-the-hub-vnet"></a><span data-ttu-id="bcbb4-198">部署中心 VNet</span><span class="sxs-lookup"><span data-stu-id="bcbb4-198">Deploy the hub VNet</span></span>

<span data-ttu-id="bcbb4-199">此步骤部署中心 VNet 并将其连接到模拟的本地 VNet。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-199">This step deploys the hub VNet and connects it to the simulated on-premises VNet.</span></span>

1. <span data-ttu-id="bcbb4-200">打开 `hub-vnet.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-200">Open the `hub-vnet.json` file.</span></span>

2. <span data-ttu-id="bcbb4-201">搜索 `adminPassword`，然后在参数中输入用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-201">Search for `adminPassword` and enter a user name and password in the parameters.</span></span>

3. <span data-ttu-id="bcbb4-202">搜索 `sharedKey` 的所有实例，然后输入某个共享密钥的值。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-202">Search for all instances of `sharedKey` and enter a value for a shared key.</span></span> <span data-ttu-id="bcbb4-203">保存文件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-203">Save the file.</span></span>

   ```json
   "sharedKey": "abc123",
   ```

4. <span data-ttu-id="bcbb4-204">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-204">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet.json --deploy
   ```

5. <span data-ttu-id="bcbb4-205">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-205">Wait for the deployment to finish.</span></span> <span data-ttu-id="bcbb4-206">此部署创建虚拟网络、虚拟机、VPN 网关和到上一部分创建的网关的连接。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-206">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway created in the previous section.</span></span> <span data-ttu-id="bcbb4-207">VPN 网关可能需要 40 多分钟才能完成。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-207">The VPN gateway can take more than 40 minutes to complete.</span></span>

### <a name="deploy-ad-ds-in-azure"></a><span data-ttu-id="bcbb4-208">在 Azure 中部署 AD DS</span><span class="sxs-lookup"><span data-stu-id="bcbb4-208">Deploy AD DS in Azure</span></span>

<span data-ttu-id="bcbb4-209">此步骤在 Azure 中部署 AD DS 域控制器。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-209">This step deploys AD DS domain controllers in Azure.</span></span>

1. <span data-ttu-id="bcbb4-210">打开 `hub-adds.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-210">Open the `hub-adds.json` file.</span></span>

2. <span data-ttu-id="bcbb4-211">搜索 `Password` 和 `adminPassword` 的所有实例。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-211">Search for all instances of `Password` and `adminPassword`.</span></span> <span data-ttu-id="bcbb4-212">在参数中输入用户名和密码的值，然后保存文件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-212">Enter values for the user name and password in the parameters and save the file.</span></span>

3. <span data-ttu-id="bcbb4-213">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-213">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-adds-rg -l <location> -p hub-adds.json --deploy
   ```

<span data-ttu-id="bcbb4-214">此部署步骤可能需要数分钟的时间，因为需要将两个 VM 加入到域中，而该域托管在模拟的本地数据中心，然后需要在其上安装 AD DS。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-214">This deployment step may take several minutes, because it joins the two VMs to the domain hosted in the simulated on-premises datacenter, and installs AD DS on them.</span></span>

### <a name="deploy-the-spoke-vnets"></a><span data-ttu-id="bcbb4-215">部署辐射 VNet</span><span class="sxs-lookup"><span data-stu-id="bcbb4-215">Deploy the spoke VNets</span></span>

<span data-ttu-id="bcbb4-216">此步骤部署辐射 VNet。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-216">This step deploys the spoke VNets.</span></span>

1. <span data-ttu-id="bcbb4-217">打开 `spoke1.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-217">Open the `spoke1.json` file.</span></span>

2. <span data-ttu-id="bcbb4-218">搜索 `adminPassword`，然后在参数中输入用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-218">Search for `adminPassword` and enter a user name and password in the parameters.</span></span>

3. <span data-ttu-id="bcbb4-219">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-219">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg -l <location> -p spoke1.json --deploy
   ```

4. <span data-ttu-id="bcbb4-220">针对 `spoke2.json` 文件重复步骤 1 和 2。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-220">Repeat steps 1 and 2 for the file `spoke2.json`.</span></span>

5. <span data-ttu-id="bcbb4-221">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-221">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg -l <location> -p spoke2.json --deploy
   ```

### <a name="peer-the-hub-vnet-to-the-spoke-vnets"></a><span data-ttu-id="bcbb4-222">将中心 VNet 与辐射 VNet 对等互连</span><span class="sxs-lookup"><span data-stu-id="bcbb4-222">Peer the hub VNet to the spoke VNets</span></span>

<span data-ttu-id="bcbb4-223">若要创建从中心 VNet 到辐射 VNet 的对等互连连接，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-223">To create a peering connection from the hub VNet to the spoke VNets, run the following command:</span></span>

```bash
azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet-peering.json --deploy
```

### <a name="deploy-the-nva"></a><span data-ttu-id="bcbb4-224">部署 NVA</span><span class="sxs-lookup"><span data-stu-id="bcbb4-224">Deploy the NVA</span></span>

<span data-ttu-id="bcbb4-225">此步骤在 `dmz` 子网中部署 NVA。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-225">This step deploys an NVA in the `dmz` subnet.</span></span>

1. <span data-ttu-id="bcbb4-226">打开 `hub-nva.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-226">Open the `hub-nva.json` file.</span></span>

2. <span data-ttu-id="bcbb4-227">搜索 `adminPassword`，然后在参数中输入用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-227">Search for `adminPassword` and enter a user name and password in the parameters.</span></span>

3. <span data-ttu-id="bcbb4-228">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-228">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg -l <location> -p hub-nva.json --deploy
   ```

### <a name="test-connectivity"></a><span data-ttu-id="bcbb4-229">测试连接</span><span class="sxs-lookup"><span data-stu-id="bcbb4-229">Test connectivity</span></span>

<span data-ttu-id="bcbb4-230">测试从模拟本地环境到中心 VNet 的连接。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-230">Test conectivity from the simulated on-premises environment to the hub VNet.</span></span>

1. <span data-ttu-id="bcbb4-231">使用 Azure 门户在 `onprem-jb-rg` 资源组中找到名为 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-231">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="bcbb4-232">单击 `Connect` 来与 VM 建立远程桌面会话。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-232">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="bcbb4-233">使用 `onprem.json` 参数文件中指定的密码。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-233">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="bcbb4-234">在 VM 中打开 PowerShell 控制台，使用 `Test-NetConnection` cmdlet 验证能否连接到中心 VNet 中的 Jumpbox VM。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-234">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VM in the hub VNet.</span></span>

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```

<span data-ttu-id="bcbb4-235">输出应如下所示：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-235">The output should look similar to the following:</span></span>

```powershell
ComputerName     : 10.0.0.68
RemoteAddress    : 10.0.0.68
RemotePort       : 3389
InterfaceAlias   : Ethernet 2
SourceAddress    : 192.168.1.000
TcpTestSucceeded : True
```

> [!NOTE]
> <span data-ttu-id="bcbb4-236">默认情况下，Windows Server VM 不允许 Azure 中的 ICMP 响应。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-236">By default, Windows Server VMs do not allow ICMP responses in Azure.</span></span> <span data-ttu-id="bcbb4-237">若要使用 `ping` 来测试连接，需在“Windows 高级防火墙”中为每个 VM 启用 ICMP 流量。</span><span class="sxs-lookup"><span data-stu-id="bcbb4-237">If you want to use `ping` to test connectivity, you need to enable ICMP traffic in the Windows Advanced Firewall for each VM.</span></span>

<span data-ttu-id="bcbb4-238">重复相同的步骤，测试到辐射 VNet 的连接：</span><span class="sxs-lookup"><span data-stu-id="bcbb4-238">Repeat the sames steps to test connectivity to the spoke VNets:</span></span>

```powershell
Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
```

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[guidance-hub-spoke]: ./hub-spoke.md
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[虚拟数据中心]: https://aka.ms/vdc
[virtual datacenter]: https://aka.ms/vdc
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
