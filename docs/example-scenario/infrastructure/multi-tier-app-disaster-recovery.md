---
title: 针对 HA/DR 生成的多层 Web 应用程序
titleSuffix: Azure Example Scenarios
description: 使用 Azure 虚拟机、可用性集、可用性区域、Azure Site Recovery 和 Azure 流量管理器在 Azure 上创建可实现高可用性和灾难恢复的多层 Web 应用程序。
author: sujayt
ms.date: 11/16/2018
ms.custom: product-team
ms.openlocfilehash: d2b466cc691c02ea3c5fb6ff72d91865d33c3378
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54114090"
---
# <a name="multitier-web-application-built-for-high-availability-and-disaster-recovery-on-azure"></a><span data-ttu-id="9b95c-103">Azure 上可实现高可用性和灾难恢复的多层 Web 应用程序</span><span class="sxs-lookup"><span data-stu-id="9b95c-103">Multitier web application built for high availability and disaster recovery on Azure</span></span>

<span data-ttu-id="9b95c-104">本示例方案适用于需要部署可实现高可用性和灾难恢复的可复原多层应用程序的任何行业。</span><span class="sxs-lookup"><span data-stu-id="9b95c-104">This example scenario is applicable to any industry that needs to deploy resilient multitier applications built for high availability and disaster recovery.</span></span> <span data-ttu-id="9b95c-105">在本方案中，应用程序由三个层组成。</span><span class="sxs-lookup"><span data-stu-id="9b95c-105">In this scenario, the application consists of three layers.</span></span>

- <span data-ttu-id="9b95c-106">Web 层：包含用户界面的最上层。</span><span class="sxs-lookup"><span data-stu-id="9b95c-106">Web tier: The top layer including the user interface.</span></span> <span data-ttu-id="9b95c-107">此层分析用户交互，并将操作传递到下一层进行处理。</span><span class="sxs-lookup"><span data-stu-id="9b95c-107">This layer parses user interactions and passes the actions to next layer for processing.</span></span>
- <span data-ttu-id="9b95c-108">业务层：处理用户交互，并针对后续步骤做出逻辑决策。</span><span class="sxs-lookup"><span data-stu-id="9b95c-108">Business tier: Processes the user interactions and makes logical decisions about the next steps.</span></span> <span data-ttu-id="9b95c-109">此层连接 Web 层和数据层。</span><span class="sxs-lookup"><span data-stu-id="9b95c-109">This layer connects the web tier and the data tier.</span></span>
- <span data-ttu-id="9b95c-110">数据层：存储应用程序数据。</span><span class="sxs-lookup"><span data-stu-id="9b95c-110">Data tier: Stores the application data.</span></span> <span data-ttu-id="9b95c-111">通常使用数据库、对象存储或文件存储。</span><span class="sxs-lookup"><span data-stu-id="9b95c-111">Either a database, object storage, or file storage is typically used.</span></span>

<span data-ttu-id="9b95c-112">常见应用程序方案包括 Windows 或 Linux 上运行的任何任务关键型应用程序。</span><span class="sxs-lookup"><span data-stu-id="9b95c-112">Common application scenarios include any mission critical application running on Windows or Linux.</span></span> <span data-ttu-id="9b95c-113">这可能是现成的应用程序（例如 SAP 和 SharePoint），也可能是自定义的业务线应用程序。</span><span class="sxs-lookup"><span data-stu-id="9b95c-113">This can be an off-the-shelf application such as SAP and SharePoint or a custom line-of-business application.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="9b95c-114">相关用例</span><span class="sxs-lookup"><span data-stu-id="9b95c-114">Relevant use cases</span></span>

<span data-ttu-id="9b95c-115">其他相关用例包括：</span><span class="sxs-lookup"><span data-stu-id="9b95c-115">Other relevant use cases include:</span></span>

- <span data-ttu-id="9b95c-116">部署 SAP 和 SharePoint 等高度可复原的应用程序</span><span class="sxs-lookup"><span data-stu-id="9b95c-116">Deploying highly resilient applications such as SAP and SharePoint</span></span>
- <span data-ttu-id="9b95c-117">为业务线应用程序设计业务连续性和灾难恢复计划</span><span class="sxs-lookup"><span data-stu-id="9b95c-117">Designing a business continuity and disaster recovery plan for line-of-business applications</span></span>
- <span data-ttu-id="9b95c-118">配置灾难恢复并出于合规目的执行相关的演练</span><span class="sxs-lookup"><span data-stu-id="9b95c-118">Configure disaster recovery and perform related drills for compliance purposes</span></span>

## <a name="architecture"></a><span data-ttu-id="9b95c-119">体系结构</span><span class="sxs-lookup"><span data-stu-id="9b95c-119">Architecture</span></span>

<span data-ttu-id="9b95c-120">本方案演示一个使用 ASP.NET 和 Microsoft SQL Server 的多层应用程序。</span><span class="sxs-lookup"><span data-stu-id="9b95c-120">This scenario demonstrates a multitier application that uses ASP.NET and Microsoft SQL Server.</span></span> <span data-ttu-id="9b95c-121">在[支持可用性区域的 Azure 区域](/azure/availability-zones/az-overview#regions-that-support-availability-zones)中，可以在跨可用性区域的源区域中部署虚拟机 (VM)，并将 VM 复制到用于灾难恢复的目标区域。</span><span class="sxs-lookup"><span data-stu-id="9b95c-121">In [Azure regions that support availability zones](/azure/availability-zones/az-overview#regions-that-support-availability-zones), you can deploy your virtual machines (VMs) in a source region across availability zones and replicate the VMs to the target region used for disaster recovery.</span></span> <span data-ttu-id="9b95c-122">在不支持可用性区域的 Azure 区域中，可以在可用性集内部署 VM，并将 VM 复制到目标区域。</span><span class="sxs-lookup"><span data-stu-id="9b95c-122">In Azure regions that don't support availability zones, you can deploy your VMs within an availability set and replicate the VMs to the target region.</span></span>

![高度可复原的多层 Web 应用程序的体系结构概览][architecture]

- <span data-ttu-id="9b95c-124">在支持局部区域的区域中，将每个层中的 VM 分布在两个可用性区域之间。</span><span class="sxs-lookup"><span data-stu-id="9b95c-124">Distribute the VMs in each tier across two availability zones in regions that support zones.</span></span> <span data-ttu-id="9b95c-125">在其他区域，将每个层中的 VM 部署到一个可用性集内。</span><span class="sxs-lookup"><span data-stu-id="9b95c-125">In other regions, deploy the VMs in each tier within one availability set.</span></span>
- <span data-ttu-id="9b95c-126">可以将数据库层配置为使用 Always On 可用性组。</span><span class="sxs-lookup"><span data-stu-id="9b95c-126">The database tier can be configured to use Always On availability groups.</span></span> <span data-ttu-id="9b95c-127">使用此 SQL Server 配置时，最多可以为群集中的一个主数据库配置八个辅助数据库。</span><span class="sxs-lookup"><span data-stu-id="9b95c-127">With this SQL Server configuration, one primary database within a cluster is configured with up to eight secondary databases.</span></span> <span data-ttu-id="9b95c-128">如果主数据库发生问题，群集可以故障转移到某个辅助数据库，使应用程序保持可用。</span><span class="sxs-lookup"><span data-stu-id="9b95c-128">If an issue occurs with the primary database, the cluster fails over to one of the secondary databases, allowing the application to remain available.</span></span> <span data-ttu-id="9b95c-129">有关详细信息，请参阅[适用于 SQL Server 的 Always On 可用性组概述][docs-sql-always-on]。</span><span class="sxs-lookup"><span data-stu-id="9b95c-129">For more information, see [Overview of Always On availability groups for SQL Server][docs-sql-always-on].</span></span>
- <span data-ttu-id="9b95c-130">对于灾难恢复方案，可以配置 SQL Always On 异步本机复制，以复制到用于灾难恢复的目标区域。</span><span class="sxs-lookup"><span data-stu-id="9b95c-130">For disaster recovery scenarios, you can configure SQL Always On asynchronous native replication to the target region used for disaster recovery.</span></span> <span data-ttu-id="9b95c-131">还可以配置 Azure Site Recovery 复制，以便在数据更改率处于 Azure Site recovery 支持的限制范围内时复制到目标区域。</span><span class="sxs-lookup"><span data-stu-id="9b95c-131">You can also configure Azure Site Recovery replication to the target region if the data change rate is within supported limits of Azure Site Recovery.</span></span>
- <span data-ttu-id="9b95c-132">用户通过流量管理器终结点访问前端 ASP.NET Web 层。</span><span class="sxs-lookup"><span data-stu-id="9b95c-132">Users access the front-end ASP.NET web tier via the traffic manager endpoint.</span></span>
- <span data-ttu-id="9b95c-133">流量管理器将流量重定向到主要源区域中的主要公共 IP 终结点。</span><span class="sxs-lookup"><span data-stu-id="9b95c-133">The traffic manager redirects traffic to the primary public IP endpoint in the primary source region.</span></span>
- <span data-ttu-id="9b95c-134">公共 IP 通过公共负载均衡器将调用重定向到某个 Web 层 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="9b95c-134">The public IP redirects the call to one of the web tier VM instances through a public load balancer.</span></span> <span data-ttu-id="9b95c-135">所有 Web 层 VM 实例位于一个子网中。</span><span class="sxs-lookup"><span data-stu-id="9b95c-135">All web tier VM instances are in one subnet.</span></span>
- <span data-ttu-id="9b95c-136">在 Web 层 VM 中，每个调用将通过内部负载均衡器路由到业务层中的某个 VM 实例进行处理。</span><span class="sxs-lookup"><span data-stu-id="9b95c-136">From the web tier VM, each call is routed to one of the VM instances in the business tier through an internal load balancer for processing.</span></span> <span data-ttu-id="9b95c-137">所有业务层 VM 位于独立的子网中。</span><span class="sxs-lookup"><span data-stu-id="9b95c-137">All business tier VMs are in a separate subnet.</span></span>
- <span data-ttu-id="9b95c-138">操作将在业务层中进行处理，ASP.NET 应用程序通过 Azure 内部负载均衡器连接到后端层中的 Microsoft SQL Server 群集。</span><span class="sxs-lookup"><span data-stu-id="9b95c-138">The operation is processed in the business tier and the ASP.NET application connects to Microsoft SQL Server cluster in a back-end tier via an Azure internal load balancer.</span></span> <span data-ttu-id="9b95c-139">这些后端 SQL Server 实例位于独立的子网中。</span><span class="sxs-lookup"><span data-stu-id="9b95c-139">These back-end SQL Server instances are in a separate subnet.</span></span>
- <span data-ttu-id="9b95c-140">流量管理器的辅助终结点配置为用于灾难恢复的目标区域中的公共 IP。</span><span class="sxs-lookup"><span data-stu-id="9b95c-140">The traffic manager's secondary endpoint is configured as the public IP in the target region used for disaster recovery.</span></span>
- <span data-ttu-id="9b95c-141">如果主要区域发生中断，则你可以调用 Azure Site Recovery 故障转移，应用程序将在目标区域中变为活动状态。</span><span class="sxs-lookup"><span data-stu-id="9b95c-141">In the event of a primary region disruption, you invoke Azure Site Recovery failover and the application becomes active in the target region.</span></span>
- <span data-ttu-id="9b95c-142">流量管理器终结点自动将客户端流量重定向到目标区域中的公共 IP。</span><span class="sxs-lookup"><span data-stu-id="9b95c-142">The traffic manager endpoint automatically redirects the client traffic to the public IP in the target region.</span></span>

### <a name="components"></a><span data-ttu-id="9b95c-143">组件</span><span class="sxs-lookup"><span data-stu-id="9b95c-143">Components</span></span>

- <span data-ttu-id="9b95c-144">[可用性集][docs-availability-sets]确保在 Azure 上部署的 VM 能够跨群集中多个隔离的硬件节点分布。</span><span class="sxs-lookup"><span data-stu-id="9b95c-144">[Availability sets][docs-availability-sets] ensure that the VMs you deploy on Azure are distributed across multiple isolated hardware nodes in a cluster.</span></span> <span data-ttu-id="9b95c-145">如果 Azure 中发生硬件或软件故障，只有一部分 VM 会受到影响，而整个解决方案将保持可用且正常运行。</span><span class="sxs-lookup"><span data-stu-id="9b95c-145">If a hardware or software failure occurs within Azure, only a subset of your VMs are affected and your entire solution remains available and operational.</span></span>
- <span data-ttu-id="9b95c-146">数据中心发生故障时，[可用性区域][docs-availability-zones]可以保护应用程序和数据。</span><span class="sxs-lookup"><span data-stu-id="9b95c-146">[Availability zones][docs-availability-zones] protect your applications and data from datacenter failures.</span></span> <span data-ttu-id="9b95c-147">可用性区域是 Azure 区域中独立的物理位置。</span><span class="sxs-lookup"><span data-stu-id="9b95c-147">Availability zones are separate physical locations within an Azure region.</span></span> <span data-ttu-id="9b95c-148">每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源以及散热和网络设备。</span><span class="sxs-lookup"><span data-stu-id="9b95c-148">Each zone consists of one or more datacenters equipped with independent power, cooling, and networking.</span></span>
- <span data-ttu-id="9b95c-149">使用 [Azure Site Recovery (ASR)][docs-azure-site-recovery] 可将 VM 复制到另一个 Azure 区域以满足业务连续性和灾难恢复需求。</span><span class="sxs-lookup"><span data-stu-id="9b95c-149">[Azure Site Recovery (ASR)][docs-azure-site-recovery] allows you to replicate VMs to another Azure region for business continuity and disaster recovery needs.</span></span> <span data-ttu-id="9b95c-150">可以定期执行灾难恢复演练来确保满足合规需求。</span><span class="sxs-lookup"><span data-stu-id="9b95c-150">You can conduct periodic disaster recovery drills to ensure you meet the compliance needs.</span></span> <span data-ttu-id="9b95c-151">源区域中发生故障时，VM 将按照指定的设置复制到所选区域，以便恢复应用程序。</span><span class="sxs-lookup"><span data-stu-id="9b95c-151">The VM will be replicated with the specified settings to the selected region so that you can recover your applications in the event of outages in the source region.</span></span>
- <span data-ttu-id="9b95c-152">[Azure 流量管理器][docs-traffic-manager]是基于 DNS 的流量负载均衡器，可在全球 Azure 区域之间以最佳方式向服务分配流量，同时提供高可用性和响应能力。</span><span class="sxs-lookup"><span data-stu-id="9b95c-152">[Azure Traffic Manager][docs-traffic-manager] is a DNS-based traffic load balancer that distributes traffic optimally to services across global Azure regions while providing high availability and responsiveness.</span></span>
- <span data-ttu-id="9b95c-153">[Azure 负载均衡器][docs-load-balancer]根据定义的规则和运行状况探测分配入站流量。</span><span class="sxs-lookup"><span data-stu-id="9b95c-153">[Azure Load Balancer][docs-load-balancer] distributes inbound traffic according to defined rules and health probes.</span></span> <span data-ttu-id="9b95c-154">负载均衡器提供低延迟和高吞吐量，可纵向扩展到数百万个流，以满足所有 TCP 和 UDP 应用程序的需求。</span><span class="sxs-lookup"><span data-stu-id="9b95c-154">A load balancer provides low latency and high throughput, scaling up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="9b95c-155">本方案使用公共负载均衡器将传入的客户端流量分配到 Web 层。</span><span class="sxs-lookup"><span data-stu-id="9b95c-155">A public load balancer is used in this scenario to distribute incoming client traffic to the web tier.</span></span> <span data-ttu-id="9b95c-156">本方案使用内部负载均衡器将来自业务层的流量分配到后端 SQL Server 群集。</span><span class="sxs-lookup"><span data-stu-id="9b95c-156">An internal load balancer is used in this scenario to distribute traffic from the business tier to the back-end SQL Server cluster.</span></span>

### <a name="alternatives"></a><span data-ttu-id="9b95c-157">备选项</span><span class="sxs-lookup"><span data-stu-id="9b95c-157">Alternatives</span></span>

- <span data-ttu-id="9b95c-158">可以使用其他操作系统来替换 Windows，因为基础结构中没有任何组件依赖于操作系统。</span><span class="sxs-lookup"><span data-stu-id="9b95c-158">Windows can be replaced by other operating systems because nothing in the infrastructure is dependent on the operating system.</span></span>
- <span data-ttu-id="9b95c-159">[适用于 Linux 的 SQL Server][docs-sql-server-linux] 可以替代后端数据存储。</span><span class="sxs-lookup"><span data-stu-id="9b95c-159">[SQL Server for Linux][docs-sql-server-linux] can replace the back-end data store.</span></span>
- <span data-ttu-id="9b95c-160">可以使用任何可用的标准数据库应用程序替换数据库。</span><span class="sxs-lookup"><span data-stu-id="9b95c-160">The database can be replaced by any standard database application available.</span></span>

## <a name="other-considerations"></a><span data-ttu-id="9b95c-161">其他注意事项</span><span class="sxs-lookup"><span data-stu-id="9b95c-161">Other considerations</span></span>

### <a name="scalability"></a><span data-ttu-id="9b95c-162">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="9b95c-162">Scalability</span></span>

<span data-ttu-id="9b95c-163">可以根据缩放要求，在每个层中添加或删除 VM。</span><span class="sxs-lookup"><span data-stu-id="9b95c-163">You can add or remove VMs in each tier based on your scaling requirements.</span></span> <span data-ttu-id="9b95c-164">由于本方案使用负载均衡器，因此你可以在某个层中添加更多的 VM，而不会影响应用程序的运行时间。</span><span class="sxs-lookup"><span data-stu-id="9b95c-164">Because this scenario uses load balancers, you can add more VMs to a tier without affecting application uptime.</span></span>

<span data-ttu-id="9b95c-165">若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="9b95c-165">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="9b95c-166">安全</span><span class="sxs-lookup"><span data-stu-id="9b95c-166">Security</span></span>

<span data-ttu-id="9b95c-167">进入前端应用层的所有虚拟网络流量受网络安全组的保护。</span><span class="sxs-lookup"><span data-stu-id="9b95c-167">All the virtual network traffic into the front-end application tier is protected by network security groups.</span></span> <span data-ttu-id="9b95c-168">规则会限制流量的流动，只有前端应用程序层 VM 实例可以访问后端数据库层。</span><span class="sxs-lookup"><span data-stu-id="9b95c-168">Rules limit the flow of traffic so that only the front-end application tier VM instances can access the back-end database tier.</span></span> <span data-ttu-id="9b95c-169">不允许业务层或数据库层发出的出站 Internet 流量。</span><span class="sxs-lookup"><span data-stu-id="9b95c-169">No outbound internet traffic is allowed from the business tier or database tier.</span></span> <span data-ttu-id="9b95c-170">为了减少受攻击面，请勿打开直接远程管理端口。</span><span class="sxs-lookup"><span data-stu-id="9b95c-170">To reduce the attack footprint, no direct remote management ports are open.</span></span> <span data-ttu-id="9b95c-171">有关详细信息，请参阅 [Azure 网络安全组][docs-nsg]。</span><span class="sxs-lookup"><span data-stu-id="9b95c-171">For more information, see [Azure network security groups][docs-nsg].</span></span>

<span data-ttu-id="9b95c-172">若需安全方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="9b95c-172">For general guidance on designing secure scenarios, see the [Azure Security Documentation][security].</span></span>

## <a name="pricing"></a><span data-ttu-id="9b95c-173">定价</span><span class="sxs-lookup"><span data-stu-id="9b95c-173">Pricing</span></span>

<span data-ttu-id="9b95c-174">使用 Azure Site Recovery 为 Azure VM 配置灾难恢复会持续产生以下费用。</span><span class="sxs-lookup"><span data-stu-id="9b95c-174">Configuring disaster recovery for Azure VMs using Azure Site Recovery will incur the following charges on an ongoing basis.</span></span>

- <span data-ttu-id="9b95c-175">每个 VM 的 Azure Site Recovery 许可费用。</span><span class="sxs-lookup"><span data-stu-id="9b95c-175">Azure Site Recovery licensing cost per VM.</span></span>
- <span data-ttu-id="9b95c-176">将数据更改从源 VM 磁盘复制到另一个 Azure 区域时产生的网络出口费用。</span><span class="sxs-lookup"><span data-stu-id="9b95c-176">Network egress costs to replicate data changes from the source VM disks to another Azure region.</span></span> <span data-ttu-id="9b95c-177">Azure Site Recovery 使用内置压缩，可将数据传输要求减少约 50%。</span><span class="sxs-lookup"><span data-stu-id="9b95c-177">Azure Site Recovery uses built-in compression to reduce the data transfer requirements by approximately 50%.</span></span>
- <span data-ttu-id="9b95c-178">恢复站点上的存储费用。</span><span class="sxs-lookup"><span data-stu-id="9b95c-178">Storage costs on the recovery site.</span></span> <span data-ttu-id="9b95c-179">此存储通常相当于源区域存储加上将恢复点保留为恢复快照所需的任何附加存储。</span><span class="sxs-lookup"><span data-stu-id="9b95c-179">This is typically the same as the source region storage plus any additional storage needed to maintain the recovery points as snapshots for recovery.</span></span>

<span data-ttu-id="9b95c-180">我们提供了一个[示例成本计算器][calculator]，用于计算使用六个虚拟机为某个三层应用程序配置灾难恢复所需的费用。</span><span class="sxs-lookup"><span data-stu-id="9b95c-180">We have provided a [sample cost calculator][calculator] for configuring disaster recovery for a three-tier application using six virtual machines.</span></span> <span data-ttu-id="9b95c-181">成本计算器中已预配置所有服务。</span><span class="sxs-lookup"><span data-stu-id="9b95c-181">All of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="9b95c-182">若要了解特定用例的定价变化情况，请更改相应的变量来估算费用。</span><span class="sxs-lookup"><span data-stu-id="9b95c-182">To see how the pricing would change for your particular use case, change the appropriate variables to estimate the cost.</span></span>

<!-- links -->
[architecture]: ./media/arhitecture-disaster-recovery-multi-tier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: ../../checklist/availability.md
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[docs-availability-zones]: /azure/availability-zones/az-overview
[docs-load-balancer]: /azure/load-balancer/load-balancer-overview
[docs-nsg]: /azure/virtual-network/security-overview
[docs-vmss]: /azure/virtual-machine-scale-sets/overview
[docs-sql-always-on]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[docs-vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[docs-vnet]: /azure/virtual-network/virtual-networks-overview
[docs-sql-server-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[docs-traffic-manager]: /azure/traffic-manager/
[docs-azure-site-recovery]: /azure/site-recovery/azure-to-azure-quickstart/
[docs-availability-sets]: /azure/virtual-machines/windows/manage-availability/
[calculator]: https://azure.com/e/6835332265044d6d931d68c917979e6d/
