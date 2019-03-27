---
title: 实现安全的混合网络体系结构
titleSuffix: Azure Reference Architectures
description: 在 Azure 中实现安全的混合网络体系结构。
author: telmosampaio
ms.date: 10/22/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, networking
ms.openlocfilehash: 82327cca08e614bfe5226c9ca1a414388878a7c2
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243508"
---
# <a name="implement-a-dmz-between-azure-and-your-on-premises-datacenter"></a><span data-ttu-id="ccbca-103">在 Azure 与本地数据中心之间实现外围网络</span><span class="sxs-lookup"><span data-stu-id="ccbca-103">Implement a DMZ between Azure and your on-premises datacenter</span></span>

<span data-ttu-id="ccbca-104">此参考体系结构显示了一个可将本地网络扩展到 Azure 的安全混合网络。</span><span class="sxs-lookup"><span data-stu-id="ccbca-104">This reference architecture shows a secure hybrid network that extends an on-premises network to Azure.</span></span> <span data-ttu-id="ccbca-105">此体系结构在本地网络与 Azure 虚拟网络 (VNet) 之间实现一个 DMZ（也称为外围网络）。</span><span class="sxs-lookup"><span data-stu-id="ccbca-105">The architecture implements a DMZ, also called a *perimeter network*, between the on-premises network and an Azure virtual network (VNet).</span></span> <span data-ttu-id="ccbca-106">DMZ 包括防火墙和数据包检查等实现安全功能的网络虚拟设备 (NVA)。</span><span class="sxs-lookup"><span data-stu-id="ccbca-106">The DMZ includes network virtual appliances (NVAs) that implement security functionality such as firewalls and packet inspection.</span></span> <span data-ttu-id="ccbca-107">来自 VNet 的所有传出流量都通过本地网络强制隧道传输到 Internet，以便可以进行审核。</span><span class="sxs-lookup"><span data-stu-id="ccbca-107">All outgoing traffic from the VNet is force-tunneled to the Internet through the on-premises network, so that it can be audited.</span></span> <span data-ttu-id="ccbca-108">[**部署此解决方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="ccbca-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

> [!NOTE]
> <span data-ttu-id="ccbca-109">还可以使用 [Azure 防火墙](/azure/firewall/)（基于云的网络安全服务）实现此方案。</span><span class="sxs-lookup"><span data-stu-id="ccbca-109">This scenario can also be accomplished using [Azure Firewall](/azure/firewall/), a cloud-based network security service.</span></span>

![安全混合网络体系结构](./images/dmz-private.png)

<span data-ttu-id="ccbca-111">下载此体系结构的 [Visio 文件][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="ccbca-111">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="ccbca-112">此体系结构需要使用 [VPN 网关][ra-vpn]或 [ExpressRoute][ra-expressroute] 连接来连接到本地数据中心。</span><span class="sxs-lookup"><span data-stu-id="ccbca-112">This architecture requires a connection to your on-premises datacenter, using either a [VPN gateway][ra-vpn] or an [ExpressRoute][ra-expressroute] connection.</span></span> <span data-ttu-id="ccbca-113">此体系结构的典型用途包括：</span><span class="sxs-lookup"><span data-stu-id="ccbca-113">Typical uses for this architecture include:</span></span>

- <span data-ttu-id="ccbca-114">在本地运行一部分工作负荷，在 Azure 中运行一部分工作负荷的混合应用程序。</span><span class="sxs-lookup"><span data-stu-id="ccbca-114">Hybrid applications where workloads run partly on-premises and partly in Azure.</span></span>
- <span data-ttu-id="ccbca-115">需要精细控制从本地数据中心进入 Azure VNet 的流量的基础结构。</span><span class="sxs-lookup"><span data-stu-id="ccbca-115">Infrastructure that requires granular control over traffic entering an Azure VNet from an on-premises datacenter.</span></span>
- <span data-ttu-id="ccbca-116">必须审核传出流量的应用程序。</span><span class="sxs-lookup"><span data-stu-id="ccbca-116">Applications that must audit outgoing traffic.</span></span> <span data-ttu-id="ccbca-117">这通常是许多商业系统的监管要求，可以帮助防止公开披露私有信息。</span><span class="sxs-lookup"><span data-stu-id="ccbca-117">This is often a regulatory requirement of many commercial systems and can help to prevent public disclosure of private information.</span></span>

## <a name="architecture"></a><span data-ttu-id="ccbca-118">体系结构</span><span class="sxs-lookup"><span data-stu-id="ccbca-118">Architecture</span></span>

<span data-ttu-id="ccbca-119">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="ccbca-119">The architecture consists of the following components.</span></span>

- <span data-ttu-id="ccbca-120">**本地网络**。</span><span class="sxs-lookup"><span data-stu-id="ccbca-120">**On-premises network**.</span></span> <span data-ttu-id="ccbca-121">组织中实现的专用局域网。</span><span class="sxs-lookup"><span data-stu-id="ccbca-121">A private local-area network implemented in an organization.</span></span>
- <span data-ttu-id="ccbca-122">**Azure 虚拟网络 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="ccbca-122">**Azure virtual network (VNet)**.</span></span> <span data-ttu-id="ccbca-123">VNet 承载在 Azure 中运行的应用程序和其他资源。</span><span class="sxs-lookup"><span data-stu-id="ccbca-123">The VNet hosts the application and other resources running in Azure.</span></span>
- <span data-ttu-id="ccbca-124">**网关**。</span><span class="sxs-lookup"><span data-stu-id="ccbca-124">**Gateway**.</span></span> <span data-ttu-id="ccbca-125">网关在本地网络中的路由器与 VNet 之间提供连接。</span><span class="sxs-lookup"><span data-stu-id="ccbca-125">The gateway provides connectivity between the routers in the on-premises network and the VNet.</span></span>
- <span data-ttu-id="ccbca-126">**网络虚拟设备 (NVA)**。</span><span class="sxs-lookup"><span data-stu-id="ccbca-126">**Network virtual appliance (NVA)**.</span></span> <span data-ttu-id="ccbca-127">NVA 是一种通用术语，用于描述执行诸如允许或拒绝访问（作为防火墙）、优化广域网 (WAN) 操作（包括网络压缩）、自定义路由或其他网络功能这类任务的 VM。</span><span class="sxs-lookup"><span data-stu-id="ccbca-127">NVA is a generic term that describes a VM performing tasks such as allowing or denying access as a firewall, optimizing wide area network (WAN) operations (including network compression), custom routing, or other network functionality.</span></span>
- <span data-ttu-id="ccbca-128">**Web 层、业务层和数据层子网**。</span><span class="sxs-lookup"><span data-stu-id="ccbca-128">**Web tier, business tier, and data tier subnets**.</span></span> <span data-ttu-id="ccbca-129">承载实现在云中运行的示例第 3 层应用程序的 VM 和服务的子网。</span><span class="sxs-lookup"><span data-stu-id="ccbca-129">Subnets hosting the VMs and services that implement an example 3-tier application running in the cloud.</span></span> <span data-ttu-id="ccbca-130">有关详细信息，请参阅[在 Azure 上运行 N 层体系结构所用的 Windows VM][ra-n-tier]。</span><span class="sxs-lookup"><span data-stu-id="ccbca-130">See [Running Windows VMs for an N-tier architecture on Azure][ra-n-tier] for more information.</span></span>
- <span data-ttu-id="ccbca-131">**用户定义的路由 (UDR)**。</span><span class="sxs-lookup"><span data-stu-id="ccbca-131">**User defined routes (UDR)**.</span></span> <span data-ttu-id="ccbca-132">[用户定义的路由][udr-overview]定义 Azure VNet 中的 IP 流量流。</span><span class="sxs-lookup"><span data-stu-id="ccbca-132">[User defined routes][udr-overview] define the flow of IP traffic within Azure VNets.</span></span>

    > [!NOTE]
    > <span data-ttu-id="ccbca-133">根据 VPN 连接的要求，可以配置边界网关协议 (BGP) 路由，而不是使用 UDR 实现通过本地网络定向返回流量的转发规则。</span><span class="sxs-lookup"><span data-stu-id="ccbca-133">Depending on the requirements of your VPN connection, you can configure Border Gateway Protocol (BGP) routes instead of using UDRs to implement the forwarding rules that direct traffic back through the on-premises network.</span></span>
    >

- <span data-ttu-id="ccbca-134">**管理子网**。</span><span class="sxs-lookup"><span data-stu-id="ccbca-134">**Management subnet**.</span></span> <span data-ttu-id="ccbca-135">此子网包含为 VNet 中运行的组件实现管理和监视功能的 VM。</span><span class="sxs-lookup"><span data-stu-id="ccbca-135">This subnet contains VMs that implement management and monitoring capabilities for the components running in the VNet.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ccbca-136">建议</span><span class="sxs-lookup"><span data-stu-id="ccbca-136">Recommendations</span></span>

<span data-ttu-id="ccbca-137">以下建议适用于大多数方案。</span><span class="sxs-lookup"><span data-stu-id="ccbca-137">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="ccbca-138">除非有优先于这些建议的特定要求，否则请遵循这些建议。</span><span class="sxs-lookup"><span data-stu-id="ccbca-138">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="access-control-recommendations"></a><span data-ttu-id="ccbca-139">访问控制建议</span><span class="sxs-lookup"><span data-stu-id="ccbca-139">Access control recommendations</span></span>

<span data-ttu-id="ccbca-140">使用[基于角色的访问控制][rbac] (RBAC) 在应用程序中管理资源。</span><span class="sxs-lookup"><span data-stu-id="ccbca-140">Use [Role-Based Access Control][rbac] (RBAC) to manage the resources in your application.</span></span> <span data-ttu-id="ccbca-141">考虑创建以下[自定义角色][rbac-custom-roles]：</span><span class="sxs-lookup"><span data-stu-id="ccbca-141">Consider creating the following [custom roles][rbac-custom-roles]:</span></span>

- <span data-ttu-id="ccbca-142">一个 DevOps 角色，具有管理应用程序的基础结构、部署应用程序组件以及监视和重新启动 VM 的权限。</span><span class="sxs-lookup"><span data-stu-id="ccbca-142">A DevOps role with permissions to administer the infrastructure for the application, deploy the application components, and monitor and restart VMs.</span></span>

- <span data-ttu-id="ccbca-143">一个集中式 IT 管理员角色，用于管理和监视网络资源。</span><span class="sxs-lookup"><span data-stu-id="ccbca-143">A centralized IT administrator role to manage and monitor network resources.</span></span>

- <span data-ttu-id="ccbca-144">一个安全性 IT 管理员角色，用于管理安全网络资源，如 NVA。</span><span class="sxs-lookup"><span data-stu-id="ccbca-144">A security IT administrator role to manage secure network resources such as the NVAs.</span></span>

<span data-ttu-id="ccbca-145">DevOps 和 IT 管理员角色不应具有 NVA 资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="ccbca-145">The DevOps and IT administrator roles should not have access to the NVA resources.</span></span> <span data-ttu-id="ccbca-146">这应限制为安全性 IT 管理员角色。</span><span class="sxs-lookup"><span data-stu-id="ccbca-146">This should be restricted to the security IT administrator role.</span></span>

### <a name="resource-group-recommendations"></a><span data-ttu-id="ccbca-147">有关资源组的建议</span><span class="sxs-lookup"><span data-stu-id="ccbca-147">Resource group recommendations</span></span>

<span data-ttu-id="ccbca-148">可以通过将 Azure 资源（如 VM、VNet 和负载均衡器）分组到资源组中，来轻松地管理它们。</span><span class="sxs-lookup"><span data-stu-id="ccbca-148">Azure resources such as VMs, VNets, and load balancers can be easily managed by grouping them together into resource groups.</span></span> <span data-ttu-id="ccbca-149">将 RBAC 角色分配给每个资源组以限制访问。</span><span class="sxs-lookup"><span data-stu-id="ccbca-149">Assign RBAC roles to each resource group to restrict access.</span></span>

<span data-ttu-id="ccbca-150">建议创建以下资源组：</span><span class="sxs-lookup"><span data-stu-id="ccbca-150">We recommend creating the following resource groups:</span></span>

- <span data-ttu-id="ccbca-151">一个包含 VNet（不包括 VM）、NSG 以及用于连接到本地网络的网关资源的资源组。</span><span class="sxs-lookup"><span data-stu-id="ccbca-151">A resource group containing the VNet (excluding the VMs), NSGs, and the gateway resources for connecting to the on-premises network.</span></span> <span data-ttu-id="ccbca-152">将集中式 IT 管理员角色分配给此资源组。</span><span class="sxs-lookup"><span data-stu-id="ccbca-152">Assign the centralized IT administrator role to this resource group.</span></span>
- <span data-ttu-id="ccbca-153">一个包含 NVA 的 VM（包括负载均衡器）、jumpbox 和其他管理 VM 以及强制所有流量经过 NVA 的网关子网 UDR 的资源组。</span><span class="sxs-lookup"><span data-stu-id="ccbca-153">A resource group containing the VMs for the NVAs (including the load balancer), the jumpbox and other management VMs, and the UDR for the gateway subnet that forces all traffic through the NVAs.</span></span> <span data-ttu-id="ccbca-154">将安全性 IT 管理员角色分配给此资源组。</span><span class="sxs-lookup"><span data-stu-id="ccbca-154">Assign the security IT administrator role to this resource group.</span></span>
- <span data-ttu-id="ccbca-155">用于每个应用层的单独资源组，包含负载均衡器和 VM。</span><span class="sxs-lookup"><span data-stu-id="ccbca-155">Separate resource groups for each application tier that contain the load balancer and VMs.</span></span> <span data-ttu-id="ccbca-156">请注意，此资源组不应包含每个层的子网。</span><span class="sxs-lookup"><span data-stu-id="ccbca-156">Note that this resource group shouldn't include the subnets for each tier.</span></span> <span data-ttu-id="ccbca-157">将 DevOps 角色分配给此资源组。</span><span class="sxs-lookup"><span data-stu-id="ccbca-157">Assign the DevOps role to this resource group.</span></span>

### <a name="virtual-network-gateway-recommendations"></a><span data-ttu-id="ccbca-158">有关虚拟网络网关的建议</span><span class="sxs-lookup"><span data-stu-id="ccbca-158">Virtual network gateway recommendations</span></span>

<span data-ttu-id="ccbca-159">本地流量会通过虚拟网络网关传递到 VNet。</span><span class="sxs-lookup"><span data-stu-id="ccbca-159">On-premises traffic passes to the VNet through a virtual network gateway.</span></span> <span data-ttu-id="ccbca-160">建议使用 [Azure VPN 网关][guidance-vpn-gateway]或 [Azure ExpressRoute 网关][guidance-expressroute]。</span><span class="sxs-lookup"><span data-stu-id="ccbca-160">We recommend an [Azure VPN gateway][guidance-vpn-gateway] or an [Azure ExpressRoute gateway][guidance-expressroute].</span></span>

### <a name="nva-recommendations"></a><span data-ttu-id="ccbca-161">有关 NVA 的建议</span><span class="sxs-lookup"><span data-stu-id="ccbca-161">NVA recommendations</span></span>

<span data-ttu-id="ccbca-162">NVA 可提供不同服务来管理和监视网络流量。</span><span class="sxs-lookup"><span data-stu-id="ccbca-162">NVAs provide different services for managing and monitoring network traffic.</span></span> <span data-ttu-id="ccbca-163">[Azure 市场][azure-marketplace-nva]提供多个可以使用的第三方供应商 NVA。</span><span class="sxs-lookup"><span data-stu-id="ccbca-163">The [Azure Marketplace][azure-marketplace-nva] offers several third-party vendor NVAs that you can use.</span></span> <span data-ttu-id="ccbca-164">如果这些第三方 NVA 都不满足你的要求，则可以使用 VM 创建自定义 NVA。</span><span class="sxs-lookup"><span data-stu-id="ccbca-164">If none of these third-party NVAs meet your requirements, you can create a custom NVA using VMs.</span></span>

<span data-ttu-id="ccbca-165">例如，此参考体系结构的解决方案部署在 VM 上实现具有以下功能的 NVA：</span><span class="sxs-lookup"><span data-stu-id="ccbca-165">For example, the solution deployment for this reference architecture implements an NVA with the following functionality on a VM:</span></span>

- <span data-ttu-id="ccbca-166">流量使用 NVA 网络接口 (NIC) 上的 [IP 转发][ip-forwarding]进行路由。</span><span class="sxs-lookup"><span data-stu-id="ccbca-166">Traffic is routed using [IP forwarding][ip-forwarding] on the NVA network interfaces (NICs).</span></span>
- <span data-ttu-id="ccbca-167">仅当合适时才允许流量经过 NVA。</span><span class="sxs-lookup"><span data-stu-id="ccbca-167">Traffic is permitted to pass through the NVA only if it is appropriate to do so.</span></span> <span data-ttu-id="ccbca-168">参考体系结构中的每个 NVA VM 都是简单的 Linux 路由器。</span><span class="sxs-lookup"><span data-stu-id="ccbca-168">Each NVA VM in the reference architecture is a simple Linux router.</span></span> <span data-ttu-id="ccbca-169">入站流量在网络接口 eth0 上到达，出站流量与通过网络接口 eth1 调度的自定义脚本定义的规则匹配。</span><span class="sxs-lookup"><span data-stu-id="ccbca-169">Inbound traffic arrives on network interface *eth0*, and outbound traffic matches rules defined by custom scripts dispatched through network interface *eth1*.</span></span>
- <span data-ttu-id="ccbca-170">NVA 只能从管理子网进行配置。</span><span class="sxs-lookup"><span data-stu-id="ccbca-170">The NVAs can only be configured from the management subnet.</span></span>
- <span data-ttu-id="ccbca-171">路由到管理子网的流量不会经过 NVA。</span><span class="sxs-lookup"><span data-stu-id="ccbca-171">Traffic routed to the management subnet does not pass through the NVAs.</span></span> <span data-ttu-id="ccbca-172">否则，如果 NVA 发生故障，则没有指向管理子网的路由可用于修复它们。</span><span class="sxs-lookup"><span data-stu-id="ccbca-172">Otherwise, if the NVAs fail, there would be no route to the management subnet to fix them.</span></span>
- <span data-ttu-id="ccbca-173">NVA 的 VM 放置在负载均衡器后面的[可用性集][availability-set]中。</span><span class="sxs-lookup"><span data-stu-id="ccbca-173">The VMs for the NVA are placed in an [availability set][availability-set] behind a load balancer.</span></span> <span data-ttu-id="ccbca-174">网关子网中的 UDR 将 NVA 请求定向到负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="ccbca-174">The UDR in the gateway subnet directs NVA requests to the load balancer.</span></span>

<span data-ttu-id="ccbca-175">包含第 7 层 NVA，以在 NVA 级别终止应用程序连接，并保持与后端层的相关性。</span><span class="sxs-lookup"><span data-stu-id="ccbca-175">Include a layer-7 NVA to terminate application connections at the NVA level and maintain affinity with the backend tiers.</span></span> <span data-ttu-id="ccbca-176">这可以保证建立对称连接，使得来自后端层的响应流量可通过 NVA 返回。</span><span class="sxs-lookup"><span data-stu-id="ccbca-176">This guarantees symmetric connectivity, in which response traffic from the backend tiers returns through the NVA.</span></span>

<span data-ttu-id="ccbca-177">可考虑的另一个选项是按顺序连接多个 NVA，其中每个 NVA 都执行专用安全任务。</span><span class="sxs-lookup"><span data-stu-id="ccbca-177">Another option to consider is connecting multiple NVAs in series, with each NVA performing a specialized security task.</span></span> <span data-ttu-id="ccbca-178">这样可以按照每个 NVA 来管理每个安全功能。</span><span class="sxs-lookup"><span data-stu-id="ccbca-178">This allows each security function to be managed on a per-NVA basis.</span></span> <span data-ttu-id="ccbca-179">例如，实现防火墙的 NVA 可以与运行标识服务的 NVA 按顺序放置。</span><span class="sxs-lookup"><span data-stu-id="ccbca-179">For example, an NVA implementing a firewall could be placed in series with an NVA running identity services.</span></span> <span data-ttu-id="ccbca-180">便于管理的代价是会添加额外网络跃点，这可能会增加延迟，因此请确保这不会影响应用程序性能。</span><span class="sxs-lookup"><span data-stu-id="ccbca-180">The tradeoff for ease of management is the addition of extra network hops that may increase latency, so ensure that this doesn't affect your application's performance.</span></span>

### <a name="nsg-recommendations"></a><span data-ttu-id="ccbca-181">有关 NSG 的建议</span><span class="sxs-lookup"><span data-stu-id="ccbca-181">NSG recommendations</span></span>

<span data-ttu-id="ccbca-182">VPN 网关会针对与本地网络的连接公开公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ccbca-182">The VPN gateway exposes a public IP address for the connection to the on-premises network.</span></span> <span data-ttu-id="ccbca-183">建议为入站 NVA 子网创建网络安全组 (NSG)，其规则是阻止不是源自本地网络的所有流量。</span><span class="sxs-lookup"><span data-stu-id="ccbca-183">We recommend creating a network security group (NSG) for the inbound NVA subnet, with rules to block all traffic not originating from the on-premises network.</span></span>

<span data-ttu-id="ccbca-184">还建议对每个子网使用 NSG，以针对入站流量提供第二层保护，并绕过错误配置的或已禁用的 NVA。</span><span class="sxs-lookup"><span data-stu-id="ccbca-184">We also recommend NSGs for each subnet to provide a second level of protection against inbound traffic bypassing an incorrectly configured or disabled NVA.</span></span> <span data-ttu-id="ccbca-185">例如，参考体系结构中的 Web 层子网实现的 NSG 的一个规则是忽略从本地网络 (192.168.0.0/16) 或 VNet 接收的请求之外的所有请求，另一个规则是忽略不是在端口 80 上进行的所有请求。</span><span class="sxs-lookup"><span data-stu-id="ccbca-185">For example, the web tier subnet in the reference architecture implements an NSG with a rule to ignore all requests other than those received from the on-premises network (192.168.0.0/16) or the VNet, and another rule that ignores all requests not made on port 80.</span></span>

### <a name="internet-access-recommendations"></a><span data-ttu-id="ccbca-186">有关 Internet 访问的建议</span><span class="sxs-lookup"><span data-stu-id="ccbca-186">Internet access recommendations</span></span>

<span data-ttu-id="ccbca-187">使用站点到站点 VPN 隧道通过本地网络[强制隧道][azure-forced-tunneling]传递所有出站 Internet 流量，并使用网络地址转换 (NAT) 路由到 Internet。</span><span class="sxs-lookup"><span data-stu-id="ccbca-187">[Force-tunnel][azure-forced-tunneling] all outbound Internet traffic through your on-premises network using the site-to-site VPN tunnel, and route to the Internet using network address translation (NAT).</span></span> <span data-ttu-id="ccbca-188">这可以防止任何存储在数据层中的机密信息的意外泄漏，并允许检查和审核所有传出流量。</span><span class="sxs-lookup"><span data-stu-id="ccbca-188">This prevents accidental leakage of any confidential information stored in your data tier and allows inspection and auditing of all outgoing traffic.</span></span>

> [!NOTE]
> <span data-ttu-id="ccbca-189">请勿完全阻止来自应用层的 Internet 流量，因为这会阻止这些层使用依赖于公共 IP 地址的 Azure PaaS 服务（如 VM 诊断日志记录、VM 扩展的下澡和其他功能）。</span><span class="sxs-lookup"><span data-stu-id="ccbca-189">Don't completely block Internet traffic from the application tiers, as this will prevent these tiers from using Azure PaaS services that rely on public IP addresses, such as VM diagnostics logging, downloading of VM extensions, and other functionality.</span></span> <span data-ttu-id="ccbca-190">Azure 诊断还要求组件可以读取和写入 Azure 存储帐户。</span><span class="sxs-lookup"><span data-stu-id="ccbca-190">Azure diagnostics also requires that components can read and write to an Azure Storage account.</span></span>

<span data-ttu-id="ccbca-191">验证出站 Internet 流量是否正确地强制隧道传递。</span><span class="sxs-lookup"><span data-stu-id="ccbca-191">Verify that outbound internet traffic is force-tunneled correctly.</span></span> <span data-ttu-id="ccbca-192">如果在本地服务器上使用 VPN 连接与[路由和远程访问服务][routing-and-remote-access-service]，请使用诸如 [WireShark][wireshark] 或 [Microsoft 消息分析器](https://www.microsoft.com/download/details.aspx?id=44226)这类工具。</span><span class="sxs-lookup"><span data-stu-id="ccbca-192">If you're using a VPN connection with the [routing and remote access service][routing-and-remote-access-service] on an on-premises server, use a tool such as [WireShark][wireshark] or [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226).</span></span>

### <a name="management-subnet-recommendations"></a><span data-ttu-id="ccbca-193">有关管理子网的建议</span><span class="sxs-lookup"><span data-stu-id="ccbca-193">Management subnet recommendations</span></span>

<span data-ttu-id="ccbca-194">管理子网包含执行管理和监视功能的 jumpbox。</span><span class="sxs-lookup"><span data-stu-id="ccbca-194">The management subnet contains a jumpbox that performs management and monitoring functionality.</span></span> <span data-ttu-id="ccbca-195">将所有安全管理任务的执行限制到 jumpbox。</span><span class="sxs-lookup"><span data-stu-id="ccbca-195">Restrict execution of all secure management tasks to the jumpbox.</span></span>

<span data-ttu-id="ccbca-196">请勿为 jumpbox 创建公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ccbca-196">Do not create a public IP address for the jumpbox.</span></span> <span data-ttu-id="ccbca-197">而是创建一个路由以通过传入网关访问 jumpbox。</span><span class="sxs-lookup"><span data-stu-id="ccbca-197">Instead, create one route to access the jumpbox through the incoming gateway.</span></span> <span data-ttu-id="ccbca-198">创建 NSG 规则，以便管理子网仅响应来自允许的路由的请求。</span><span class="sxs-lookup"><span data-stu-id="ccbca-198">Create NSG rules so the management subnet only responds to requests from the allowed route.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="ccbca-199">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="ccbca-199">Scalability considerations</span></span>

<span data-ttu-id="ccbca-200">参考体系结构使用负载均衡器将本地网络流量定向到 NVA 设备池，这些设备会对流量进行路由。</span><span class="sxs-lookup"><span data-stu-id="ccbca-200">The reference architecture uses a load balancer to direct on-premises network traffic to a pool of NVA devices, which route the traffic.</span></span> <span data-ttu-id="ccbca-201">NVA 放置在[可用性集][availability-set]中。</span><span class="sxs-lookup"><span data-stu-id="ccbca-201">The NVAs are placed in an [availability set][availability-set].</span></span> <span data-ttu-id="ccbca-202">此设计使你可以随着时间推移监视 NVA 的吞吐量，以及添加 NVA 设备以响应负载增加。</span><span class="sxs-lookup"><span data-stu-id="ccbca-202">This design allows you to monitor the throughput of the NVAs over time and add NVA devices in response to increases in load.</span></span>

<span data-ttu-id="ccbca-203">标准 SKU VPN 网关支持最多 100 Mbps 的持续吞吐量。</span><span class="sxs-lookup"><span data-stu-id="ccbca-203">The standard SKU VPN gateway supports sustained throughput of up to 100 Mbps.</span></span> <span data-ttu-id="ccbca-204">高性能 SKU 提供最多 200 Mbps。</span><span class="sxs-lookup"><span data-stu-id="ccbca-204">The High Performance SKU provides up to 200 Mbps.</span></span> <span data-ttu-id="ccbca-205">对于更高带宽，请考虑升级到 ExpressRoute 网关。</span><span class="sxs-lookup"><span data-stu-id="ccbca-205">For higher bandwidths, consider upgrading to an ExpressRoute gateway.</span></span> <span data-ttu-id="ccbca-206">ExpressRoute 提供最多 10 Gbps 的带宽，且延迟低于 VPN 连接。</span><span class="sxs-lookup"><span data-stu-id="ccbca-206">ExpressRoute provides up to 10 Gbps bandwidth with lower latency than a VPN connection.</span></span>

<span data-ttu-id="ccbca-207">有关 Azure 网关的可伸缩性的详细信息，请参阅[使用 Azure 和本地 VPN 实现混合网络体系结构][guidance-vpn-gateway-scalability]和[使用 Azure ExpressRoute 实现混合网络体系结构][guidance-expressroute-scalability]中的可伸缩性注意事项部分。</span><span class="sxs-lookup"><span data-stu-id="ccbca-207">For more information about the scalability of Azure gateways, see the scalability consideration section in [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-scalability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-scalability].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="ccbca-208">可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="ccbca-208">Availability considerations</span></span>

<span data-ttu-id="ccbca-209">如上所述，参考体系结构使用位于负载均衡器后面的 NVA 设备池。</span><span class="sxs-lookup"><span data-stu-id="ccbca-209">As mentioned, the reference architecture uses a pool of NVA devices behind a load balancer.</span></span> <span data-ttu-id="ccbca-210">负载均衡器使用运行状况探测监视每个 NVA，并且会从池中删除任何无法响应的 NVA。</span><span class="sxs-lookup"><span data-stu-id="ccbca-210">The load balancer uses a health probe to monitor each NVA and will remove any unresponsive NVAs from the pool.</span></span>

<span data-ttu-id="ccbca-211">如果要使用 Azure ExpressRoute 在 VNet 与本地网络之间提供连接，请[配置 VPN 网关以提供故障转移][ra-vpn-failover]（在 ExpressRoute 连接成为不可用状态时）。</span><span class="sxs-lookup"><span data-stu-id="ccbca-211">If you're using Azure ExpressRoute to provide connectivity between the VNet and on-premises network, [configure a VPN gateway to provide failover][ra-vpn-failover] if the ExpressRoute connection becomes unavailable.</span></span>

<span data-ttu-id="ccbca-212">有关为 VPN 和 ExpressRoute 连接保持可用性的特定信息，请参阅[使用 Azure 和本地 VPN 实现混合网络体系结构][guidance-vpn-gateway-availability]和[使用 Azure ExpressRoute 实现混合网络体系结构][guidance-expressroute-availability]中的可用性注意事项。</span><span class="sxs-lookup"><span data-stu-id="ccbca-212">For specific information on maintaining availability for VPN and ExpressRoute connections, see the availability considerations in [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-availability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-availability].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="ccbca-213">可管理性注意事项</span><span class="sxs-lookup"><span data-stu-id="ccbca-213">Manageability considerations</span></span>

<span data-ttu-id="ccbca-214">所有应用程序和资源监视都应由管理子网中的 jumpbox 执行。</span><span class="sxs-lookup"><span data-stu-id="ccbca-214">All application and resource monitoring should be performed by the jumpbox in the management subnet.</span></span> <span data-ttu-id="ccbca-215">根据应用程序要求，可能需要管理子网中的其他监视资源。</span><span class="sxs-lookup"><span data-stu-id="ccbca-215">Depending on your application requirements, you may need additional monitoring resources in the management subnet.</span></span> <span data-ttu-id="ccbca-216">如果是这样，则应通过 jumpbox 访问这些资源。</span><span class="sxs-lookup"><span data-stu-id="ccbca-216">If so, these resources should be accessed through the jumpbox.</span></span>

<span data-ttu-id="ccbca-217">如果从本地网络到 Azure 的网关连接断开，仍可以通过部署公共 IP 地址，将该地址添加到 Jumpbox，然后从 Internet 远程访问，来连接 Jumpbox。</span><span class="sxs-lookup"><span data-stu-id="ccbca-217">If gateway connectivity from your on-premises network to Azure is down, you can still reach the jumpbox by deploying a public IP address, adding it to the jumpbox, and remoting in from the internet.</span></span>

<span data-ttu-id="ccbca-218">参考体系结构中每个层的子网都受 NSG 规则保护。</span><span class="sxs-lookup"><span data-stu-id="ccbca-218">Each tier's subnet in the reference architecture is protected by NSG rules.</span></span> <span data-ttu-id="ccbca-219">可能需要创建一个规则以打开用于在 Windows VM 上进行远程桌面协议 (RDP) 访问的端口 3389，或是用于在 Linux VM 上进行安全外壳 (SSH) 访问的端口 22。</span><span class="sxs-lookup"><span data-stu-id="ccbca-219">You may need to create a rule to open port 3389 for remote desktop protocol (RDP) access on Windows VMs or port 22 for secure shell (SSH) access on Linux VMs.</span></span> <span data-ttu-id="ccbca-220">其他管理和监视工具可能需要规则打开其他端口。</span><span class="sxs-lookup"><span data-stu-id="ccbca-220">Other management and monitoring tools may require rules to open additional ports.</span></span>

<span data-ttu-id="ccbca-221">如果要使用 ExpressRoute 在本地数据中心与 Azure 之间提供连接，请使用 [Azure 连接 工具包 (AzureCT)][azurect] 监视和解决连接问题.</span><span class="sxs-lookup"><span data-stu-id="ccbca-221">If you're using ExpressRoute to provide the connectivity between your on-premises datacenter and Azure, use the [Azure Connectivity Toolkit (AzureCT)][azurect] to monitor and troubleshoot connection issues.</span></span>

<span data-ttu-id="ccbca-222">可以在文章[使用 Azure 和本地 VPN 实现混合网络体系结构][guidance-vpn-gateway-manageability]和[使用 Azure ExpressRoute 实现混合网络体系结构][guidance-expressroute-manageability]中找到专门针对监视和管理 VPN 和 ExpressRoute 连接的其他信息。</span><span class="sxs-lookup"><span data-stu-id="ccbca-222">You can find additional information specifically aimed at monitoring and managing VPN and ExpressRoute connections in the articles [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-manageability] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-manageability].</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ccbca-223">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="ccbca-223">Security considerations</span></span>

<span data-ttu-id="ccbca-224">此参考体系结构实现多个安全级别。</span><span class="sxs-lookup"><span data-stu-id="ccbca-224">This reference architecture implements multiple levels of security.</span></span>

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a><span data-ttu-id="ccbca-225">通过 NVA 路由所有本地用户请求</span><span class="sxs-lookup"><span data-stu-id="ccbca-225">Routing all on-premises user requests through the NVA</span></span>

<span data-ttu-id="ccbca-226">网关子网中的 UDR 阻止从本地接收的请求之外的所有用户请求。</span><span class="sxs-lookup"><span data-stu-id="ccbca-226">The UDR in the gateway subnet blocks all user requests other than those received from on-premises.</span></span> <span data-ttu-id="ccbca-227">UDR 将允许的请求传递到专用 DMZ 子网中的 NVA，如果 NVA 规则允许，则这些请求会传递到应用程序。</span><span class="sxs-lookup"><span data-stu-id="ccbca-227">The UDR passes allowed requests to the NVAs in the private DMZ subnet, and these requests are passed on to the application if they are allowed by the NVA rules.</span></span> <span data-ttu-id="ccbca-228">可以将其他路由添加到 UDR，但请确保它们不会无意中绕过 NVA 或阻止以管理子网为目标的管理流量。</span><span class="sxs-lookup"><span data-stu-id="ccbca-228">You can add other routes to the UDR, but make sure they don't inadvertently bypass the NVAs or block administrative traffic intended for the management subnet.</span></span>

<span data-ttu-id="ccbca-229">NVA 前面的负载均衡器还通过忽略在负载均衡规则中未打开的端口上的流量来充当安全设备。</span><span class="sxs-lookup"><span data-stu-id="ccbca-229">The load balancer in front of the NVAs also acts as a security device by ignoring traffic on ports that are not open in the load balancing rules.</span></span> <span data-ttu-id="ccbca-230">参考体系结构中的负载均衡器仅侦听端口 80 上的 HTTP 请求和端口 443 上的 HTTPS 请求。</span><span class="sxs-lookup"><span data-stu-id="ccbca-230">The load balancers in the reference architecture only listen for HTTP requests on port 80 and HTTPS requests on port 443.</span></span> <span data-ttu-id="ccbca-231">记录添加到负载均衡器的任何其他规则，并监视流量以确保不存在安全问题。</span><span class="sxs-lookup"><span data-stu-id="ccbca-231">Document any additional rules that you add to the load balancers, and monitor traffic to ensure there are no security issues.</span></span>

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a><span data-ttu-id="ccbca-232">使用 NSG 阻止/传递应用层之间的流量</span><span class="sxs-lookup"><span data-stu-id="ccbca-232">Using NSGs to block/pass traffic between application tiers</span></span>

<span data-ttu-id="ccbca-233">各层之间的流量使用 NSG 进行限制。</span><span class="sxs-lookup"><span data-stu-id="ccbca-233">Traffic between tiers is restricted by using NSGs.</span></span> <span data-ttu-id="ccbca-234">业务层阻止不是源自 Web 层的所有流量，而数据层阻止不是源自业务层的所有流量。</span><span class="sxs-lookup"><span data-stu-id="ccbca-234">The business tier blocks all traffic that doesn't originate in the web tier, and the data tier blocks all traffic that doesn't originate in the business tier.</span></span> <span data-ttu-id="ccbca-235">如果要求扩展 NSG 规则以允许对这些层进行更广泛的访问，请针对安全风险权衡这些要求。</span><span class="sxs-lookup"><span data-stu-id="ccbca-235">If you have a requirement to expand the NSG rules to allow broader access to these tiers, weigh these requirements against the security risks.</span></span> <span data-ttu-id="ccbca-236">每个新入站通道都表示可能会发生意外或有意的数据泄露或应用程序损坏。</span><span class="sxs-lookup"><span data-stu-id="ccbca-236">Each new inbound pathway represents an opportunity for accidental or purposeful data leakage or application damage.</span></span>

### <a name="devops-access"></a><span data-ttu-id="ccbca-237">DevOps 访问权限</span><span class="sxs-lookup"><span data-stu-id="ccbca-237">DevOps access</span></span>

<span data-ttu-id="ccbca-238">使用 [RBAC][rbac] 可限制 DevOps 可以在每个层执行的操作。</span><span class="sxs-lookup"><span data-stu-id="ccbca-238">Use [RBAC][rbac] to restrict the operations that DevOps can perform on each tier.</span></span> <span data-ttu-id="ccbca-239">授予权限时，请使用[最低特权原则][security-principle-of-least-privilege]。</span><span class="sxs-lookup"><span data-stu-id="ccbca-239">When granting permissions, use the [principle of least privilege][security-principle-of-least-privilege].</span></span> <span data-ttu-id="ccbca-240">记录所有管理操作并执行定期审核，确保所有配置更改按计划进行。</span><span class="sxs-lookup"><span data-stu-id="ccbca-240">Log all administrative operations and perform regular audits to ensure any configuration changes were planned.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="ccbca-241">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="ccbca-241">Deploy the solution</span></span>

<span data-ttu-id="ccbca-242">[GitHub][github-folder] 上提供了可实施这些建议的参考体系结构部署。</span><span class="sxs-lookup"><span data-stu-id="ccbca-242">A deployment for a reference architecture that implements these recommendations is available on [GitHub][github-folder].</span></span>

### <a name="prerequisites"></a><span data-ttu-id="ccbca-243">先决条件</span><span class="sxs-lookup"><span data-stu-id="ccbca-243">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-resources"></a><span data-ttu-id="ccbca-244">部署资源</span><span class="sxs-lookup"><span data-stu-id="ccbca-244">Deploy resources</span></span>

1. <span data-ttu-id="ccbca-245">导航到参考体系结构 GitHub 存储库的 `/dmz/secure-vnet-hybrid` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="ccbca-245">Navigate to the `/dmz/secure-vnet-hybrid` folder of the reference architectures GitHub repository.</span></span>

2. <span data-ttu-id="ccbca-246">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ccbca-246">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p onprem.json --deploy
    ```

3. <span data-ttu-id="ccbca-247">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ccbca-247">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p secure-vnet-hybrid.json --deploy
    ```

### <a name="connect-the-on-premises-and-azure-gateways"></a><span data-ttu-id="ccbca-248">连接本地网关和 Azure 网关</span><span class="sxs-lookup"><span data-stu-id="ccbca-248">Connect the on-premises and Azure gateways</span></span>

<span data-ttu-id="ccbca-249">此步骤连接两个本地网络网关。</span><span class="sxs-lookup"><span data-stu-id="ccbca-249">In this step, you will connect the two local network gateways.</span></span>

1. <span data-ttu-id="ccbca-250">在 Azure 门户中，导航到已创建的资源组。</span><span class="sxs-lookup"><span data-stu-id="ccbca-250">In the Azure Portal, navigate to the resource group that you created.</span></span>

2. <span data-ttu-id="ccbca-251">找到名为 `ra-vpn-vgw-pip` 的资源，并复制“概述”边栏选项卡中显示的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ccbca-251">Find the resource named `ra-vpn-vgw-pip` and copy the IP address shown in the **Overview** blade.</span></span>

3. <span data-ttu-id="ccbca-252">找到名为 `onprem-vpn-lgw` 的资源。</span><span class="sxs-lookup"><span data-stu-id="ccbca-252">Find the resource named `onprem-vpn-lgw`.</span></span>

4. <span data-ttu-id="ccbca-253">单击“配置”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="ccbca-253">Click the **Configuration** blade.</span></span> <span data-ttu-id="ccbca-254">在“IP 地址”下，粘贴步骤 2 中获取的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ccbca-254">Under **IP address**, paste in the IP address from step 2.</span></span>

    ![“IP 地址”字段的屏幕截图](./images/local-net-gw.png)

5. <span data-ttu-id="ccbca-256">单击“保存”并等待操作完成。</span><span class="sxs-lookup"><span data-stu-id="ccbca-256">Click **Save** and wait for the operation to complete.</span></span> <span data-ttu-id="ccbca-257">可能需要大约 5 分钟。</span><span class="sxs-lookup"><span data-stu-id="ccbca-257">It can take about 5 minutes.</span></span>

6. <span data-ttu-id="ccbca-258">找到名为 `onprem-vpn-gateway1-pip` 的资源。</span><span class="sxs-lookup"><span data-stu-id="ccbca-258">Find the resource named `onprem-vpn-gateway1-pip`.</span></span> <span data-ttu-id="ccbca-259">复制“概述”边栏选项卡中显示的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ccbca-259">Copy the IP address shown in the **Overview** blade.</span></span>

7. <span data-ttu-id="ccbca-260">找到名为 `ra-vpn-lgw` 的资源。</span><span class="sxs-lookup"><span data-stu-id="ccbca-260">Find the resource named `ra-vpn-lgw`.</span></span>

8. <span data-ttu-id="ccbca-261">单击“配置”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="ccbca-261">Click the **Configuration** blade.</span></span> <span data-ttu-id="ccbca-262">在“IP 地址”下，粘贴步骤 6 中获取的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ccbca-262">Under **IP address**, paste in the IP address from step 6.</span></span>

9. <span data-ttu-id="ccbca-263">单击“保存”并等待操作完成。</span><span class="sxs-lookup"><span data-stu-id="ccbca-263">Click **Save** and wait for the operation to complete.</span></span>

10. <span data-ttu-id="ccbca-264">若要验证连接，请转到每个网关的“连接”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="ccbca-264">To verify the connection, go to the **Connections** blade for each gateway.</span></span> <span data-ttu-id="ccbca-265">状态应为“已连接”。</span><span class="sxs-lookup"><span data-stu-id="ccbca-265">The status should be **Connected**.</span></span>

### <a name="verify-that-network-traffic-reaches-the-web-tier"></a><span data-ttu-id="ccbca-266">验证网络流量是否抵达 Web 层</span><span class="sxs-lookup"><span data-stu-id="ccbca-266">Verify that network traffic reaches the web tier</span></span>

1. <span data-ttu-id="ccbca-267">在 Azure 门户中，导航到已创建的资源组。</span><span class="sxs-lookup"><span data-stu-id="ccbca-267">In the Azure Portal, navigate to the resource group that you created.</span></span>

2. <span data-ttu-id="ccbca-268">找到名为 `int-dmz-lb` 的资源，即专用外围网络前面的负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="ccbca-268">Find the resource named `int-dmz-lb`, which is the load balancer in front of the private DMZ.</span></span> <span data-ttu-id="ccbca-269">复制“概述”边栏选项卡中的专用 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ccbca-269">Copy the private IP address from the **Overview** blade.</span></span>

3. <span data-ttu-id="ccbca-270">找到名为 `jb-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="ccbca-270">Find the VM named `jb-vm1`.</span></span> <span data-ttu-id="ccbca-271">单击“连接”，使用远程桌面连接到 VM。</span><span class="sxs-lookup"><span data-stu-id="ccbca-271">Click **Connect** and use Remote Desktop to connect to the VM.</span></span> <span data-ttu-id="ccbca-272">用户名和密码已在 onprem.json 文件中指定。</span><span class="sxs-lookup"><span data-stu-id="ccbca-272">The user name and password are specified in the onprem.json file.</span></span>

4. <span data-ttu-id="ccbca-273">在远程桌面会话中打开 Web 浏览器，并导航到步骤 2 中获取的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ccbca-273">From the Remote Desktop Session, open a web browser and navigate to the IP address from step 2.</span></span> <span data-ttu-id="ccbca-274">应会看到默认的 Apache2 服务器主页。</span><span class="sxs-lookup"><span data-stu-id="ccbca-274">You should see the default Apache2 server home page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ccbca-275">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ccbca-275">Next steps</span></span>

- <span data-ttu-id="ccbca-276">了解如何[在 Azure 与 Internet 之间实现外围网络](./secure-vnet-dmz.md)。</span><span class="sxs-lookup"><span data-stu-id="ccbca-276">Learn how to implement a [DMZ between Azure and the Internet](./secure-vnet-dmz.md).</span></span>
- <span data-ttu-id="ccbca-277">了解如何实现[高可用性混合网络体系结构][ra-vpn-failover]。</span><span class="sxs-lookup"><span data-stu-id="ccbca-277">Learn how to implement a [highly available hybrid network architecture][ra-vpn-failover].</span></span>
- <span data-ttu-id="ccbca-278">有关使用 Azure 管理网络安全的详细信息，请参阅 [Microsoft 云服务和网络安全][cloud-services-network-security]。</span><span class="sxs-lookup"><span data-stu-id="ccbca-278">For more information about managing network security with Azure, see [Microsoft cloud services and network security][cloud-services-network-security].</span></span>
- <span data-ttu-id="ccbca-279">有关在 Azure 中保护资源的详细信息，请参阅 [Microsoft Azure 安全入门][getting-started-with-azure-security]。</span><span class="sxs-lookup"><span data-stu-id="ccbca-279">For detailed information about protecting resources in Azure, see [Getting started with Microsoft Azure security][getting-started-with-azure-security].</span></span>
- <span data-ttu-id="ccbca-280">有关解决 Azure 网关连接上的安全问题的其他详细信息，请参阅[使用 Azure 和本地 VPN 实现混合网络体系结构][guidance-vpn-gateway-security]和[使用 Azure ExpressRoute 实现混合网络体系结构][guidance-expressroute-security]。</span><span class="sxs-lookup"><span data-stu-id="ccbca-280">For additional details on addressing security concerns across an Azure gateway connection, see [Implementing a hybrid network architecture with Azure and on-premises VPN][guidance-vpn-gateway-security] and [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute-security].</span></span>
- [<span data-ttu-id="ccbca-281">排查 Azure 中的网络虚拟设备问题</span><span class="sxs-lookup"><span data-stu-id="ccbca-281">Troubleshoot network virtual appliance issues in Azure</span></span>](/azure/virtual-network/virtual-network-troubleshoot-nva)

<!-- links -->

[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[azure-marketplace-nva]: https://azuremarketplace.microsoft.com/marketplace/apps/category/networking
[cloud-services-network-security]: /azure/best-practices-network-security
[getting-started-with-azure-security]: /azure/security/azure-security-getting-started
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/dmz/secure-vnet-hybrid
[guidance-expressroute]: ../hybrid-networking/expressroute.md
[guidance-expressroute-availability]: ../hybrid-networking/expressroute.md#availability-considerations
[guidance-expressroute-manageability]: ../hybrid-networking/expressroute.md#manageability-considerations
[guidance-expressroute-security]: ../hybrid-networking/expressroute.md#security-considerations
[guidance-expressroute-scalability]: ../hybrid-networking/expressroute.md#scalability-considerations
[guidance-vpn-gateway]: ../hybrid-networking/vpn.md
[guidance-vpn-gateway-availability]: ../hybrid-networking/vpn.md#availability-considerations
[guidance-vpn-gateway-manageability]: ../hybrid-networking/vpn.md#manageability-considerations
[guidance-vpn-gateway-scalability]: ../hybrid-networking/vpn.md#scalability-considerations
[guidance-vpn-gateway-security]: ../hybrid-networking/vpn.md#security-considerations
[ip-forwarding]: /azure/virtual-network/virtual-networks-udr-overview#ip-forwarding
[ra-expressroute]: ../hybrid-networking/expressroute.md
[ra-n-tier]: ../virtual-machines-windows/n-tier.md
[ra-vpn]: ../hybrid-networking/vpn.md
[ra-vpn-failover]: ../hybrid-networking/expressroute-vpn-failover.md
[rbac]: /azure/active-directory/role-based-access-control-configure
[rbac-custom-roles]: /azure/active-directory/role-based-access-control-custom-roles
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: /azure/virtual-network/virtual-networks-udr-overview
[visio-download]: https://archcenter.blob.core.windows.net/cdn/dmz-reference-architectures.vsdx
[wireshark]: https://www.wireshark.org/
