---
title: 使用 VPN 将本地网络连接到 Azure
titleSuffix: Azure Reference Architectures
description: 实现这样一个安全的站点到站点网络体系结构：跨 Azure 虚拟网络，以及使用 VPN 建立连接的本地网络。
author: RohitSharma-pnp
ms.date: 10/22/2018
ms.openlocfilehash: 5d3c8eeeb04398c29a25e90956888d9f79572e4f
ms.sourcegitcommit: 8d951fd7e9534054b160be48a1881ae0857561ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53329392"
---
# <a name="connect-an-on-premises-network-to-azure-using-a-vpn-gateway"></a><span data-ttu-id="f484e-103">使用 VPN 网关将本地网络连接到 Azure</span><span class="sxs-lookup"><span data-stu-id="f484e-103">Connect an on-premises network to Azure using a VPN gateway</span></span>

<span data-ttu-id="f484e-104">此参考体系结构演示如何使用站点到站点虚拟专用网络 (VPN) 将本地网络扩展到 Azure。</span><span class="sxs-lookup"><span data-stu-id="f484e-104">This reference architecture shows how to extend an on-premises network to Azure, using a site-to-site virtual private network (VPN).</span></span> <span data-ttu-id="f484e-105">本地网络与 Azure 虚拟专用网络 (VNet) 之间的流量通过 IPSec VPN 隧道传送。</span><span class="sxs-lookup"><span data-stu-id="f484e-105">Traffic flows between the on-premises network and an Azure Virtual Network (VNet) through an IPSec VPN tunnel.</span></span> <span data-ttu-id="f484e-106">[**部署此解决方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="f484e-106">[**Deploy this solution**](#deploy-the-solution).</span></span>

![跨本地和 Azure 基础结构的混合网络](./images/vpn.png)

<span data-ttu-id="f484e-108">下载此体系结构的 [Visio 文件][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="f484e-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="f484e-109">体系结构</span><span class="sxs-lookup"><span data-stu-id="f484e-109">Architecture</span></span>

<span data-ttu-id="f484e-110">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="f484e-110">The architecture consists of the following components.</span></span>

- <span data-ttu-id="f484e-111">**本地网络**。</span><span class="sxs-lookup"><span data-stu-id="f484e-111">**On-premises network**.</span></span> <span data-ttu-id="f484e-112">组织中运行的专用局域网。</span><span class="sxs-lookup"><span data-stu-id="f484e-112">A private local-area network running within an organization.</span></span>

- <span data-ttu-id="f484e-113">**VPN 设备**。</span><span class="sxs-lookup"><span data-stu-id="f484e-113">**VPN appliance**.</span></span> <span data-ttu-id="f484e-114">用于与本地网络建立外部连接的设备或服务。</span><span class="sxs-lookup"><span data-stu-id="f484e-114">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="f484e-115">该 VPN 设备可以是硬件设备，也可以是软件解决方案，例如 Windows Server 2012 中的路由和远程访问服务 (RRAS)。</span><span class="sxs-lookup"><span data-stu-id="f484e-115">The VPN appliance may be a hardware device, or it can be a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="f484e-116">有关支持的 VPN 设备的列表以及有关如何配置它们以连接到 Azure VPN 网关的信息，请参阅[关于站点到站点 VPN 网关连接的 VPN 设备][vpn-appliance]一文中针对所选设备的说明。</span><span class="sxs-lookup"><span data-stu-id="f484e-116">For a list of supported VPN appliances and information on configuring them to connect to an Azure VPN gateway, see the instructions for the selected device in the article [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

- <span data-ttu-id="f484e-117">**虚拟网络 (VNet)**。</span><span class="sxs-lookup"><span data-stu-id="f484e-117">**Virtual network (VNet)**.</span></span> <span data-ttu-id="f484e-118">云应用程序和 Azure VPN 网关的组件驻留在相同 [VNet][azure-virtual-network] 中。</span><span class="sxs-lookup"><span data-stu-id="f484e-118">The cloud application and the components for the Azure VPN gateway reside in the same [VNet][azure-virtual-network].</span></span>

- <span data-ttu-id="f484e-119">**Azure VPN 网关**。</span><span class="sxs-lookup"><span data-stu-id="f484e-119">**Azure VPN gateway**.</span></span> <span data-ttu-id="f484e-120">[VPN 网关][azure-vpn-gateway]服务使你可以通过 VPN 设备将 VNet 连接到本地网络。</span><span class="sxs-lookup"><span data-stu-id="f484e-120">The [VPN gateway][azure-vpn-gateway] service enables you to connect the VNet to the on-premises network through a VPN appliance.</span></span> <span data-ttu-id="f484e-121">有关详细信息，请参阅[将本地网络连接到 Microsoft Azure 虚拟网络][connect-to-an-Azure-vnet]。</span><span class="sxs-lookup"><span data-stu-id="f484e-121">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span> <span data-ttu-id="f484e-122">VPN 网关包括以下元素：</span><span class="sxs-lookup"><span data-stu-id="f484e-122">The VPN gateway includes the following elements:</span></span>

  - <span data-ttu-id="f484e-123">**虚拟网络网关**。</span><span class="sxs-lookup"><span data-stu-id="f484e-123">**Virtual network gateway**.</span></span> <span data-ttu-id="f484e-124">为 VNet 提供虚拟 VPN 设备的资源。</span><span class="sxs-lookup"><span data-stu-id="f484e-124">A resource that provides a virtual VPN appliance for the VNet.</span></span> <span data-ttu-id="f484e-125">它负责将流量从本地网络路由到 VNet。</span><span class="sxs-lookup"><span data-stu-id="f484e-125">It is responsible for routing traffic from the on-premises network to the VNet.</span></span>
  - <span data-ttu-id="f484e-126">**本地网络网关**。</span><span class="sxs-lookup"><span data-stu-id="f484e-126">**Local network gateway**.</span></span> <span data-ttu-id="f484e-127">本地 VPN 设备的抽象。</span><span class="sxs-lookup"><span data-stu-id="f484e-127">An abstraction of the on-premises VPN appliance.</span></span> <span data-ttu-id="f484e-128">从云应用程序到本地网络的网络流量通过此网关进行路由。</span><span class="sxs-lookup"><span data-stu-id="f484e-128">Network traffic from the cloud application to the on-premises network is routed through this gateway.</span></span>
  - <span data-ttu-id="f484e-129">**连接**。</span><span class="sxs-lookup"><span data-stu-id="f484e-129">**Connection**.</span></span> <span data-ttu-id="f484e-130">该连接包含一些属性，这些属性指定连接类型 (IPSec)，以及与本地 VPN 设备共享的、用于加密流量的密钥。</span><span class="sxs-lookup"><span data-stu-id="f484e-130">The connection has properties that specify the connection type (IPSec) and the key shared with the on-premises VPN appliance to encrypt traffic.</span></span>
  - <span data-ttu-id="f484e-131">**网关子网**。</span><span class="sxs-lookup"><span data-stu-id="f484e-131">**Gateway subnet**.</span></span> <span data-ttu-id="f484e-132">虚拟网络网关保留在自己的子网中，该子网需满足下面“建议”一节中所述的要求。</span><span class="sxs-lookup"><span data-stu-id="f484e-132">The virtual network gateway is held in its own subnet, which is subject to various requirements, described in the Recommendations section below.</span></span>

- <span data-ttu-id="f484e-133">**云应用程序**。</span><span class="sxs-lookup"><span data-stu-id="f484e-133">**Cloud application**.</span></span> <span data-ttu-id="f484e-134">Azure 中托管的应用程序。</span><span class="sxs-lookup"><span data-stu-id="f484e-134">The application hosted in Azure.</span></span> <span data-ttu-id="f484e-135">它可以包含多个层，以及通过 Azure 负载均衡器连接的多个子网。</span><span class="sxs-lookup"><span data-stu-id="f484e-135">It might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="f484e-136">有关应用程序基础结构的详细信息，请参阅[运行 Windows VM 工作负荷][windows-vm-ra]和[运行 Linux VM 工作负荷][linux-vm-ra]。</span><span class="sxs-lookup"><span data-stu-id="f484e-136">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

- <span data-ttu-id="f484e-137">**内部负载均衡器**。</span><span class="sxs-lookup"><span data-stu-id="f484e-137">**Internal load balancer**.</span></span> <span data-ttu-id="f484e-138">来自 VPN 网关的网络流量通过内部负载均衡器路由到云应用程序。</span><span class="sxs-lookup"><span data-stu-id="f484e-138">Network traffic from the VPN gateway is routed to the cloud application through an internal load balancer.</span></span> <span data-ttu-id="f484e-139">该负载均衡器位于应用程序的前端子网中。</span><span class="sxs-lookup"><span data-stu-id="f484e-139">The load balancer is located in the front-end subnet of the application.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f484e-140">建议</span><span class="sxs-lookup"><span data-stu-id="f484e-140">Recommendations</span></span>

<span data-ttu-id="f484e-141">以下建议适用于大多数方案。</span><span class="sxs-lookup"><span data-stu-id="f484e-141">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="f484e-142">除非有优先于这些建议的特定要求，否则请遵循这些建议。</span><span class="sxs-lookup"><span data-stu-id="f484e-142">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="vnet-and-gateway-subnet"></a><span data-ttu-id="f484e-143">VNet 和网关子网</span><span class="sxs-lookup"><span data-stu-id="f484e-143">VNet and gateway subnet</span></span>

<span data-ttu-id="f484e-144">创建地址空间的大小足以容纳所有所需资源的 Azure VNet。</span><span class="sxs-lookup"><span data-stu-id="f484e-144">Create an Azure VNet with an address space large enough for all of your required resources.</span></span> <span data-ttu-id="f484e-145">确保 VNet 地址空间具有足够空间，以便在将来可能需要更多 VM 时增长。</span><span class="sxs-lookup"><span data-stu-id="f484e-145">Ensure that the VNet address space has sufficient room for growth if additional VMs are likely to be needed in the future.</span></span> <span data-ttu-id="f484e-146">VNet 的地址空间不得与本地网络重叠。</span><span class="sxs-lookup"><span data-stu-id="f484e-146">The address space of the VNet must not overlap with the on-premises network.</span></span> <span data-ttu-id="f484e-147">例如，上图将地址空间 10.20.0.0/16 用于 VNet。</span><span class="sxs-lookup"><span data-stu-id="f484e-147">For example, the diagram above uses the address space 10.20.0.0/16 for the VNet.</span></span>

<span data-ttu-id="f484e-148">创建一个名为 *GatewaySubnet* 的子网，使其地址范围为 /27。</span><span class="sxs-lookup"><span data-stu-id="f484e-148">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="f484e-149">此子网是虚拟网络网关所必需的。</span><span class="sxs-lookup"><span data-stu-id="f484e-149">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="f484e-150">向此子网分配 32 个地址将有助于防止将来达到网关大小限制。</span><span class="sxs-lookup"><span data-stu-id="f484e-150">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span> <span data-ttu-id="f484e-151">此外，避免将此子网置于地址空间中间。</span><span class="sxs-lookup"><span data-stu-id="f484e-151">Also, avoid placing this subnet in the middle of the address space.</span></span> <span data-ttu-id="f484e-152">一个好的做法是将网关子网的地址空间设置在 VNet 地址空间上端。</span><span class="sxs-lookup"><span data-stu-id="f484e-152">A good practice is to set the address space for the gateway subnet at the upper end of the VNet address space.</span></span> <span data-ttu-id="f484e-153">图中所示的示例使用 10.20.255.224/27。</span><span class="sxs-lookup"><span data-stu-id="f484e-153">The example shown in the diagram uses 10.20.255.224/27.</span></span>  <span data-ttu-id="f484e-154">下面是用于计算 [CIDR] 的快速过程：</span><span class="sxs-lookup"><span data-stu-id="f484e-154">Here is a quick procedure to calculate the [CIDR]:</span></span>

1. <span data-ttu-id="f484e-155">将 VNet 地址空间中的变量位设置为 1（直到网关子网所使用的位），然后将剩余位设置为 0。</span><span class="sxs-lookup"><span data-stu-id="f484e-155">Set the variable bits in the address space of the VNet to 1, up to the bits being used by the gateway subnet, then set the remaining bits to 0.</span></span>
2. <span data-ttu-id="f484e-156">将得到的位转换为十进制，然后将它表示为前缀长度设置为网关子网大小的地址空间。</span><span class="sxs-lookup"><span data-stu-id="f484e-156">Convert the resulting bits to decimal and express it as an address space with the prefix length set to the size of the gateway subnet.</span></span>

<span data-ttu-id="f484e-157">例如，对于 IP 地址范围为 10.20.0.0/16 的 VNet，应用上面的步骤 1 会成为 10.20.0b11111111.0b11100000。</span><span class="sxs-lookup"><span data-stu-id="f484e-157">For example, for a VNet with an IP address range of 10.20.0.0/16, applying step #1 above becomes 10.20.0b11111111.0b11100000.</span></span>  <span data-ttu-id="f484e-158">将该数字转换为十进制并将它表示为地址空间会生成 10.20.255.224/27。</span><span class="sxs-lookup"><span data-stu-id="f484e-158">Converting that to decimal and expressing it as an address space yields 10.20.255.224/27.</span></span>

> [!WARNING]
> <span data-ttu-id="f484e-159">请勿向网关子网部署任何 VM。</span><span class="sxs-lookup"><span data-stu-id="f484e-159">Do not deploy any VMs to the gateway subnet.</span></span> <span data-ttu-id="f484e-160">此外，请勿向此子网分配 NSG，因为它会导致网关停止工作。</span><span class="sxs-lookup"><span data-stu-id="f484e-160">Also, do not assign an NSG to this subnet, as it will cause the gateway to stop functioning.</span></span>
>

### <a name="virtual-network-gateway"></a><span data-ttu-id="f484e-161">虚拟网络网关</span><span class="sxs-lookup"><span data-stu-id="f484e-161">Virtual network gateway</span></span>

<span data-ttu-id="f484e-162">为虚拟网络网关分配公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f484e-162">Allocate a public IP address for the virtual network gateway.</span></span>

<span data-ttu-id="f484e-163">在网关子网中创建虚拟网络网关，并将新分配的公共 IP 地址分配给它。</span><span class="sxs-lookup"><span data-stu-id="f484e-163">Create the virtual network gateway in the gateway subnet and assign it the newly allocated public IP address.</span></span> <span data-ttu-id="f484e-164">使用最符合你要求并且通过 VPN 设备启用的网关类型：</span><span class="sxs-lookup"><span data-stu-id="f484e-164">Use the gateway type that most closely matches your requirements and that is enabled by your VPN appliance:</span></span>

- <span data-ttu-id="f484e-165">如果需要基于策略条件（如地址前缀）密切控制如何对请求进行路由，请创建[基于策略的网关][policy-based-routing]。</span><span class="sxs-lookup"><span data-stu-id="f484e-165">Create a [policy-based gateway][policy-based-routing] if you need to closely control how requests are routed based on policy criteria such as address prefixes.</span></span> <span data-ttu-id="f484e-166">基于策略的网关使用静态路由，仅适用于站点到站点连接。</span><span class="sxs-lookup"><span data-stu-id="f484e-166">Policy-based gateways use static routing, and only work with site-to-site connections.</span></span>

- <span data-ttu-id="f484e-167">如果使用 RRAS 连接到本地网络、支持多站点或跨区域连接或是实现 VNet 到 VNet 连接（包含遍历多个 VNet 的路由），请创建[基于路由的网关][route-based-routing]。</span><span class="sxs-lookup"><span data-stu-id="f484e-167">Create a [route-based gateway][route-based-routing] if you connect to the on-premises network using RRAS, support multi-site or cross-region connections, or implement VNet-to-VNet connections (including routes that traverse multiple VNets).</span></span> <span data-ttu-id="f484e-168">基于路由的网关使用动态路由定向网络之间的流量。</span><span class="sxs-lookup"><span data-stu-id="f484e-168">Route-based gateways use dynamic routing to direct traffic between networks.</span></span> <span data-ttu-id="f484e-169">它们在网络路径中的容错能力好于静态路由，因为它们可以尝试备用路由。</span><span class="sxs-lookup"><span data-stu-id="f484e-169">They can tolerate failures in the network path better than static routes because they can try alternative routes.</span></span> <span data-ttu-id="f484e-170">基于路由的网关还可以减少管理开销，因为路由在网络地址更改时可以无需手动更新。</span><span class="sxs-lookup"><span data-stu-id="f484e-170">Route-based gateways can also reduce the management overhead because routes might not need to be updated manually when network addresses change.</span></span>

<span data-ttu-id="f484e-171">有关支持的 VPN 设备的列表，请参阅[关于站点到站点 VPN 网关连接的 VPN 设备][vpn-appliances]。</span><span class="sxs-lookup"><span data-stu-id="f484e-171">For a list of supported VPN appliances, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliances].</span></span>

> [!NOTE]
> <span data-ttu-id="f484e-172">创建网关后，必须先删除并重新创建网关才能更改网关类型。</span><span class="sxs-lookup"><span data-stu-id="f484e-172">After the gateway has been created, you cannot change between gateway types without deleting and re-creating the gateway.</span></span>
>

<span data-ttu-id="f484e-173">选择最符合你吞吐量要求的 Azure VPN 网关 SKU。</span><span class="sxs-lookup"><span data-stu-id="f484e-173">Select the Azure VPN gateway SKU that most closely matches your throughput requirements.</span></span> <span data-ttu-id="f484e-174">有关详细信息，请参阅[网关 SKU][azure-gateway-skus]</span><span class="sxs-lookup"><span data-stu-id="f484e-174">For more informayion, see [Gateway SKUs][azure-gateway-skus]</span></span>

> [!NOTE]
> <span data-ttu-id="f484e-175">基本 SKU 不与 Azure ExpressRoute 兼容。</span><span class="sxs-lookup"><span data-stu-id="f484e-175">The Basic SKU is not compatible with Azure ExpressRoute.</span></span> <span data-ttu-id="f484e-176">可以在创建网关之后[更改 SKU][changing-SKUs]。</span><span class="sxs-lookup"><span data-stu-id="f484e-176">You can [change the SKU][changing-SKUs] after the gateway has been created.</span></span>
>

<span data-ttu-id="f484e-177">会基于预配和提供网关的时间量进行收费。</span><span class="sxs-lookup"><span data-stu-id="f484e-177">You are charged based on the amount of time that the gateway is provisioned and available.</span></span> <span data-ttu-id="f484e-178">请参阅 [VPN 网关定价][azure-gateway-charges]。</span><span class="sxs-lookup"><span data-stu-id="f484e-178">See [VPN Gateway Pricing][azure-gateway-charges].</span></span>

<span data-ttu-id="f484e-179">为网关子网创建将传入应用程序流量从网关定向到内部负载均衡器，而不是允许请求直接传递到应用程序 VM 的路由规则。</span><span class="sxs-lookup"><span data-stu-id="f484e-179">Create routing rules for the gateway subnet that direct incoming application traffic from the gateway to the internal load balancer, rather than allowing requests to pass directly to the application VMs.</span></span>

### <a name="on-premises-network-connection"></a><span data-ttu-id="f484e-180">本地网络连接</span><span class="sxs-lookup"><span data-stu-id="f484e-180">On-premises network connection</span></span>

<span data-ttu-id="f484e-181">创建本地网络网关。</span><span class="sxs-lookup"><span data-stu-id="f484e-181">Create a local network gateway.</span></span> <span data-ttu-id="f484e-182">指定本地 VPN 设备的公共 IP 地址以及本地网络的地址空间。</span><span class="sxs-lookup"><span data-stu-id="f484e-182">Specify the public IP address of the on-premises VPN appliance, and the address space of the on-premises network.</span></span> <span data-ttu-id="f484e-183">请注意，本地 VPN 设备必须具有可由 Azure VPN 网关中的本地网络网关访问的公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f484e-183">Note that the on-premises VPN appliance must have a public IP address that can be accessed by the local network gateway in Azure VPN Gateway.</span></span> <span data-ttu-id="f484e-184">VPN 设备不能位于网络地址转换 (NAT) 设备后面。</span><span class="sxs-lookup"><span data-stu-id="f484e-184">The VPN device cannot be located behind a network address translation (NAT) device.</span></span>

<span data-ttu-id="f484e-185">为虚拟网络网关和本地网络网关创建站点到站点连接。</span><span class="sxs-lookup"><span data-stu-id="f484e-185">Create a site-to-site connection for the virtual network gateway and the local network gateway.</span></span> <span data-ttu-id="f484e-186">选择站点到站点 (IPSec) 连接类型，并指定共享密钥。</span><span class="sxs-lookup"><span data-stu-id="f484e-186">Select the site-to-site (IPSec) connection type, and specify the shared key.</span></span> <span data-ttu-id="f484e-187">具有 Azure VPN 网关的站点到站点加密基于 IPSec 协议，使用预共享密钥进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f484e-187">Site-to-site encryption with the Azure VPN gateway is based on the IPSec protocol, using preshared keys for authentication.</span></span> <span data-ttu-id="f484e-188">创建 Azure VPN 网关时需指定密钥。</span><span class="sxs-lookup"><span data-stu-id="f484e-188">You specify the key when you create the Azure VPN gateway.</span></span> <span data-ttu-id="f484e-189">必须使用相同密钥配置在本地运行的 VPN 设备。</span><span class="sxs-lookup"><span data-stu-id="f484e-189">You must configure the VPN appliance running on-premises with the same key.</span></span> <span data-ttu-id="f484e-190">当前不支持其他身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="f484e-190">Other authentication mechanisms are not currently supported.</span></span>

<span data-ttu-id="f484e-191">确保本地路由基础结构配置为将目标为 Azure VNet 中的地址的请求转发到 VPN 设备。</span><span class="sxs-lookup"><span data-stu-id="f484e-191">Ensure that the on-premises routing infrastructure is configured to forward requests intended for addresses in the Azure VNet to the VPN device.</span></span>

<span data-ttu-id="f484e-192">在本地网络中打开云应用程序所需的任何端口。</span><span class="sxs-lookup"><span data-stu-id="f484e-192">Open any ports required by the cloud application in the on-premises network.</span></span>

<span data-ttu-id="f484e-193">测试连接以验证以下事项：</span><span class="sxs-lookup"><span data-stu-id="f484e-193">Test the connection to verify that:</span></span>

- <span data-ttu-id="f484e-194">本地 VPN 设备通过 Azure VPN 网关将流量正确路由到云应用程序。</span><span class="sxs-lookup"><span data-stu-id="f484e-194">The on-premises VPN appliance correctly routes traffic to the cloud application through the Azure VPN gateway.</span></span>
- <span data-ttu-id="f484e-195">VNet 将流量正确路由回本地网络。</span><span class="sxs-lookup"><span data-stu-id="f484e-195">The VNet correctly routes traffic back to the on-premises network.</span></span>
- <span data-ttu-id="f484e-196">正确阻止两个方向上的禁止流量。</span><span class="sxs-lookup"><span data-stu-id="f484e-196">Prohibited traffic in both directions is blocked correctly.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="f484e-197">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="f484e-197">Scalability considerations</span></span>

<span data-ttu-id="f484e-198">可以通过从基本或标准 VPN 网关 SKU 升级到高性能 VPN SKU，来实现有限纵向可缩放性。</span><span class="sxs-lookup"><span data-stu-id="f484e-198">You can achieve limited vertical scalability by moving from the Basic or Standard VPN Gateway SKUs to the High Performance VPN SKU.</span></span>

<span data-ttu-id="f484e-199">对于预期存在大量 VPN 流量的 VNet，请考虑将不同工作负载分发到单独的较小 VNet 以及为每个 VNet 都配置 VPN 网关。</span><span class="sxs-lookup"><span data-stu-id="f484e-199">For VNets that expect a large volume of VPN traffic, consider distributing the different workloads into separate smaller VNets and configuring a VPN gateway for each of them.</span></span>

<span data-ttu-id="f484e-200">可以按水平或垂直方式对 VNet 进行分区。</span><span class="sxs-lookup"><span data-stu-id="f484e-200">You can partition the VNet either horizontally or vertically.</span></span> <span data-ttu-id="f484e-201">若要进行水平分区，请将某些 VM 实例从每个层移动到新 VNet 的子网中。</span><span class="sxs-lookup"><span data-stu-id="f484e-201">To partition horizontally, move some VM instances from each tier into subnets of the new VNet.</span></span> <span data-ttu-id="f484e-202">结果是每个 VNet 都具有相同结构和功能。</span><span class="sxs-lookup"><span data-stu-id="f484e-202">The result is that each VNet has the same structure and functionality.</span></span> <span data-ttu-id="f484e-203">若要进行垂直分区，请重新设计每个层，以将功能划分为不同逻辑区域（如处理订单、开发票、客户帐户管理等）。</span><span class="sxs-lookup"><span data-stu-id="f484e-203">To partition vertically, redesign each tier to divide the functionality into different logical areas (such as handling orders, invoicing, customer account management, and so on).</span></span> <span data-ttu-id="f484e-204">每个功能区域随后可以放置在自己的 VNet 中。</span><span class="sxs-lookup"><span data-stu-id="f484e-204">Each functional area can then be placed in its own VNet.</span></span>

<span data-ttu-id="f484e-205">在 VNet 中复制本地 Active Directory 域控制器以及在 VNet 中实现 DNS 可以帮助减少从本地到云的某些安全相关和管理流量。</span><span class="sxs-lookup"><span data-stu-id="f484e-205">Replicating an on-premises Active Directory domain controller in the VNet, and implementing DNS in the VNet, can help to reduce some of the security-related and administrative traffic flowing from on-premises to the cloud.</span></span> <span data-ttu-id="f484e-206">有关详细信息，请参阅[将 Active Directory 域服务 (AD DS) 扩展到 Azure][adds-extend-domain]。</span><span class="sxs-lookup"><span data-stu-id="f484e-206">For more information, see [Extending Active Directory Domain Services (AD DS) to Azure][adds-extend-domain].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="f484e-207">可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="f484e-207">Availability considerations</span></span>

<span data-ttu-id="f484e-208">如果需要确保本地网络对 Azure VPN 网关保持可用，请为本地 VPN 网关实现故障转移群集。</span><span class="sxs-lookup"><span data-stu-id="f484e-208">If you need to ensure that the on-premises network remains available to the Azure VPN gateway, implement a failover cluster for the on-premises VPN gateway.</span></span>

<span data-ttu-id="f484e-209">如果组织具有多个本地站点，请创建与一个或多个 Azure VNet 之间的[多站点连接][vpn-gateway-multi-site]。</span><span class="sxs-lookup"><span data-stu-id="f484e-209">If your organization has multiple on-premises sites, create [multi-site connections][vpn-gateway-multi-site] to one or more Azure VNets.</span></span> <span data-ttu-id="f484e-210">此方法需要动态（基于路由的）路由，因此请确保本地 VPN 网关支持此功能。</span><span class="sxs-lookup"><span data-stu-id="f484e-210">This approach requires dynamic (route-based) routing, so make sure that the on-premises VPN gateway supports this feature.</span></span>

<span data-ttu-id="f484e-211">有关服务级别协议的详细信息，请参阅 [VPN 网关的 SLA][sla-for-vpn-gateway]。</span><span class="sxs-lookup"><span data-stu-id="f484e-211">For details about service level agreements, see [SLA for VPN Gateway][sla-for-vpn-gateway].</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="f484e-212">可管理性注意事项</span><span class="sxs-lookup"><span data-stu-id="f484e-212">Manageability considerations</span></span>

<span data-ttu-id="f484e-213">监视来自本地 VPN 设备的诊断信息。</span><span class="sxs-lookup"><span data-stu-id="f484e-213">Monitor diagnostic information from on-premises VPN appliances.</span></span> <span data-ttu-id="f484e-214">此过程取决于 VPN 设备提供的功能。</span><span class="sxs-lookup"><span data-stu-id="f484e-214">This process depends on the features provided by the VPN appliance.</span></span> <span data-ttu-id="f484e-215">例如，如果在 Windows Server 2012 上使用路由和远程访问服务，则可使用 [RRAS 日志记录][rras-logging]。</span><span class="sxs-lookup"><span data-stu-id="f484e-215">For example, if you are using the Routing and Remote Access Service on Windows Server 2012, [RRAS logging][rras-logging].</span></span>

<span data-ttu-id="f484e-216">使用 [Azure VPN 网关诊断][gateway-diagnostic-logs]可捕获有关连接问题的信息。</span><span class="sxs-lookup"><span data-stu-id="f484e-216">Use [Azure VPN gateway diagnostics][gateway-diagnostic-logs] to capture information about connectivity issues.</span></span> <span data-ttu-id="f484e-217">这些日志可以用于跟踪信息，如连接请求的源和目标、使用的协议以及连接的建立方式（或尝试失败的原因）。</span><span class="sxs-lookup"><span data-stu-id="f484e-217">These logs can be used to track information such as the source and destinations of connection requests, which protocol was used, and how the connection was established (or why the attempt failed).</span></span>

<span data-ttu-id="f484e-218">使用 Azure 门户中提供的审核日志监视 Azure VPN 网关的运行日志。</span><span class="sxs-lookup"><span data-stu-id="f484e-218">Monitor the operational logs of the Azure VPN gateway using the audit logs available in the Azure portal.</span></span> <span data-ttu-id="f484e-219">为本地网络网关、Azure 网络网关和连接分别提供了单独的日志。</span><span class="sxs-lookup"><span data-stu-id="f484e-219">Separate logs are available for the local network gateway, the Azure network gateway, and the connection.</span></span> <span data-ttu-id="f484e-220">此信息可以用于跟踪对网关进行的任何更改，并且在以前正常运行的网关由于某种原因而停止工作时可能会十分有用。</span><span class="sxs-lookup"><span data-stu-id="f484e-220">This information can be used to track any changes made to the gateway, and can be useful if a previously functioning gateway stops working for some reason.</span></span>

![Azure 门户中的审核日志](../_images/guidance-hybrid-network-vpn/audit-logs.png)

<span data-ttu-id="f484e-222">监视连接，并跟踪连接失败事件。</span><span class="sxs-lookup"><span data-stu-id="f484e-222">Monitor connectivity, and track connectivity failure events.</span></span> <span data-ttu-id="f484e-223">可以使用监视包（如 [Nagios][nagios]）捕获并报告此信息。</span><span class="sxs-lookup"><span data-stu-id="f484e-223">You can use a monitoring package such as [Nagios][nagios] to capture and report this information.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f484e-224">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="f484e-224">Security considerations</span></span>

<span data-ttu-id="f484e-225">为每个 VPN 网关生成不同的共享密钥。</span><span class="sxs-lookup"><span data-stu-id="f484e-225">Generate a different shared key for each VPN gateway.</span></span> <span data-ttu-id="f484e-226">使用强共享密钥可帮助抵御暴力攻击。</span><span class="sxs-lookup"><span data-stu-id="f484e-226">Use a strong shared key to help resist brute-force attacks.</span></span>

> [!NOTE]
> <span data-ttu-id="f484e-227">当前无法使用 Azure Key Vault 为 Azure VPN 网关预共享密钥。</span><span class="sxs-lookup"><span data-stu-id="f484e-227">Currently, you cannot use Azure Key Vault to preshare keys for the Azure VPN gateway.</span></span>
>

<span data-ttu-id="f484e-228">确保本地 VPN 设备使用的加密方法[与 Azure VPN 网关兼容][vpn-appliance-ipsec]。</span><span class="sxs-lookup"><span data-stu-id="f484e-228">Ensure that the on-premises VPN appliance uses an encryption method that is [compatible with the Azure VPN gateway][vpn-appliance-ipsec].</span></span> <span data-ttu-id="f484e-229">对于基于策略的路由，Azure VPN 网关支持 AES256、AES128 和 3DES 加密算法。</span><span class="sxs-lookup"><span data-stu-id="f484e-229">For policy-based routing, the Azure VPN gateway supports the AES256, AES128, and 3DES encryption algorithms.</span></span> <span data-ttu-id="f484e-230">基于路由的网关支持 AES256 和 3DES。</span><span class="sxs-lookup"><span data-stu-id="f484e-230">Route-based gateways support AES256 and 3DES.</span></span>

<span data-ttu-id="f484e-231">如果本地 VPN 设备位于在外围网络 (DMZ) 与 Internet 之间具有防火墙的外围网络中，则可能必须配置[其他防火墙规则][additional-firewall-rules]以允许实现站点到站点 VPN 连接。</span><span class="sxs-lookup"><span data-stu-id="f484e-231">If your on-premises VPN appliance is on a perimeter network (DMZ) that has a firewall between the perimeter network and the Internet, you might have to configure [additional firewall rules][additional-firewall-rules] to allow the site-to-site VPN connection.</span></span>

<span data-ttu-id="f484e-232">如果 VNet 中的应用程序将数据发送到 Internet，请考虑[实现强制隧道][forced-tunneling]以通过本地网络路由所有 Internet 绑定流量。</span><span class="sxs-lookup"><span data-stu-id="f484e-232">If the application in the VNet sends data to the Internet, consider [implementing forced tunneling][forced-tunneling] to route all Internet-bound traffic through the on-premises network.</span></span> <span data-ttu-id="f484e-233">此方法使你可以审核应用程序从本地基础结构进行的传出请求。</span><span class="sxs-lookup"><span data-stu-id="f484e-233">This approach enables you to audit outgoing requests made by the application from the on-premises infrastructure.</span></span>

> [!NOTE]
> <span data-ttu-id="f484e-234">强制隧道可能会影响与 Azure 服务（例如存储服务）和 Windows 许可证管理器之间的连接。</span><span class="sxs-lookup"><span data-stu-id="f484e-234">Forced tunneling can impact connectivity to Azure services (the Storage Service, for example) and the Windows license manager.</span></span>
>

## <a name="deploy-the-solution"></a><span data-ttu-id="f484e-235">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="f484e-235">Deploy the solution</span></span>

<span data-ttu-id="f484e-236">**先决条件**。</span><span class="sxs-lookup"><span data-stu-id="f484e-236">**Prerequisites**.</span></span> <span data-ttu-id="f484e-237">必须提供一个已配置适当网络设备的现有本地基础结构。</span><span class="sxs-lookup"><span data-stu-id="f484e-237">You must have an existing on-premises infrastructure already configured with a suitable network appliance.</span></span>

<span data-ttu-id="f484e-238">若要部署该解决方案，请执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="f484e-238">To deploy the solution, perform the following steps.</span></span>

<!-- markdownlint-disable MD033 -->

1. <span data-ttu-id="f484e-239">单击下面的按钮：</span><span class="sxs-lookup"><span data-stu-id="f484e-239">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fvpn%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="f484e-240">等待该链接在 Azure 门户中打开，然后执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="f484e-240">Wait for the link to open in the Azure portal, then follow these steps:</span></span>
   - <span data-ttu-id="f484e-241">参数文件中已定义**资源组**名称，因此请选择“新建”，并在文本框中输入 `ra-hybrid-vpn-rg`。</span><span class="sxs-lookup"><span data-stu-id="f484e-241">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-hybrid-vpn-rg` in the text box.</span></span>
   - <span data-ttu-id="f484e-242">从“位置”下拉框中选择区域。</span><span class="sxs-lookup"><span data-stu-id="f484e-242">Select the region from the **Location** drop down box.</span></span>
   - <span data-ttu-id="f484e-243">不要编辑“模板根 URI”或“参数根 URI”文本框。</span><span class="sxs-lookup"><span data-stu-id="f484e-243">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   - <span data-ttu-id="f484e-244">查看条款和条件，并单击“我同意上述条款和条件”复选框。</span><span class="sxs-lookup"><span data-stu-id="f484e-244">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   - <span data-ttu-id="f484e-245">单击“购买”按钮。</span><span class="sxs-lookup"><span data-stu-id="f484e-245">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="f484e-246">等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="f484e-246">Wait for the deployment to complete.</span></span>

<!-- markdownlint-enable MD033 -->

<span data-ttu-id="f484e-247">若要排查连接问题，请参阅[排查混合 VPN 连接问题](./troubleshoot-vpn.md)。</span><span class="sxs-lookup"><span data-stu-id="f484e-247">To troubleshoot the connection, see [Troubleshoot a hybrid VPN connection](./troubleshoot-vpn.md).</span></span>

<!-- links -->

[adds-extend-domain]: ../identity/adds-extend-domain.md
[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md

[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-virtual-network]: /azure/virtual-network/virtual-networks-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-gateway-skus]: /azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: /azure/vpn-gateway/vpn-gateway-multi-site
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: https://blogs.technet.microsoft.com/keithmayer/2016/10/12/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[vpn-appliances]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[vpn-appliance-ipsec]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec-parameters
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
