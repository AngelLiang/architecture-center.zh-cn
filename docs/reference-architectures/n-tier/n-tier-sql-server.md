---
title: 使用 SQL Server 的 N 层应用程序
description: 如何在 Azure 上实现多层体系结构，以确保可用性、安全性、可伸缩性和可管理性。
author: MikeWasson
ms.date: 06/23/2018
ms.openlocfilehash: 7c8184d25cf6b3bd358adc2728329fd3bd08503a
ms.sourcegitcommit: 58d93e7ac9a6d44d5668a187a6827d7cd4f5a34d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37142295"
---
# <a name="n-tier-application-with-sql-server"></a><span data-ttu-id="a9497-103">使用 SQL Server 的 N 层应用程序</span><span class="sxs-lookup"><span data-stu-id="a9497-103">N-tier application with SQL Server</span></span>

<span data-ttu-id="a9497-104">此参考体系结构演示如何使用 Windows 上适用于数据层的 SQL Server 部署为 N 层应用程序配置的 VM 和虚拟网络。</span><span class="sxs-lookup"><span data-stu-id="a9497-104">This reference architecture shows how to deploy VMs and a virtual network configured for an N-tier application, using SQL Server on Windows for the data tier.</span></span> [<span data-ttu-id="a9497-105">**部署此解决方案**。</span><span class="sxs-lookup"><span data-stu-id="a9497-105">**Deploy this solution**.</span></span>](#deploy-the-solution) 

<span data-ttu-id="a9497-106">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="a9497-106">![[0]][0]</span></span>

<span data-ttu-id="a9497-107">下载此体系结构的 [Visio 文件][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="a9497-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="a9497-108">体系结构</span><span class="sxs-lookup"><span data-stu-id="a9497-108">Architecture</span></span> 

<span data-ttu-id="a9497-109">此体系结构具有以下组件：</span><span class="sxs-lookup"><span data-stu-id="a9497-109">The architecture has the following components:</span></span>

* <span data-ttu-id="a9497-110">**资源组。**</span><span class="sxs-lookup"><span data-stu-id="a9497-110">**Resource group.**</span></span> <span data-ttu-id="a9497-111">[资源组][resource-manager-overview]用于对资源进行分组，以便可以按生存期、所有者或其他条件对其进行管理。</span><span class="sxs-lookup"><span data-stu-id="a9497-111">[Resource groups][resource-manager-overview] are used to group resources so they can be managed by lifetime, owner, or other criteria.</span></span>

* <span data-ttu-id="a9497-112">**虚拟网络 (VNet) 和子网。**</span><span class="sxs-lookup"><span data-stu-id="a9497-112">**Virtual network (VNet) and subnets.**</span></span> <span data-ttu-id="a9497-113">每个 Azure VM 都会部署到可细分为多个子网的 VNet 中。</span><span class="sxs-lookup"><span data-stu-id="a9497-113">Every Azure VM is deployed into a VNet that can be segmented into multiple subnets.</span></span> <span data-ttu-id="a9497-114">为每个层创建一个单独的子网。</span><span class="sxs-lookup"><span data-stu-id="a9497-114">Create a separate subnet for each tier.</span></span> 

* <span data-ttu-id="a9497-115">**NSG。**</span><span class="sxs-lookup"><span data-stu-id="a9497-115">**NSGs.**</span></span> <span data-ttu-id="a9497-116">使用[网络安全组][nsg] (NSG) 来限制 VNet 中的网络流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-116">Use [network security groups][nsg] (NSGs) to restrict network traffic within the VNet.</span></span> <span data-ttu-id="a9497-117">例如，在此处显示的 3 层体系结构中，数据库层不接受来自 Web 前端的流量，仅接受来自业务层和管理子网的流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-117">For example, in the 3-tier architecture shown here, the database tier does not accept traffic from the web front end, only from the business tier and the management subnet.</span></span>

* <span data-ttu-id="a9497-118">**虚拟机**。</span><span class="sxs-lookup"><span data-stu-id="a9497-118">**Virtual machines**.</span></span> <span data-ttu-id="a9497-119">有关如何配置 VM 的建议，请参阅[在 Azure 上运行 Windows VM](./windows-vm.md) 和[在 Azure 上运行 Linux VM](./linux-vm.md)。</span><span class="sxs-lookup"><span data-stu-id="a9497-119">For recommendations on configuring VMs, see [Run a Windows VM on Azure](./windows-vm.md) and [Run a Linux VM on Azure](./linux-vm.md).</span></span>

* <span data-ttu-id="a9497-120">**可用性集。**</span><span class="sxs-lookup"><span data-stu-id="a9497-120">**Availability sets.**</span></span> <span data-ttu-id="a9497-121">为每个层创建一个[可用性集][azure-availability-sets]，并且在每个层中至少预配两个 VM。</span><span class="sxs-lookup"><span data-stu-id="a9497-121">Create an [availability set][azure-availability-sets] for each tier, and provision at least two VMs in each tier.</span></span> <span data-ttu-id="a9497-122">这样，VM 便可以满足 VM 的更高[服务级别协议 (SLA)][vm-sla]。</span><span class="sxs-lookup"><span data-stu-id="a9497-122">This makes the VMs eligible for a higher [service level agreement (SLA)][vm-sla] for VMs.</span></span> 

* <span data-ttu-id="a9497-123">**VM 规模集**（未显示）。</span><span class="sxs-lookup"><span data-stu-id="a9497-123">**VM scale set** (not shown).</span></span> <span data-ttu-id="a9497-124">可以使用 [VM 规模集][vmss]来替代可用性集。</span><span class="sxs-lookup"><span data-stu-id="a9497-124">A [VM scale set][vmss] is an alternative to using an availability set.</span></span> <span data-ttu-id="a9497-125">可以通过规模集轻松地手动横向扩展某个层中的 VM，也可以按预定义规则自动这样做。</span><span class="sxs-lookup"><span data-stu-id="a9497-125">A scale sets makes it easy to scale out the VMs in a tier, either manually or automatically based on predefined rules.</span></span>

* <span data-ttu-id="a9497-126">**Azure 负载均衡器。**</span><span class="sxs-lookup"><span data-stu-id="a9497-126">**Azure Load balancers.**</span></span> <span data-ttu-id="a9497-127">[负载均衡器][load-balancer]将传入的 Internet 请求分配到 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="a9497-127">The [load balancers][load-balancer] distribute incoming Internet requests to the VM instances.</span></span> <span data-ttu-id="a9497-128">使用[公共负载均衡器][load-balancer-external]将传入的 Internet 流量分配到 Web 层，使用[内部负载均衡器][load-balancer-internal]将来自 Web 层的网络流量分配到业务层。</span><span class="sxs-lookup"><span data-stu-id="a9497-128">Use a [public load balancer][load-balancer-external] to distribute incoming Internet traffic to the web tier, and an [internal load balancer][load-balancer-internal] to distribute network traffic from the web tier to the business tier.</span></span>

* <span data-ttu-id="a9497-129">**公共 IP 地址**。</span><span class="sxs-lookup"><span data-stu-id="a9497-129">**Public IP address**.</span></span> <span data-ttu-id="a9497-130">公共负载均衡器需要使用一个公共 IP 地址来接收 Internet 流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-130">A public IP address is needed for the public load balancer to receive Internet traffic.</span></span>

* <span data-ttu-id="a9497-131">**Jumpbox。**</span><span class="sxs-lookup"><span data-stu-id="a9497-131">**Jumpbox.**</span></span> <span data-ttu-id="a9497-132">也称为[守护主机]。</span><span class="sxs-lookup"><span data-stu-id="a9497-132">Also called a [bastion host].</span></span> <span data-ttu-id="a9497-133">网络上的一个安全 VM，管理员使用它来连接到其他 VM。</span><span class="sxs-lookup"><span data-stu-id="a9497-133">A secure VM on the network that administrators use to connect to the other VMs.</span></span> <span data-ttu-id="a9497-134">Jumpbox 中的某个 NSG 只允许来自安全列表中的公共 IP 地址的远程流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-134">The jumpbox has an NSG that allows remote traffic only from public IP addresses on a safe list.</span></span> <span data-ttu-id="a9497-135">该 NSG 应允许远程桌面 (RDP) 流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-135">The NSG should permit remote desktop (RDP) traffic.</span></span>

* <span data-ttu-id="a9497-136">**SQL Server Always On 可用性组。**</span><span class="sxs-lookup"><span data-stu-id="a9497-136">**SQL Server Always On Availability Group.**</span></span> <span data-ttu-id="a9497-137">通过启用复制和故障转移，在数据层提供高可用性。</span><span class="sxs-lookup"><span data-stu-id="a9497-137">Provides high availability at the data tier, by enabling replication and failover.</span></span> <span data-ttu-id="a9497-138">它使用 Windows Server 故障转移群集 (WSFC) 技术进行故障转移。</span><span class="sxs-lookup"><span data-stu-id="a9497-138">It uses Windows Server Failover Cluster (WSFC) technology for failover.</span></span>

* <span data-ttu-id="a9497-139">**Active Directory 域服务 (AD DS) 服务器**。</span><span class="sxs-lookup"><span data-stu-id="a9497-139">**Active Directory Domain Services (AD DS) Servers**.</span></span> <span data-ttu-id="a9497-140">故障转移群集及其关联的群集角色的计算机对象在 Active Directory 域服务 (AD DS) 中创建。</span><span class="sxs-lookup"><span data-stu-id="a9497-140">The computer objects for the failover cluster and its associated clustered roles are created in Active Directory Domain Services (AD DS).</span></span>

* <span data-ttu-id="a9497-141">**云见证**。</span><span class="sxs-lookup"><span data-stu-id="a9497-141">**Cloud Witness**.</span></span> <span data-ttu-id="a9497-142">故障转移群集要求其节点的半数以上处于运行状态，这称为“建立仲裁”。</span><span class="sxs-lookup"><span data-stu-id="a9497-142">A failover cluster requires more than half of its nodes to be running, which is known as having quorum.</span></span> <span data-ttu-id="a9497-143">如果群集只有两个节点，则网络分区之后，每个节点都会认为自己是主节点。</span><span class="sxs-lookup"><span data-stu-id="a9497-143">If the cluster has just two nodes, a network partition could cause each node to think it's the master node.</span></span> <span data-ttu-id="a9497-144">在这种情况下，需要使用见证来打破“僵持”局面，建立仲裁。</span><span class="sxs-lookup"><span data-stu-id="a9497-144">In that case, you need a *witness* to break ties and establish quorum.</span></span> <span data-ttu-id="a9497-145">见证是一种可以充当僵持局面打破者并建立仲裁的资源，例如共享磁盘。</span><span class="sxs-lookup"><span data-stu-id="a9497-145">A witness is a resource such as a shared disk that can act as a tie breaker to establish quorum.</span></span> <span data-ttu-id="a9497-146">云见证是一种使用 Azure Blob 存储的见证。</span><span class="sxs-lookup"><span data-stu-id="a9497-146">Cloud Witness is a type of witness that uses Azure Blob Storage.</span></span> <span data-ttu-id="a9497-147">若要详细了解仲裁的概念，请参阅[了解群集和池仲裁](/windows-server/storage/storage-spaces/understand-quorum)。</span><span class="sxs-lookup"><span data-stu-id="a9497-147">To learn more about the concept of quorum, see [Understanding cluster and pool quorum](/windows-server/storage/storage-spaces/understand-quorum).</span></span> <span data-ttu-id="a9497-148">有关云见证的详细信息，请参阅[部署故障转移群集的云见证](/windows-server/failover-clustering/deploy-cloud-witness)。</span><span class="sxs-lookup"><span data-stu-id="a9497-148">For more information about Cloud Witness, see [Deploy a Cloud Witness for a Failover Cluster](/windows-server/failover-clustering/deploy-cloud-witness).</span></span> 

* <span data-ttu-id="a9497-149">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="a9497-149">**Azure DNS**.</span></span> <span data-ttu-id="a9497-150">[Azure DNS][azure-dns] 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。</span><span class="sxs-lookup"><span data-stu-id="a9497-150">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="a9497-151">通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。</span><span class="sxs-lookup"><span data-stu-id="a9497-151">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span>

## <a name="recommendations"></a><span data-ttu-id="a9497-152">建议</span><span class="sxs-lookup"><span data-stu-id="a9497-152">Recommendations</span></span>

<span data-ttu-id="a9497-153">你的要求可能不同于此处描述的体系结构。</span><span class="sxs-lookup"><span data-stu-id="a9497-153">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="a9497-154">请使用以下建议作为入手点。</span><span class="sxs-lookup"><span data-stu-id="a9497-154">Use these recommendations as a starting point.</span></span> 

### <a name="vnet--subnets"></a><span data-ttu-id="a9497-155">VNet/子网</span><span class="sxs-lookup"><span data-stu-id="a9497-155">VNet / Subnets</span></span>

<span data-ttu-id="a9497-156">在创建 VNet 时，确定每个子网中的资源需要多少 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="a9497-156">When you create the VNet, determine how many IP addresses your resources in each subnet require.</span></span> <span data-ttu-id="a9497-157">使用 [CIDR] 表示法为所需的 IP 地址指定子网掩码和足够大的 VNet 地址范围。</span><span class="sxs-lookup"><span data-stu-id="a9497-157">Specify a subnet mask and a VNet address range large enough for the required IP addresses, using [CIDR] notation.</span></span> <span data-ttu-id="a9497-158">使用标准[专用 IP 地址块][private-ip-space]内的一个地址空间，这些地址块为 10.0.0.0/8、172.16.0.0/12 和 192.168.0.0/16。</span><span class="sxs-lookup"><span data-stu-id="a9497-158">Use an address space that falls within the standard [private IP address blocks][private-ip-space], which are 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16.</span></span>

<span data-ttu-id="a9497-159">如果以后需要在 VNet 与本地网络之间设置一个网关，请选择一个不与你的本地网络重叠的地址范围。</span><span class="sxs-lookup"><span data-stu-id="a9497-159">Choose an address range that does not overlap with your on-premises network, in case you need to set up a gateway between the VNet and your on-premise network later.</span></span> <span data-ttu-id="a9497-160">在创建 VNet 后，将无法更改地址范围。</span><span class="sxs-lookup"><span data-stu-id="a9497-160">Once you create the VNet, you can't change the address range.</span></span>

<span data-ttu-id="a9497-161">在设计子网时一定要牢记功能和安全要求。</span><span class="sxs-lookup"><span data-stu-id="a9497-161">Design subnets with functionality and security requirements in mind.</span></span> <span data-ttu-id="a9497-162">同一层或同一角色中的所有 VM 应当置于同一子网，这可能是一个安全边界。</span><span class="sxs-lookup"><span data-stu-id="a9497-162">All VMs within the same tier or role should go into the same subnet, which can be a security boundary.</span></span> <span data-ttu-id="a9497-163">有关设计 VNet 和子网的详细信息，请参阅[规划和设计 Azure 虚拟网络][plan-network]。</span><span class="sxs-lookup"><span data-stu-id="a9497-163">For more information about designing VNets and subnets, see [Plan and design Azure Virtual Networks][plan-network].</span></span>

### <a name="load-balancers"></a><span data-ttu-id="a9497-164">负载均衡器</span><span class="sxs-lookup"><span data-stu-id="a9497-164">Load balancers</span></span>

<span data-ttu-id="a9497-165">不要将 VM 直接向 Internet 公开，而是改为给每个 VM 提供专用 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="a9497-165">Do not expose the VMs directly to the Internet, but instead give each VM a private IP address.</span></span> <span data-ttu-id="a9497-166">客户端使用公共负载均衡器的 IP 地址进行连接。</span><span class="sxs-lookup"><span data-stu-id="a9497-166">Clients connect using the IP address of the public load balancer.</span></span>

<span data-ttu-id="a9497-167">定义用于将网络流量定向到 VM 的负载均衡器规则。</span><span class="sxs-lookup"><span data-stu-id="a9497-167">Define load balancer rules to direct network traffic to the VMs.</span></span> <span data-ttu-id="a9497-168">例如，若要启用 HTTP 流量，请创建将前端配置中的端口 80 映射到后端地址池上的端口 80 的规则。</span><span class="sxs-lookup"><span data-stu-id="a9497-168">For example, to enable HTTP traffic, create a rule that maps port 80 from the front-end configuration to port 80 on the back-end address pool.</span></span> <span data-ttu-id="a9497-169">当客户端将 HTTP 请求发送到端口 80 时，负载均衡器会通过使用包括源 IP 地址的[哈希算法][load-balancer-hashing]选择后端 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="a9497-169">When a client sends an HTTP request to port 80, the load balancer selects a back-end IP address by using a [hashing algorithm][load-balancer-hashing] that includes the source IP address.</span></span> <span data-ttu-id="a9497-170">这样，客户端请求就会分布在所有 VM 上。</span><span class="sxs-lookup"><span data-stu-id="a9497-170">In that way, client requests are distributed across all the VMs.</span></span>

### <a name="network-security-groups"></a><span data-ttu-id="a9497-171">网络安全组</span><span class="sxs-lookup"><span data-stu-id="a9497-171">Network security groups</span></span>

<span data-ttu-id="a9497-172">使用 NSG 规则限制各个层之间的流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-172">Use NSG rules to restrict traffic between tiers.</span></span> <span data-ttu-id="a9497-173">例如，在上面显示的 3 层体系结构中，Web 层不直接与数据库层进行通信。</span><span class="sxs-lookup"><span data-stu-id="a9497-173">For example, in the 3-tier architecture shown above, the web tier does not communicate directly with the database tier.</span></span> <span data-ttu-id="a9497-174">为强制实现此目的，数据库层应当阻止来自 Web 层子网的传入流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-174">To enforce this, the database tier should block incoming traffic from the web tier subnet.</span></span>  

1. <span data-ttu-id="a9497-175">拒绝来自 VNet 的所有入站流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-175">Deny all inbound traffic from the VNet.</span></span> <span data-ttu-id="a9497-176">（在规则中使用 `VIRTUAL_NETWORK` 标记。）</span><span class="sxs-lookup"><span data-stu-id="a9497-176">(Use the `VIRTUAL_NETWORK` tag in the rule.)</span></span> 
2. <span data-ttu-id="a9497-177">允许来自业务层子网的入站流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-177">Allow inbound traffic from the business tier subnet.</span></span>  
3. <span data-ttu-id="a9497-178">允许来自数据库层子网本身的入站流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-178">Allow inbound traffic from the database tier subnet itself.</span></span> <span data-ttu-id="a9497-179">此规则允许在数据库 VM 之间通信，这是进行数据库复制和故障转移所必需的。</span><span class="sxs-lookup"><span data-stu-id="a9497-179">This rule allows communication between the database VMs, which is needed for database replication and failover.</span></span>
4. <span data-ttu-id="a9497-180">允许来自 Jumpbox 子网的 RDP 流量（端口 3389）。</span><span class="sxs-lookup"><span data-stu-id="a9497-180">Allow RDP traffic (port 3389) from the jumpbox subnet.</span></span> <span data-ttu-id="a9497-181">此规则允许管理员从 jumpbox 连接到数据库层。</span><span class="sxs-lookup"><span data-stu-id="a9497-181">This rule lets administrators connect to the database tier from the jumpbox.</span></span>

<span data-ttu-id="a9497-182">创建优先级比第一项规则更高的规则 2 &ndash; 4，以便替代第一项规则。</span><span class="sxs-lookup"><span data-stu-id="a9497-182">Create rules 2 &ndash; 4 with higher priority than the first rule, so they override it.</span></span>


### <a name="sql-server-always-on-availability-groups"></a><span data-ttu-id="a9497-183">SQL Server Always On 可用性组</span><span class="sxs-lookup"><span data-stu-id="a9497-183">SQL Server Always On Availability Groups</span></span>

<span data-ttu-id="a9497-184">建议使用 [Always On 可用性组][sql-alwayson]以实现高可用性。</span><span class="sxs-lookup"><span data-stu-id="a9497-184">We recommend [Always On Availability Groups][sql-alwayson] for SQL Server high availability.</span></span> <span data-ttu-id="a9497-185">在 Windows Server 2016 之前，Always On 可用性组需要一个域控制器，并且可用性组中的所有节点必须在同一 AD 域中。</span><span class="sxs-lookup"><span data-stu-id="a9497-185">Prior to Windows Server 2016, Always On Availability Groups require a domain controller, and all nodes in the availability group must be in the same AD domain.</span></span>

<span data-ttu-id="a9497-186">其他层通过[可用性组侦听程序][sql-alwayson-listeners]连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="a9497-186">Other tiers connect to the database through an [availability group listener][sql-alwayson-listeners].</span></span> <span data-ttu-id="a9497-187">该侦听程序使得 SQL 客户端能够在不知道 SQL Server 物理实例名称的情况下进行连接。</span><span class="sxs-lookup"><span data-stu-id="a9497-187">The listener enables a SQL client to connect without knowing the name of the physical instance of SQL Server.</span></span> <span data-ttu-id="a9497-188">访问数据库的 VM 必须加入域。</span><span class="sxs-lookup"><span data-stu-id="a9497-188">VMs that access the database must be joined to the domain.</span></span> <span data-ttu-id="a9497-189">客户端（在本例中为另一个层）使用 DNS 将该侦听程序的虚拟网络名称解析为 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="a9497-189">The client (in this case, another tier) uses DNS to resolve the listener's virtual network name into IP addresses.</span></span>

<span data-ttu-id="a9497-190">如下所述配置 SQL Server Always On 可用性组：</span><span class="sxs-lookup"><span data-stu-id="a9497-190">Configure the SQL Server Always On Availability Group as follows:</span></span>

1. <span data-ttu-id="a9497-191">创建一个 Windows Server 故障转移群集 (WSFC) 群集、一个 SQL Server Always On 可用性组和一个主要副本。</span><span class="sxs-lookup"><span data-stu-id="a9497-191">Create a Windows Server Failover Clustering (WSFC) cluster, a SQL Server Always On Availability Group, and a primary replica.</span></span> <span data-ttu-id="a9497-192">有关详细信息，请参阅 [Always On 可用性组入门 (SQL Server)][sql-alwayson-getting-started]。</span><span class="sxs-lookup"><span data-stu-id="a9497-192">For more information, see [Getting Started with Always On Availability Groups][sql-alwayson-getting-started].</span></span> 
2. <span data-ttu-id="a9497-193">创建一个具有静态专用 IP 地址的内部负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="a9497-193">Create an internal load balancer with a static private IP address.</span></span>
3. <span data-ttu-id="a9497-194">创建一个可用性组侦听程序，并将该侦听程序的 DNS 名称映射到一个内部负载均衡器的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="a9497-194">Create an availability group listener, and map the listener's DNS name to the IP address of an internal load balancer.</span></span> 
4. <span data-ttu-id="a9497-195">为 SQL Server 侦听端口（默认情况下为 TCP 端口 1433）创建一个负载均衡器规则。</span><span class="sxs-lookup"><span data-stu-id="a9497-195">Create a load balancer rule for the SQL Server listening port (TCP port 1433 by default).</span></span> <span data-ttu-id="a9497-196">该负载均衡器规则必须启用*浮动 IP*，也称为“直接服务器返回”。</span><span class="sxs-lookup"><span data-stu-id="a9497-196">The load balancer rule must enable *floating IP*, also called Direct Server Return.</span></span> <span data-ttu-id="a9497-197">这将导致 VM 直接回复客户端，从而实现到主要副本的直接连接。</span><span class="sxs-lookup"><span data-stu-id="a9497-197">This causes the VM to reply directly to the client, which enables a direct connection to the primary replica.</span></span>
  
   > [!NOTE]
   > <span data-ttu-id="a9497-198">当启用了浮动 IP 时，前端端口号必须与负载均衡器规则中的后端端口号相同。</span><span class="sxs-lookup"><span data-stu-id="a9497-198">When floating IP is enabled, the front-end port number must be the same as the back-end port number in the load balancer rule.</span></span>
   > 
   > 

<span data-ttu-id="a9497-199">当 SQL 客户端尝试连接时，负载均衡器会将连接请求路由到主要副本。</span><span class="sxs-lookup"><span data-stu-id="a9497-199">When a SQL client tries to connect, the load balancer routes the connection request to the primary replica.</span></span> <span data-ttu-id="a9497-200">如果发生到其他副本的故障转移，则负载均衡器会自动将后续请求路由到新的主要副本。</span><span class="sxs-lookup"><span data-stu-id="a9497-200">If there is a failover to another replica, the load balancer automatically routes subsequent requests to a new primary replica.</span></span> <span data-ttu-id="a9497-201">有关详细信息，请参阅[Configure an ILB listener for SQL Server Always On Availability Groups][sql-alwayson-ilb]（为 SQL Server Always On 可用性组配置 ILB 侦听程序）。</span><span class="sxs-lookup"><span data-stu-id="a9497-201">For more information, see [Configure an ILB listener for SQL Server Always On Availability Groups][sql-alwayson-ilb].</span></span>

<span data-ttu-id="a9497-202">在故障转移期间，现有的客户端连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="a9497-202">During a failover, existing client connections are closed.</span></span> <span data-ttu-id="a9497-203">在故障转移完成后，新连接将被路由到新的主要副本。</span><span class="sxs-lookup"><span data-stu-id="a9497-203">After the failover completes, new connections will be routed to the new primary replica.</span></span>

<span data-ttu-id="a9497-204">如果应用程序执行的读取操作显著多于写入操作，则可以将一些只读查询转移到次要副本。</span><span class="sxs-lookup"><span data-stu-id="a9497-204">If your application makes significantly more reads than writes, you can offload some of the read-only queries to a secondary replica.</span></span> <span data-ttu-id="a9497-205">请参阅[Using a Listener to Connect to a Read-Only Secondary Replica (Read-Only Routing)][sql-alwayson-read-only-routing]（使用侦听程序连接到只读次要副本（只读路由））。</span><span class="sxs-lookup"><span data-stu-id="a9497-205">See [Using a Listener to Connect to a Read-Only Secondary Replica (Read-Only Routing)][sql-alwayson-read-only-routing].</span></span>

<span data-ttu-id="a9497-206">通过执行可用性组的[强制手动故障转移][sql-alwayson-force-failover]来测试部署。</span><span class="sxs-lookup"><span data-stu-id="a9497-206">Test your deployment by [forcing a manual failover][sql-alwayson-force-failover] of the availability group.</span></span>

### <a name="jumpbox"></a><span data-ttu-id="a9497-207">Jumpbox</span><span class="sxs-lookup"><span data-stu-id="a9497-207">Jumpbox</span></span>

<span data-ttu-id="a9497-208">不要允许通过公共 Internet 对运行应用程序工作负荷的 VM 进行 RDP 访问。</span><span class="sxs-lookup"><span data-stu-id="a9497-208">Do not allow RDP access from the public Internet to the VMs that run the application workload.</span></span> <span data-ttu-id="a9497-209">相反，对这些 VM 的所有 RDP 访问都必须通过 jumpbox 进行。</span><span class="sxs-lookup"><span data-stu-id="a9497-209">Instead, all RDP access to these VMs must come through the jumpbox.</span></span> <span data-ttu-id="a9497-210">管理员登录到 jumpbox，然后从 jumpbox 登录到其他 VM。</span><span class="sxs-lookup"><span data-stu-id="a9497-210">An administrator logs into the jumpbox, and then logs into the other VM from the jumpbox.</span></span> <span data-ttu-id="a9497-211">Jumpbox 允许来自 Internet 的 RDP 流量，但仅允许来自已知的安全 IP 地址的流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-211">The jumpbox allows RDP traffic from the Internet, but only from known, safe IP addresses.</span></span>

<span data-ttu-id="a9497-212">Jumpbox 的性能要求非常低，因此请选择一个较小的 VM 大小。</span><span class="sxs-lookup"><span data-stu-id="a9497-212">The jumpbox has minimal performance requirements, so select a small VM size.</span></span> <span data-ttu-id="a9497-213">为 jumpbox 创建一个[公共 IP 地址]。</span><span class="sxs-lookup"><span data-stu-id="a9497-213">Create a [public IP address] for the jumpbox.</span></span> <span data-ttu-id="a9497-214">将 jumpbox 放置在与其他 VM 相同的 VNet 中，但将其置于一个单独的管理子网中。</span><span class="sxs-lookup"><span data-stu-id="a9497-214">Place the jumpbox in the same VNet as the other VMs, but in a separate management subnet.</span></span>

<span data-ttu-id="a9497-215">若要确保 Jumpbox 的安全，请添加一项 NSG 规则，仅允许来自一组安全的公共 IP 地址的 RDP 连接。</span><span class="sxs-lookup"><span data-stu-id="a9497-215">To secure the jumpbox, add an NSG rule that allows RDP connections only from a safe set of public IP addresses.</span></span> <span data-ttu-id="a9497-216">为其他子网配置 NSG 以允许来自管理子网的 RDP 流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-216">Configure the NSGs for the other subnets to allow RDP traffic from the management subnet.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="a9497-217">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="a9497-217">Scalability considerations</span></span>

<span data-ttu-id="a9497-218">[VM 规模集][vmss]用于部署和管理一组相同的 VM。</span><span class="sxs-lookup"><span data-stu-id="a9497-218">[VM scale sets][vmss] help you to deploy and manage a set of identical VMs.</span></span> <span data-ttu-id="a9497-219">规模集支持基于性能指标自动缩放。</span><span class="sxs-lookup"><span data-stu-id="a9497-219">Scale sets support autoscaling based on performance metrics.</span></span> <span data-ttu-id="a9497-220">VM 上的负载增加时，会自动向负载均衡器添加更多 VM。</span><span class="sxs-lookup"><span data-stu-id="a9497-220">As the load on the VMs increases, additional VMs are automatically added to the load balancer.</span></span> <span data-ttu-id="a9497-221">如果需要快速横向扩展 VM，或者需要进行自动缩放，请考虑规模集。</span><span class="sxs-lookup"><span data-stu-id="a9497-221">Consider scale sets if you need to quickly scale out VMs, or need to autoscale.</span></span>

<span data-ttu-id="a9497-222">有两种基本方法可用来配置规模集中部署的 VM：</span><span class="sxs-lookup"><span data-stu-id="a9497-222">There are two basic ways to configure VMs deployed in a scale set:</span></span>

- <span data-ttu-id="a9497-223">在预配 VM 后使用扩展对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="a9497-223">Use extensions to configure the VM after it is provisioned.</span></span> <span data-ttu-id="a9497-224">使用此方法时，启动新 VM 实例的所需时间可能会长于启动不带扩展的 VM 的所需时间。</span><span class="sxs-lookup"><span data-stu-id="a9497-224">With this approach, new VM instances may take longer to start up than a VM with no extensions.</span></span>

- <span data-ttu-id="a9497-225">使用自定义磁盘映像部署[托管磁盘](/azure/storage/storage-managed-disks-overview)。</span><span class="sxs-lookup"><span data-stu-id="a9497-225">Deploy a [managed disk](/azure/storage/storage-managed-disks-overview) with a custom disk image.</span></span> <span data-ttu-id="a9497-226">此选项的部署速度可能更快。</span><span class="sxs-lookup"><span data-stu-id="a9497-226">This option may be quicker to deploy.</span></span> <span data-ttu-id="a9497-227">但是，它要求将映像保持最新。</span><span class="sxs-lookup"><span data-stu-id="a9497-227">However, it requires you to keep the image up to date.</span></span>

<span data-ttu-id="a9497-228">有关其他注意事项，请参阅[规模集的设计注意事项][vmss-design]。</span><span class="sxs-lookup"><span data-stu-id="a9497-228">For additional considerations, see [Design considerations for scale sets][vmss-design].</span></span>

> [!TIP]
> <span data-ttu-id="a9497-229">在使用任何自动缩放解决方案时，请早早提前使用生产级工作负荷测试它。</span><span class="sxs-lookup"><span data-stu-id="a9497-229">When using any autoscale solution, test it with production-level workloads well in advance.</span></span>

<span data-ttu-id="a9497-230">每个 Azure 订阅都有适用的默认限制，包括每个区域的最大 VM 数量。</span><span class="sxs-lookup"><span data-stu-id="a9497-230">Each Azure subscription has default limits in place, including a maximum number of VMs per region.</span></span> <span data-ttu-id="a9497-231">可以通过提出支持请求来提高上限。</span><span class="sxs-lookup"><span data-stu-id="a9497-231">You can increase the limit by filing a support request.</span></span> <span data-ttu-id="a9497-232">有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束][subscription-limits]。</span><span class="sxs-lookup"><span data-stu-id="a9497-232">For more information, see [Azure subscription and service limits, quotas, and constraints][subscription-limits].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="a9497-233">可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="a9497-233">Availability considerations</span></span>

<span data-ttu-id="a9497-234">如果不使用 VM 规模集，请将同一层中的 VM 置于一个可用性集中。</span><span class="sxs-lookup"><span data-stu-id="a9497-234">If you are not using VM scale sets, put VMs in the same tier into an availability set.</span></span> <span data-ttu-id="a9497-235">请在可用性集中至少创建两个 VM 以支持 [Azure VM 的可用性 SLA][vm-sla]。</span><span class="sxs-lookup"><span data-stu-id="a9497-235">Create at least two VMs in the availability set to support the [availability SLA for Azure VMs][vm-sla].</span></span> <span data-ttu-id="a9497-236">有关详细信息，请参阅[管理虚拟机的可用性][availability-set]。</span><span class="sxs-lookup"><span data-stu-id="a9497-236">For more information, see [Manage the availability of virtual machines][availability-set].</span></span> 

<span data-ttu-id="a9497-237">负载均衡器使用[运行状况探测][health-probes]监视 VM 实例的可用性。</span><span class="sxs-lookup"><span data-stu-id="a9497-237">The load balancer uses [health probes][health-probes] to monitor the availability of VM instances.</span></span> <span data-ttu-id="a9497-238">如果探测在超时期限内无法到达实例，负载均衡器会停止向该 VM 发送流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-238">If a probe cannot reach an instance within a timeout period, the load balancer stops sending traffic to that VM.</span></span> <span data-ttu-id="a9497-239">但是，负载均衡器将继续探测，并且如果 VM 再次变得可用，负载均衡器会继续向该 VM 发送流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-239">However, the load balancer will continue to probe, and if the VM becomes available again, the load balancer resumes sending traffic to that VM.</span></span>

<span data-ttu-id="a9497-240">下面是有关负载均衡器运行状况探测的一些建议：</span><span class="sxs-lookup"><span data-stu-id="a9497-240">Here are some recommendations on load balancer health probes:</span></span>

* <span data-ttu-id="a9497-241">探测可以测试 HTTP 或 TCP。</span><span class="sxs-lookup"><span data-stu-id="a9497-241">Probes can test either HTTP or TCP.</span></span> <span data-ttu-id="a9497-242">如果 VM 运行 HTTP 服务器，请创建 HTTP 探测。</span><span class="sxs-lookup"><span data-stu-id="a9497-242">If your VMs run an HTTP server, create an HTTP probe.</span></span> <span data-ttu-id="a9497-243">否则，请创建 TCP 探测。</span><span class="sxs-lookup"><span data-stu-id="a9497-243">Otherwise create a TCP probe.</span></span>
* <span data-ttu-id="a9497-244">对于 HTTP 探测，请指定指向 HTTP 终结点的路径。</span><span class="sxs-lookup"><span data-stu-id="a9497-244">For an HTTP probe, specify the path to an HTTP endpoint.</span></span> <span data-ttu-id="a9497-245">探测将检查是否有来自此路径的 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="a9497-245">The probe checks for an HTTP 200 response from this path.</span></span> <span data-ttu-id="a9497-246">这可以是根路径 ("/")，也可以是一个运行状况监视终结点，该终结点实现某些自定义逻辑来检查应用程序运行状况。</span><span class="sxs-lookup"><span data-stu-id="a9497-246">This can be the root path ("/"), or a health-monitoring endpoint that implements some custom logic to check the health of the application.</span></span> <span data-ttu-id="a9497-247">终结点必须允许匿名 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="a9497-247">The endpoint must allow anonymous HTTP requests.</span></span>
* <span data-ttu-id="a9497-248">探测发送自[已知 IP 地址][health-probe-ip] 168.63.129.16。</span><span class="sxs-lookup"><span data-stu-id="a9497-248">The probe is sent from a [known IP address][health-probe-ip], 168.63.129.16.</span></span> <span data-ttu-id="a9497-249">请确保任何防火墙策略或网络安全组 (NSG) 规则都未阻止与此 IP 地址之间的流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-249">Make sure you don't block traffic to or from this IP address in any firewall policies or network security group (NSG) rules.</span></span>
* <span data-ttu-id="a9497-250">使用[运行状况探测日志][health-probe-log]查看运行状况探测的状态。</span><span class="sxs-lookup"><span data-stu-id="a9497-250">Use [health probe logs][health-probe-log] to view the status of the health probes.</span></span> <span data-ttu-id="a9497-251">请在 Azure 门户中为每个负载均衡器启用日志记录。</span><span class="sxs-lookup"><span data-stu-id="a9497-251">Enable logging in the Azure portal for each load balancer.</span></span> <span data-ttu-id="a9497-252">日志将写入到 Azure Blob 存储。</span><span class="sxs-lookup"><span data-stu-id="a9497-252">Logs are written to Azure Blob storage.</span></span> <span data-ttu-id="a9497-253">这些日志显示后端有多少个 VM 由于失败的探测响应而未收到网络流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-253">The logs show how many VMs on the back end are not receiving network traffic due to failed probe responses.</span></span>

<span data-ttu-id="a9497-254">如果需要的可用性高于 [VM 的 Azure SLA][vm-sla] 提供的可用性，请考虑跨两个区域复制应用程序，使用 Azure 流量管理器进行故障转移。</span><span class="sxs-lookup"><span data-stu-id="a9497-254">If you need higher availability than the [Azure SLA for VMs][vm-sla] provides, consider replication the application across two regions, using Azure Traffic Manager for failover.</span></span> <span data-ttu-id="a9497-255">有关详细信息，请参阅[通过多区域 N 层应用程序确保高可用性][multi-dc]。</span><span class="sxs-lookup"><span data-stu-id="a9497-255">For more information, see [Multi-region N-tier application for high availability][multi-dc].</span></span>  

## <a name="security-considerations"></a><span data-ttu-id="a9497-256">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="a9497-256">Security considerations</span></span>

<span data-ttu-id="a9497-257">虚拟网络是 Azure 中的流量隔离边界。</span><span class="sxs-lookup"><span data-stu-id="a9497-257">Virtual networks are a traffic isolation boundary in Azure.</span></span> <span data-ttu-id="a9497-258">一个 VNet 中的 VM 无法直接与其他 VNet 中的 VM 通信。</span><span class="sxs-lookup"><span data-stu-id="a9497-258">VMs in one VNet cannot communicate directly with VMs in a different VNet.</span></span> <span data-ttu-id="a9497-259">同一个 VNet 中的 VM 之间可以通信，除非你创建[网络安全组][nsg] (NSG) 来限制流量。</span><span class="sxs-lookup"><span data-stu-id="a9497-259">VMs within the same VNet can communicate, unless you create [network security groups][nsg] (NSGs) to restrict traffic.</span></span> <span data-ttu-id="a9497-260">有关详细信息，请参阅 [Microsoft 云服务和网络安全性][network-security]。</span><span class="sxs-lookup"><span data-stu-id="a9497-260">For more information, see [Microsoft cloud services and network security][network-security].</span></span>

<span data-ttu-id="a9497-261">对于传入 Internet 流量，负载均衡器规则定义哪些流量可以到达后端。</span><span class="sxs-lookup"><span data-stu-id="a9497-261">For incoming Internet traffic, the load balancer rules define which traffic can reach the back end.</span></span> <span data-ttu-id="a9497-262">但是，负载均衡器规则不支持 IP 安全列表，因此如果要将某些公共 IP 地址添加到安全列表，请将 NSG 添加到子网。</span><span class="sxs-lookup"><span data-stu-id="a9497-262">However, load balancer rules don't support IP safe lists, so if you want to add certain public IP addresses to a safe list, add an NSG to the subnet.</span></span>

<span data-ttu-id="a9497-263">请考虑添加一个网络虚拟设备 (NVA) 以在 Internet 与 Azure 虚拟网络之间创建一个外围网络。</span><span class="sxs-lookup"><span data-stu-id="a9497-263">Consider adding a network virtual appliance (NVA) to create a DMZ between the Internet and the Azure virtual network.</span></span> <span data-ttu-id="a9497-264">NVA 是虚拟设备的一个通用术语，可以执行与网络相关的任务，例如防火墙、包检查、审核和自定义路由。</span><span class="sxs-lookup"><span data-stu-id="a9497-264">NVA is a generic term for a virtual appliance that can perform network-related tasks, such as firewall, packet inspection, auditing, and custom routing.</span></span> <span data-ttu-id="a9497-265">有关详细信息，请参阅[在 Azure 与 Internet 之间实现外围网络][dmz]。</span><span class="sxs-lookup"><span data-stu-id="a9497-265">For more information, see [Implementing a DMZ between Azure and the Internet][dmz].</span></span>

<span data-ttu-id="a9497-266">加密静态的敏感数据并使用 [Azure Key Vault][azure-key-vault] 管理数据库加密密钥。</span><span class="sxs-lookup"><span data-stu-id="a9497-266">Encrypt sensitive data at rest and use [Azure Key Vault][azure-key-vault] to manage the database encryption keys.</span></span> <span data-ttu-id="a9497-267">Key Vault 可以将加密密钥存储在硬件安全模块 (HSM) 中。</span><span class="sxs-lookup"><span data-stu-id="a9497-267">Key Vault can store encryption keys in hardware security modules (HSMs).</span></span> <span data-ttu-id="a9497-268">有关详细信息，请参阅[为 Azure VM 上的 SQL Server 配置 Azure Key Vault 集成][sql-keyvault]。</span><span class="sxs-lookup"><span data-stu-id="a9497-268">For more information, see [Configure Azure Key Vault Integration for SQL Server on Azure VMs][sql-keyvault].</span></span> <span data-ttu-id="a9497-269">另外，建议将应用程序机密（例如数据库连接字符串）也存储在 Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="a9497-269">It's also recommended to store application secrets, such as database connection strings, in Key Vault.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="a9497-270">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="a9497-270">Deploy the solution</span></span>

<span data-ttu-id="a9497-271">[GitHub][github-folder] 中提供了此参考体系结构的部署。</span><span class="sxs-lookup"><span data-stu-id="a9497-271">A deployment for this reference architecture is available on [GitHub][github-folder].</span></span> <span data-ttu-id="a9497-272">请注意，整个部署最长可能需要两小时的时间，包括运行相关脚本来配置 AD DS、Windows Server 故障转移群集以及 SQL Server 可用性组。</span><span class="sxs-lookup"><span data-stu-id="a9497-272">Note that the entire deployment can take up to two hours, which includes running the scripts to configure AD DS, the Windows Server failover cluster, and the SQL Server availability group.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="a9497-273">先决条件</span><span class="sxs-lookup"><span data-stu-id="a9497-273">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-solution"></a><span data-ttu-id="a9497-274">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="a9497-274">Deploy the solution</span></span>

1. <span data-ttu-id="a9497-275">运行以下命令来创建资源组。</span><span class="sxs-lookup"><span data-stu-id="a9497-275">Run the following command to create a resource group.</span></span>

    ```bash
    az group create --location <location> --name <resource-group-name>
    ```

2. <span data-ttu-id="a9497-276">运行以下命令来创建云见证的存储帐户。</span><span class="sxs-lookup"><span data-stu-id="a9497-276">Run the following command to create a Storage account for the Cloud Witness.</span></span>

    ```bash
    az storage account create --location <location> \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --sku Standard_LRS
    ```

3. <span data-ttu-id="a9497-277">导航到参考体系结构 GitHub 存储库的 `virtual-machines\n-tier-windows` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="a9497-277">Navigate to the `virtual-machines\n-tier-windows` folder of the reference architectures GitHub repository.</span></span>

4. <span data-ttu-id="a9497-278">打开 `n-tier-windows.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="a9497-278">Open the `n-tier-windows.json` file.</span></span> 

5. <span data-ttu-id="a9497-279">搜索“witnessStorageBlobEndPoint”的所有实例，将占位符文本替换为步骤 2 中的存储帐户的名称。</span><span class="sxs-lookup"><span data-stu-id="a9497-279">Search for all instances of "witnessStorageBlobEndPoint" and replace the placeholder text with the name of the Storage account from step 2.</span></span>

    ```json
    "witnessStorageBlobEndPoint": "https://[replace-with-storageaccountname].blob.core.windows.net",
    ```

6. <span data-ttu-id="a9497-280">运行以下命令，列出存储帐户的帐户密钥。</span><span class="sxs-lookup"><span data-stu-id="a9497-280">Run the following command to list the account keys for the storage account.</span></span>

    ```bash
    az storage account keys list \
      --account-name <storage-account-name> \
      --resource-group <resource-group-name>
    ```

    <span data-ttu-id="a9497-281">输出应如下所示。</span><span class="sxs-lookup"><span data-stu-id="a9497-281">The output should look like the following.</span></span> <span data-ttu-id="a9497-282">复制 `key1` 的值。</span><span class="sxs-lookup"><span data-stu-id="a9497-282">Copy the value of `key1`.</span></span>

    ```json
    [
    {
        "keyName": "key1",
        "permissions": "Full",
        "value": "..."
    },
    {
        "keyName": "key2",
        "permissions": "Full",
        "value": "..."
    }
    ]
    ```

7. <span data-ttu-id="a9497-283">在 `n-tier-windows.json` 文件中，搜索“witnessStorageAccountKey”的所有实例，然后将帐户密钥粘贴进去。</span><span class="sxs-lookup"><span data-stu-id="a9497-283">In the `n-tier-windows.json` file, search for all instances of "witnessStorageAccountKey" and paste in the account key.</span></span>

    ```json
    "witnessStorageAccountKey": "[replace-with-storagekey]"
    ```

8. <span data-ttu-id="a9497-284">在 `n-tier-windows.json` 文件中，搜索 `testPassw0rd!23`、`test$!Passw0rd111`、`AweS0me@SQLServicePW` 的所有实例。</span><span class="sxs-lookup"><span data-stu-id="a9497-284">In the `n-tier-windows.json` file, search for all instances `testPassw0rd!23`, `test$!Passw0rd111`, and `AweS0me@SQLServicePW`.</span></span> <span data-ttu-id="a9497-285">将其替换为你自己的密码，然后保存文件。</span><span class="sxs-lookup"><span data-stu-id="a9497-285">Replace them with your own passwords and save the file.</span></span>

    > [!NOTE]
    > <span data-ttu-id="a9497-286">如果更改管理员用户名，则还必须更新 JSON 文件中的 `extensions` 块。</span><span class="sxs-lookup"><span data-stu-id="a9497-286">If you change the adminstrator user name, you must also update the `extensions` blocks in the JSON file.</span></span> 

9. <span data-ttu-id="a9497-287">运行以下命令来部署体系结构。</span><span class="sxs-lookup"><span data-stu-id="a9497-287">Run the following command to deploy the architecture.</span></span>

    ```bash
    azbb -s <your subscription_id> -g <resource_group_name> -l <location> -p n-tier-windows.json --deploy
    ```

<span data-ttu-id="a9497-288">若要详细了解如何使用 Azure 构建基块部署此示例参考体系结构，请访问 [GitHub 存储库][git]。</span><span class="sxs-lookup"><span data-stu-id="a9497-288">For more information on deploying this sample reference architecture using Azure Building Blocks, visit the [GitHub repository][git].</span></span>


<!-- links -->
[dmz]: ../dmz/secure-vnet-dmz.md
[multi-dc]: multi-region-sql-server.md
[n-tier]: n-tier.md
[azure-administration]: /azure/automation/automation-intro
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-windows-manage-availability#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-dns]: /azure/dns/dns-overview
[azure-key-vault]: https://azure.microsoft.com/services/key-vault
[守护主机]: https://en.wikipedia.org/wiki/Bastion_host
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[cidr]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[git]: https://github.com/mspnp/template-building-blocks
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/n-tier-windows
[lb-external-create]: /azure/load-balancer/load-balancer-get-started-internet-portal
[lb-internal-create]: /azure/load-balancer/load-balancer-get-started-ilb-arm-portal
[load-balancer-external]: /azure/load-balancer/load-balancer-internet-overview
[load-balancer-internal]: /azure/load-balancer/load-balancer-internal-overview
[nsg]: /azure/virtual-network/virtual-networks-nsg
[operations-management-suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[plan-network]: /azure/virtual-network/virtual-network-vnet-plan-design-arm
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[公共 IP 地址]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[public IP address]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[sql-alwayson]: https://msdn.microsoft.com/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/library/gg509118.aspx
[sql-alwayson-ilb]: /azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener
[sql-alwayson-listeners]: https://msdn.microsoft.com/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: /azure/virtual-machines/virtual-machines-windows-ps-sql-keyvault
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[vnet faq]: /azure/virtual-network/virtual-networks-faq
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[0]: ./images/n-tier-sql-server.png "使用 Microsoft Azure 的 N 层体系结构"
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview 
[vmss]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[load-balancer]: /azure/load-balancer/load-balancer-get-started-internet-arm-cli
[load-balancer-hashing]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[vmss-design]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview
[subscription-limits]: /azure/azure-subscription-service-limits
[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[health-probes]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[health-probe-log]: /azure/load-balancer/load-balancer-monitor-log
[health-probe-ip]: /azure/virtual-network/virtual-networks-nsg#special-rules
[network-security]: /azure/best-practices-network-security
