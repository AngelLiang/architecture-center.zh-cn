---
title: Azure 上高度可扩展且高度安全的 WordPress 网站
description: 构建高度可扩展且高度安全的 WordPress 网站以用于媒体事件。
author: david-stanford
ms.date: 09/18/2018
ms.openlocfilehash: 6ff39d09fa301c8c68ce2a644cc489c0e87a22fa
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610594"
---
# <a name="highly-scalable-and-secure-wordpress-website"></a><span data-ttu-id="ef23d-103">高度可缩放的安全 WordPress 网站</span><span class="sxs-lookup"><span data-stu-id="ef23d-103">Highly scalable and secure WordPress website</span></span>

<span data-ttu-id="ef23d-104">本示例方案适用于需要高度可缩放的安全 WordPress 安装的公司。</span><span class="sxs-lookup"><span data-stu-id="ef23d-104">This example scenario is applicable to companies that need a highly scalable and secure installation of WordPress.</span></span> <span data-ttu-id="ef23d-105">本方案基于一个用于大型会议、已成功进行扩展以应对会议期间站点出现的流量高峰的部署。</span><span class="sxs-lookup"><span data-stu-id="ef23d-105">This scenario is based on a deployment that was used for a large convention and was successfully able to scale to meet the spike traffic that sessions drove to the site.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="ef23d-106">相关用例</span><span class="sxs-lookup"><span data-stu-id="ef23d-106">Relevant use cases</span></span>

<span data-ttu-id="ef23d-107">其他相关用例包括：</span><span class="sxs-lookup"><span data-stu-id="ef23d-107">Other relevant use cases include:</span></span>

* <span data-ttu-id="ef23d-108">媒体活动导致流量激增。</span><span class="sxs-lookup"><span data-stu-id="ef23d-108">Media events that cause traffic surges.</span></span>
* <span data-ttu-id="ef23d-109">博客网站使用 WordPress 作为内容管理系统。</span><span class="sxs-lookup"><span data-stu-id="ef23d-109">Blogs that use WordPress as their content management system.</span></span>
* <span data-ttu-id="ef23d-110">企业或电子商务网站使用 WordPress。</span><span class="sxs-lookup"><span data-stu-id="ef23d-110">Business or e-commerce websites that use WordPress.</span></span>
* <span data-ttu-id="ef23d-111">使用其他内容管理系统构建网站。</span><span class="sxs-lookup"><span data-stu-id="ef23d-111">Web sites built using other content management systems.</span></span>

## <a name="architecture"></a><span data-ttu-id="ef23d-112">体系结构</span><span class="sxs-lookup"><span data-stu-id="ef23d-112">Architecture</span></span>

<span data-ttu-id="ef23d-113">[![可缩放的安全 WordPress 部署中涉及的 Azure 组件体系结构概况](media/secure-scalable-wordpress.png)](media/secure-scalable-wordpress.png#lightbox)</span><span class="sxs-lookup"><span data-stu-id="ef23d-113">[![Architecture overview of the Azure components involved in a scalable and secure WordPress deployment](media/secure-scalable-wordpress.png)](media/secure-scalable-wordpress.png#lightbox)</span></span>

<span data-ttu-id="ef23d-114">本方案涉及到一个使用 Ubuntu Web 服务器和 MariaDB 的可缩放安全 WordPress 安装。</span><span class="sxs-lookup"><span data-stu-id="ef23d-114">This scenario covers a scalable and secure installation of WordPress that uses Ubuntu web servers and MariaDB.</span></span> <span data-ttu-id="ef23d-115">本方案包含两个不同的数据流，第一个是用户访问网站时产生的流：</span><span class="sxs-lookup"><span data-stu-id="ef23d-115">There are two distinct data flows in this scenario the first is users access the website:</span></span>

1. <span data-ttu-id="ef23d-116">用户通过 CDN 访问前端网站。</span><span class="sxs-lookup"><span data-stu-id="ef23d-116">Users access the front-end website through a CDN.</span></span>
2. <span data-ttu-id="ef23d-117">CDN 使用 Azure 负载均衡器作为来源，从中提取未缓存的所有数据。</span><span class="sxs-lookup"><span data-stu-id="ef23d-117">The CDN uses an Azure load balancer as the origin, and pulls any data that isn't cached from there.</span></span>
3. <span data-ttu-id="ef23d-118">Azure 负载均衡器将请求分配到 Web 服务器的虚拟机规模集。</span><span class="sxs-lookup"><span data-stu-id="ef23d-118">The Azure load balancer distributes requests to the virtual machine scale sets of web servers.</span></span>
4. <span data-ttu-id="ef23d-119">WordPress 应用程序从 Maria DB 群集提取所有动态信息，这些都是托管在 Azure 文件中的静态内容。</span><span class="sxs-lookup"><span data-stu-id="ef23d-119">The WordPress application pulls any dynamic information out of the Maria DB clusters, all static content is hosted in Azure Files.</span></span>
5. <span data-ttu-id="ef23d-120">SSL 密钥存储在 Azure Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="ef23d-120">SSL keys are stored Azure Key Vault.</span></span>

<span data-ttu-id="ef23d-121">第二个工作流涉及到作者供应新内容：</span><span class="sxs-lookup"><span data-stu-id="ef23d-121">The second workflow is how authors contribute new content:</span></span>

1. <span data-ttu-id="ef23d-122">作者安全连接到公共 VPN 网关。</span><span class="sxs-lookup"><span data-stu-id="ef23d-122">Authors connect securely to the public VPN gateway.</span></span>
2. <span data-ttu-id="ef23d-123">VPN 身份验证信息存储在 Azure Active Directory 中。</span><span class="sxs-lookup"><span data-stu-id="ef23d-123">VPN authentication information is stored in Azure Active Directory.</span></span>
3. <span data-ttu-id="ef23d-124">然后与管理员跳转盒建立连接。</span><span class="sxs-lookup"><span data-stu-id="ef23d-124">A connection is then established to the Admin jump boxes.</span></span>
4. <span data-ttu-id="ef23d-125">作者可以通过管理员跳转盒连接到创作群集的 Azure 负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="ef23d-125">From the admin jump box, the author is then able to connect to the Azure load balancer for the authoring cluster.</span></span>
5. <span data-ttu-id="ef23d-126">Azure 负载均衡器将流量分配到对 Maria DB 群集拥有写入访问权限的 Web 服务器虚拟机规模集。</span><span class="sxs-lookup"><span data-stu-id="ef23d-126">The Azure load balancer distributes traffic to the virtual machine scale sets of web servers that have write access to the Maria DB cluster.</span></span>
6. <span data-ttu-id="ef23d-127">新的静态内容上传到 Azure 文件，动态内容写入到 Maria DB 群集。</span><span class="sxs-lookup"><span data-stu-id="ef23d-127">New static content is uploaded to Azure files and dynamic content is written into the Maria DB cluster.</span></span>
7. <span data-ttu-id="ef23d-128">然后，通过 rsync 或主/从复制将这些更改复制到备用区域。</span><span class="sxs-lookup"><span data-stu-id="ef23d-128">These changes are then replicated to the alternate region via rsync or master/slave replication.</span></span>

### <a name="components"></a><span data-ttu-id="ef23d-129">组件</span><span class="sxs-lookup"><span data-stu-id="ef23d-129">Components</span></span>

* <span data-ttu-id="ef23d-130">[Azure 内容分发网络 (CDN)](/azure/cdn/cdn-overview) 是可以高效地向用户传送 Web 内容的分布式服务器网络。</span><span class="sxs-lookup"><span data-stu-id="ef23d-130">[Azure Content Delivery Network (CDN)](/azure/cdn/cdn-overview) is a distributed network of servers that efficiently delivers web content to users.</span></span> <span data-ttu-id="ef23d-131">CDN 将缓存的内容存储在靠近最终用户的接入点位置中的边缘服务器上，因此可以最小化延迟。</span><span class="sxs-lookup"><span data-stu-id="ef23d-131">CDNs minimize latency by storing cached content on edge servers in point-of-presence locations near to end users.</span></span>
* <span data-ttu-id="ef23d-132">[虚拟网络](/azure/virtual-network/virtual-networks-overview)允许 VM 之类的资源以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。</span><span class="sxs-lookup"><span data-stu-id="ef23d-132">[Virtual networks](/azure/virtual-network/virtual-networks-overview) allow resources such as VMs to securely communicate with each other, the Internet, and on-premises networks.</span></span> <span data-ttu-id="ef23d-133">虚拟网络提供隔离和细分，可以筛选和路由流量，并且允许在不同位置之间进行连接。</span><span class="sxs-lookup"><span data-stu-id="ef23d-133">Virtual networks provide isolation and segmentation, filter and route traffic, and allow connection between locations.</span></span> <span data-ttu-id="ef23d-134">通过 VNet 在两个网络之间建立对等互连。</span><span class="sxs-lookup"><span data-stu-id="ef23d-134">The two networks are connected via Vnet peering.</span></span>
* <span data-ttu-id="ef23d-135">[网络安全组](/azure/virtual-network/security-overview)包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝入站或出站网络流量。</span><span class="sxs-lookup"><span data-stu-id="ef23d-135">[Network security groups](/azure/virtual-network/security-overview) contain a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> <span data-ttu-id="ef23d-136">本方案中的虚拟网络受网络安全组规则的保护，这些规则可以限制应用程序组件之间的流量。</span><span class="sxs-lookup"><span data-stu-id="ef23d-136">The virtual networks in this scenario are secured with network security group rules that restrict the flow of traffic between the application components.</span></span>
* <span data-ttu-id="ef23d-137">[负载均衡器](/azure/load-balancer/load-balancer-overview)根据规则和运行状况探测来分配入站流量。</span><span class="sxs-lookup"><span data-stu-id="ef23d-137">[Load balancers](/azure/load-balancer/load-balancer-overview) distribute inbound traffic according to rules and health probes.</span></span> <span data-ttu-id="ef23d-138">负载均衡器提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。</span><span class="sxs-lookup"><span data-stu-id="ef23d-138">A load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.</span></span> <span data-ttu-id="ef23d-139">本方案使用负载均衡器将来自内容分发网络的流量分配到前端 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="ef23d-139">A load balancer is used in this scenario to distribute traffic from the content deliver network to the front-end web servers.</span></span>
* <span data-ttu-id="ef23d-140">使用[虚拟机规模集][docs-vmss]可以创建并管理一组完全相同的、负载均衡的 VM。</span><span class="sxs-lookup"><span data-stu-id="ef23d-140">[Virtual machine scale sets][docs-vmss] let you create and manage a group of identical load-balanced VMs.</span></span> <span data-ttu-id="ef23d-141">可以根据需求或定义的计划自动增减 VM 实例的数目。</span><span class="sxs-lookup"><span data-stu-id="ef23d-141">The number of VM instances can automatically increase or decrease in response to demand or a defined schedule.</span></span> <span data-ttu-id="ef23d-142">本方案使用了两个不同的虚拟机规模集 - 提供内容的前端 Web 服务器使用其中的一个规模集，创作新内容的前端 Web 服务器使用另一个规模集。</span><span class="sxs-lookup"><span data-stu-id="ef23d-142">Two separate virtual machine scale sets are used in this scenario - one for the front-end web-servers serving content, and one for the front-end webservers used to author new content.</span></span>
* <span data-ttu-id="ef23d-143">[Azure 文件](/azure/storage/files/storage-files-introduction)在云中提供完全托管的文件共享，该共享托管本方案中的所有 WordPress 内容，使所有 VM 都可以访问这些数据。</span><span class="sxs-lookup"><span data-stu-id="ef23d-143">[Azure Files](/azure/storage/files/storage-files-introduction) provides a fully-managed file share in the cloud that hosts all of the WordPress content in this scenario, so that all of the VMs have access to the data.</span></span>
* <span data-ttu-id="ef23d-144">[Azure Key Vault](/azure/key-vault/key-vault-overview) 用于存储密码、证书和密钥以及严格控制对其的访问。</span><span class="sxs-lookup"><span data-stu-id="ef23d-144">[Azure Key Vault](/azure/key-vault/key-vault-overview) is used to store and tightly control access to passwords, certificates, and keys.</span></span>
* <span data-ttu-id="ef23d-145">[Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis) 是多租户、基于云的目录和标识管理服务。</span><span class="sxs-lookup"><span data-stu-id="ef23d-145">[Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis) is a multi-tenant, cloud-based directory and identity management service.</span></span> <span data-ttu-id="ef23d-146">在本方案中，Azure AD 为网站和 VPN 隧道提供身份验证服务。</span><span class="sxs-lookup"><span data-stu-id="ef23d-146">In this scenario, Azure AD provides authentication services for the website and the VPN tunnels.</span></span>

### <a name="alternatives"></a><span data-ttu-id="ef23d-147">备选项</span><span class="sxs-lookup"><span data-stu-id="ef23d-147">Alternatives</span></span>

* <span data-ttu-id="ef23d-148">[SQL Server for Linux](/azure/virtual-machines/linux/sql/sql-server-linux-virtual-machines-overview) 可以取代 MariaDB 数据存储。</span><span class="sxs-lookup"><span data-stu-id="ef23d-148">[SQL Server for Linux](/azure/virtual-machines/linux/sql/sql-server-linux-virtual-machines-overview) can replace the MariaDB data store.</span></span>
* <span data-ttu-id="ef23d-149">如果你偏向于使用完全托管的解决方案，可以用 [Azure Database for MySQL](/azure/mysql/overview) 取代 MariaDB 数据存储。</span><span class="sxs-lookup"><span data-stu-id="ef23d-149">[Azure database for MySQL](/azure/mysql/overview) can replace the MariaDB data store if you prefer a fully managed solution.</span></span>

## <a name="considerations"></a><span data-ttu-id="ef23d-150">注意事项</span><span class="sxs-lookup"><span data-stu-id="ef23d-150">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="ef23d-151">可用性</span><span class="sxs-lookup"><span data-stu-id="ef23d-151">Availability</span></span>

<span data-ttu-id="ef23d-152">本方案中的 VM 实例部署在多个区域中，通过 RSYNC 复制两个实例中的 WordPress 内容数据，并对 MariaDB 群集使用主从复制。</span><span class="sxs-lookup"><span data-stu-id="ef23d-152">The VM instances in this scenario are deployed across multiple regions, with the data replicated between the two via RSYNC for the WordPress content and master slave replication for the MariaDB clusters.</span></span>

<span data-ttu-id="ef23d-153">若要了解其他可用性主题，请参阅 Azure 体系结构中心的[可用性核对清单][availability]。</span><span class="sxs-lookup"><span data-stu-id="ef23d-153">For other availability topics, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="ef23d-154">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="ef23d-154">Scalability</span></span>

<span data-ttu-id="ef23d-155">本方案对每个区域中的两个前端 Web 服务器群集使用虚拟机规模集。</span><span class="sxs-lookup"><span data-stu-id="ef23d-155">This scenario uses virtual machine scale sets for the two front-end web server clusters in each region.</span></span> <span data-ttu-id="ef23d-156">使用规模集时，运行前端应用程序层的 VM 实例数可以根据客户需求或定义的计划自动进行缩放。</span><span class="sxs-lookup"><span data-stu-id="ef23d-156">With scale sets, the number of VM instances that run the front-end application tier can automatically scale in response to customer demand, or based on a defined schedule.</span></span> <span data-ttu-id="ef23d-157">有关详细信息，请参阅[虚拟机规模集的自动缩放概述][docs-vmss-autoscale]。</span><span class="sxs-lookup"><span data-stu-id="ef23d-157">For more information, see [Overview of autoscale with virtual machine scale sets][docs-vmss-autoscale].</span></span>

<span data-ttu-id="ef23d-158">后端是可用性集中的 MariaDB 群集。</span><span class="sxs-lookup"><span data-stu-id="ef23d-158">The back end is a MariaDB cluster in an availability set.</span></span> <span data-ttu-id="ef23d-159">有关详细信息，请参阅 [MariaDB 群集教程][mariadb-tutorial]。</span><span class="sxs-lookup"><span data-stu-id="ef23d-159">For more information, see the [MariaDB cluster tutorial][mariadb-tutorial].</span></span>

<span data-ttu-id="ef23d-160">若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="ef23d-160">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="ef23d-161">安全</span><span class="sxs-lookup"><span data-stu-id="ef23d-161">Security</span></span>

<span data-ttu-id="ef23d-162">所有虚拟网络流量都进入前端应用程序层，受网络安全组的保护。</span><span class="sxs-lookup"><span data-stu-id="ef23d-162">All the virtual network traffic into the front-end application tier and protected by network security groups.</span></span> <span data-ttu-id="ef23d-163">规则会限制流量的流动，只有前端应用程序层 VM 实例可以访问后端数据库层。</span><span class="sxs-lookup"><span data-stu-id="ef23d-163">Rules limit the flow of traffic so that only the front-end application tier VM instances can access the back-end database tier.</span></span> <span data-ttu-id="ef23d-164">不允许数据库层发出的出站 Internet 流量。</span><span class="sxs-lookup"><span data-stu-id="ef23d-164">No outbound Internet traffic is allowed from the database tier.</span></span> <span data-ttu-id="ef23d-165">为了减少受攻击面，请勿打开直接远程管理端口。</span><span class="sxs-lookup"><span data-stu-id="ef23d-165">To reduce the attack footprint, no direct remote management ports are open.</span></span> <span data-ttu-id="ef23d-166">有关详细信息，请参阅 [Azure 网络安全组][docs-nsg]。</span><span class="sxs-lookup"><span data-stu-id="ef23d-166">For more information, see [Azure network security groups][docs-nsg].</span></span>

<span data-ttu-id="ef23d-167">若需安全方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="ef23d-167">For general guidance on designing secure scenarios, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="ef23d-168">复原</span><span class="sxs-lookup"><span data-stu-id="ef23d-168">Resiliency</span></span>

<span data-ttu-id="ef23d-169">本方案结合多个区域、数据复制和虚拟机规模集使用 Azure 负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="ef23d-169">In combination with the use of multiple regions, data replication and virtual machine scale sets, this scenario uses Azure load balancers.</span></span> <span data-ttu-id="ef23d-170">这些网络组件可将流量分配到连接的 VM 实例，并且包括运行状况探测，可以确保流量只分配到正常运行的 VM。</span><span class="sxs-lookup"><span data-stu-id="ef23d-170">These networking components distribute traffic to the connected VM instances, and include health probes that ensure traffic is only distributed to healthy VMs.</span></span> <span data-ttu-id="ef23d-171">所有这些网络组件通过 CDN 安装在前面。</span><span class="sxs-lookup"><span data-stu-id="ef23d-171">All of these networking components are fronted via a CDN.</span></span> <span data-ttu-id="ef23d-172">这样，网络资源和应用程序便可以灵活应对那些会导致流量中断并影响最终用户访问的问题。</span><span class="sxs-lookup"><span data-stu-id="ef23d-172">This makes the networking resources and application resilient to issues that would otherwise disrupt traffic and impact end-user access.</span></span>

<span data-ttu-id="ef23d-173">若需可复原方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="ef23d-173">For general guidance on designing resilient scenarios, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="ef23d-174">定价</span><span class="sxs-lookup"><span data-stu-id="ef23d-174">Pricing</span></span>

<span data-ttu-id="ef23d-175">为了方便用户了解运行本方案的成本，我们已在成本计算器中预配置了所有服务。</span><span class="sxs-lookup"><span data-stu-id="ef23d-175">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="ef23d-176">若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。</span><span class="sxs-lookup"><span data-stu-id="ef23d-176">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="ef23d-177">我们根据上面的体系结构示意图，提供了预配置的[成本配置文件][pricing]。</span><span class="sxs-lookup"><span data-stu-id="ef23d-177">We have provided a pre-configured [cost profile][pricing] based on the architecture diagram provided above.</span></span> <span data-ttu-id="ef23d-178">若要为自己的用例配置定价计算器，请考虑以下几个因素：</span><span class="sxs-lookup"><span data-stu-id="ef23d-178">To configure the pricing calculator for your use case, there are a couple main things to consider:</span></span>

* <span data-ttu-id="ef23d-179">预期每月的流量有多少 (GB)？</span><span class="sxs-lookup"><span data-stu-id="ef23d-179">How much traffic are you expecting in terms of GB/month?</span></span> <span data-ttu-id="ef23d-180">流量对成本的影响最大，因为它会影响在虚拟机规模集中公开数据所需的 VM 数量。</span><span class="sxs-lookup"><span data-stu-id="ef23d-180">The amount of traffic will have the biggest impact on your cost, as it will impact the number of VMs that are required to surface the data in the virtual machine scale set.</span></span> <span data-ttu-id="ef23d-181">此外，它直接关系到通过 CDN 公开的数据量。</span><span class="sxs-lookup"><span data-stu-id="ef23d-181">Additionally, it will directly correlate with the amount of data that is surfaced via the CDN.</span></span>
* <span data-ttu-id="ef23d-182">要将多少新数据写入网站？</span><span class="sxs-lookup"><span data-stu-id="ef23d-182">How much new data are you going to be writing to your website?</span></span> <span data-ttu-id="ef23d-183">写入网站的新数据关系到在区域之间镜像的数据量。</span><span class="sxs-lookup"><span data-stu-id="ef23d-183">New data written to your website correlates with how much data is mirrored across the regions.</span></span>
* <span data-ttu-id="ef23d-184">有多少内容是动态的？</span><span class="sxs-lookup"><span data-stu-id="ef23d-184">How much of your content is dynamic?</span></span> <span data-ttu-id="ef23d-185">有多少内容是静态的？</span><span class="sxs-lookup"><span data-stu-id="ef23d-185">How much is static?</span></span> <span data-ttu-id="ef23d-186">动态与静态内容的差异会影响需要从数据库层检索的数据量，以及要在 CDN 中缓存的数据量。</span><span class="sxs-lookup"><span data-stu-id="ef23d-186">The variance around dynamic and static content influences how much data has to be retrieved from the database tier versus how much will be cached in the CDN.</span></span>

<!-- links -->
[architecture]: ./media/architecture-secure-scalable-wordpress.png
[mariadb-tutorial]: /azure/virtual-machines/linux/classic/mariadb-mysql-cluster
[docs-vmss]: /azure/virtual-machine-scale-sets/overview
[docs-vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[docs-nsg]: /azure/virtual-network/security-overview
[security]: /azure/security/
[availability]: ../../checklist/availability.md
[resiliency]: /azure/architecture/resiliency/
[scalability]: /azure/architecture/checklist/scalability
[pricing]: https://azure.com/e/a8c4809dab444c1ca4870c489fbb196b
