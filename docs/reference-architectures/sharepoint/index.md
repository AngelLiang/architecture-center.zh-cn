---
title: 在 Azure 中运行具有高可用性的 SharePoint Server 2016 场
titleSuffix: Azure Reference Architectures
description: 建议为在 Azure 中部署具有高可用性的 SharePoint Server 2016 场而使用的体系结构。
author: njray
ms.date: 07/26/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 0cce207dedd0eb42e29a152b3bb84acc2dca323a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58344982"
---
# <a name="run-a-highly-available-sharepoint-server-2016-farm-in-azure"></a><span data-ttu-id="6d97a-103">在 Azure 中运行具有高可用性的 SharePoint Server 2016 场</span><span class="sxs-lookup"><span data-stu-id="6d97a-103">Run a highly available SharePoint Server 2016 farm in Azure</span></span>

<span data-ttu-id="6d97a-104">此参考体系结构演示有关使用 MinRole 拓扑和 SQL Server Always On 可用性组在 Azure 中部署具有高可用性的 SharePoint Server 2016 场的成熟做法。</span><span class="sxs-lookup"><span data-stu-id="6d97a-104">This reference architecture shows proven practices for deploying a highly available SharePoint Server 2016 farm on Azure, using MinRole topology and SQL Server Always On availability groups.</span></span> <span data-ttu-id="6d97a-105">无需提供面向 Internet 的终结点，即可在安全的虚拟网络中部署 SharePoint 场。</span><span class="sxs-lookup"><span data-stu-id="6d97a-105">The SharePoint farm is deployed in a secured virtual network with no Internet-facing endpoint or presence.</span></span> <span data-ttu-id="6d97a-106">[**部署此解决方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="6d97a-106">[**Deploy this solution**](#deploy-the-solution).</span></span>

![在 Azure 中运行具有高可用性的 SharePoint Server 2016 场的参考体系结构](./images/sharepoint-ha.png)

<span data-ttu-id="6d97a-108">下载此体系结构的 [Visio 文件][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="6d97a-109">体系结构</span><span class="sxs-lookup"><span data-stu-id="6d97a-109">Architecture</span></span>

<span data-ttu-id="6d97a-110">此体系结构构建在[运行用于 N 层应用程序的 Windows VM][windows-n-tier] 中所示的体系结构基础之上。</span><span class="sxs-lookup"><span data-stu-id="6d97a-110">This architecture builds on the one shown in [Run Windows VMs for an N-tier application][windows-n-tier].</span></span> <span data-ttu-id="6d97a-111">它在 Azure 虚拟网络 (VNet) 中部署一个具有高可用性的 SharePoint Server 2016 场。</span><span class="sxs-lookup"><span data-stu-id="6d97a-111">It deploys a SharePoint Server 2016 farm with high availability inside an Azure virtual network (VNet).</span></span> <span data-ttu-id="6d97a-112">此体系结构适合用于测试或生产环境、包含 Office 365 的 SharePoint 混合基础结构，或用作灾难恢复方案的基础。</span><span class="sxs-lookup"><span data-stu-id="6d97a-112">This architecture is suitable for a test or production environment, a SharePoint hybrid infrastructure with Office 365, or as the basis for a disaster recovery scenario.</span></span>

<span data-ttu-id="6d97a-113">该体系结构包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="6d97a-113">The architecture consists of the following components:</span></span>

- <span data-ttu-id="6d97a-114">**资源组**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-114">**Resource groups**.</span></span> <span data-ttu-id="6d97a-115">[资源组][resource-group]是保存相关 Azure 资源的容器。</span><span class="sxs-lookup"><span data-stu-id="6d97a-115">A [resource group][resource-group] is a container that holds related Azure resources.</span></span> <span data-ttu-id="6d97a-116">SharePoint 服务器使用一个资源组，独立于 VM 的基础结构组件（例如虚拟网络和负载均衡器）使用另一个资源组。</span><span class="sxs-lookup"><span data-stu-id="6d97a-116">One resource group is used for the SharePoint servers, and another resource group is used for infrastructure components that are independent of VMs, such as the virtual network and load balancers.</span></span>

- <span data-ttu-id="6d97a-117">**虚拟网络 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-117">**Virtual network (VNet)**.</span></span> <span data-ttu-id="6d97a-118">VM 部署在具有唯一 Intranet 地址空间的 VNet 中。</span><span class="sxs-lookup"><span data-stu-id="6d97a-118">The VMs are deployed in a VNet with a unique intranet address space.</span></span> <span data-ttu-id="6d97a-119">VNet 进一步细分为子网。</span><span class="sxs-lookup"><span data-stu-id="6d97a-119">The VNet is further subdivided into subnets.</span></span>

- <span data-ttu-id="6d97a-120">**虚拟机 (VM)**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-120">**Virtual machines (VMs)**.</span></span> <span data-ttu-id="6d97a-121">VM 部署到 VNet 中，已将专用静态 IP 地址分配到所有 VM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-121">The VMs are deployed into the VNet, and private static IP addresses are assigned to all of the VMs.</span></span> <span data-ttu-id="6d97a-122">建议对运行 SQL Server 和 SharePoint Server 2016 的 VM 使用静态 IP 地址，以免重启后 IP 地址缓存出现问题以及地址发生更改。</span><span class="sxs-lookup"><span data-stu-id="6d97a-122">Static IP addresses are recommended for the VMs running SQL Server and SharePoint Server 2016, to avoid issues with IP address caching and changes of addresses after a restart.</span></span>

- <span data-ttu-id="6d97a-123">**可用性集**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-123">**Availability sets**.</span></span> <span data-ttu-id="6d97a-124">将每个 SharePoint 角色的 VM 放入单独的[可用性集][availability-set]，并为每个角色至少预配两个虚拟机 (VM)。</span><span class="sxs-lookup"><span data-stu-id="6d97a-124">Place the VMs for each SharePoint role into separate [availability sets][availability-set], and provision at least two virtual machines (VMs) for each role.</span></span> <span data-ttu-id="6d97a-125">这样，VM 便可以满足更高的服务级别协议 (SLA)。</span><span class="sxs-lookup"><span data-stu-id="6d97a-125">This makes the VMs eligible for a higher service level agreement (SLA).</span></span>

- <span data-ttu-id="6d97a-126">**内部负载均衡器**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-126">**Internal load balancer**.</span></span> <span data-ttu-id="6d97a-127">[负载均衡器][load-balancer]将 SharePoint 请求流量从本地网络分配到 SharePoint 场的前端 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="6d97a-127">The [load balancer][load-balancer] distributes SharePoint request traffic from the on-premises network to the front-end web servers of the SharePoint farm.</span></span>

- <span data-ttu-id="6d97a-128">**网络安全组 (NSG)**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-128">**Network security groups (NSGs)**.</span></span> <span data-ttu-id="6d97a-129">对于包含虚拟机的每个子网，需创建一个[网络安全组][nsg]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-129">For each subnet that contains virtual machines, a [network security group][nsg] is created.</span></span> <span data-ttu-id="6d97a-130">使用 NSG 限制 VNet 中的网络流量，以隔离子网。</span><span class="sxs-lookup"><span data-stu-id="6d97a-130">Use NSGs to restrict network traffic within the VNet, in order to isolate subnets.</span></span>

- <span data-ttu-id="6d97a-131">**网关**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-131">**Gateway**.</span></span> <span data-ttu-id="6d97a-132">网关在本地网络与 Azure 虚拟网络之间提供连接。</span><span class="sxs-lookup"><span data-stu-id="6d97a-132">The gateway provides a connection between your on-premises network and the Azure virtual network.</span></span> <span data-ttu-id="6d97a-133">连接可以使用 ExpressRoute 或站点到站点 VPN。</span><span class="sxs-lookup"><span data-stu-id="6d97a-133">Your connection can use ExpressRoute or site-to-site VPN.</span></span> <span data-ttu-id="6d97a-134">有关详细信息，请参阅[将本地网络连接到 Azure][hybrid-ra]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-134">For more information, see [Connect an on-premises network to Azure][hybrid-ra].</span></span>

- <span data-ttu-id="6d97a-135">**Windows Server Active Directory (AD) 域控制器**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-135">**Windows Server Active Directory (AD) domain controllers**.</span></span> <span data-ttu-id="6d97a-136">此参考体系结构部署 Windows Server AD 域控制器。</span><span class="sxs-lookup"><span data-stu-id="6d97a-136">This reference architecture deploys Windows Server AD domain controllers.</span></span> <span data-ttu-id="6d97a-137">这些域控制器在 Azure VNet 中运行，并与本地 Windows Server AD 林建立了信任关系。</span><span class="sxs-lookup"><span data-stu-id="6d97a-137">These domain controllers run in the Azure VNet and have a trust relationship with the on-premises Windows Server AD forest.</span></span> <span data-ttu-id="6d97a-138">针对 SharePoint 场资源发出的客户端 Web 请求在 VNet 中进行身份验证，而不是跨网关连接将该身份验证流量发送到本地网络。</span><span class="sxs-lookup"><span data-stu-id="6d97a-138">Client web requests for SharePoint farm resources are authenticated in the VNet rather than sending that authentication traffic across the gateway connection to the on-premises network.</span></span> <span data-ttu-id="6d97a-139">在 DNS 中创建 Intranet A 或 CNAME 记录，使 Intranet 用户能够将 SharePoint 场的名称解析成内部负载均衡器的专用 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="6d97a-139">In DNS, intranet A or CNAME records are created so that intranet users can resolve the name of the SharePoint farm to the private IP address of the internal load balancer.</span></span>

  <span data-ttu-id="6d97a-140">SharePoint Server 2016 也支持使用 [Azure Active Directory 域服务](/azure/active-directory-domain-services/)。</span><span class="sxs-lookup"><span data-stu-id="6d97a-140">SharePoint Server 2016 also supports using [Azure Active Directory Domain Services](/azure/active-directory-domain-services/).</span></span> <span data-ttu-id="6d97a-141">Azure AD 域服务提供托管域服务，因此不需在 Azure 中部署和管理域控制器。</span><span class="sxs-lookup"><span data-stu-id="6d97a-141">Azure AD Domain Services provides managed domain services, so that you don't need to deploy and manage domain controllers in Azure.</span></span>

- <span data-ttu-id="6d97a-142">**SQL Server Always On 可用性组**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-142">**SQL Server Always On Availability Group**.</span></span> <span data-ttu-id="6d97a-143">为了实现 SQL Server 数据库的高可用性，我们建议创建 [SQL Server Always On 可用性组][sql-always-on]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-143">For high availability of the SQL Server database, we recommend [SQL Server Always On Availability Groups][sql-always-on].</span></span> <span data-ttu-id="6d97a-144">将两个虚拟机用于 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="6d97a-144">Two virtual machines are used for SQL Server.</span></span> <span data-ttu-id="6d97a-145">一个虚拟机包含主数据库副本，另一个虚拟机包含次要副本。</span><span class="sxs-lookup"><span data-stu-id="6d97a-145">One contains the primary database replica and the other contains the secondary replica.</span></span>

- <span data-ttu-id="6d97a-146">**多数节点 VM**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-146">**Majority node VM**.</span></span> <span data-ttu-id="6d97a-147">此 VM 可让故障转移群集建立仲裁。</span><span class="sxs-lookup"><span data-stu-id="6d97a-147">This VM allows the failover cluster to establish quorum.</span></span> <span data-ttu-id="6d97a-148">有关详细信息，请参阅[了解故障转移群集中的仲裁配置][sql-quorum]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-148">For more information, see [Understanding Quorum Configurations in a Failover Cluster][sql-quorum].</span></span>

- <span data-ttu-id="6d97a-149">**SharePoint 服务器**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-149">**SharePoint servers**.</span></span> <span data-ttu-id="6d97a-150">SharePoint 服务器执行 Web 前端、缓存、应用程序和搜索角色。</span><span class="sxs-lookup"><span data-stu-id="6d97a-150">The SharePoint servers perform the web front-end, caching, application, and search roles.</span></span>

- <span data-ttu-id="6d97a-151">**Jumpbox**。</span><span class="sxs-lookup"><span data-stu-id="6d97a-151">**Jumpbox**.</span></span> <span data-ttu-id="6d97a-152">也称为[守护主机][bastion-host]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-152">Also called a [bastion host][bastion-host].</span></span> <span data-ttu-id="6d97a-153">这是管理员在网络上用来连接其他 VM 的安全 VM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-153">This is a secure VM on the network that administrators use to connect to the other VMs.</span></span> <span data-ttu-id="6d97a-154">Jumpbox 中的某个 NSG 只允许来自安全列表中的公共 IP 地址的远程流量。</span><span class="sxs-lookup"><span data-stu-id="6d97a-154">The jumpbox has an NSG that allows remote traffic only from public IP addresses on a safe list.</span></span> <span data-ttu-id="6d97a-155">该 NSG 应允许远程桌面 (RDP) 流量。</span><span class="sxs-lookup"><span data-stu-id="6d97a-155">The NSG should permit remote desktop (RDP) traffic.</span></span>

## <a name="recommendations"></a><span data-ttu-id="6d97a-156">建议</span><span class="sxs-lookup"><span data-stu-id="6d97a-156">Recommendations</span></span>

<span data-ttu-id="6d97a-157">你的要求可能不同于此处描述的体系结构。</span><span class="sxs-lookup"><span data-stu-id="6d97a-157">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="6d97a-158">请使用以下建议作为入手点。</span><span class="sxs-lookup"><span data-stu-id="6d97a-158">Use these recommendations as a starting point.</span></span>

### <a name="resource-group-recommendations"></a><span data-ttu-id="6d97a-159">有关资源组的建议</span><span class="sxs-lookup"><span data-stu-id="6d97a-159">Resource group recommendations</span></span>

<span data-ttu-id="6d97a-160">我们建议根据服务器角色区分资源组，并针对属于全局资源的基础结构组件单独创建一个资源组。</span><span class="sxs-lookup"><span data-stu-id="6d97a-160">We recommend separating resource groups according to the server role, and having a separate resource group for infrastructure components that are global resources.</span></span> <span data-ttu-id="6d97a-161">在此体系结构中，SharePoint 资源组来自一个组，SQL Server 和其他实用工具资产来自另一个组。</span><span class="sxs-lookup"><span data-stu-id="6d97a-161">In this architecture, the SharePoint resources form one group, while the SQL Server and other utility assets form another.</span></span>

### <a name="virtual-network-and-subnet-recommendations"></a><span data-ttu-id="6d97a-162">有关虚拟网络和子网的建议</span><span class="sxs-lookup"><span data-stu-id="6d97a-162">Virtual network and subnet recommendations</span></span>

<span data-ttu-id="6d97a-163">对每个 SharePoint 角色使用一个子网，此外，对网关和 Jumpbox 各使用一个子网。</span><span class="sxs-lookup"><span data-stu-id="6d97a-163">Use one subnet for each SharePoint role, plus a subnet for the gateway and one for the jumpbox.</span></span>

<span data-ttu-id="6d97a-164">网关子网必须命名为 *GatewaySubnet*。</span><span class="sxs-lookup"><span data-stu-id="6d97a-164">The gateway subnet must be named *GatewaySubnet*.</span></span> <span data-ttu-id="6d97a-165">从虚拟网络地址空间的最后一个部分中分配网关子网地址空间。</span><span class="sxs-lookup"><span data-stu-id="6d97a-165">Assign the gateway subnet address space from the last part of the virtual network address space.</span></span> <span data-ttu-id="6d97a-166">有关详细信息，请参阅[使用 VPN 网关将本地网络连接到 Azure][hybrid-vpn-ra]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-166">For more information, see [Connect an on-premises network to Azure using a VPN gateway][hybrid-vpn-ra].</span></span>

### <a name="vm-recommendations"></a><span data-ttu-id="6d97a-167">VM 建议</span><span class="sxs-lookup"><span data-stu-id="6d97a-167">VM recommendations</span></span>

<span data-ttu-id="6d97a-168">此体系结构至少需要 44 个核心：</span><span class="sxs-lookup"><span data-stu-id="6d97a-168">This architecture requires a minimum of 44 cores:</span></span>

- <span data-ttu-id="6d97a-169">Standard_DS3_v2 虚拟机上有 8 个 SharePoint 服务器（每个服务器需要 4 个核心）= 32 个核心</span><span class="sxs-lookup"><span data-stu-id="6d97a-169">8 SharePoint servers on Standard_DS3_v2 (4 cores each) = 32 cores</span></span>
- <span data-ttu-id="6d97a-170">Standard_DS1_v2 虚拟机上有 2 个 Active Directory 域控制器（每个域控制器需要 1 个核心）= 2 个核心</span><span class="sxs-lookup"><span data-stu-id="6d97a-170">2 Active Directory domain controllers on Standard_DS1_v2 (1 core each) = 2 cores</span></span>
- <span data-ttu-id="6d97a-171">Standard_DS3_v2 上有 2 个 SQL Server VM = 8 个核心</span><span class="sxs-lookup"><span data-stu-id="6d97a-171">2 SQL Server VMs on Standard_DS3_v2 = 8 cores</span></span>
- <span data-ttu-id="6d97a-172">Standard_DS1_v2 虚拟机上有 1 个多数节点 = 1 个核心</span><span class="sxs-lookup"><span data-stu-id="6d97a-172">1 majority node on Standard_DS1_v2 = 1 core</span></span>
- <span data-ttu-id="6d97a-173">Standard_DS1_v2 虚拟机上有 1 个管理服务器 = 1 个核心</span><span class="sxs-lookup"><span data-stu-id="6d97a-173">1 management server on Standard_DS1_v2 = 1 core</span></span>

<span data-ttu-id="6d97a-174">确保 Azure 订阅提供足够的 VM 核心配额用于部署，否则部署将会失败。</span><span class="sxs-lookup"><span data-stu-id="6d97a-174">Make sure your Azure subscription has enough VM core quota for the deployment, or the deployment will fail.</span></span> <span data-ttu-id="6d97a-175">请参阅 [Azure 订阅和服务限制、配额与约束][quotas]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-175">See [Azure subscription and service limits, quotas, and constraints][quotas].</span></span>

<span data-ttu-id="6d97a-176">对于除搜索索引器以外的所有 SharePoint 角色，我们建议使用 [Standard_DS3_v2][vm-sizes-general] VM 大小。</span><span class="sxs-lookup"><span data-stu-id="6d97a-176">For all SharePoint roles except the Search Indexer, we recommended using the [Standard_DS3_v2][vm-sizes-general] VM size.</span></span> <span data-ttu-id="6d97a-177">搜索索引器应至少使用 [Standard_DS13_v2][vm-sizes-memory] 大小。</span><span class="sxs-lookup"><span data-stu-id="6d97a-177">The Search Indexer should be at least the [Standard_DS13_v2][vm-sizes-memory] size.</span></span> <span data-ttu-id="6d97a-178">对于测试，此参考体系结构的参数文件为搜索索引器角色指定了较小的 DS3_v2 大小。</span><span class="sxs-lookup"><span data-stu-id="6d97a-178">For testing, the parameter files for this reference architecture specify the smaller DS3_v2 size for the Search Indexer role.</span></span> <span data-ttu-id="6d97a-179">对于生产部署，请更新参数文件以使用 DS13 大小或更大大小。</span><span class="sxs-lookup"><span data-stu-id="6d97a-179">For a production deployment, update the parameter files to use the DS13 size or larger.</span></span> <span data-ttu-id="6d97a-180">有关详细信息，请参阅 [SharePoint Server 2016 的硬件和软件要求][sharepoint-reqs]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-180">For more information, see [Hardware and software requirements for SharePoint Server 2016][sharepoint-reqs].</span></span>

<span data-ttu-id="6d97a-181">对于 SQL Server VM，建议至少配备 4 个核心和 8 GB RAM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-181">For the SQL Server VMs, we recommend a minimum of 4 cores and 8 GB RAM.</span></span> <span data-ttu-id="6d97a-182">此参考体系结构的参数文件指定了 DS3_v2 大小。</span><span class="sxs-lookup"><span data-stu-id="6d97a-182">The parameter files for this reference architecture specify the DS3_v2 size.</span></span> <span data-ttu-id="6d97a-183">对于生产部署，可能需要指定更大的 VM 大小。</span><span class="sxs-lookup"><span data-stu-id="6d97a-183">For a production deployment, you might need to specify a larger VM size.</span></span> <span data-ttu-id="6d97a-184">有关详细信息，请参阅[存储和 SQL Server 容量规划与配置 (SharePoint Server)](/sharepoint/administration/storage-and-sql-server-capacity-planning-and-configuration#estimate-memory-requirements)。</span><span class="sxs-lookup"><span data-stu-id="6d97a-184">For more information, see [Storage and SQL Server capacity planning and configuration (SharePoint Server)](/sharepoint/administration/storage-and-sql-server-capacity-planning-and-configuration#estimate-memory-requirements).</span></span>

### <a name="nsg-recommendations"></a><span data-ttu-id="6d97a-185">有关 NSG 的建议</span><span class="sxs-lookup"><span data-stu-id="6d97a-185">NSG recommendations</span></span>

<span data-ttu-id="6d97a-186">建议针对包含 VM 的每个子网创建一个 NSG，以实现子网隔离。</span><span class="sxs-lookup"><span data-stu-id="6d97a-186">We recommend having one NSG for each subnet that contains VMs, to enable subnet isolation.</span></span> <span data-ttu-id="6d97a-187">若要配置子网隔离，请添加 NSG 规则用于定义每个子网允许或拒绝的入站或出站流量。</span><span class="sxs-lookup"><span data-stu-id="6d97a-187">If you want to configure subnet isolation, add NSG rules that define the allowed or denied inbound or outbound traffic for each subnet.</span></span> <span data-ttu-id="6d97a-188">有关详细信息，请参阅[使用网络安全组筛选网络流量][virtual-networks-nsg]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-188">For more information, see [Filter network traffic with network security groups][virtual-networks-nsg].</span></span>

<span data-ttu-id="6d97a-189">请不要将 NSG 分配到网关子网，否则网关将会停止运行。</span><span class="sxs-lookup"><span data-stu-id="6d97a-189">Do not assign an NSG to the gateway subnet, or the gateway will stop functioning.</span></span>

### <a name="storage-recommendations"></a><span data-ttu-id="6d97a-190">有关存储的建议</span><span class="sxs-lookup"><span data-stu-id="6d97a-190">Storage recommendations</span></span>

<span data-ttu-id="6d97a-191">场中 VM 的存储配置应该符合对本地部署使用的相应最佳做法。</span><span class="sxs-lookup"><span data-stu-id="6d97a-191">The storage configuration of the VMs in the farm should match the appropriate best practices used for on-premises deployments.</span></span> <span data-ttu-id="6d97a-192">SharePoint 服务器应该单独提供一个磁盘用于日志。</span><span class="sxs-lookup"><span data-stu-id="6d97a-192">SharePoint servers should have a separate disk for logs.</span></span> <span data-ttu-id="6d97a-193">托管搜索索引角色的 SharePoint 服务器需要提供额外的磁盘空间用于存储搜索索引。</span><span class="sxs-lookup"><span data-stu-id="6d97a-193">SharePoint servers hosting search index roles require additional disk space for the search index to be stored.</span></span> <span data-ttu-id="6d97a-194">对于 SQL Server，标准做法是将数据和日志区分开来。</span><span class="sxs-lookup"><span data-stu-id="6d97a-194">For SQL Server, the standard practice is to separate data and logs.</span></span> <span data-ttu-id="6d97a-195">为数据库备份存储添加更多磁盘，并为 [tempdb][tempdb] 单独使用一个磁盘。</span><span class="sxs-lookup"><span data-stu-id="6d97a-195">Add more disks for database backup storage, and use a separate disk for [tempdb][tempdb].</span></span>

<span data-ttu-id="6d97a-196">为了获得最高可靠性，我们建议使用 [Azure 托管磁盘][managed-disks]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-196">For best reliability, we recommend using [Azure Managed Disks][managed-disks].</span></span> <span data-ttu-id="6d97a-197">托管磁盘可确保隔离可用性集中 VM 的磁盘，以避免单一故障点。</span><span class="sxs-lookup"><span data-stu-id="6d97a-197">Managed disks ensure that the disks for VMs within an availability set are isolated to avoid single points of failure.</span></span>

> [!NOTE]
> <span data-ttu-id="6d97a-198">目前，此参考体系结构的资源管理器模板不使用托管磁盘。</span><span class="sxs-lookup"><span data-stu-id="6d97a-198">Currently the Resource Manager template for this reference architecture does not use managed disks.</span></span> <span data-ttu-id="6d97a-199">我们正在计划将该模板更新为使用托管磁盘。</span><span class="sxs-lookup"><span data-stu-id="6d97a-199">We are planning to update the template to use managed disks.</span></span>

<span data-ttu-id="6d97a-200">为所有 SharePoint 和 SQL Server VM 使用高级托管磁盘。</span><span class="sxs-lookup"><span data-stu-id="6d97a-200">Use Premium managed disks for all SharePoint and SQL Server VMs.</span></span> <span data-ttu-id="6d97a-201">可为多数节点服务器、域控制器和管理服务器使用标准托管磁盘。</span><span class="sxs-lookup"><span data-stu-id="6d97a-201">You can use Standard managed disks for the majority node server, the domain controllers, and the management server.</span></span>

### <a name="sharepoint-server-recommendations"></a><span data-ttu-id="6d97a-202">有关 SharePoint Server 的建议</span><span class="sxs-lookup"><span data-stu-id="6d97a-202">SharePoint Server recommendations</span></span>

<span data-ttu-id="6d97a-203">在配置 SharePoint 场之前，请确保为每个服务创建了一个 Windows Server Active Directory 服务帐户。</span><span class="sxs-lookup"><span data-stu-id="6d97a-203">Before configuring the SharePoint farm, make sure you have one Windows Server Active Directory service account per service.</span></span> <span data-ttu-id="6d97a-204">对于此体系结构，至少需要提供以下域级帐户才能隔离每个角色的特权：</span><span class="sxs-lookup"><span data-stu-id="6d97a-204">For this architecture, you need at a minimum the following domain-level accounts to isolate privilege per role:</span></span>

- <span data-ttu-id="6d97a-205">SQL Server 服务帐户</span><span class="sxs-lookup"><span data-stu-id="6d97a-205">SQL Server Service account</span></span>
- <span data-ttu-id="6d97a-206">安装用户帐户</span><span class="sxs-lookup"><span data-stu-id="6d97a-206">Setup User account</span></span>
- <span data-ttu-id="6d97a-207">服务器场帐户</span><span class="sxs-lookup"><span data-stu-id="6d97a-207">Server Farm account</span></span>
- <span data-ttu-id="6d97a-208">搜索服务帐户</span><span class="sxs-lookup"><span data-stu-id="6d97a-208">Search Service account</span></span>
- <span data-ttu-id="6d97a-209">内容访问帐户</span><span class="sxs-lookup"><span data-stu-id="6d97a-209">Content Access account</span></span>
- <span data-ttu-id="6d97a-210">Web 应用池帐户</span><span class="sxs-lookup"><span data-stu-id="6d97a-210">Web App Pool accounts</span></span>
- <span data-ttu-id="6d97a-211">服务应用池帐户</span><span class="sxs-lookup"><span data-stu-id="6d97a-211">Service App Pool accounts</span></span>
- <span data-ttu-id="6d97a-212">缓存超级用户帐户</span><span class="sxs-lookup"><span data-stu-id="6d97a-212">Cache Super User account</span></span>
- <span data-ttu-id="6d97a-213">缓存超级读取者帐户</span><span class="sxs-lookup"><span data-stu-id="6d97a-213">Cache Super Reader account</span></span>

<span data-ttu-id="6d97a-214">为了满足每秒最低 200 MB 磁盘吞吐量的支持要求，请确保规划好搜索体系结构。</span><span class="sxs-lookup"><span data-stu-id="6d97a-214">To meet the support requirement for disk throughput of 200 MB per second minimum, make sure to plan the Search architecture.</span></span> <span data-ttu-id="6d97a-215">请参阅[在 SharePoint Server 2013 中规划企业搜索体系结构][sharepoint-search]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-215">See [Plan enterprise search architecture in SharePoint Server 2013][sharepoint-search].</span></span> <span data-ttu-id="6d97a-216">另请遵照[有关在 SharePoint Server 2016 中爬网的最佳做法][sharepoint-crawling]中的准则。</span><span class="sxs-lookup"><span data-stu-id="6d97a-216">Also follow the guidelines in [Best practices for crawling in SharePoint Server 2016][sharepoint-crawling].</span></span>

<span data-ttu-id="6d97a-217">此外，请将搜索组件数据存储在高性能的独立存储卷或分区中。</span><span class="sxs-lookup"><span data-stu-id="6d97a-217">In addition, store the search component data on a separate storage volume or partition with high performance.</span></span> <span data-ttu-id="6d97a-218">为了减少负载并提高吞吐量，请配置此体系结构中所需的对象缓存用户帐户。</span><span class="sxs-lookup"><span data-stu-id="6d97a-218">To reduce load and improve throughput, configure the object cache user accounts, which are required in this architecture.</span></span> <span data-ttu-id="6d97a-219">将 Windows Server 操作系统文件、SharePoint Server 2016 程序文件和诊断日志拆分到具有普通性能的三个独立存储卷或分区中。</span><span class="sxs-lookup"><span data-stu-id="6d97a-219">Split the Windows Server operating system files, the SharePoint Server 2016 program files, and diagnostics logs across three separate storage volumes or partitions with normal performance.</span></span>

<span data-ttu-id="6d97a-220">有关这些建议的详细信息，请参阅 [SharePoint Server 2016 中的初始部署管理帐户和服务帐户][sharepoint-accounts]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-220">For more information about these recommendations, see [Initial deployment administrative and service accounts in SharePoint Server 2016][sharepoint-accounts].</span></span>

### <a name="hybrid-workloads"></a><span data-ttu-id="6d97a-221">混合工作负荷</span><span class="sxs-lookup"><span data-stu-id="6d97a-221">Hybrid workloads</span></span>

<span data-ttu-id="6d97a-222">此参考体系结构部署一个可用作 [SharePoint 混合环境][sharepoint-hybrid]的 SharePoint Server 2016 场 &mdash; 也就是说，要将 SharePoint Server 2016 扩展成 Office 365 SharePoint Online。</span><span class="sxs-lookup"><span data-stu-id="6d97a-222">This reference architecture deploys a SharePoint Server 2016 farm that can be used as a [SharePoint hybrid environment][sharepoint-hybrid] &mdash; that is, extending SharePoint Server 2016 to Office 365 SharePoint Online.</span></span> <span data-ttu-id="6d97a-223">如果已安装 Office Online Server，请参阅 [Azure 中的 Office Web 应用和 Office Online Server 支持能力][office-web-apps]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-223">If you have Office Online Server, see [Office Web Apps and Office Online Server supportability in Azure][office-web-apps].</span></span>

<span data-ttu-id="6d97a-224">此部署中的默认服务应用程序旨在支持混合工作负荷。</span><span class="sxs-lookup"><span data-stu-id="6d97a-224">The default service applications in this deployment are designed to support hybrid workloads.</span></span> <span data-ttu-id="6d97a-225">可以将所有 SharePoint Server 2016 和 Office 365 混合工作负荷都部署到此服务器场而无需对 SharePoint 基础结构进行更改，但有一个例外：云混合搜索服务应用程序不能部署到托管现有搜索拓扑的服务器。</span><span class="sxs-lookup"><span data-stu-id="6d97a-225">All SharePoint Server 2016 and Office 365 hybrid workloads can be deployed to this farm without changes to the SharePoint infrastructure, with one exception: The Cloud Hybrid Search Service Application must not be deployed onto servers hosting an existing search topology.</span></span> <span data-ttu-id="6d97a-226">因此，必须将一个或多个基于搜索角色的 VM 添加到该场，以支持此混合方案。</span><span class="sxs-lookup"><span data-stu-id="6d97a-226">Therefore, one or more search-role-based VMs must be added to the farm to support this hybrid scenario.</span></span>

### <a name="sql-server-always-on-availability-groups"></a><span data-ttu-id="6d97a-227">SQL Server Always On 可用性组</span><span class="sxs-lookup"><span data-stu-id="6d97a-227">SQL Server Always On Availability Groups</span></span>

<span data-ttu-id="6d97a-228">SharePoint Server 2016 无法使用 Azure SQL 数据库，因此，此体系结构使用了 SQL Server 虚拟机。</span><span class="sxs-lookup"><span data-stu-id="6d97a-228">This architecture uses SQL Server virtual machines because SharePoint Server 2016 cannot use Azure SQL Database.</span></span> <span data-ttu-id="6d97a-229">为了支持 SQL Server 中的高可用性，我们建议使用 Always On 可用性组。Always On 可用性组指定一组可共同故障转移的数据库，使这些数据库具有高可用性和可恢复性。</span><span class="sxs-lookup"><span data-stu-id="6d97a-229">To support high availability in SQL Server, we recommend using Always On Availability Groups, which specify a set of databases that fail over together, making them highly-available and recoverable.</span></span> <span data-ttu-id="6d97a-230">在此参考体系结构中，部署期间会创建数据库，但必须手动启用 Always On 可用性组，并将 SharePoint 数据库添加到该可用性组。</span><span class="sxs-lookup"><span data-stu-id="6d97a-230">In this reference architecture, the databases are created during deployment, but you must manually enable Always On Availability Groups and add the SharePoint databases to an availability group.</span></span> <span data-ttu-id="6d97a-231">有关详细信息，请参阅[创建可用性组和添加 SharePoint 数据库][create-availability-group]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-231">For more information, see [Create the availability group and add the SharePoint databases][create-availability-group].</span></span>

<span data-ttu-id="6d97a-232">我们还建议将侦听器 IP 地址（SQL Server 虚拟机内部负载均衡器的专用 IP 地址）添加到群集。</span><span class="sxs-lookup"><span data-stu-id="6d97a-232">We also recommend adding a listener IP address to the cluster, which is the private IP address of the internal load balancer for the SQL Server virtual machines.</span></span>

<span data-ttu-id="6d97a-233">有关建议的 VM 大小，以及 Azure 中运行的 SQL Server 的其他性能建议，请参阅[有关 Azure 虚拟机中 SQL Server 的性能最佳做法][sql-performance]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-233">For recommended VM sizes and other performance recommendations for SQL Server running in Azure, see [Performance best practices for SQL Server in Azure Virtual Machines][sql-performance].</span></span> <span data-ttu-id="6d97a-234">另请遵循[有关 SharePoint Server 2016 场中 SQL Server 的最佳做法][sql-sharepoint-best-practices]中的建议。</span><span class="sxs-lookup"><span data-stu-id="6d97a-234">Also follow the recommendations in [Best practices for SQL Server in a SharePoint Server 2016 farm][sql-sharepoint-best-practices].</span></span>

<span data-ttu-id="6d97a-235">我们建议将多数节点服务器放在复制伙伴的独立计算机上。</span><span class="sxs-lookup"><span data-stu-id="6d97a-235">We recommend that the majority node server reside on a separate computer from the replication partners.</span></span> <span data-ttu-id="6d97a-236">通过该服务器，高安全模式会话中的辅助复制伙伴服务器可以判断是否要启动自动故障转移。</span><span class="sxs-lookup"><span data-stu-id="6d97a-236">The server enables the secondary replication partner server in a high-safety mode session to recognize whether to initiate an automatic failover.</span></span> <span data-ttu-id="6d97a-237">与两个伙伴不同，多数节点服务器不会为数据库提供服务，但只是为自动故障转移提供支持。</span><span class="sxs-lookup"><span data-stu-id="6d97a-237">Unlike the two partners, the majority node server doesn't serve the database but rather supports automatic failover.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="6d97a-238">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="6d97a-238">Scalability considerations</span></span>

<span data-ttu-id="6d97a-239">若要纵向扩展现有的服务器，只需更改 VM 大小。</span><span class="sxs-lookup"><span data-stu-id="6d97a-239">To scale up the existing servers, simply change the VM size.</span></span>

<span data-ttu-id="6d97a-240">使用 SharePoint Server 2016 中的 [MinRoles][minroles] 功能，可以基于服务器的角色横向扩展服务器，以及从角色中删除服务器。</span><span class="sxs-lookup"><span data-stu-id="6d97a-240">With the [MinRoles][minroles] capability in SharePoint Server 2016, you can scale out servers based on the server's role and also remove servers from a role.</span></span> <span data-ttu-id="6d97a-241">将服务器添加到某个角色时，可以指定任意单个角色或一个组合角色。</span><span class="sxs-lookup"><span data-stu-id="6d97a-241">When you add servers to a role, you can specify any of the single roles or one of the combined roles.</span></span> <span data-ttu-id="6d97a-242">但是，如果将服务器添加到搜索角色，则还必须使用 PowerShell 重新配置搜索拓扑。</span><span class="sxs-lookup"><span data-stu-id="6d97a-242">If you add servers to the Search role, however, you must also reconfigure the search topology using PowerShell.</span></span> <span data-ttu-id="6d97a-243">还可以使用 MinRoles 转换角色。</span><span class="sxs-lookup"><span data-stu-id="6d97a-243">You can also convert roles using MinRoles.</span></span> <span data-ttu-id="6d97a-244">有关详细信息，请参阅[在 SharePoint Server 2016 中管理 MinRole 服务器场][sharepoint-minrole]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-244">For more information, see [Managing a MinRole Server Farm in SharePoint Server 2016][sharepoint-minrole].</span></span>

<span data-ttu-id="6d97a-245">请注意，SharePoint Server 2016 不支持使用虚拟机规模集进行自动缩放。</span><span class="sxs-lookup"><span data-stu-id="6d97a-245">Note that SharePoint Server 2016 doesn't support using virtual machine scale sets for auto-scaling.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="6d97a-246">可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="6d97a-246">Availability considerations</span></span>

<span data-ttu-id="6d97a-247">此参考体系结构支持 Azure 区域中的高可用性，因为在一个可用性集中至少为每个角色部署了两个 VM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-247">This reference architecture supports high availability within an Azure region, because each role has at least two VMs deployed in an availability set.</span></span>

<span data-ttu-id="6d97a-248">为了防范区域性故障，请在不同的 Azure 区域中单独创建一个灾难恢复场。</span><span class="sxs-lookup"><span data-stu-id="6d97a-248">To protect against a regional failure, create a separate disaster recovery farm in a different Azure region.</span></span> <span data-ttu-id="6d97a-249">恢复时间目标 (RTO) 和恢复点目标 (RPO) 确定了设置要求。</span><span class="sxs-lookup"><span data-stu-id="6d97a-249">Your recovery time objectives (RTOs) and recovery point objectives (RPOs) will determine the setup requirements.</span></span> <span data-ttu-id="6d97a-250">有关详细信息，请参阅[选择 SharePoint 2016 的灾难恢复策略][sharepoint-dr]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-250">For details, see [Choose a disaster recovery strategy for SharePoint 2016][sharepoint-dr].</span></span> <span data-ttu-id="6d97a-251">次要区域应该是与主要区域*配对的区域*。</span><span class="sxs-lookup"><span data-stu-id="6d97a-251">The secondary region should be a *paired region* with the primary region.</span></span> <span data-ttu-id="6d97a-252">如果发生大范围的服务中断，会优先恢复每个配对中的一个区域。</span><span class="sxs-lookup"><span data-stu-id="6d97a-252">In the event of a broad outage, recovery of one region is prioritized out of every pair.</span></span> <span data-ttu-id="6d97a-253">有关详细信息，请参阅[业务连续性和灾难恢复 (BCDR)：Azure 配对区域][paired-regions]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-253">For more information, see [Business continuity and disaster recovery (BCDR): Azure Paired Regions][paired-regions].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="6d97a-254">可管理性注意事项</span><span class="sxs-lookup"><span data-stu-id="6d97a-254">Manageability considerations</span></span>

<span data-ttu-id="6d97a-255">若要操作和维护服务器、服务器场和站点，请遵循有关 SharePoint 操作的建议做法。</span><span class="sxs-lookup"><span data-stu-id="6d97a-255">To operate and maintain servers, server farms, and sites, follow the recommended practices for SharePoint operations.</span></span> <span data-ttu-id="6d97a-256">有关详细信息，请参阅 [针对 SharePoint Server 2016 的操作][sharepoint-ops]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-256">For more information, see [Operations for SharePoint Server 2016][sharepoint-ops].</span></span>

<span data-ttu-id="6d97a-257">在 SharePoint 环境中管理 SQL Server 时要考虑的任务可能与通常要对数据库应用程序考虑的任务不同。</span><span class="sxs-lookup"><span data-stu-id="6d97a-257">The tasks to consider when managing SQL Server in a SharePoint environment may differ from the ones typically considered for a database application.</span></span> <span data-ttu-id="6d97a-258">最佳做法是使用夜间增量备份，每周完全备份所有的 SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="6d97a-258">A best practice is to fully back up all SQL databases weekly with incremental nightly backups.</span></span> <span data-ttu-id="6d97a-259">每隔 15 分钟备份一次事务日志。</span><span class="sxs-lookup"><span data-stu-id="6d97a-259">Back up transaction logs every 15 minutes.</span></span> <span data-ttu-id="6d97a-260">另一种做法是针对数据库执行 SQL Server 维护任务，同时禁用内置的 SharePoint 任务。</span><span class="sxs-lookup"><span data-stu-id="6d97a-260">Another practice is to implement SQL Server maintenance tasks on the databases while disabling the built-in SharePoint ones.</span></span> <span data-ttu-id="6d97a-261">有关详细信息，请参阅[存储和 SQL Server 容量规划与配置][sql-server-capacity-planning]。</span><span class="sxs-lookup"><span data-stu-id="6d97a-261">For more information, see [Storage and SQL Server capacity planning and configuration][sql-server-capacity-planning].</span></span>

## <a name="security-considerations"></a><span data-ttu-id="6d97a-262">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="6d97a-262">Security considerations</span></span>

<span data-ttu-id="6d97a-263">用于运行 SharePoint Server 2016 的域级服务帐户需要使用 Windows Server AD 域控制器来执行域加入和身份验证过程。</span><span class="sxs-lookup"><span data-stu-id="6d97a-263">The domain-level service accounts used to run SharePoint Server 2016 require Windows Server AD domain controllers for domain-join and authentication processes.</span></span> <span data-ttu-id="6d97a-264">不能将 Azure Active Directory 域服务用于此目的。</span><span class="sxs-lookup"><span data-stu-id="6d97a-264">Azure Active Directory Domain Services can't be used for this purpose.</span></span> <span data-ttu-id="6d97a-265">为了扩展已在 Intranet 中部署的 Windows Server AD 标识基础结构，此体系结构使用了现有本地 Windows Server AD 林的两个 Windows Server AD 副本域控制器。</span><span class="sxs-lookup"><span data-stu-id="6d97a-265">To extend the Windows Server AD identity infrastructure already in place in the intranet, this architecture uses two Windows Server AD replica domain controllers of an existing on-premises Windows Server AD forest.</span></span>

<span data-ttu-id="6d97a-266">此外，做好安全巩固规划始终是明智的做法。</span><span class="sxs-lookup"><span data-stu-id="6d97a-266">In addition, it's always wise to plan for security hardening.</span></span> <span data-ttu-id="6d97a-267">其他建议包括：</span><span class="sxs-lookup"><span data-stu-id="6d97a-267">Other recommendations include:</span></span>

- <span data-ttu-id="6d97a-268">将规则添加到 NSG 以隔离子网和角色。</span><span class="sxs-lookup"><span data-stu-id="6d97a-268">Add rules to NSGs to isolate subnets and roles.</span></span>
- <span data-ttu-id="6d97a-269">不要向 VM 分配公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="6d97a-269">Don't assign public IP addresses to VMs.</span></span>
- <span data-ttu-id="6d97a-270">为了检测入侵和分析工作负荷，请考虑在前端 Web 服务器的前面使用网络虚拟设备，而不要使用内部 Azure 负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="6d97a-270">For intrusion detection and analysis of payloads, consider using a network virtual appliance in front of the front-end web servers instead of an internal Azure load balancer.</span></span>
- <span data-ttu-id="6d97a-271">可以选择使用 IPsec 策略来加密服务器之间的明文流量。</span><span class="sxs-lookup"><span data-stu-id="6d97a-271">As an option, use IPsec policies for encryption of cleartext traffic between servers.</span></span> <span data-ttu-id="6d97a-272">如果同时还在进行子网隔离，请更新网络安全组规则，以允许 IPsec 流量。</span><span class="sxs-lookup"><span data-stu-id="6d97a-272">If you are also doing subnet isolation, update your network security group rules to allow IPsec traffic.</span></span>
- <span data-ttu-id="6d97a-273">为 VM 安装反恶意软件代理。</span><span class="sxs-lookup"><span data-stu-id="6d97a-273">Install anti-malware agents for the VMs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="6d97a-274">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="6d97a-274">Deploy the solution</span></span>

<span data-ttu-id="6d97a-275">[GitHub][github] 中提供了此参考体系结构的部署。</span><span class="sxs-lookup"><span data-stu-id="6d97a-275">A deployment for this reference architecture is available on [GitHub][github].</span></span> <span data-ttu-id="6d97a-276">整个部署可能需要几个小时才能完成。</span><span class="sxs-lookup"><span data-stu-id="6d97a-276">The entire deployment can take several hours to complete.</span></span>

<span data-ttu-id="6d97a-277">该部署在订阅中创建以下资源组：</span><span class="sxs-lookup"><span data-stu-id="6d97a-277">The deployment creates the following resource groups in your subscription:</span></span>

- <span data-ttu-id="6d97a-278">ra-onprem-sp2016-rg</span><span class="sxs-lookup"><span data-stu-id="6d97a-278">ra-onprem-sp2016-rg</span></span>
- <span data-ttu-id="6d97a-279">ra-sp2016-network-rg</span><span class="sxs-lookup"><span data-stu-id="6d97a-279">ra-sp2016-network-rg</span></span>

<span data-ttu-id="6d97a-280">模板参数文件将引用这些名称，因此，如果更改了这些名称，请相应地更新参数文件。</span><span class="sxs-lookup"><span data-stu-id="6d97a-280">The template parameter files refer to these names, so if you change them, update the parameter files to match.</span></span>

<span data-ttu-id="6d97a-281">参数文件在不同的位置包含了硬编码的密码。</span><span class="sxs-lookup"><span data-stu-id="6d97a-281">The parameter files include a hard-coded password in various places.</span></span> <span data-ttu-id="6d97a-282">在部署之前，请更改这些值。</span><span class="sxs-lookup"><span data-stu-id="6d97a-282">Change these values before you deploy.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="6d97a-283">先决条件</span><span class="sxs-lookup"><span data-stu-id="6d97a-283">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deployment-steps"></a><span data-ttu-id="6d97a-284">部署步骤</span><span class="sxs-lookup"><span data-stu-id="6d97a-284">Deployment steps</span></span>

1. <span data-ttu-id="6d97a-285">运行以下命令以部署模拟的本地网络。</span><span class="sxs-lookup"><span data-stu-id="6d97a-285">Run the following command to deploy a simulated on-premises network.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p onprem.json --deploy
    ```

2. <span data-ttu-id="6d97a-286">运行以下命令以部署 Azure VNet 和 VPN 网关。</span><span class="sxs-lookup"><span data-stu-id="6d97a-286">Run the following command to deploy the Azure VNet and the VPN gateway.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p connections.json --deploy
    ```

3. <span data-ttu-id="6d97a-287">运行以下命令以部署 jumpbox、AD 域控制器和 SQL Server VM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-287">Run the following command to deploy the jumpbox, AD domain controllers, and SQL Server VMs.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure1.json --deploy
    ```

4. <span data-ttu-id="6d97a-288">运行以下命令以创建故障转移群集和可用性组。</span><span class="sxs-lookup"><span data-stu-id="6d97a-288">Run the following command to create the failover cluster and the availability group.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure2-cluster.json --deploy
    ```

5. <span data-ttu-id="6d97a-289">运行以下命令以部署剩余 VM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-289">Run the following command to deploy the remaining VMs.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure3.json --deploy
    ```

<span data-ttu-id="6d97a-290">此时，验证是否可以为 SQL Server Always On 可用性组建立从 Web 前端到负载均衡器的 TCP 连接。</span><span class="sxs-lookup"><span data-stu-id="6d97a-290">At this point, verify that you can make a TCP connection from the web front end to the load balancer for the SQL Server Always On availability group.</span></span> <span data-ttu-id="6d97a-291">为此，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="6d97a-291">To do so, perform the following steps:</span></span>

1. <span data-ttu-id="6d97a-292">使用 Azure 门户在 `ra-sp2016-network-rg` 资源组中找到名为 `ra-sp-jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-292">Use the Azure portal to find the VM named `ra-sp-jb-vm1` in the `ra-sp2016-network-rg` resource group.</span></span> <span data-ttu-id="6d97a-293">这是 jumpbox VM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-293">This is the jumpbox VM.</span></span>

2. <span data-ttu-id="6d97a-294">单击 `Connect` 来与 VM 建立远程桌面会话。</span><span class="sxs-lookup"><span data-stu-id="6d97a-294">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="6d97a-295">使用 `azure1.json` 参数文件中指定的密码。</span><span class="sxs-lookup"><span data-stu-id="6d97a-295">Use the password that you specified in the `azure1.json` parameter file.</span></span>

3. <span data-ttu-id="6d97a-296">从远程桌面会话登录到 10.0.5.4。</span><span class="sxs-lookup"><span data-stu-id="6d97a-296">From the Remote Desktop session, log into 10.0.5.4.</span></span> <span data-ttu-id="6d97a-297">这是名为 `ra-sp-app-vm1` 的 VM 的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="6d97a-297">This is the IP address of the VM named `ra-sp-app-vm1`.</span></span>

4. <span data-ttu-id="6d97a-298">在 VM 中打开 PowerShell 控制台，使用 `Test-NetConnection` cmdlet 验证能否连接到负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="6d97a-298">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the load balancer.</span></span>

    ```powershell
    Test-NetConnection 10.0.3.100 -Port 1433
    ```

<span data-ttu-id="6d97a-299">输出应如下所示：</span><span class="sxs-lookup"><span data-stu-id="6d97a-299">The output should look similar to the following:</span></span>

```console
ComputerName     : 10.0.3.100
RemoteAddress    : 10.0.3.100
RemotePort       : 1433
InterfaceAlias   : Ethernet 3
SourceAddress    : 10.0.0.132
TcpTestSucceeded : True
```

<span data-ttu-id="6d97a-300">如果失败，请使用 Azure 门户重启名为 `ra-sp-sql-vm2` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-300">If it fails, use the Azure Portal to restart the VM named `ra-sp-sql-vm2`.</span></span> <span data-ttu-id="6d97a-301">VM 重启后，再次运行 `Test-NetConnection` 命令。</span><span class="sxs-lookup"><span data-stu-id="6d97a-301">After the VM restarts, run the `Test-NetConnection` command again.</span></span> <span data-ttu-id="6d97a-302">在 VM 重启后，可能需要等待大约一分钟，连接才能成功。</span><span class="sxs-lookup"><span data-stu-id="6d97a-302">You may need to wait about a minute after the VM restarts for the connection to succeed.</span></span>

<span data-ttu-id="6d97a-303">现在，如下所示完成部署。</span><span class="sxs-lookup"><span data-stu-id="6d97a-303">Now complete the deployment as follows.</span></span>

1. <span data-ttu-id="6d97a-304">运行以下命令以部署 SharePoint 场的主节点。</span><span class="sxs-lookup"><span data-stu-id="6d97a-304">Run the following command to deploy the SharePoint farm primary node.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure4-sharepoint-server.json --deploy
    ```

2. <span data-ttu-id="6d97a-305">运行以下命令以部署 SharePoint 缓存、搜索和 web。</span><span class="sxs-lookup"><span data-stu-id="6d97a-305">Run the following command to deploy the SharePoint cache, search, and web.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure5-sharepoint-farm.json --deploy
    ```

3. <span data-ttu-id="6d97a-306">运行以下命令以创建 NSG 规则。</span><span class="sxs-lookup"><span data-stu-id="6d97a-306">Run the following command to create the NSG rules.</span></span>

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure6-security.json --deploy
    ```

### <a name="validate-the-deployment"></a><span data-ttu-id="6d97a-307">验证部署</span><span class="sxs-lookup"><span data-stu-id="6d97a-307">Validate the deployment</span></span>

1. <span data-ttu-id="6d97a-308">在 [Azure 门户][azure-portal]中，导航到 `ra-onprem-sp2016-rg` 资源组。</span><span class="sxs-lookup"><span data-stu-id="6d97a-308">In the [Azure portal][azure-portal], navigate to the `ra-onprem-sp2016-rg` resource group.</span></span>

2. <span data-ttu-id="6d97a-309">在资源列表中，选择名为 `ra-onpr-u-vm1` 的 VM 资源。</span><span class="sxs-lookup"><span data-stu-id="6d97a-309">In the list of resources, select the VM resource named `ra-onpr-u-vm1`.</span></span>

3. <span data-ttu-id="6d97a-310">根据[连接到虚拟机][connect-to-vm]中所述连接到该 VM。</span><span class="sxs-lookup"><span data-stu-id="6d97a-310">Connect to the VM, as described in [Connect to virtual machine][connect-to-vm].</span></span> <span data-ttu-id="6d97a-311">用户名为 `\onpremuser`。</span><span class="sxs-lookup"><span data-stu-id="6d97a-311">The user name is `\onpremuser`.</span></span>

4. <span data-ttu-id="6d97a-312">与该 VM 建立远程连接后，请在该 VM 中打开浏览器并导航到 `http://portal.contoso.local`。</span><span class="sxs-lookup"><span data-stu-id="6d97a-312">When the remote connection to the VM is established, open a browser in the VM and navigate to `http://portal.contoso.local`.</span></span>

5. <span data-ttu-id="6d97a-313">在“Windows 安全性”框中，使用用户名 `contoso.local\testuser` 登录到 SharePoint 门户。</span><span class="sxs-lookup"><span data-stu-id="6d97a-313">In the **Windows Security** box, log on to the SharePoint portal using `contoso.local\testuser` for the user name.</span></span>

<span data-ttu-id="6d97a-314">执行此项登录会在本地网络使用的 Fabrikam.com 域与 SharePoint 门户使用的 contoso.local 域之间建立隧道。</span><span class="sxs-lookup"><span data-stu-id="6d97a-314">This logon tunnels from the Fabrikam.com domain used by the on-premises network to the contoso.local domain used by the SharePoint portal.</span></span> <span data-ttu-id="6d97a-315">SharePoint 站点打开后，便会出现根演示站点。</span><span class="sxs-lookup"><span data-stu-id="6d97a-315">When the SharePoint site opens, you'll see the root demo site.</span></span>

<span data-ttu-id="6d97a-316">**此参考体系结构的供稿人** &mdash; Joe Davies、Bob Fox、Neil Hodgkinson、Paul Stork</span><span class="sxs-lookup"><span data-stu-id="6d97a-316">**_Contributors to this reference architecture_** &mdash; Joe Davies, Bob Fox, Neil Hodgkinson, Paul Stork</span></span>

<!-- links -->

[availability-set]: /azure/virtual-machines/windows/manage-availability
[azure-portal]: https://portal.azure.com
[azure-ps]: /powershell/azure/overview
[azure-pricing]: https://azure.microsoft.com/pricing/calculator/
[bastion-host]: https://en.wikipedia.org/wiki/Bastion_host
[create-availability-group]: /SharePoint/administration/sharepoint-intranet-farm-in-azure-phase-5-create-the-availability-group-and-add
[connect-to-vm]: /azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine
[github]: https://github.com/mspnp/reference-architectures
[hybrid-ra]: ../hybrid-networking/index.md
[hybrid-vpn-ra]: ../hybrid-networking/vpn.md
[load-balancer]: /azure/load-balancer/load-balancer-internal-overview
[managed-disks]: /azure/storage/storage-managed-disks-overview
[minroles]: https://technet.microsoft.com/library/mt346114(v=office.16).aspx
[nsg]: /azure/virtual-network/virtual-networks-nsg
[office-web-apps]: https://support.microsoft.com/help/3199955/office-web-apps-and-office-online-server-supportability-in-azure
[paired-regions]: /azure/best-practices-availability-paired-regions
[readme]: https://github.com/mspnp/reference-architectures/tree/master/sharepoint/sharepoint-2016
[resource-group]: /azure/azure-resource-manager/resource-group-overview
[quotas]: /azure/azure-subscription-service-limits
[sharepoint-accounts]: https://technet.microsoft.com/library/ee662513(v=office.16).aspx
[sharepoint-crawling]: https://technet.microsoft.com/library/dn535606(v=office.16).aspx
[sharepoint-dr]: https://technet.microsoft.com/library/ff628971(v=office.16).aspx
[sharepoint-hybrid]: https://aka.ms/sphybrid
[sharepoint-minrole]: https://technet.microsoft.com/library/mt743705(v=office.16).aspx
[sharepoint-ops]: https://technet.microsoft.com/library/cc262289(v=office.16).aspx
[sharepoint-reqs]: https://technet.microsoft.com/library/cc262485(v=office.16).aspx
[sharepoint-search]: https://technet.microsoft.com/library/dn342836.aspx
[sql-always-on]: /sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server
[sql-performance]: /azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance
[sql-server-capacity-planning]: https://technet.microsoft.com/library/cc298801(v=office.16).aspx
[sql-quorum]: https://technet.microsoft.com/library/cc731739(v=ws.11).aspx
[sql-sharepoint-best-practices]: https://technet.microsoft.com/library/hh292622(v=office.16).aspx
[tempdb]: /sql/relational-databases/databases/tempdb-database
[virtual-networks-nsg]: /azure/virtual-network/virtual-networks-nsg
[visio-download]: https://archcenter.blob.core.windows.net/cdn/Sharepoint-2016.vsdx
[vm-sizes-general]: /azure/virtual-machines/windows/sizes-general
[vm-sizes-memory]: /azure/virtual-machines/windows/sizes-memory
[windows-n-tier]: ../virtual-machines-windows/n-tier.md
