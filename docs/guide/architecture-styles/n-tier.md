---
title: N 层体系结构样式
titleSuffix: Azure Application Architecture Guide
description: 介绍 Azure 上 N 层体系结构的优点、挑战和最佳做法。
author: MikeWasson
ms.date: 08/30/2018
ms.custom: seojan19
ms.openlocfilehash: 4e8aae0032d20df05e1b16a47fda4afa720ed0d9
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54110299"
---
# <a name="n-tier-architecture-style"></a><span data-ttu-id="35601-103">N 层体系结构样式</span><span class="sxs-lookup"><span data-stu-id="35601-103">N-tier architecture style</span></span>

<span data-ttu-id="35601-104">N 层体系结构将应用程序分成逻辑层和物理层。</span><span class="sxs-lookup"><span data-stu-id="35601-104">An N-tier architecture divides an application into **logical layers** and **physical tiers**.</span></span>

![N 层体系结构样式的逻辑图](./images/n-tier-logical.svg)

<span data-ttu-id="35601-106">层是分离职责和管理依赖关系的方式。</span><span class="sxs-lookup"><span data-stu-id="35601-106">Layers are a way to separate responsibilities and manage dependencies.</span></span> <span data-ttu-id="35601-107">每个层都有特定的责任。</span><span class="sxs-lookup"><span data-stu-id="35601-107">Each layer has a specific responsibility.</span></span> <span data-ttu-id="35601-108">较高层可使用较低层中的服务，反之则不行。</span><span class="sxs-lookup"><span data-stu-id="35601-108">A higher layer can use services in a lower layer, but not the other way around.</span></span>

<span data-ttu-id="35601-109">层在物理上是分隔开的，在不同的计算机上运行。</span><span class="sxs-lookup"><span data-stu-id="35601-109">Tiers are physically separated, running on separate machines.</span></span> <span data-ttu-id="35601-110">一个层可直接调用另一个层，或使用异步消息传递（消息队列）。</span><span class="sxs-lookup"><span data-stu-id="35601-110">A tier can call to another tier directly, or use asynchronous messaging (message queue).</span></span> <span data-ttu-id="35601-111">虽然每个层可能托管在自己的层中，但这并不是必需的。</span><span class="sxs-lookup"><span data-stu-id="35601-111">Although each layer might be hosted in its own tier, that's not required.</span></span> <span data-ttu-id="35601-112">多个层可能托管在同一层上。</span><span class="sxs-lookup"><span data-stu-id="35601-112">Several layers might be hosted on the same tier.</span></span> <span data-ttu-id="35601-113">在物理上分隔层可以提高可伸缩性和复原能力，但因额外的网络通信也增加了延迟。</span><span class="sxs-lookup"><span data-stu-id="35601-113">Physically separating the tiers improves scalability and resiliency, but also adds latency from the additional network communication.</span></span>

<span data-ttu-id="35601-114">传统的三层应用程序有表示层、中间层和数据库层。</span><span class="sxs-lookup"><span data-stu-id="35601-114">A traditional three-tier application has a presentation tier, a middle tier, and a database tier.</span></span> <span data-ttu-id="35601-115">中间层是可选的。</span><span class="sxs-lookup"><span data-stu-id="35601-115">The middle tier is optional.</span></span> <span data-ttu-id="35601-116">更复杂的应用程序可以多于三层。</span><span class="sxs-lookup"><span data-stu-id="35601-116">More complex applications can have more than three tiers.</span></span> <span data-ttu-id="35601-117">上图显示具有两个中间层，且封装了不同功能区域的应用程序。</span><span class="sxs-lookup"><span data-stu-id="35601-117">The diagram above shows an application with two middle tiers, encapsulating different areas of functionality.</span></span>

<span data-ttu-id="35601-118">N 层应用程序可以有封闭的层体系结构或开放的层体系结构：</span><span class="sxs-lookup"><span data-stu-id="35601-118">An N-tier application can have a **closed layer architecture** or an **open layer architecture**:</span></span>

- <span data-ttu-id="35601-119">在封闭的层体系结构中，层只能调用紧邻的下一层。</span><span class="sxs-lookup"><span data-stu-id="35601-119">In a closed layer architecture, a layer can only call the next layer immediately down.</span></span>
- <span data-ttu-id="35601-120">在开放的层体系结构中，层可以调用它下面的任何层。</span><span class="sxs-lookup"><span data-stu-id="35601-120">In an open layer architecture, a layer can call any of the layers below it.</span></span>

<span data-ttu-id="35601-121">封闭的层体系结构限制层之间的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="35601-121">A closed layer architecture limits the dependencies between layers.</span></span> <span data-ttu-id="35601-122">但是，如果一个层仅将请求传递到下一层，可能会产生不必要的流量。</span><span class="sxs-lookup"><span data-stu-id="35601-122">However, it might create unnecessary network traffic, if one layer simply passes requests along to the next layer.</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="35601-123">何时使用此架构</span><span class="sxs-lookup"><span data-stu-id="35601-123">When to use this architecture</span></span>

<span data-ttu-id="35601-124">N 层体系结构通常作为服务架构 (IaaS) 应用程序实现，每个层都在独立的 VM 集中运行。</span><span class="sxs-lookup"><span data-stu-id="35601-124">N-tier architectures are typically implemented as infrastructure-as-service (IaaS) applications, with each tier running on a separate set of VMs.</span></span> <span data-ttu-id="35601-125">然而，N 层应用程序不需要只是 IaaS。</span><span class="sxs-lookup"><span data-stu-id="35601-125">However, an N-tier application doesn't need to be pure IaaS.</span></span> <span data-ttu-id="35601-126">通常，对体系结构的某些部分使用托管服务是有利的，特别是缓存、消息传递和数据存储。</span><span class="sxs-lookup"><span data-stu-id="35601-126">Often, it's advantageous to use managed services for some parts of the architecture, particularly caching, messaging, and data storage.</span></span>

<span data-ttu-id="35601-127">请考虑将 N 层体系结构用于：</span><span class="sxs-lookup"><span data-stu-id="35601-127">Consider an N-tier architecture for:</span></span>

- <span data-ttu-id="35601-128">简单的 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="35601-128">Simple web applications.</span></span>
- <span data-ttu-id="35601-129">将本地应用程序迁移到 Azure 并进行最小的重构。</span><span class="sxs-lookup"><span data-stu-id="35601-129">Migrating an on-premises application to Azure with minimal refactoring.</span></span>
- <span data-ttu-id="35601-130">统一开发本地和云应用程序。</span><span class="sxs-lookup"><span data-stu-id="35601-130">Unified development of on-premises and cloud applications.</span></span>

<span data-ttu-id="35601-131">N 层体系结构在传统的本地应用程序中很常见，因此将现有工作负载迁移到 Azure 是很适合的。</span><span class="sxs-lookup"><span data-stu-id="35601-131">N-tier architectures are very common in traditional on-premises applications, so it's a natural fit for migrating existing workloads to Azure.</span></span>

## <a name="benefits"></a><span data-ttu-id="35601-132">优点</span><span class="sxs-lookup"><span data-stu-id="35601-132">Benefits</span></span>

- <span data-ttu-id="35601-133">云与本地之间，云平台之间具有可移植性。</span><span class="sxs-lookup"><span data-stu-id="35601-133">Portability between cloud and on-premises, and between cloud platforms.</span></span>
- <span data-ttu-id="35601-134">对于大多数开发者来说，学习曲线较少。</span><span class="sxs-lookup"><span data-stu-id="35601-134">Less learning curve for most developers.</span></span>
- <span data-ttu-id="35601-135">从传统应用程序模型自然演变。</span><span class="sxs-lookup"><span data-stu-id="35601-135">Natural evolution from the traditional application model.</span></span>
- <span data-ttu-id="35601-136">对异构环境 (Windows/Linux) 开放</span><span class="sxs-lookup"><span data-stu-id="35601-136">Open to heterogeneous environment (Windows/Linux)</span></span>

## <a name="challenges"></a><span data-ttu-id="35601-137">挑战</span><span class="sxs-lookup"><span data-stu-id="35601-137">Challenges</span></span>

- <span data-ttu-id="35601-138">很容易最终得到一个只在数据库上执行CRUD操作的中间层，在不做任何有用工作的情况下增加额外的延迟。</span><span class="sxs-lookup"><span data-stu-id="35601-138">It's easy to end up with a middle tier that just does CRUD operations on the database, adding extra latency without doing any useful work.</span></span>
- <span data-ttu-id="35601-139">单一式设计阻止了独立部署各项功能。</span><span class="sxs-lookup"><span data-stu-id="35601-139">Monolithic design prevents independent deployment of features.</span></span>
- <span data-ttu-id="35601-140">管理 IaaS 应用程序的工作量要大于管理只使用托管服务的应用程序。</span><span class="sxs-lookup"><span data-stu-id="35601-140">Managing an IaaS application is more work than an application that uses only managed services.</span></span>
- <span data-ttu-id="35601-141">管理大型系统中的网络安全比较困难。</span><span class="sxs-lookup"><span data-stu-id="35601-141">It can be difficult to manage network security in a large system.</span></span>

## <a name="best-practices"></a><span data-ttu-id="35601-142">最佳做法</span><span class="sxs-lookup"><span data-stu-id="35601-142">Best practices</span></span>

- <span data-ttu-id="35601-143">使用自动缩放处理负载中的更改。</span><span class="sxs-lookup"><span data-stu-id="35601-143">Use autoscaling to handle changes in load.</span></span> <span data-ttu-id="35601-144">请参阅[自动缩放的最佳做法][autoscaling]。</span><span class="sxs-lookup"><span data-stu-id="35601-144">See [Autoscaling best practices][autoscaling].</span></span>
- <span data-ttu-id="35601-145">使用异步消息传递来分离层。</span><span class="sxs-lookup"><span data-stu-id="35601-145">Use asynchronous messaging to decouple tiers.</span></span>
- <span data-ttu-id="35601-146">缓存半静态数据。</span><span class="sxs-lookup"><span data-stu-id="35601-146">Cache semi-static data.</span></span> <span data-ttu-id="35601-147">请参阅[缓存的最佳做法][caching]。</span><span class="sxs-lookup"><span data-stu-id="35601-147">See [Caching best practices][caching].</span></span>
- <span data-ttu-id="35601-148">请使用 [SQL Server Always On 可用性组][sql-always-on]等解决方案配置高可用性的数据层。</span><span class="sxs-lookup"><span data-stu-id="35601-148">Configure database tier for high availability, using a solution such as [SQL Server Always On Availability Groups][sql-always-on].</span></span>
- <span data-ttu-id="35601-149">在前端和 Internet 之间放置 Web 应用程序防火墙 (WAF)。</span><span class="sxs-lookup"><span data-stu-id="35601-149">Place a web application firewall (WAF) between the front end and the Internet.</span></span>
- <span data-ttu-id="35601-150">将每个层放置在自己的子网中，并将子网用作安全边界。</span><span class="sxs-lookup"><span data-stu-id="35601-150">Place each tier in its own subnet, and use subnets as a security boundary.</span></span>
- <span data-ttu-id="35601-151">通过仅允许来自中间层的请求，限制对数据层的访问。</span><span class="sxs-lookup"><span data-stu-id="35601-151">Restrict access to the data tier, by allowing requests only from the middle tier(s).</span></span>

## <a name="n-tier-architecture-on-virtual-machines"></a><span data-ttu-id="35601-152">虚拟机上的 N 层体系结构</span><span class="sxs-lookup"><span data-stu-id="35601-152">N-tier architecture on virtual machines</span></span>

<span data-ttu-id="35601-153">本部分介绍在 VM 上运行的建议的 N 层体系结构。</span><span class="sxs-lookup"><span data-stu-id="35601-153">This section describes a recommended N-tier architecture running on VMs.</span></span>

![N 层体系结构的物理图](./images/n-tier-physical.png)

<span data-ttu-id="35601-155">每个层包含两个或多个 VM，它们放置在可用性集或 VM 规模集中。</span><span class="sxs-lookup"><span data-stu-id="35601-155">Each tier consists of two or more VMs, placed in an availability set or VM scale set.</span></span> <span data-ttu-id="35601-156">如果一个 VM 失败，多个 VM 可以提供复原能力。</span><span class="sxs-lookup"><span data-stu-id="35601-156">Multiple VMs provide resiliency in case one VM fails.</span></span> <span data-ttu-id="35601-157">负载均衡器用于将请求分布到一个层中的 VM 上。</span><span class="sxs-lookup"><span data-stu-id="35601-157">Load balancers are used to distribute requests across the VMs in a tier.</span></span> <span data-ttu-id="35601-158">通过向池添加更多 VM 可以水平缩放层。</span><span class="sxs-lookup"><span data-stu-id="35601-158">A tier can be scaled horizontally by adding more VMs to the pool.</span></span>

<span data-ttu-id="35601-159">每个层也放置在自己的子网中，这意味着它们的内部 IP 地址在同一个地址范围内。</span><span class="sxs-lookup"><span data-stu-id="35601-159">Each tier is also placed inside its own subnet, meaning their internal IP addresses fall within the same address range.</span></span> <span data-ttu-id="35601-160">这样可以容易地应用网络安全组 (NSG) 规则，并将表路由到各个层。</span><span class="sxs-lookup"><span data-stu-id="35601-160">That makes it easy to apply network security group (NSG) rules and route tables to individual tiers.</span></span>

<span data-ttu-id="35601-161">Web 和业务层是无状态的。</span><span class="sxs-lookup"><span data-stu-id="35601-161">The web and business tiers are stateless.</span></span> <span data-ttu-id="35601-162">任何 VM 都可以处理该层的任何请求。</span><span class="sxs-lookup"><span data-stu-id="35601-162">Any VM can handle any request for that tier.</span></span> <span data-ttu-id="35601-163">数据层应该包含复制的数据库。</span><span class="sxs-lookup"><span data-stu-id="35601-163">The data tier should consist of a replicated database.</span></span> <span data-ttu-id="35601-164">对于 Windows，我们推荐 SQL Server，使用 Always On 可用性组实现高可用性。</span><span class="sxs-lookup"><span data-stu-id="35601-164">For Windows, we recommend SQL Server, using Always On Availability Groups for high availability.</span></span> <span data-ttu-id="35601-165">对于 Linux，请选择支持复制的数据库，例如 Apache Cassandra。</span><span class="sxs-lookup"><span data-stu-id="35601-165">For Linux, choose a database that supports replication, such as Apache Cassandra.</span></span>

<span data-ttu-id="35601-166">网络安全组 (NSG) 限制对每个层的访问。</span><span class="sxs-lookup"><span data-stu-id="35601-166">Network Security Groups (NSGs) restrict access to each tier.</span></span> <span data-ttu-id="35601-167">例如，数据库层仅允许来自业务层的访问。</span><span class="sxs-lookup"><span data-stu-id="35601-167">For example, the database tier only allows access from the business tier.</span></span>

<span data-ttu-id="35601-168">有关更多详细信息和可部署的资源管理器模板，请参阅以下参考体系结构：</span><span class="sxs-lookup"><span data-stu-id="35601-168">For more details and a deployable Resource Manager template, see the following reference architectures:</span></span>

- <span data-ttu-id="35601-169">[运行用于 N 层应用程序的 Windows VM][n-tier-windows]</span><span class="sxs-lookup"><span data-stu-id="35601-169">[Run Windows VMs for an N-tier application][n-tier-windows]</span></span>
- <span data-ttu-id="35601-170">[运行用于 N 层应用程序的 Linux VM][n-tier-linux]</span><span class="sxs-lookup"><span data-stu-id="35601-170">[Run Linux VMs for an N-tier application][n-tier-linux]</span></span>

### <a name="additional-considerations"></a><span data-ttu-id="35601-171">其他注意事项</span><span class="sxs-lookup"><span data-stu-id="35601-171">Additional considerations</span></span>

- <span data-ttu-id="35601-172">N 层体系结构不限于三层。</span><span class="sxs-lookup"><span data-stu-id="35601-172">N-tier architectures are not restricted to three tiers.</span></span> <span data-ttu-id="35601-173">对于更复杂的应用程序，通常会有更多层。</span><span class="sxs-lookup"><span data-stu-id="35601-173">For more complex applications, it is common to have more tiers.</span></span> <span data-ttu-id="35601-174">在这种情况下，请考虑使用第 7 层路由将请求路由到特定的层。</span><span class="sxs-lookup"><span data-stu-id="35601-174">In that case, consider using layer-7 routing to route requests to a particular tier.</span></span>

- <span data-ttu-id="35601-175">层是可伸缩性、可靠性和安全性的边界。</span><span class="sxs-lookup"><span data-stu-id="35601-175">Tiers are the boundary of scalability, reliability, and security.</span></span> <span data-ttu-id="35601-176">请考虑为这些区域中有不同需求的服务提供单独的层。</span><span class="sxs-lookup"><span data-stu-id="35601-176">Consider having separate tiers for services with different requirements in those areas.</span></span>

- <span data-ttu-id="35601-177">使用 VM 规模集进行自动缩放。</span><span class="sxs-lookup"><span data-stu-id="35601-177">Use VM Scale Sets for autoscaling.</span></span>

- <span data-ttu-id="35601-178">在体系结构中寻找可以使用托管服务而无需进行大量重构的位置。</span><span class="sxs-lookup"><span data-stu-id="35601-178">Look for places in the architecture where you can use a managed service without significant refactoring.</span></span> <span data-ttu-id="35601-179">具体来说，就是缓存、消息传递、存储和数据库。</span><span class="sxs-lookup"><span data-stu-id="35601-179">In particular, look at caching, messaging, storage, and databases.</span></span>

- <span data-ttu-id="35601-180">为了提高安全性，请在应用程序前放置网络 DMZ。</span><span class="sxs-lookup"><span data-stu-id="35601-180">For higher security, place a network DMZ in front of the application.</span></span> <span data-ttu-id="35601-181">DMZ 包括防火墙和数据包检查等实现安全功能的网络虚拟设备 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="35601-181">The DMZ includes network virtual appliances (NVAs) that implement security functionality such as firewalls and packet inspection.</span></span> <span data-ttu-id="35601-182">有关详细信息，请参阅[网络 DMZ 参考体系结构][dmz]。</span><span class="sxs-lookup"><span data-stu-id="35601-182">For more information, see [Network DMZ reference architecture][dmz].</span></span>

- <span data-ttu-id="35601-183">为实现高可用性，请在可用性集中放置两个或多个 NVA，并使用外部负载均衡器在实例间分布 Internet 请求。</span><span class="sxs-lookup"><span data-stu-id="35601-183">For high availability, place two or more NVAs in an availability set, with an external load balancer to distribute Internet requests across the instances.</span></span> <span data-ttu-id="35601-184">有关详细信息，请参阅[部署高可用性网络虚拟设备][ha-nva]。</span><span class="sxs-lookup"><span data-stu-id="35601-184">For more information, see [Deploy highly available network virtual appliances][ha-nva].</span></span>

- <span data-ttu-id="35601-185">不允许将 RDP 或 SSH 访问定向到正在运行应用程序代码的 VM。</span><span class="sxs-lookup"><span data-stu-id="35601-185">Do not allow direct RDP or SSH access to VMs that are running application code.</span></span> <span data-ttu-id="35601-186">相反，运算符应登录到 jumpbox，也称为壁垒主机。</span><span class="sxs-lookup"><span data-stu-id="35601-186">Instead, operators should log into a jumpbox, also called a bastion host.</span></span> <span data-ttu-id="35601-187">这是管理员在网络上用来连接其他 VM 的 VM。</span><span class="sxs-lookup"><span data-stu-id="35601-187">This is a VM on the network that administrators use to connect to the other VMs.</span></span> <span data-ttu-id="35601-188">jumpbox 中有一个 NSG，它仅允许来自已批准的公共 IP 地址的 RDP 或 SSH。</span><span class="sxs-lookup"><span data-stu-id="35601-188">The jumpbox has an NSG that allows RDP or SSH only from approved public IP addresses.</span></span>

- <span data-ttu-id="35601-189">可使用站点到站点虚拟专用网络 (VPN) 或 Azure ExpressRoute，将 Azure 虚拟网络扩展到本地网络。</span><span class="sxs-lookup"><span data-stu-id="35601-189">You can extend the Azure virtual network to your on-premises network using a site-to-site virtual private network (VPN) or Azure ExpressRoute.</span></span> <span data-ttu-id="35601-190">有关详细信息，请参阅[混合网络参考体系结构][hybrid-network]。</span><span class="sxs-lookup"><span data-stu-id="35601-190">For more information, see [Hybrid network reference architecture][hybrid-network].</span></span>

- <span data-ttu-id="35601-191">如果组织使用 Active Directory 管理标识，建议将 Active Directory 环境扩展到 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="35601-191">If your organization uses Active Directory to manage identity, you may want to extend your Active Directory environment to the Azure VNet.</span></span> <span data-ttu-id="35601-192">有关详细信息，请参阅[标识管理参考体系结构][identity]。</span><span class="sxs-lookup"><span data-stu-id="35601-192">For more information, see [Identity management reference architecture][identity].</span></span>

- <span data-ttu-id="35601-193">如果需要比 VM 提供的 Azure SLA 更高的可用性，可以跨两个区域复制应用程序，并使用 Azure 流量管理器进行故障转移。</span><span class="sxs-lookup"><span data-stu-id="35601-193">If you need higher availability than the Azure SLA for VMs provides, replicate the application across two regions and use Azure Traffic Manager for failover.</span></span> <span data-ttu-id="35601-194">有关详细信息，请参阅[在多个区域中运行 Windows VM][multiregion-windows] 或[在多个区域中运行 Linux VM][multiregion-linux]。</span><span class="sxs-lookup"><span data-stu-id="35601-194">For more information, see [Run Windows VMs in multiple regions][multiregion-windows] or [Run Linux VMs in multiple regions][multiregion-linux].</span></span>

[autoscaling]: ../../best-practices/auto-scaling.md
[caching]: ../../best-practices/caching.md
[dmz]: ../../reference-architectures/dmz/index.md
[ha-nva]: ../../reference-architectures/dmz/nva-ha.md
[hybrid-network]: ../../reference-architectures/hybrid-networking/index.md
[identity]: ../../reference-architectures/identity/index.md
[multiregion-linux]: ../../reference-architectures/virtual-machines-linux/multi-region-application.md
[multiregion-windows]: ../../reference-architectures/virtual-machines-windows/multi-region-application.md
[n-tier-linux]: ../../reference-architectures/virtual-machines-linux/n-tier.md
[n-tier-windows]: ../../reference-architectures/virtual-machines-windows/n-tier.md
[sql-always-on]: /sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server