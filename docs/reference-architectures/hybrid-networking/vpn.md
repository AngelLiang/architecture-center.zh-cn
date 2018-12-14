---
title: 使用 VPN 将本地网络连接到 Azure
titleSuffix: Azure Reference Architectures
description: 实现这样一个安全的站点到站点网络体系结构：跨 Azure 虚拟网络，以及使用 VPN 建立连接的本地网络。
author: RohitSharma-pnp
ms.date: 10/22/2018
ms.openlocfilehash: a1bb2e250cb261e1a56abfb58b099fd078c068e5
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53120435"
---
# <a name="connect-an-on-premises-network-to-azure-using-a-vpn-gateway"></a><span data-ttu-id="82628-103">使用 VPN 网关将本地网络连接到 Azure</span><span class="sxs-lookup"><span data-stu-id="82628-103">Connect an on-premises network to Azure using a VPN gateway</span></span>

<span data-ttu-id="82628-104">此参考体系结构演示如何使用站点到站点虚拟专用网络 (VPN) 将本地网络扩展到 Azure。</span><span class="sxs-lookup"><span data-stu-id="82628-104">This reference architecture shows how to extend an on-premises network to Azure, using a site-to-site virtual private network (VPN).</span></span> <span data-ttu-id="82628-105">本地网络与 Azure 虚拟专用网络 (VNet) 之间的流量通过 IPSec VPN 隧道传送。</span><span class="sxs-lookup"><span data-stu-id="82628-105">Traffic flows between the on-premises network and an Azure Virtual Network (VNet) through an IPSec VPN tunnel.</span></span> <span data-ttu-id="82628-106">[**部署此解决方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="82628-106">[**Deploy this solution**](#deploy-the-solution).</span></span>

![跨本地和 Azure 基础结构的混合网络](./images/vpn.png)

<span data-ttu-id="82628-108">下载此体系结构的 [Visio 文件][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="82628-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="82628-109">体系结构</span><span class="sxs-lookup"><span data-stu-id="82628-109">Architecture</span></span>

<span data-ttu-id="82628-110">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="82628-110">The architecture consists of the following components.</span></span>

- <span data-ttu-id="82628-111">**本地网络**。</span><span class="sxs-lookup"><span data-stu-id="82628-111">**On-premises network**.</span></span> <span data-ttu-id="82628-112">组织中运行的专用局域网。</span><span class="sxs-lookup"><span data-stu-id="82628-112">A private local-area network running within an organization.</span></span>

- <span data-ttu-id="82628-113">**VPN 设备**。</span><span class="sxs-lookup"><span data-stu-id="82628-113">**VPN appliance**.</span></span> <span data-ttu-id="82628-114">用于与本地网络建立外部连接的设备或服务。</span><span class="sxs-lookup"><span data-stu-id="82628-114">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="82628-115">该 VPN 设备可以是硬件设备，也可以是软件解决方案，例如 Windows Server 2012 中的路由和远程访问服务 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="82628-115">The VPN appliance may be a hardware device, or it can be a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="82628-116">有关支持的 VPN 设备的列表以及有关如何配置它们以连接到 Azure VPN 网关的信息，请参阅[关于站点到站点 VPN 网关连接的 VPN 设备][vpn-appliance]一文中针对所选设备的说明。</span><span class="sxs-lookup"><span data-stu-id="82628-116">For a list of supported VPN appliances and information on configuring them to connect to an Azure VPN gateway, see the instructions for the selected device in the article [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

- <span data-ttu-id="82628-117">**虚拟网络 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="82628-117">**Virtual network (VNet)**.</span></span> <span data-ttu-id="82628-118">云应用程序和 Azure VPN 网关的组件驻留在相同 [VNet][azure-virtual-network] 中。</span><span class="sxs-lookup"><span data-stu-id="82628-118">The cloud application and the components for the Azure VPN gateway reside in the same [VNet][azure-virtual-network].</span></span>

- <span data-ttu-id="82628-119">**Azure VPN 网关**。</span><span class="sxs-lookup"><span data-stu-id="82628-119">**Azure VPN gateway**.</span></span> <span data-ttu-id="82628-120">[VPN 网关][azure-vpn-gateway]服务使你可以通过 VPN 设备将 VNet 连接到本地网络。</span><span class="sxs-lookup"><span data-stu-id="82628-120">The [VPN gateway][azure-vpn-gateway] service enables you to connect the VNet to the on-premises network through a VPN appliance.</span></span> <span data-ttu-id="82628-121">有关详细信息，请参阅[将本地网络连接到 Microsoft Azure 虚拟网络][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="82628-121">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span> <span data-ttu-id="82628-122">VPN 网关包括以下元素：</span><span class="sxs-lookup"><span data-stu-id="82628-122">The VPN gateway includes the following elements:</span></span>

  - <span data-ttu-id="82628-123">**虚拟网络网关**。</span><span class="sxs-lookup"><span data-stu-id="82628-123">**Virtual network gateway**.</span></span> <span data-ttu-id="82628-124">为 VNet 提供虚拟 VPN 设备的资源。</span><span class="sxs-lookup"><span data-stu-id="82628-124">A resource that provides a virtual VPN appliance for the VNet.</span></span> <span data-ttu-id="82628-125">它负责将流量从本地网络路由到 VNet。</span><span class="sxs-lookup"><span data-stu-id="82628-125">It is responsible for routing traffic from the on-premises network to the VNet.</span></span>
  - <span data-ttu-id="82628-126">**本地网络网关**。</span><span class="sxs-lookup"><span data-stu-id="82628-126">**Local network gateway**.</span></span> <span data-ttu-id="82628-127">本地 VPN 设备的抽象。</span><span class="sxs-lookup"><span data-stu-id="82628-127">An abstraction of the on-premises VPN appliance.</span></span> <span data-ttu-id="82628-128">从云应用程序到本地网络的网络流量通过此网关进行路由。</span><span class="sxs-lookup"><span data-stu-id="82628-128">Network traffic from the cloud application to the on-premises network is routed through this gateway.</span></span>
  - <span data-ttu-id="82628-129">**连接**。</span><span class="sxs-lookup"><span data-stu-id="82628-129">**Connection**.</span></span> <span data-ttu-id="82628-130">该连接包含一些属性，这些属性指定连接类型 (IPSec)，以及与本地 VPN 设备共享的、用于加密流量的密钥。</span><span class="sxs-lookup"><span data-stu-id="82628-130">The connection has properties that specify the connection type (IPSec) and the key shared with the on-premises VPN appliance to encrypt traffic.</span></span>
  - <span data-ttu-id="82628-131">**网关子网**。</span><span class="sxs-lookup"><span data-stu-id="82628-131">**Gateway subnet**.</span></span> <span data-ttu-id="82628-132">虚拟网络网关保留在自己的子网中，该子网需满足下面“建议”一节中所述的要求。</span><span class="sxs-lookup"><span data-stu-id="82628-132">The virtual network gateway is held in its own subnet, which is subject to various requirements, described in the Recommendations section below.</span></span>

- <span data-ttu-id="82628-133">**云应用程序**。</span><span class="sxs-lookup"><span data-stu-id="82628-133">**Cloud application**.</span></span> <span data-ttu-id="82628-134">Azure 中托管的应用程序。</span><span class="sxs-lookup"><span data-stu-id="82628-134">The application hosted in Azure.</span></span> <span data-ttu-id="82628-135">它可以包含多个层，以及通过 Azure 负载均衡器连接的多个子网。</span><span class="sxs-lookup"><span data-stu-id="82628-135">It might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="82628-136">有关应用程序基础结构的详细信息，请参阅[运行 Windows VM 工作负荷][windows-vm-ra]和[运行 Linux VM 工作负荷][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="82628-136">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

- <span data-ttu-id="82628-137">**内部负载均衡器**。</span><span class="sxs-lookup"><span data-stu-id="82628-137">**Internal load balancer**.</span></span> <span data-ttu-id="82628-138">来自 VPN 网关的网络流量通过内部负载均衡器路由到云应用程序。</span><span class="sxs-lookup"><span data-stu-id="82628-138">Network traffic from the VPN gateway is routed to the cloud application through an internal load balancer.</span></span> <span data-ttu-id="82628-139">该负载均衡器位于应用程序的前端子网中。</span><span class="sxs-lookup"><span data-stu-id="82628-139">The load balancer is located in the front-end subnet of the application.</span></span>

## <a name="recommendations"></a><span data-ttu-id="82628-140">建议</span><span class="sxs-lookup"><span data-stu-id="82628-140">Recommendations</span></span>

<span data-ttu-id="82628-141">以下建议适用于大多数方案。</span><span class="sxs-lookup"><span data-stu-id="82628-141">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="82628-142">除非有优先于这些建议的特定要求，否则请遵循这些建议。</span><span class="sxs-lookup"><span data-stu-id="82628-142">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="vnet-and-gateway-subnet"></a><span data-ttu-id="82628-143">VNet 和网关子网</span><span class="sxs-lookup"><span data-stu-id="82628-143">VNet and gateway subnet</span></span>

<span data-ttu-id="82628-144">创建地址空间的大小足以容纳所有所需资源的 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="82628-144">Create an Azure VNet with an address space large enough for all of your required resources.</span></span> <span data-ttu-id="82628-145">确保 VNet 地址空间具有足够空间，以便在将来可能需要更多 VM 时增长。</span><span class="sxs-lookup"><span data-stu-id="82628-145">Ensure that the VNet address space has sufficient room for growth if additional VMs are likely to be needed in the future.</span></span> <span data-ttu-id="82628-146">VNet 的地址空间不得与本地网络重叠。</span><span class="sxs-lookup"><span data-stu-id="82628-146">The address space of the VNet must not overlap with the on-premises network.</span></span> <span data-ttu-id="82628-147">例如，上图将地址空间 10.20.0.0/16 用于 VNet。</span><span class="sxs-lookup"><span data-stu-id="82628-147">For example, the diagram above uses the address space 10.20.0.0/16 for the VNet.</span></span>

<span data-ttu-id="82628-148">创建一个名为 *GatewaySubnet* 的子网，使其地址范围为 /27。</span><span class="sxs-lookup"><span data-stu-id="82628-148">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="82628-149">此子网是虚拟网络网关所必需的。</span><span class="sxs-lookup"><span data-stu-id="82628-149">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="82628-150">向此子网分配 32 个地址将有助于防止将来达到网关大小限制。</span><span class="sxs-lookup"><span data-stu-id="82628-150">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span> <span data-ttu-id="82628-151">此外，避免将此子网置于地址空间中间。</span><span class="sxs-lookup"><span data-stu-id="82628-151">Also, avoid placing this subnet in the middle of the address space.</span></span> <span data-ttu-id="82628-152">一个好的做法是将网关子网的地址空间设置在 VNet 地址空间上端。</span><span class="sxs-lookup"><span data-stu-id="82628-152">A good practice is to set the address space for the gateway subnet at the upper end of the VNet address space.</span></span> <span data-ttu-id="82628-153">图中所示的示例使用 10.20.255.224/27。</span><span class="sxs-lookup"><span data-stu-id="82628-153">The example shown in the diagram uses 10.20.255.224/27.</span></span>  <span data-ttu-id="82628-154">下面是用于计算 [CIDR] 的快速过程：</span><span class="sxs-lookup"><span data-stu-id="82628-154">Here is a quick procedure to calculate the [CIDR]:</span></span>

1. <span data-ttu-id="82628-155">将 VNet 地址空间中的变量位设置为 1（直到网关子网所使用的位），然后将剩余位设置为 0。</span><span class="sxs-lookup"><span data-stu-id="82628-155">Set the variable bits in the address space of the VNet to 1, up to the bits being used by the gateway subnet, then set the remaining bits to 0.</span></span>
2. <span data-ttu-id="82628-156">将得到的位转换为十进制，然后将它表示为前缀长度设置为网关子网大小的地址空间。</span><span class="sxs-lookup"><span data-stu-id="82628-156">Convert the resulting bits to decimal and express it as an address space with the prefix length set to the size of the gateway subnet.</span></span>

<span data-ttu-id="82628-157">例如，对于 IP 地址范围为 10.20.0.0/16 的 VNet，应用上面的步骤 1 会成为 10.20.0b11111111.0b11100000。</span><span class="sxs-lookup"><span data-stu-id="82628-157">For example, for a VNet with an IP address range of 10.20.0.0/16, applying step #1 above becomes 10.20.0b11111111.0b11100000.</span></span>  <span data-ttu-id="82628-158">将该数字转换为十进制并将它表示为地址空间会生成 10.20.255.224/27。</span><span class="sxs-lookup"><span data-stu-id="82628-158">Converting that to decimal and expressing it as an address space yields 10.20.255.224/27.</span></span>

> [!WARNING]
> <span data-ttu-id="82628-159">请勿向网关子网部署任何 VM。</span><span class="sxs-lookup"><span data-stu-id="82628-159">Do not deploy any VMs to the gateway subnet.</span></span> <span data-ttu-id="82628-160">此外，请勿向此子网分配 NSG，因为它会导致网关停止工作。</span><span class="sxs-lookup"><span data-stu-id="82628-160">Also, do not assign an NSG to this subnet, as it will cause the gateway to stop functioning.</span></span>
>

### <a name="virtual-network-gateway"></a><span data-ttu-id="82628-161">虚拟网络网关</span><span class="sxs-lookup"><span data-stu-id="82628-161">Virtual network gateway</span></span>

<span data-ttu-id="82628-162">为虚拟网络网关分配公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82628-162">Allocate a public IP address for the virtual network gateway.</span></span>

<span data-ttu-id="82628-163">在网关子网中创建虚拟网络网关，并将新分配的公共 IP 地址分配给它。</span><span class="sxs-lookup"><span data-stu-id="82628-163">Create the virtual network gateway in the gateway subnet and assign it the newly allocated public IP address.</span></span> <span data-ttu-id="82628-164">使用最符合你要求并且通过 VPN 设备启用的网关类型：</span><span class="sxs-lookup"><span data-stu-id="82628-164">Use the gateway type that most closely matches your requirements and that is enabled by your VPN appliance:</span></span>

- <span data-ttu-id="82628-165">如果需要基于策略条件（如地址前缀）密切控制如何对请求进行路由，请创建[基于策略的网关][policy-based-routing]。</span><span class="sxs-lookup"><span data-stu-id="82628-165">Create a [policy-based gateway][policy-based-routing] if you need to closely control how requests are routed based on policy criteria such as address prefixes.</span></span> <span data-ttu-id="82628-166">基于策略的网关使用静态路由，仅适用于站点到站点连接。</span><span class="sxs-lookup"><span data-stu-id="82628-166">Policy-based gateways use static routing, and only work with site-to-site connections.</span></span>

- <span data-ttu-id="82628-167">如果使用 RRAS 连接到本地网络、支持多站点或跨区域连接或是实现 VNet 到 VNet 连接（包含遍历多个 VNet 的路由），请创建[基于路由的网关][route-based-routing]。</span><span class="sxs-lookup"><span data-stu-id="82628-167">Create a [route-based gateway][route-based-routing] if you connect to the on-premises network using RRAS, support multi-site or cross-region connections, or implement VNet-to-VNet connections (including routes that traverse multiple VNets).</span></span> <span data-ttu-id="82628-168">基于路由的网关使用动态路由定向网络之间的流量。</span><span class="sxs-lookup"><span data-stu-id="82628-168">Route-based gateways use dynamic routing to direct traffic between networks.</span></span> <span data-ttu-id="82628-169">它们在网络路径中的容错能力好于静态路由，因为它们可以尝试备用路由。</span><span class="sxs-lookup"><span data-stu-id="82628-169">They can tolerate failures in the network path better than static routes because they can try alternative routes.</span></span> <span data-ttu-id="82628-170">基于路由的网关还可以减少管理开销，因为路由在网络地址更改时可以无需手动更新。</span><span class="sxs-lookup"><span data-stu-id="82628-170">Route-based gateways can also reduce the management overhead because routes might not need to be updated manually when network addresses change.</span></span>

<span data-ttu-id="82628-171">有关支持的 VPN 设备的列表，请参阅[关于站点到站点 VPN 网关连接的 VPN 设备][vpn-appliances]。</span><span class="sxs-lookup"><span data-stu-id="82628-171">For a list of supported VPN appliances, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliances].</span></span>

> [!NOTE]
> <span data-ttu-id="82628-172">创建网关后，必须先删除并重新创建网关才能更改网关类型。</span><span class="sxs-lookup"><span data-stu-id="82628-172">After the gateway has been created, you cannot change between gateway types without deleting and re-creating the gateway.</span></span>
>

<span data-ttu-id="82628-173">选择最符合你吞吐量要求的 Azure VPN 网关 SKU。</span><span class="sxs-lookup"><span data-stu-id="82628-173">Select the Azure VPN gateway SKU that most closely matches your throughput requirements.</span></span> <span data-ttu-id="82628-174">有关详细信息，请参阅[网关 SKU][azure-gateway-skus]</span><span class="sxs-lookup"><span data-stu-id="82628-174">For more informayion, see [Gateway SKUs][azure-gateway-skus]</span></span>

> [!NOTE]
> <span data-ttu-id="82628-175">基本 SKU 不与 Azure ExpressRoute 兼容。</span><span class="sxs-lookup"><span data-stu-id="82628-175">The Basic SKU is not compatible with Azure ExpressRoute.</span></span> <span data-ttu-id="82628-176">可以在创建网关之后[更改 SKU][changing-SKUs]。</span><span class="sxs-lookup"><span data-stu-id="82628-176">You can [change the SKU][changing-SKUs] after the gateway has been created.</span></span>
>

<span data-ttu-id="82628-177">会基于预配和提供网关的时间量进行收费。</span><span class="sxs-lookup"><span data-stu-id="82628-177">You are charged based on the amount of time that the gateway is provisioned and available.</span></span> <span data-ttu-id="82628-178">请参阅 [VPN 网关定价][azure-gateway-charges]。</span><span class="sxs-lookup"><span data-stu-id="82628-178">See [VPN Gateway Pricing][azure-gateway-charges].</span></span>

<span data-ttu-id="82628-179">为网关子网创建将传入应用程序流量从网关定向到内部负载均衡器，而不是允许请求直接传递到应用程序 VM 的路由规则。</span><span class="sxs-lookup"><span data-stu-id="82628-179">Create routing rules for the gateway subnet that direct incoming application traffic from the gateway to the internal load balancer, rather than allowing requests to pass directly to the application VMs.</span></span>

### <a name="on-premises-network-connection"></a><span data-ttu-id="82628-180">本地网络连接</span><span class="sxs-lookup"><span data-stu-id="82628-180">On-premises network connection</span></span>

<span data-ttu-id="82628-181">创建本地网络网关。</span><span class="sxs-lookup"><span data-stu-id="82628-181">Create a local network gateway.</span></span> <span data-ttu-id="82628-182">指定本地 VPN 设备的公共 IP 地址以及本地网络的地址空间。</span><span class="sxs-lookup"><span data-stu-id="82628-182">Specify the public IP address of the on-premises VPN appliance, and the address space of the on-premises network.</span></span> <span data-ttu-id="82628-183">请注意，本地 VPN 设备必须具有可由 Azure VPN 网关中的本地网络网关访问的公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82628-183">Note that the on-premises VPN appliance must have a public IP address that can be accessed by the local network gateway in Azure VPN Gateway.</span></span> <span data-ttu-id="82628-184">VPN 设备不能位于网络地址转换 (NAT) 设备后面。</span><span class="sxs-lookup"><span data-stu-id="82628-184">The VPN device cannot be located behind a network address translation (NAT) device.</span></span>

<span data-ttu-id="82628-185">为虚拟网络网关和本地网络网关创建站点到站点连接。</span><span class="sxs-lookup"><span data-stu-id="82628-185">Create a site-to-site connection for the virtual network gateway and the local network gateway.</span></span> <span data-ttu-id="82628-186">选择站点到站点 (IPSec) 连接类型，并指定共享密钥。</span><span class="sxs-lookup"><span data-stu-id="82628-186">Select the site-to-site (IPSec) connection type, and specify the shared key.</span></span> <span data-ttu-id="82628-187">具有 Azure VPN 网关的站点到站点加密基于 IPSec 协议，使用预共享密钥进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="82628-187">Site-to-site encryption with the Azure VPN gateway is based on the IPSec protocol, using preshared keys for authentication.</span></span> <span data-ttu-id="82628-188">创建 Azure VPN 网关时需指定密钥。</span><span class="sxs-lookup"><span data-stu-id="82628-188">You specify the key when you create the Azure VPN gateway.</span></span> <span data-ttu-id="82628-189">必须使用相同密钥配置在本地运行的 VPN 设备。</span><span class="sxs-lookup"><span data-stu-id="82628-189">You must configure the VPN appliance running on-premises with the same key.</span></span> <span data-ttu-id="82628-190">当前不支持其他身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="82628-190">Other authentication mechanisms are not currently supported.</span></span>

<span data-ttu-id="82628-191">确保本地路由基础结构配置为将目标为 Azure VNet 中的地址的请求转发到 VPN 设备。</span><span class="sxs-lookup"><span data-stu-id="82628-191">Ensure that the on-premises routing infrastructure is configured to forward requests intended for addresses in the Azure VNet to the VPN device.</span></span>

<span data-ttu-id="82628-192">在本地网络中打开云应用程序所需的任何端口。</span><span class="sxs-lookup"><span data-stu-id="82628-192">Open any ports required by the cloud application in the on-premises network.</span></span>

<span data-ttu-id="82628-193">测试连接以验证以下事项：</span><span class="sxs-lookup"><span data-stu-id="82628-193">Test the connection to verify that:</span></span>

- <span data-ttu-id="82628-194">本地 VPN 设备通过 Azure VPN 网关将流量正确路由到云应用程序。</span><span class="sxs-lookup"><span data-stu-id="82628-194">The on-premises VPN appliance correctly routes traffic to the cloud application through the Azure VPN gateway.</span></span>
- <span data-ttu-id="82628-195">VNet 将流量正确路由回本地网络。</span><span class="sxs-lookup"><span data-stu-id="82628-195">The VNet correctly routes traffic back to the on-premises network.</span></span>
- <span data-ttu-id="82628-196">正确阻止两个方向上的禁止流量。</span><span class="sxs-lookup"><span data-stu-id="82628-196">Prohibited traffic in both directions is blocked correctly.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="82628-197">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="82628-197">Scalability considerations</span></span>

<span data-ttu-id="82628-198">可以通过从基本或标准 VPN 网关 SKU 升级到高性能 VPN SKU，来实现有限纵向可缩放性。</span><span class="sxs-lookup"><span data-stu-id="82628-198">You can achieve limited vertical scalability by moving from the Basic or Standard VPN Gateway SKUs to the High Performance VPN SKU.</span></span>

<span data-ttu-id="82628-199">对于预期存在大量 VPN 流量的 VNet，请考虑将不同工作负载分发到单独的较小 VNet 以及为每个 VNet 都配置 VPN 网关。</span><span class="sxs-lookup"><span data-stu-id="82628-199">For VNets that expect a large volume of VPN traffic, consider distributing the different workloads into separate smaller VNets and configuring a VPN gateway for each of them.</span></span>

<span data-ttu-id="82628-200">可以按水平或垂直方式对 VNet 进行分区。</span><span class="sxs-lookup"><span data-stu-id="82628-200">You can partition the VNet either horizontally or vertically.</span></span> <span data-ttu-id="82628-201">若要进行水平分区，请将某些 VM 实例从每个层移动到新 VNet 的子网中。</span><span class="sxs-lookup"><span data-stu-id="82628-201">To partition horizontally, move some VM instances from each tier into subnets of the new VNet.</span></span> <span data-ttu-id="82628-202">结果是每个 VNet 都具有相同结构和功能。</span><span class="sxs-lookup"><span data-stu-id="82628-202">The result is that each VNet has the same structure and functionality.</span></span> <span data-ttu-id="82628-203">若要进行垂直分区，请重新设计每个层，以将功能划分为不同逻辑区域（如处理订单、开发票、客户帐户管理等）。</span><span class="sxs-lookup"><span data-stu-id="82628-203">To partition vertically, redesign each tier to divide the functionality into different logical areas (such as handling orders, invoicing, customer account management, and so on).</span></span> <span data-ttu-id="82628-204">每个功能区域随后可以放置在自己的 VNet 中。</span><span class="sxs-lookup"><span data-stu-id="82628-204">Each functional area can then be placed in its own VNet.</span></span>

<span data-ttu-id="82628-205">在 VNet 中复制本地 Active Directory 域控制器以及在 VNet 中实现 DNS 可以帮助减少从本地到云的某些安全相关和管理流量。</span><span class="sxs-lookup"><span data-stu-id="82628-205">Replicating an on-premises Active Directory domain controller in the VNet, and implementing DNS in the VNet, can help to reduce some of the security-related and administrative traffic flowing from on-premises to the cloud.</span></span> <span data-ttu-id="82628-206">有关详细信息，请参阅[将 Active Directory 域服务 (AD DS) 扩展到 Azure][adds-extend-domain]。</span><span class="sxs-lookup"><span data-stu-id="82628-206">For more information, see [Extending Active Directory Domain Services (AD DS) to Azure][adds-extend-domain].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="82628-207">可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="82628-207">Availability considerations</span></span>

<span data-ttu-id="82628-208">如果需要确保本地网络对 Azure VPN 网关保持可用，请为本地 VPN 网关实现故障转移群集。</span><span class="sxs-lookup"><span data-stu-id="82628-208">If you need to ensure that the on-premises network remains available to the Azure VPN gateway, implement a failover cluster for the on-premises VPN gateway.</span></span>

<span data-ttu-id="82628-209">如果组织具有多个本地站点，请创建与一个或多个 Azure VNet 之间的[多站点连接][vpn-gateway-multi-site]。</span><span class="sxs-lookup"><span data-stu-id="82628-209">If your organization has multiple on-premises sites, create [multi-site connections][vpn-gateway-multi-site] to one or more Azure VNets.</span></span> <span data-ttu-id="82628-210">此方法需要动态（基于路由的）路由，因此请确保本地 VPN 网关支持此功能。</span><span class="sxs-lookup"><span data-stu-id="82628-210">This approach requires dynamic (route-based) routing, so make sure that the on-premises VPN gateway supports this feature.</span></span>

<span data-ttu-id="82628-211">有关服务级别协议的详细信息，请参阅 [VPN 网关的 SLA][sla-for-vpn-gateway]。</span><span class="sxs-lookup"><span data-stu-id="82628-211">For details about service level agreements, see [SLA for VPN Gateway][sla-for-vpn-gateway].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="82628-212">可管理性注意事项</span><span class="sxs-lookup"><span data-stu-id="82628-212">Manageability considerations</span></span>

<span data-ttu-id="82628-213">监视来自本地 VPN 设备的诊断信息。</span><span class="sxs-lookup"><span data-stu-id="82628-213">Monitor diagnostic information from on-premises VPN appliances.</span></span> <span data-ttu-id="82628-214">此过程取决于 VPN 设备提供的功能。</span><span class="sxs-lookup"><span data-stu-id="82628-214">This process depends on the features provided by the VPN appliance.</span></span> <span data-ttu-id="82628-215">例如，如果在 Windows Server 2012 上使用路由和远程访问服务，则可使用 [RRAS 日志记录][rras-logging]。</span><span class="sxs-lookup"><span data-stu-id="82628-215">For example, if you are using the Routing and Remote Access Service on Windows Server 2012, [RRAS logging][rras-logging].</span></span>

<span data-ttu-id="82628-216">使用 [Azure VPN 网关诊断][gateway-diagnostic-logs]可捕获有关连接问题的信息。</span><span class="sxs-lookup"><span data-stu-id="82628-216">Use [Azure VPN gateway diagnostics][gateway-diagnostic-logs] to capture information about connectivity issues.</span></span> <span data-ttu-id="82628-217">这些日志可以用于跟踪信息，如连接请求的源和目标、使用的协议以及连接的建立方式（或尝试失败的原因）。</span><span class="sxs-lookup"><span data-stu-id="82628-217">These logs can be used to track information such as the source and destinations of connection requests, which protocol was used, and how the connection was established (or why the attempt failed).</span></span>

<span data-ttu-id="82628-218">使用 Azure 门户中提供的审核日志监视 Azure VPN 网关的运行日志。</span><span class="sxs-lookup"><span data-stu-id="82628-218">Monitor the operational logs of the Azure VPN gateway using the audit logs available in the Azure portal.</span></span> <span data-ttu-id="82628-219">为本地网络网关、Azure 网络网关和连接分别提供了单独的日志。</span><span class="sxs-lookup"><span data-stu-id="82628-219">Separate logs are available for the local network gateway, the Azure network gateway, and the connection.</span></span> <span data-ttu-id="82628-220">此信息可以用于跟踪对网关进行的任何更改，并且在以前正常运行的网关由于某种原因而停止工作时可能会十分有用。</span><span class="sxs-lookup"><span data-stu-id="82628-220">This information can be used to track any changes made to the gateway, and can be useful if a previously functioning gateway stops working for some reason.</span></span>

![Azure 门户中的审核日志](../_images/guidance-hybrid-network-vpn/audit-logs.png)

<span data-ttu-id="82628-222">监视连接，并跟踪连接失败事件。</span><span class="sxs-lookup"><span data-stu-id="82628-222">Monitor connectivity, and track connectivity failure events.</span></span> <span data-ttu-id="82628-223">可以使用监视包（如 [Nagios][nagios]）捕获并报告此信息。</span><span class="sxs-lookup"><span data-stu-id="82628-223">You can use a monitoring package such as [Nagios][nagios] to capture and report this information.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="82628-224">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="82628-224">Security considerations</span></span>

<span data-ttu-id="82628-225">为每个 VPN 网关生成不同的共享密钥。</span><span class="sxs-lookup"><span data-stu-id="82628-225">Generate a different shared key for each VPN gateway.</span></span> <span data-ttu-id="82628-226">使用强共享密钥可帮助抵御暴力攻击。</span><span class="sxs-lookup"><span data-stu-id="82628-226">Use a strong shared key to help resist brute-force attacks.</span></span>

> [!NOTE]
> <span data-ttu-id="82628-227">当前无法使用 Azure Key Vault 为 Azure VPN 网关预共享密钥。</span><span class="sxs-lookup"><span data-stu-id="82628-227">Currently, you cannot use Azure Key Vault to preshare keys for the Azure VPN gateway.</span></span>
>

<span data-ttu-id="82628-228">确保本地 VPN 设备使用的加密方法[与 Azure VPN 网关兼容][vpn-appliance-ipsec]。</span><span class="sxs-lookup"><span data-stu-id="82628-228">Ensure that the on-premises VPN appliance uses an encryption method that is [compatible with the Azure VPN gateway][vpn-appliance-ipsec].</span></span> <span data-ttu-id="82628-229">对于基于策略的路由，Azure VPN 网关支持 AES256、AES128 和 3DES 加密算法。</span><span class="sxs-lookup"><span data-stu-id="82628-229">For policy-based routing, the Azure VPN gateway supports the AES256, AES128, and 3DES encryption algorithms.</span></span> <span data-ttu-id="82628-230">基于路由的网关支持 AES256 和 3DES。</span><span class="sxs-lookup"><span data-stu-id="82628-230">Route-based gateways support AES256 and 3DES.</span></span>

<span data-ttu-id="82628-231">如果本地 VPN 设备位于在外围网络 (DMZ) 与 Internet 之间具有防火墙的外围网络中，则可能必须配置[其他防火墙规则][additional-firewall-rules]以允许实现站点到站点 VPN 连接。</span><span class="sxs-lookup"><span data-stu-id="82628-231">If your on-premises VPN appliance is on a perimeter network (DMZ) that has a firewall between the perimeter network and the Internet, you might have to configure [additional firewall rules][additional-firewall-rules] to allow the site-to-site VPN connection.</span></span>

<span data-ttu-id="82628-232">如果 VNet 中的应用程序将数据发送到 Internet，请考虑[实现强制隧道][forced-tunneling]以通过本地网络路由所有 Internet 绑定流量。</span><span class="sxs-lookup"><span data-stu-id="82628-232">If the application in the VNet sends data to the Internet, consider [implementing forced tunneling][forced-tunneling] to route all Internet-bound traffic through the on-premises network.</span></span> <span data-ttu-id="82628-233">此方法使你可以审核应用程序从本地基础结构进行的传出请求。</span><span class="sxs-lookup"><span data-stu-id="82628-233">This approach enables you to audit outgoing requests made by the application from the on-premises infrastructure.</span></span>

> [!NOTE]
> <span data-ttu-id="82628-234">强制隧道可能会影响与 Azure 服务（例如存储服务）和 Windows 许可证管理器之间的连接。</span><span class="sxs-lookup"><span data-stu-id="82628-234">Forced tunneling can impact connectivity to Azure services (the Storage Service, for example) and the Windows license manager.</span></span>
>

## <a name="troubleshooting"></a><span data-ttu-id="82628-235">故障排除</span><span class="sxs-lookup"><span data-stu-id="82628-235">Troubleshooting</span></span>

<span data-ttu-id="82628-236">有关常见 VPN 相关错误故障排除的常规信息，请参阅[常见 VPN 相关错误故障排除][troubleshooting-vpn-errors]。</span><span class="sxs-lookup"><span data-stu-id="82628-236">For general information on troubleshooting common VPN-related errors, see [Troubleshooting common VPN related errors][troubleshooting-vpn-errors].</span></span>

<span data-ttu-id="82628-237">以下建议可用于确定本地 VPN 设备是否正常运行。</span><span class="sxs-lookup"><span data-stu-id="82628-237">The following recommendations are useful for determining if your on-premises VPN appliance is functioning correctly.</span></span>

- <span data-ttu-id="82628-238">**在 VPN 设备生成的任何日志文件中检查是否存在错误或故障。**</span><span class="sxs-lookup"><span data-stu-id="82628-238">**Check any log files generated by the VPN appliance for errors or failures.**</span></span>

    <span data-ttu-id="82628-239">这会帮助确定 VPN 设备是否正常运行。</span><span class="sxs-lookup"><span data-stu-id="82628-239">This will help you determine if the VPN appliance is functioning correctly.</span></span> <span data-ttu-id="82628-240">此信息的位置因设备而异。</span><span class="sxs-lookup"><span data-stu-id="82628-240">The location of this information will vary according to your appliance.</span></span> <span data-ttu-id="82628-241">例如，如果在 Windows Server 2012 上使用 RRAS，则可以使用以下 PowerShell 命令显示 RRAS 服务的错误事件信息：</span><span class="sxs-lookup"><span data-stu-id="82628-241">For example, if you are using RRAS on Windows Server 2012, you can use the following PowerShell command to display error event information for the RRAS service:</span></span>

    ```PowerShell
    Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
    ```

    <span data-ttu-id="82628-242">每个条目的 Message 属性提供错误的说明。</span><span class="sxs-lookup"><span data-stu-id="82628-242">The *Message* property of each entry provides a description of the error.</span></span> <span data-ttu-id="82628-243">一些常见示例包括：</span><span class="sxs-lookup"><span data-stu-id="82628-243">Some common examples are:</span></span>

        - Inability to connect, possibly due to an incorrect IP address specified for the Azure VPN gateway in the RRAS VPN network interface configuration.

        ```console
        EventID            : 20111
        MachineName        : on-prem-vm
        Data               : {41, 3, 0, 0}
        Index              : 14231
        Category           : (0)
        CategoryNumber     : 0
        EntryType          : Error
        Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A demand dial connection to the remote
                             interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                             successfully because of the  following error: The network connection between your computer and
                             the VPN server could not be established because the remote server is not responding. This could
                             be because one of the network devices (for example, firewalls, NAT, routers, and so on) between your computer
                             and the remote server is not configured to allow VPN connections. Please contact your
                             Administrator or your service provider to determine which device may be causing the problem.
        Source             : RemoteAccess
        ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                             your computer and the VPN server could not be established because the remote server is not
                             responding. This could be because one of the network devices (for example, firewalls, NAT, routers, and so on)
                             between your computer and the remote server is not configured to allow VPN connections. Please
                             contact your Administrator or your service provider to determine which device may be causing the
                             problem.}
        InstanceId         : 20111
        TimeGenerated      : 3/18/2016 1:26:02 PM
        TimeWritten        : 3/18/2016 1:26:02 PM
        UserName           :
        Site               :
        Container          :
        ```

        - The wrong shared key being specified in the RRAS VPN network interface configuration.

        ```console
        EventID            : 20111
        MachineName        : on-prem-vm
        Data               : {233, 53, 0, 0}
        Index              : 14245
        Category           : (0)
        CategoryNumber     : 0
        EntryType          : Error
        Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A demand dial connection to the remote
                             interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                             successfully because of the  following error: Internet key exchange (IKE) authentication credentials are unacceptable.

        Source             : RemoteAccess
        ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                             unacceptable.
                             }
        InstanceId         : 20111
        TimeGenerated      : 3/18/2016 1:34:22 PM
        TimeWritten        : 3/18/2016 1:34:22 PM
        UserName           :
        Site               :
        Container          :
        ```

    <span data-ttu-id="82628-244">还可以使用以下 PowerShell 命令获取有关通过 RRAS 服务尝试连接的事件日志信息：</span><span class="sxs-lookup"><span data-stu-id="82628-244">You can also obtain event log information about attempts to connect through the RRAS service using the following PowerShell command:</span></span>

    ```powershell
    Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
    ```

    <span data-ttu-id="82628-245">如果出现连接故障，则此日志会包含类似于以下内容的错误：</span><span class="sxs-lookup"><span data-stu-id="82628-245">In the event of a failure to connect, this log will contain errors that look similar to the following:</span></span>

    ```console
    EventID            : 20227
    MachineName        : on-prem-vm
    Data               : {}
    Index              : 4203
    Category           : (0)
    CategoryNumber     : 0
    EntryType          : Error
    Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                         AzureGateway that has failed. The error code returned on failure is 809.
    Source             : RasClient
    ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
    InstanceId         : 20227
    TimeGenerated      : 3/18/2016 1:29:21 PM
    TimeWritten        : 3/18/2016 1:29:21 PM
    UserName           :
    Site               :
    Container          :
    ```

- <span data-ttu-id="82628-246">**验证跨 VPN 网关的连接和路由。**</span><span class="sxs-lookup"><span data-stu-id="82628-246">**Verify connectivity and routing across the VPN gateway.**</span></span>

    <span data-ttu-id="82628-247">VPN 设备可能无法通过 Azure VPN 网关正确路由流量。</span><span class="sxs-lookup"><span data-stu-id="82628-247">The VPN appliance may not be correctly routing traffic through the Azure VPN Gateway.</span></span> <span data-ttu-id="82628-248">使用 [PsPing][psping] 这类工具可验证跨 VPN 网关的连接和路由。</span><span class="sxs-lookup"><span data-stu-id="82628-248">Use a tool such as [PsPing][psping] to verify connectivity and routing across the VPN gateway.</span></span> <span data-ttu-id="82628-249">例如，若要测试从本地计算机到位于 VNet 上的 Web 服务器的连接，请运行以下命令（将 `<<web-server-address>>` 替换为 Web 服务器的地址）：</span><span class="sxs-lookup"><span data-stu-id="82628-249">For example, to test connectivity from an on-premises machine to a web server located on the VNet, run the following command (replacing `<<web-server-address>>` with the address of the web server):</span></span>

    ```console
    PsPing -t <<web-server-address>>:80
    ```

    <span data-ttu-id="82628-250">如果本地计算机可以将流量路由到 Web 服务器，则你应看到类似于以下内容的输出：</span><span class="sxs-lookup"><span data-stu-id="82628-250">If the on-premises machine can route traffic to the web server, you should see output similar to the following:</span></span>

    ```console
    D:\PSTools>psping -t 10.20.0.5:80

    PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2014 Mark Russinovich
    Sysinternals - www.sysinternals.com

    TCP connect to 10.20.0.5:80:
    Infinite iterations (warmup 1) connecting test:
    Connecting to 10.20.0.5:80 (warmup): 6.21ms
    Connecting to 10.20.0.5:80: 3.79ms
    Connecting to 10.20.0.5:80: 3.44ms
    Connecting to 10.20.0.5:80: 4.81ms

      Sent = 3, Received = 3, Lost = 0 (0% loss),
      Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
    ```

    <span data-ttu-id="82628-251">如果本地计算机无法与指定目标进行通信，则你会看到如下消息：</span><span class="sxs-lookup"><span data-stu-id="82628-251">If the on-premises machine cannot communicate with the specified destination, you will see messages like this:</span></span>

    ```console
    D:\PSTools>psping -t 10.20.1.6:80

    PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2014 Mark Russinovich
    Sysinternals - www.sysinternals.com

    TCP connect to 10.20.1.6:80:
    Infinite iterations (warmup 1) connecting test:
    Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80:
      Sent = 3, Received = 0, Lost = 3 (100% loss),
      Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
    ```

- <span data-ttu-id="82628-252">**验证本地防火墙是否允许 VPN 流量通过以及正确的端口是否打开。**</span><span class="sxs-lookup"><span data-stu-id="82628-252">**Verify that the on-premises firewall allows VPN traffic to pass and that the correct ports are opened.**</span></span>

- <span data-ttu-id="82628-253">**验证本地 VPN 设备使用的加密方法是否[与 Azure VPN 网关兼容][vpn-appliance]。**</span><span class="sxs-lookup"><span data-stu-id="82628-253">**Verify that the on-premises VPN appliance uses an encryption method that is [compatible with the Azure VPN gateway][vpn-appliance].**</span></span> <span data-ttu-id="82628-254">对于基于策略的路由，Azure VPN 网关支持 AES256、AES128 和 3DES 加密算法。</span><span class="sxs-lookup"><span data-stu-id="82628-254">For policy-based routing, the Azure VPN gateway supports the AES256, AES128, and 3DES encryption algorithms.</span></span> <span data-ttu-id="82628-255">基于路由的网关支持 AES256 和 3DES。</span><span class="sxs-lookup"><span data-stu-id="82628-255">Route-based gateways support AES256 and 3DES.</span></span>

<span data-ttu-id="82628-256">以下建议可用于确定是否存在与 Azure VPN 网关有关的问题：</span><span class="sxs-lookup"><span data-stu-id="82628-256">The following recommendations are useful for determining if there is a problem with the Azure VPN gateway:</span></span>

- <span data-ttu-id="82628-257">**在 [Azure VPN 网关诊断日志][gateway-diagnostic-logs]中检查是否存在潜在问题。**</span><span class="sxs-lookup"><span data-stu-id="82628-257">**Examine [Azure VPN gateway diagnostic logs][gateway-diagnostic-logs] for potential issues.**</span></span>

- <span data-ttu-id="82628-258">**验证 Azure VPN 网关和本地 VPN 设备是否使用相同的共享身份验证密钥进行配置。**</span><span class="sxs-lookup"><span data-stu-id="82628-258">**Verify that the Azure VPN gateway and on-premises VPN appliance are configured with the same shared authentication key.**</span></span>

    <span data-ttu-id="82628-259">可以使用以下 Azure CLI 命令查看 Azure VPN 网关存储的共享密钥：</span><span class="sxs-lookup"><span data-stu-id="82628-259">You can view the shared key stored by the Azure VPN gateway using the following Azure CLI command:</span></span>

    ```azurecli
    azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
    ```

    <span data-ttu-id="82628-260">使用适合于本地 VPN 设备的命令显示为该设备配置的共享密钥。</span><span class="sxs-lookup"><span data-stu-id="82628-260">Use the command appropriate for your on-premises VPN appliance to show the shared key configured for that appliance.</span></span>

    <span data-ttu-id="82628-261">验证包含 Azure VPN 网关的 GatewaySubnet 子网是否不与 NSG 关联。</span><span class="sxs-lookup"><span data-stu-id="82628-261">Verify that the *GatewaySubnet* subnet holding the Azure VPN gateway is not associated with an NSG.</span></span>

    <span data-ttu-id="82628-262">可以使用以下 Azure CLI 命令查看子网详细信息：</span><span class="sxs-lookup"><span data-stu-id="82628-262">You can view the subnet details using the following Azure CLI command:</span></span>

    ```azurecli
    azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
    ```

    <span data-ttu-id="82628-263">确保没有名为 Network Security Group id 的数据字段。以下示例显示分配了 NSG (VPN-Gateway-Group) 的 GatewaySubnet 实例的结果。</span><span class="sxs-lookup"><span data-stu-id="82628-263">Ensure there is no data field named *Network Security Group id*. The following example shows the results for an instance of the *GatewaySubnet* that has an assigned NSG (*VPN-Gateway-Group*).</span></span> <span data-ttu-id="82628-264">如果没有为此 NSG 定义任何规则，则这可能会阻止网关正常工作。</span><span class="sxs-lookup"><span data-stu-id="82628-264">This can prevent the gateway from working correctly if there are any rules defined for this NSG.</span></span>

    ```console
    C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
        info:    Executing command network vnet subnet show
        + Looking up virtual network "profx-vnet"
        + Looking up the subnet "GatewaySubnet"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
        data:    Name                            : GatewaySubnet
        data:    Provisioning state              : Succeeded
        data:    Address prefix                  : 10.20.3.0/27
        data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
        info:    network vnet subnet show command OK
    ```

- <span data-ttu-id="82628-265">**验证 Azure VNet 中的虚拟机是否配置为允许来自 VNet 外部的流量进入。**</span><span class="sxs-lookup"><span data-stu-id="82628-265">**Verify that the virtual machines in the Azure VNet are configured to permit traffic coming in from outside the VNet.**</span></span>

    <span data-ttu-id="82628-266">检查与包含这些虚拟机的子网关联的任何 NSG 规则。</span><span class="sxs-lookup"><span data-stu-id="82628-266">Check any NSG rules associated with subnets containing these virtual machines.</span></span> <span data-ttu-id="82628-267">可以使用以下 Azure CLI 命令查看所有 NSG 规则：</span><span class="sxs-lookup"><span data-stu-id="82628-267">You can view all NSG rules using the following Azure CLI command:</span></span>

    ```azurecli
    azure network nsg show -g <<resource-group>> -n <<nsg-name>>
    ```

- <span data-ttu-id="82628-268">**验证 Azure VPN 网关是否已连接。**</span><span class="sxs-lookup"><span data-stu-id="82628-268">**Verify that the Azure VPN gateway is connected.**</span></span>

    <span data-ttu-id="82628-269">可以使用以下 Azure PowerShell 命令检查 Azure VPN 连接的当前状态。</span><span class="sxs-lookup"><span data-stu-id="82628-269">You can use the following Azure PowerShell command to check the current status of the Azure VPN connection.</span></span> <span data-ttu-id="82628-270">`<<connection-name>>` 参数是链接虚拟网络网关和本地网关的 Azure VPN 连接的名称。</span><span class="sxs-lookup"><span data-stu-id="82628-270">The `<<connection-name>>` parameter is the name of the Azure VPN connection that links the virtual network gateway and the local gateway.</span></span>

    ```powershell
    Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
    ```

    <span data-ttu-id="82628-271">以下代码片段突出显示在网关已连接（第一个示例）和断开连接（第二个示例）时生成的输出：</span><span class="sxs-lookup"><span data-stu-id="82628-271">The following snippets highlight the output generated if the gateway is connected (the first example), and disconnected (the second example):</span></span>

    ```powershell
    PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     :
    LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
    Peer                       :
    ConnectionType             : IPsec
    RoutingWeight              : 0
    SharedKey                  : ####################################
    ConnectionStatus           : Connected
    EgressBytesTransferred     : 55254803
    IngressBytesTransferred    : 32227221
    ProvisioningState          : Succeeded
    ...
    ```

    ```powershell
    PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     :
    LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
    Peer                       :
    ConnectionType             : IPsec
    RoutingWeight              : 0
    SharedKey                  : ####################################
    ConnectionStatus           : NotConnected
    EgressBytesTransferred     : 0
    IngressBytesTransferred    : 0
    ProvisioningState          : Succeeded
    ...
    ```

<span data-ttu-id="82628-272">以下建议可用于确定主机 VM 配置、网络带宽利用率或应用程序性能是否存在问题：</span><span class="sxs-lookup"><span data-stu-id="82628-272">The following recommendations are useful for determining if there is an issue with Host VM configuration, network bandwidth utilization, or application performance:</span></span>

- <span data-ttu-id="82628-273">**验证在子网中 Azure VM 上运行的来宾操作系统中的防火墙是否正确配置为允许来自本地 IP 范围的流量。**</span><span class="sxs-lookup"><span data-stu-id="82628-273">**Verify that the firewall in the guest operating system running on the Azure VMs in the subnet is configured correctly to allow permitted traffic from the on-premises IP ranges.**</span></span>

- <span data-ttu-id="82628-274">**验证流量是否未接近可供 Azure VPN 网关使用的带宽限制。**</span><span class="sxs-lookup"><span data-stu-id="82628-274">**Verify that the volume of traffic is not close to the limit of the bandwidth available to the Azure VPN gateway.**</span></span>

    <span data-ttu-id="82628-275">如何进行验证取决于在本地运行的 VPN 设备。</span><span class="sxs-lookup"><span data-stu-id="82628-275">How to verify this depends on the VPN appliance running on-premises.</span></span> <span data-ttu-id="82628-276">例如，如果在 Windows Server 2012 上使用 RRAS，则可以使用性能监视器跟踪通过 VPN 连接接收和传输的数据量。</span><span class="sxs-lookup"><span data-stu-id="82628-276">For example, if you are using RRAS on Windows Server 2012, you can use Performance Monitor to track the volume of data being received and transmitted over the VPN connection.</span></span> <span data-ttu-id="82628-277">使用 RAS Total 对象，选择 Bytes Received/Sec 和 Bytes Transmitted/Sec 计数器：</span><span class="sxs-lookup"><span data-stu-id="82628-277">Using the *RAS Total* object, select the *Bytes Received/Sec* and *Bytes Transmitted/Sec* counters:</span></span>

    ![用于监视 VPN 网络流量的性能计数器](../_images/guidance-hybrid-network-vpn/RRAS-perf-counters.png)

    <span data-ttu-id="82628-279">应该将结果与 VPN 网关可用的带宽（从基本 SKU 提供的 100 Mbps，到 VpnGw3 SKU 提供的 1.25 Gbps）进行比较：</span><span class="sxs-lookup"><span data-stu-id="82628-279">You should compare the results with the bandwidth available to the VPN gateway (from 100 Mbps for the Basic SKU to 1.25 Gbps for VpnGw3 SKU):</span></span>

    ![示例 VPN 网络性能图](../_images/guidance-hybrid-network-vpn/RRAS-perf-graph.png)

- <span data-ttu-id="82628-281">**验证是否已为应用程序负载部署了正确数量和大小的 VM。**</span><span class="sxs-lookup"><span data-stu-id="82628-281">**Verify that you have deployed the right number and size of VMs for your application load.**</span></span>

    <span data-ttu-id="82628-282">确定 Azure VNet 中是否有任何虚拟机运行缓慢。</span><span class="sxs-lookup"><span data-stu-id="82628-282">Determine if any of the virtual machines in the Azure VNet are running slowly.</span></span> <span data-ttu-id="82628-283">如果有，则它们可能过载、可能数量太少而无法处理负载或负载均衡器未正确配置。</span><span class="sxs-lookup"><span data-stu-id="82628-283">If so, they may be overloaded, there may be too few to handle the load, or the load-balancers may not be configured correctly.</span></span> <span data-ttu-id="82628-284">若要确定这种情况，请[捕获并分析诊断信息][azure-vm-diagnostics]。</span><span class="sxs-lookup"><span data-stu-id="82628-284">To determine this, [capture and analyze diagnostic information][azure-vm-diagnostics].</span></span> <span data-ttu-id="82628-285">可以使用 Azure 门户检查结果，不过还有许多可以提供有关性能数据的详细深入信息的第三方工具可用。</span><span class="sxs-lookup"><span data-stu-id="82628-285">You can examine the results using the Azure portal, but many third-party tools are also available that can provide detailed insights into the performance data.</span></span>

- <span data-ttu-id="82628-286">**验证该应用程序是否在高效使用云资源。**</span><span class="sxs-lookup"><span data-stu-id="82628-286">**Verify that the application is making efficient use of cloud resources.**</span></span>

    <span data-ttu-id="82628-287">检测每个 VM 上运行的应用程序代码以确定应用程序是否在充分利用资源。</span><span class="sxs-lookup"><span data-stu-id="82628-287">Instrument application code running on each VM to determine whether applications are making the best use of resources.</span></span> <span data-ttu-id="82628-288">可以使用 [Application Insights][application-insights] 这类工具。</span><span class="sxs-lookup"><span data-stu-id="82628-288">You can use tools such as [Application Insights][application-insights].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="82628-289">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="82628-289">Deploy the solution</span></span>

<span data-ttu-id="82628-290">**先决条件**。</span><span class="sxs-lookup"><span data-stu-id="82628-290">**Prerequisites**.</span></span> <span data-ttu-id="82628-291">必须提供一个已配置适当网络设备的现有本地基础结构。</span><span class="sxs-lookup"><span data-stu-id="82628-291">You must have an existing on-premises infrastructure already configured with a suitable network appliance.</span></span>

<span data-ttu-id="82628-292">若要部署该解决方案，请执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="82628-292">To deploy the solution, perform the following steps.</span></span>

<!-- markdownlint-disable MD033 -->

1. <span data-ttu-id="82628-293">单击下面的按钮：</span><span class="sxs-lookup"><span data-stu-id="82628-293">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fvpn%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="82628-294">等待该链接在 Azure 门户中打开，然后执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="82628-294">Wait for the link to open in the Azure portal, then follow these steps:</span></span>
   - <span data-ttu-id="82628-295">参数文件中已定义**资源组**名称，因此请选择“新建”，并在文本框中输入 `ra-hybrid-vpn-rg`。</span><span class="sxs-lookup"><span data-stu-id="82628-295">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-hybrid-vpn-rg` in the text box.</span></span>
   - <span data-ttu-id="82628-296">从“位置”下拉框中选择区域。</span><span class="sxs-lookup"><span data-stu-id="82628-296">Select the region from the **Location** drop down box.</span></span>
   - <span data-ttu-id="82628-297">不要编辑“模板根 URI”或“参数根 URI”文本框。</span><span class="sxs-lookup"><span data-stu-id="82628-297">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   - <span data-ttu-id="82628-298">查看条款和条件，并单击“我同意上述条款和条件”复选框。</span><span class="sxs-lookup"><span data-stu-id="82628-298">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   - <span data-ttu-id="82628-299">单击“购买”按钮。</span><span class="sxs-lookup"><span data-stu-id="82628-299">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="82628-300">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="82628-300">Wait for the deployment to complete.</span></span>

<!-- markdownlint-enable MD033 -->

<!-- links -->

[adds-extend-domain]: ../identity/adds-extend-domain.md
[expressroute]: ../hybrid-networking/expressroute.md
[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md

[naming conventions]: /azure/guidance/guidance-naming-conventions

[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[arm-templates]: /azure/resource-group-authoring-templates
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-portal]: /azure/azure-portal/resource-group-portal
[azure-powershell]: /azure/powershell-azure-resource-manager
[azure-virtual-network]: /azure/virtual-network/virtual-networks-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-gateway-skus]: /azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: /azure/vpn-gateway/vpn-gateway-multi-site
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: /azure/virtual-network/virtual-networks-nsg
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_2/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: https://blogs.technet.microsoft.com/keithmayer/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell/
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: https://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: https://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: https://blogs.technet.microsoft.com/keithmayer/2016/10/12/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs/
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: /azure/application-insights/app-insights-overview-usage
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[vpn-appliances]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[vpn-appliance-ipsec]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec-parameters
[virtualNetworkGateway-parameters]: https://github.com/mspnp/hybrid-networking/vpn/parameters/virtualNetworkGateway.parameters.json
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
