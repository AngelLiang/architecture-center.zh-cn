---
title: 使用 Windows VM 构建安全的 Web 应用
titleSuffix: Azure Example Scenarios
description: 使用规模集、应用程序网关和负载均衡器在 Azure 上通过 Windows Server 构建安全的多层 Web 应用程序。
author: iainfoulds
ms.date: 12/06/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: seodec18, Windows
social_image_url: /azure/architecture/example-scenario/infrastructure/media/architecture-regulated-multitier-app.png
ms.openlocfilehash: 440d208b423703fe791dcbe2cad0609fef0e6508
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246218"
---
# <a name="building-secure-web-applications-with-windows-virtual-machines-on-azure"></a><span data-ttu-id="3f3b0-103">使用 Windows 虚拟机在 Azure 上构建安全的 Web 应用程序</span><span class="sxs-lookup"><span data-stu-id="3f3b0-103">Building secure web applications with Windows virtual machines on Azure</span></span>

<span data-ttu-id="3f3b0-104">本方案提供的体系结构和设计指南适用于在 Microsoft Azure 上运行安全的多层 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-104">This scenario provides architecture and design guidance for running secure, multi-tier web applications on Microsoft Azure.</span></span> <span data-ttu-id="3f3b0-105">在此示例中，ASP.NET 应用程序通过虚拟机以安全方式连接到受保护的后端 Microsoft SQL Server 群集。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-105">In this example, an ASP.NET application securely connects to a protected back-end Microsoft SQL Server cluster using virtual machines.</span></span>

<span data-ttu-id="3f3b0-106">传统上，组织需保留旧版本地应用程序和服务来提供安全的基础结构。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-106">Traditionally, organizations had to maintain legacy on-premises applications and services to provide a secure infrastructure.</span></span> <span data-ttu-id="3f3b0-107">如果可以在 Azure 中安全地部署这些 Windows Server 应用程序，组织就可以实现其部署方式的现代化，减少其本地操作成本和管理开销。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-107">By deploying these Windows Server applications securely in Azure, organizations can modernize their deployments and reduce their on-premises operating costs and management overhead.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="3f3b0-108">相关用例</span><span class="sxs-lookup"><span data-stu-id="3f3b0-108">Relevant use cases</span></span>

<span data-ttu-id="3f3b0-109">下面是此方案的一些应用示例：</span><span class="sxs-lookup"><span data-stu-id="3f3b0-109">A few examples of where this scenario may apply:</span></span>

- <span data-ttu-id="3f3b0-110">在安全的云环境中实现应用程序部署方式的现代化。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-110">Modernizing application deployments in a secure cloud environment.</span></span>
- <span data-ttu-id="3f3b0-111">减少旧版本地应用程序和服务的管理开销。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-111">Reducing the management overhead of legacy on-premises applications and services.</span></span>
- <span data-ttu-id="3f3b0-112">改善患者的医疗保健以及对新应用程序平台的体验。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-112">Improving patient healthcare and experience with new application platforms.</span></span>

## <a name="architecture"></a><span data-ttu-id="3f3b0-113">体系结构</span><span class="sxs-lookup"><span data-stu-id="3f3b0-113">Architecture</span></span>

![从体系结构的角度概要说明针对管控行业的多层 Windows Server 应用程序中涉及的 Azure 组件][architecture]

<span data-ttu-id="3f3b0-115">此方案演示一个连接到后端数据库的前端 Web 应用程序，二者都在 Windows Server 2016 上运行。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-115">This scenario shows a front-end web application connecting to a back-end database, both running on Windows Server 2016.</span></span> <span data-ttu-id="3f3b0-116">数据流经方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="3f3b0-116">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="3f3b0-117">用户通过 Azure 应用程序网关访问前端 ASP.NET 应用程序。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-117">Users access the front-end ASP.NET application through an Azure Application Gateway.</span></span>
2. <span data-ttu-id="3f3b0-118">应用程序网关将流量分发到 Azure 虚拟机规模集中的 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-118">The Application Gateway distributes traffic to VM instances within an Azure virtual machine scale set.</span></span>
3. <span data-ttu-id="3f3b0-119">应用程序通过 Azure 负载均衡器连接到后端层中的 Microsoft SQL Server 群集。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-119">The application connects to Microsoft SQL Server cluster in a back-end tier via an Azure load balancer.</span></span> <span data-ttu-id="3f3b0-120">这些后端 SQL Server 实例位于单独的 Azure 虚拟网络中，受可以限制流量的网络安全组规则的保护。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-120">These back-end SQL Server instances are in a separate Azure virtual network, secured by network security group rules that limit traffic flow.</span></span>
4. <span data-ttu-id="3f3b0-121">该负载均衡器会将 SQL Server 流量分发到另一虚拟机规模集中的 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-121">The load balancer distributes SQL Server traffic to VM instances in another virtual machine scale set.</span></span>
5. <span data-ttu-id="3f3b0-122">Azure Blob 存储充当后端层中 SQL Server 群集的[云见证][cloud-witness]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-122">Azure Blob Storage acts as a [cloud witness][cloud-witness] for the SQL Server cluster in the back-end tier.</span></span> <span data-ttu-id="3f3b0-123">已为 Azure 存储的 VNet 服务终结点启用从 VNet 中进行连接的功能。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-123">The connection from within the VNet is enabled with a VNet Service Endpoint for Azure Storage.</span></span>

### <a name="components"></a><span data-ttu-id="3f3b0-124">组件</span><span class="sxs-lookup"><span data-stu-id="3f3b0-124">Components</span></span>

- <span data-ttu-id="3f3b0-125">[Azure 应用程序网关][appgateway-docs]是一个 7 层 Web 流量负载均衡器，可以感知应用程序并根据特定的路由规则分发流量。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-125">[Azure Application Gateway][appgateway-docs] is a layer 7 web traffic load balancer that is application-aware and can distribute traffic based on specific routing rules.</span></span> <span data-ttu-id="3f3b0-126">应用网关还可以处理 SSL 卸载，以便改进 Web 服务器性能。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-126">App Gateway can also handle SSL offloading for improved web server performance.</span></span>
- <span data-ttu-id="3f3b0-127">[Azure 虚拟网络][vnet-docs]允许 VM 之类的资源以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-127">[Azure Virtual Network][vnet-docs] allows resources such as VMs to securely communicate with each other, the Internet, and on-premises networks.</span></span> <span data-ttu-id="3f3b0-128">虚拟网络提供隔离和细分，可以筛选和路由流量，并且允许在不同位置之间进行连接。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-128">Virtual networks provide isolation and segmentation, filter and route traffic, and allow connection between locations.</span></span> <span data-ttu-id="3f3b0-129">本方案中使用了两个虚拟网络和相应的 NSG，这样是为了提供一个[外围安全区域][dmz] (DMZ)，对应用程序组件进行隔离。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-129">Two virtual networks combined with the appropriate NSGs are used in this scenario to provide a [demilitarized zone][dmz] (DMZ) and isolation of the application components.</span></span> <span data-ttu-id="3f3b0-130">虚拟网络对等互连可将两个网络连接到一起。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-130">Virtual network peering connects the two networks together.</span></span>
- <span data-ttu-id="3f3b0-131">使用 [Azure 虚拟机规模集][scaleset-docs]可以创建并管理一组完全相同的、负载均衡的 VM。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-131">[Azure virtual machine scale set][scaleset-docs] lets you create and manager a group of identical, load balanced, VMs.</span></span> <span data-ttu-id="3f3b0-132">可以根据需求或定义的计划自动增减 VM 实例的数目。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-132">The number of VM instances can automatically increase or decrease in response to demand or a defined schedule.</span></span> <span data-ttu-id="3f3b0-133">本方案中使用了两个单独的虚拟机规模集 - 一个用于前端 ASP.NET 应用程序实例，一个用于后端 SQL Server 群集 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-133">Two separate virtual machine scale sets are used in this scenario - one for the front-end ASP.NET application instances, and one for the back-end SQL Server cluster VM instances.</span></span> <span data-ttu-id="3f3b0-134">可以使用 PowerShell Desired State Configuration (DSC) 或 Azure 自定义脚本扩展，通过所需的软件和配置设置来预配 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-134">PowerShell desired state configuration (DSC) or the Azure custom script extension can be used to provision the VM instances with the required software and configuration settings.</span></span>
- <span data-ttu-id="3f3b0-135">[Azure 网络安全组][nsg-docs]包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝入站或出站网络流量。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-135">[Azure network security groups][nsg-docs] contain a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> <span data-ttu-id="3f3b0-136">本方案中的虚拟网络受网络安全组规则的保护，这些规则可以限制应用程序组件之间的流量。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-136">The virtual networks in this scenario are secured with network security group rules that restrict the flow of traffic between the application components.</span></span>
- <span data-ttu-id="3f3b0-137">[Azure 负载均衡器][loadbalancer-docs]根据规则和运行状况探测来分发入站流量。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-137">[Azure load balancer][loadbalancer-docs] distributes inbound traffic according to rules and health probes.</span></span> <span data-ttu-id="3f3b0-138">负载均衡器提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-138">A load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="3f3b0-139">本方案使用内部负载均衡器将流量从前端应用程序层分发到后端 SQL Server 群集。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-139">An internal load balancer is used in this scenario to distribute traffic from the front-end application tier to the back-end SQL Server cluster.</span></span>
- <span data-ttu-id="3f3b0-140">[Azure Blob 存储][cloudwitness-docs]充当 SQL Server 群集的云见证位置。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-140">[Azure Blob Storage][cloudwitness-docs] acts a Cloud Witness location for the SQL Server cluster.</span></span> <span data-ttu-id="3f3b0-141">此见证用于需要更多投票来决定仲裁的群集操作和决策。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-141">This witness is used for cluster operations and decisions that require an additional vote to decide quorum.</span></span> <span data-ttu-id="3f3b0-142">使用云见证，就不再需要让其他 VM 充当传统的文件共享见证。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-142">Using Cloud Witness removes the need for an additional VM to act as a traditional File Share Witness.</span></span>

### <a name="alternatives"></a><span data-ttu-id="3f3b0-143">备选项</span><span class="sxs-lookup"><span data-stu-id="3f3b0-143">Alternatives</span></span>

- <span data-ttu-id="3f3b0-144">Linux 和 Windows 可以互换使用，因为基础结构不依赖于操作系统。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-144">Linux and Windows can be used interchangeably since the infrastructure isn't dependent on the operating system.</span></span>

- <span data-ttu-id="3f3b0-145">[适用于 Linux 的 SQL Server][sql-linux] 可以替代后端数据存储。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-145">[SQL Server for Linux][sql-linux] can replace the back-end data store.</span></span>

- <span data-ttu-id="3f3b0-146">[Cosmos DB](/azure/cosmos-db/introduction) 是数据存储的另一种替代方案。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-146">[Cosmos DB](/azure/cosmos-db/introduction) is another alternative for the data store.</span></span>

## <a name="considerations"></a><span data-ttu-id="3f3b0-147">注意事项</span><span class="sxs-lookup"><span data-stu-id="3f3b0-147">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="3f3b0-148">可用性</span><span class="sxs-lookup"><span data-stu-id="3f3b0-148">Availability</span></span>

<span data-ttu-id="3f3b0-149">本方案中的 VM 实例跨[可用性区域](/azure/availability-zones/az-overview)进行部署。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-149">The VM instances in this scenario are deployed across [Availability Zones](/azure/availability-zones/az-overview).</span></span> <span data-ttu-id="3f3b0-150">每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-150">Each zone is made up of one or more datacenters equipped with independent power, cooling, and networking.</span></span> <span data-ttu-id="3f3b0-151">每个已启用的地区至少有三个可用性区域。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-151">Each enabled region has a minimum of three availability zones.</span></span> <span data-ttu-id="3f3b0-152">这样跨区域分发 VM 实例可以为应用程序层提供高可用性。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-152">This distribution of VM instances across zones provides high availability to the application tiers.</span></span>

<span data-ttu-id="3f3b0-153">可以将数据库层配置为使用 Always On 可用性组。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-153">The database tier can be configured to use Always On availability groups.</span></span> <span data-ttu-id="3f3b0-154">使用此 SQL Server 配置时，最多可以为群集中的一个主数据库配置八个辅助数据库。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-154">With this SQL Server configuration, one primary database within a cluster is configured with up to eight secondary databases.</span></span> <span data-ttu-id="3f3b0-155">如果主数据库发生问题，则群集可以故障转移到其中一个辅助数据库，这样应用程序就会继续可用。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-155">If an issue occurs with the primary database, the cluster fails over to one of the secondary databases, which allows the application to continue to be available.</span></span> <span data-ttu-id="3f3b0-156">有关详细信息，请参阅[适用于 SQL Server 的 Always On 可用性组概述][sqlalwayson-docs]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-156">For more information, see [Overview of Always On availability groups for SQL Server][sqlalwayson-docs].</span></span>

<span data-ttu-id="3f3b0-157">如需更多可用性指南，请参阅 Azure 体系结构中心的[可用性核对清单][availability]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-157">For more availability guidance, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="3f3b0-158">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="3f3b0-158">Scalability</span></span>

<span data-ttu-id="3f3b0-159">本方案将虚拟机规模集用于前端和后端组件。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-159">This scenario uses virtual machine scale sets for the front-end and back-end components.</span></span> <span data-ttu-id="3f3b0-160">使用规模集时，运行前端应用程序层的 VM 实例数可以根据客户需求或定义的计划自动进行缩放。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-160">With scale sets, the number of VM instances that run the front-end application tier can automatically scale in response to customer demand, or based on a defined schedule.</span></span> <span data-ttu-id="3f3b0-161">有关详细信息，请参阅[虚拟机规模集的自动缩放概述][vmssautoscale-docs]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-161">For more information, see [Overview of autoscale with virtual machine scale sets][vmssautoscale-docs].</span></span>

<span data-ttu-id="3f3b0-162">若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-162">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="3f3b0-163">安全</span><span class="sxs-lookup"><span data-stu-id="3f3b0-163">Security</span></span>

<span data-ttu-id="3f3b0-164">所有虚拟网络流量都进入前端应用程序层，受网络安全组的保护。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-164">All the virtual network traffic into the front-end application tier and protected by network security groups.</span></span> <span data-ttu-id="3f3b0-165">规则会限制流量的流动，只有前端应用程序层 VM 实例可以访问后端数据库层。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-165">Rules limit the flow of traffic so that only the front-end application tier VM instances can access the back-end database tier.</span></span> <span data-ttu-id="3f3b0-166">不允许数据库层发出的出站 Internet 流量。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-166">No outbound Internet traffic is allowed from the database tier.</span></span> <span data-ttu-id="3f3b0-167">为了减少受攻击面，请勿打开直接远程管理端口。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-167">To reduce the attack footprint, no direct remote management ports are open.</span></span> <span data-ttu-id="3f3b0-168">有关详细信息，请参阅 [Azure 网络安全组][nsg-docs]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-168">For more information, see [Azure network security groups][nsg-docs].</span></span>

<span data-ttu-id="3f3b0-169">若要查看支付卡行业数据安全标准 (PCI DSS 3.2) 部署指南，请参阅[相容的基础结构][pci-dss]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-169">To view guidance on deploying Payment Card Industry Data Security Standards (PCI DSS 3.2) [compliant infrastructure][pci-dss].</span></span> <span data-ttu-id="3f3b0-170">若需安全方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-170">For general guidance on designing secure scenarios, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="3f3b0-171">复原</span><span class="sxs-lookup"><span data-stu-id="3f3b0-171">Resiliency</span></span>

<span data-ttu-id="3f3b0-172">除了使用可用性区域和虚拟机规模集，本方案还使用 Azure 应用程序网关和负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-172">In combination with the use of Availability Zones and virtual machine scale sets, this scenario uses Azure Application Gateway and load balancer.</span></span> <span data-ttu-id="3f3b0-173">这两个网络组件可将流量分发到连接的 VM 实例，并且包括运行状况探测，可以确保流量只分发到正常运行的 VM。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-173">These two networking components distribute traffic to the connected VM instances, and include health probes that ensure traffic is only distributed to healthy VMs.</span></span> <span data-ttu-id="3f3b0-174">两个应用程序网关实例采用主-被配置进行配置，并使用一个区域冗余负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-174">Two Application Gateway instances are configured in an active-passive configuration, and a zone-redundant load balancer is used.</span></span> <span data-ttu-id="3f3b0-175">此配置使得网络资源和应用程序可以容忍那些本来会导致流量中断并影响最终用户访问的问题。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-175">This configuration makes the networking resources and application resilient to issues that would otherwise disrupt traffic and impact end-user access.</span></span>

<span data-ttu-id="3f3b0-176">若需可复原方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-176">For general guidance on designing resilient scenarios, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="3f3b0-177">部署方案</span><span class="sxs-lookup"><span data-stu-id="3f3b0-177">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="3f3b0-178">先决条件</span><span class="sxs-lookup"><span data-stu-id="3f3b0-178">Prerequisites</span></span>

- <span data-ttu-id="3f3b0-179">必须已经有 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-179">You must have an existing Azure account.</span></span> <span data-ttu-id="3f3b0-180">如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-180">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

- <span data-ttu-id="3f3b0-181">若要将 SQL Server 群集部署到后端规模集中，需要在 Azure Active Directory (AD) 域服务中有一个域。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-181">To deploy a SQL Server cluster into the back-end scale set, you would need a domain in Azure Active Directory (AD) Domain Services.</span></span>

### <a name="deploy-the-components"></a><span data-ttu-id="3f3b0-182">部署组件</span><span class="sxs-lookup"><span data-stu-id="3f3b0-182">Deploy the components</span></span>

<span data-ttu-id="3f3b0-183">若要通过 Azure 资源管理器模板部署此方案的核心基础结构，请执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-183">To deploy the core infrastructure for this scenario with an Azure Resource Manager template, perform the following steps.</span></span>

<!-- markdownlint-disable MD033 -->

1. <span data-ttu-id="3f3b0-184">选择“部署到 Azure”按钮：</span><span class="sxs-lookup"><span data-stu-id="3f3b0-184">Select the **Deploy to Azure** button:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Finfrastructure%2Fregulated-multitier-app.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="3f3b0-185">等待模板部署在 Azure 门户中打开，然后完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="3f3b0-185">Wait for the template deployment to open in the Azure portal, then complete the following steps:</span></span>
   - <span data-ttu-id="3f3b0-186">选择“新建”资源组，然后在文本框中提供一个名称，例如 *myWindowsscenario*。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-186">Choose to **Create new** resource group, then provide a name such as *myWindowsscenario* in the text box.</span></span>
   - <span data-ttu-id="3f3b0-187">从“位置”下拉框中选择区域。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-187">Select a region from the **Location** drop-down box.</span></span>
   - <span data-ttu-id="3f3b0-188">提供用于虚拟机规模集实例的用户名和安全密码。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-188">Provide a username and secure password for the virtual machine scale set instances.</span></span>
   - <span data-ttu-id="3f3b0-189">查看条款和条件，然后勾选“我同意上述条款和条件”。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-189">Review the terms and conditions, then check **I agree to the terms and conditions stated above**.</span></span>
   - <span data-ttu-id="3f3b0-190">选择“购买”按钮。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-190">Select the **Purchase** button.</span></span>

<!-- markdownlint-enable MD033 -->

<span data-ttu-id="3f3b0-191">部署可能需要 15-20 分钟才能完成。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-191">It can take 15-20 minutes for the deployment to complete.</span></span>

## <a name="pricing"></a><span data-ttu-id="3f3b0-192">定价</span><span class="sxs-lookup"><span data-stu-id="3f3b0-192">Pricing</span></span>

<span data-ttu-id="3f3b0-193">为了方便用户了解运行本方案的成本，我们已在成本计算器中预配置了所有服务。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-193">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="3f3b0-194">若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-194">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="3f3b0-195">我们已根据运行应用程序的规模集 VM 实例数提供了三个示例性的成本配置文件。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-195">We have provided three sample cost profiles based on the number of scale set VM instances that run your applications.</span></span>

- <span data-ttu-id="3f3b0-196">[小型][small-pricing]：此定价示例对应于两个前端和两个后端 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-196">[Small][small-pricing]: this pricing example correlates to two front-end and two back-end VM instances.</span></span>
- <span data-ttu-id="3f3b0-197">[中型][medium-pricing]：此定价示例对应于 20 个前端和 5 个后端 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-197">[Medium][medium-pricing]: this pricing example correlates to 20 front-end and 5 back-end VM instances.</span></span>
- <span data-ttu-id="3f3b0-198">[大型][large-pricing]：此定价示例对应于 100 个前端和 10 个后端 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-198">[Large][large-pricing]: this pricing example correlates to 100 front-end and 10 back-end VM instances.</span></span>

## <a name="related-resources"></a><span data-ttu-id="3f3b0-199">相关资源</span><span class="sxs-lookup"><span data-stu-id="3f3b0-199">Related resources</span></span>

<span data-ttu-id="3f3b0-200">本方案使用的后端虚拟机规模集运行 Microsoft SQL Server 群集。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-200">This scenario used a back-end virtual machine scale set that runs a Microsoft SQL Server cluster.</span></span> <span data-ttu-id="3f3b0-201">也可将 Cosmos DB 用作一个可缩放的、安全的、适用于应用程序数据的数据库层。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-201">Cosmos DB could also be used as a scalable and secure database tier for the application data.</span></span> <span data-ttu-id="3f3b0-202">使用 [Azure 虚拟网络服务终结点][vnetendpoint-docs]可以保护关键的 Azure 服务资源，只允许你在自己的虚拟网络中对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-202">An [Azure virtual network service endpoint][vnetendpoint-docs] allows you to secure your critical Azure service resources to only your virtual networks.</span></span> <span data-ttu-id="3f3b0-203">在本方案中，可以通过 VNet 终结点来确保前端应用程序层和 Cosmos DB 之间流量的安全。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-203">In this scenario, VNet endpoints allow you to secure traffic between the front-end application tier and Cosmos DB.</span></span> <span data-ttu-id="3f3b0-204">有关详细信息，请参阅 [Azure Cosmos DB 概述](/azure/cosmos-db/introduction)。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-204">For more information, see the [Azure Cosmos DB overview](/azure/cosmos-db/introduction).</span></span>

<span data-ttu-id="3f3b0-205">如需更详细的实施指南，请参阅[使用 SQL Server 的 N 层应用程序的参考体系结构][ntiersql-ra]。</span><span class="sxs-lookup"><span data-stu-id="3f3b0-205">For more detailed implementation guides, review the [reference architecture for N-tier applications using SQL Server][ntiersql-ra].</span></span>

<!-- links -->
[appgateway-docs]: /azure/application-gateway/overview
[architecture]: ./media/architecture-regulated-multitier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: ../../checklist/availability.md
[cloudwitness-docs]: /windows-server/failover-clustering/deploy-cloud-witness
[loadbalancer-docs]: /azure/load-balancer/load-balancer-overview
[nsg-docs]: /azure/virtual-network/security-overview
[ntiersql-ra]: /azure/architecture/reference-architectures/n-tier/n-tier-sql-server
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[scaleset-docs]: /azure/virtual-machine-scale-sets/overview
[sqlalwayson-docs]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[vmssautoscale-docs]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[vnet-docs]: /azure/virtual-network/virtual-networks-overview
[vnetendpoint-docs]: /azure/virtual-network/virtual-network-service-endpoints-overview
[pci-dss]: /azure/security/blueprints/pcidss-iaaswa-overview
[dmz]: /azure/virtual-network/virtual-networks-dmz-nsg
[sql-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[cloud-witness]: /windows-server/failover-clustering/deploy-cloud-witness
[small-pricing]: https://azure.com/e/711bbfcbbc884ef8aa91cdf0f2caff72
[medium-pricing]: https://azure.com/e/b622d82d79b34b8398c4bce35477856f
[large-pricing]: https://azure.com/e/1d99d8b92f90496787abecffa1473a93
