---
title: 运行 Azure SAP HANA 大型实例
titleSuffix: Azure Reference Architectures
description: 有关在 Azure 大型实例上的高可用性环境中运行 SAP HANA 的成熟做法。
author: lbrader
ms.date: 05/16/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, SAP
ms.openlocfilehash: 711e233de534597f9cd06ccaa95481a51acc4468
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485329"
---
# <a name="run-sap-hana-on-azure-large-instances"></a><span data-ttu-id="ab3cf-103">运行 Azure SAP HANA 大型实例</span><span class="sxs-lookup"><span data-stu-id="ab3cf-103">Run SAP HANA on Azure Large Instances</span></span>

<span data-ttu-id="ab3cf-104">此参考体系结构演示有关运行 Azure 上的 SAP HANA（大型实例）和实现高可用性与灾难恢复 (DR) 的一套成熟做法。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-104">This reference architecture shows a set of proven practices for running SAP HANA on Azure (Large Instances) with high availability and disaster recovery (DR).</span></span> <span data-ttu-id="ab3cf-105">此产品/服务称作 HANA 大型实例，部署在 Azure 区域中的物理服务器上。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-105">Called HANA Large Instances, this offering is deployed on physical servers in Azure regions.</span></span>

![使用 Azure 大型实例的 SAP HANA 体系结构](./images/sap-hana-large-instances.png)

<span data-ttu-id="ab3cf-107">下载此体系结构的 [Visio 文件][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

> [!NOTE]
> <span data-ttu-id="ab3cf-108">部署此参考体系结构需要获取 SAP 产品和其他非 Microsoft 技术的相应许可。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-108">Deploying this reference architecture requires appropriate licensing of SAP products and other non-Microsoft technologies.</span></span>

## <a name="architecture"></a><span data-ttu-id="ab3cf-109">体系结构</span><span class="sxs-lookup"><span data-stu-id="ab3cf-109">Architecture</span></span>

<span data-ttu-id="ab3cf-110">此体系结构包括以下基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-110">This architecture consists of the following infrastructure components.</span></span>

- <span data-ttu-id="ab3cf-111">虚拟网络。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-111">**Virtual network**.</span></span> <span data-ttu-id="ab3cf-112">[Azure 虚拟网络][vnet]服务在不同的 Azure 资源之间建立安全连接，并针对每个层划分为单独的[子网][subnet]。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-112">The [Azure Virtual Network][vnet] service securely connects Azure resources to each other and is subdivided into separate [subnets][subnet] for each layer.</span></span> <span data-ttu-id="ab3cf-113">SAP 应用程序层部署在 Azure 虚拟机 (VM) 上，以连接到大型实例上的 HANA 数据库层。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-113">SAP application layers are deployed on Azure virual machines (VMs) to connect to the HANA database layer residing on large instances.</span></span>

- <span data-ttu-id="ab3cf-114">**虚拟机**。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-114">**Virtual machines**.</span></span> <span data-ttu-id="ab3cf-115">虚拟机在 SAP 应用程序层和共享服务层中使用。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-115">Virtual machines are used in the SAP application layer and shared services layer.</span></span> <span data-ttu-id="ab3cf-116">后者包括一个 Jumpbox，让管理员设置 HANA 大型实例以及提供对其他虚拟机的访问。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-116">The latter includes a jumpbox used by administrators to set up HANA Large Instances and to provide access to other virtual machines.</span></span>

- <span data-ttu-id="ab3cf-117">**HANA 大型实例**。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-117">**HANA Large Instance**.</span></span> <span data-ttu-id="ab3cf-118">一台经认证符合 SAP HANA 定制数据中心集成 (TDI) 标准的[物理服务器][physical]运行 SAP HANA。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-118">A [physical server][physical] certified to meet SAP HANA Tailored Datacenter Integration (TDI) standards runs SAP HANA.</span></span> <span data-ttu-id="ab3cf-119">此体系结构使用两个 HANA 大型实例：主要计算单位和次要计算单位。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-119">This architecture uses two HANA Large Instances: a primary and a secondary compute unit.</span></span> <span data-ttu-id="ab3cf-120">通过 HANA 系统复制 (HSR) 提供数据层的高可用性。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-120">High availability at the data layer is provided through HANA System Replication (HSR).</span></span>

- <span data-ttu-id="ab3cf-121">**高可用性对**。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-121">**High Availability Pair**.</span></span> <span data-ttu-id="ab3cf-122">统一管理一组 HANA 大型实例刀片服务器，以提供应用程序冗余和可靠性。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-122">A group of HANA Large Instances blades are managed together to provide application redundancy and reliability.</span></span>

- <span data-ttu-id="ab3cf-123">**MSEE (Microsoft Enterprise Edge)**。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-123">**MSEE (Microsoft Enterprise Edge)**.</span></span> <span data-ttu-id="ab3cf-124">MSEE 是连接提供商或网络边缘中通过 ExpressRoute 线路连接到的连接点。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-124">MSEE is a connection point from a connectivity provider or your network edge through an ExpressRoute circuit.</span></span>

- <span data-ttu-id="ab3cf-125">**网络接口卡 (NIC)**。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-125">**Network interface cards (NICs)**.</span></span> <span data-ttu-id="ab3cf-126">为了实现通信，HANA 大型实例服务器默认提供四个虚拟 NIC。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-126">To enable communication, the HANA Large Instance server provides four virtual NICs by default.</span></span> <span data-ttu-id="ab3cf-127">此体系结构需要将一个 NIC 用于客户端通信，将第二个 NIC 用于 HSR 所需的节点间连接，将第三个 NIC 用于 HANA 大型实例存储，将第四个 NIC 用于高可用性群集中使用的 iSCSI。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-127">This architecture requires one NIC for client communication, a second NIC for the node-to-node connectivity needed by HSR, a third NIC for HANA Large Instance storage, and a fourth for iSCSI used in high availability clustering.</span></span>

- <span data-ttu-id="ab3cf-128">**网络文件系统 (NFS) 存储**。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-128">**Network File System (NFS) storage**.</span></span> <span data-ttu-id="ab3cf-129">[NFS][nfs] 服务器支持网络文件共享。网络文件共享为 HANA 大型实例提供安全的数据保留。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-129">The [NFS][nfs] server supports the network file share that provides secure data persistence for HANA Large Instance.</span></span>

- <span data-ttu-id="ab3cf-130">**ExpressRoute**。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-130">**ExpressRoute**.</span></span> <span data-ttu-id="ab3cf-131">若要在本地网络与 Azure 虚拟网络之间创建不经由公共 Internet 的专用连接，我们建议使用 [ExpressRoute][expressroute] Azure 网络服务。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-131">[ExpressRoute][expressroute] is the recommended Azure networking service for creating private connections between an on-premises network and Azure virtual networks that do not go over the public Internet.</span></span> <span data-ttu-id="ab3cf-132">Azure VM 使用另一个 ExpressRoute 连接来与 HANA 大型实例建立连接。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-132">Azure VMs connect to HANA Large Instances using another ExpressRoute connection.</span></span> <span data-ttu-id="ab3cf-133">Azure 虚拟网络与 HANA 大型实例之间的 ExpressRoute 连接设置为 Microsoft 产品/服务的一部分。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-133">The ExpressRoute connection between the Azure virtual network and the HANA Large Instances is set up as part of the Microsoft offering.</span></span>

- <span data-ttu-id="ab3cf-134">**网关**。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-134">**Gateway**.</span></span> <span data-ttu-id="ab3cf-135">ExpressRoute 网关用于将 SAP 应用层所用的 Azure 虚拟网络连接到 HANA 大型实例网络。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-135">The ExpressRoute Gateway is used to connect the Azure virtual network used for the SAP application layer to the HANA Large Instance network.</span></span> <span data-ttu-id="ab3cf-136">使用[高性能或超高性能][sku] SKU。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-136">Use the [High Performance or Ultra Performance][sku] SKU.</span></span>

- <span data-ttu-id="ab3cf-137">**灾难恢复 (DR)**。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-137">**Disaster recovery (DR)**.</span></span> <span data-ttu-id="ab3cf-138">可根据请求提供存储复制支持。存储复制配置为从主要站点复制到另一区域中的 [DR 站点][DR-site]。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-138">Upon request, storage replication is supported and will be configured from the primary to the [DR site][DR-site] located in another region.</span></span>

## <a name="recommendations"></a><span data-ttu-id="ab3cf-139">建议</span><span class="sxs-lookup"><span data-stu-id="ab3cf-139">Recommendations</span></span>

<span data-ttu-id="ab3cf-140">要求可能有所不同。请使用以下建议作为入手点。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-140">Requirements can vary, so use these recommendations as a starting point.</span></span>

### <a name="hana-large-instances-compute"></a><span data-ttu-id="ab3cf-141">HANA 大型实例计算</span><span class="sxs-lookup"><span data-stu-id="ab3cf-141">HANA Large Instances compute</span></span>

<span data-ttu-id="ab3cf-142">[大型实例][physical]是基于 Intel EX E7 CPU 体系结构的物理服务器，并在大型实例堆栈（即，特定的一组服务器或刀片服务器）中配置。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-142">[Large Instances][physical] are physical servers based on the Intel EX E7 CPU architecture and configured in a large instance stamp—that is, a specific set of servers or blades.</span></span> <span data-ttu-id="ab3cf-143">一个计算单位等于一台服务器或刀片服务器，一个堆栈由多台服务器或刀片服务器构成。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-143">A compute unit equals one server or blade, and a stamp is made up of multiple servers or blades.</span></span> <span data-ttu-id="ab3cf-144">在大型实例堆栈中，服务器不会共享，而是专门用于运行一个客户的 SAP HANA 部署。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-144">Within a large instance stamp, servers are not shared and are dedicated to running one customer’s deployment of SAP HANA.</span></span>

<span data-ttu-id="ab3cf-145">HANA 大型实例有各种可用的 SKU，S/4HANA 或其他 SAP HANA 工作负荷最多支持 20 TB 的单一内存实例（可扩展到 60 TB）。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-145">A variety of SKUs are available for HANA Large Instances, supporting up to 20 TB single instance (60 TB scale-out) of memory for S/4HANA or other SAP HANA workloads.</span></span> <span data-ttu-id="ab3cf-146">提供[两类][classes]服务器：</span><span class="sxs-lookup"><span data-stu-id="ab3cf-146">[Two classes][classes] of servers are offered:</span></span>

- <span data-ttu-id="ab3cf-147">I 类：S72、S72m、S144、S144m、S192 和 S192m</span><span class="sxs-lookup"><span data-stu-id="ab3cf-147">Type I class: S72, S72m, S144, S144m, S192, and S192m</span></span>

- <span data-ttu-id="ab3cf-148">II 类：S384、S384m、S384xm、S576m、S768m 和 S960m</span><span class="sxs-lookup"><span data-stu-id="ab3cf-148">Type II class: S384, S384m, S384xm, S576m, S768m, and S960m</span></span>

<span data-ttu-id="ab3cf-149">例如，S72 SKU 附带 768 GB RAM，3 TB 存储，以及 2 个 36 核 Intel Xeon 处理器 (E7-8890 v3)。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-149">For example, the S72 SKU comes with 768 GB RAM, 3 terabytes (TB) of storage, and 2 Intel Xeon processors (E7-8890 v3) with 36 cores.</span></span> <span data-ttu-id="ab3cf-150">请选择满足体系结构和设计会议中所决定的大小要求的 SKU。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-150">Choose a SKU that fulfills the sizing requirements you determined in your architecture and design sessions.</span></span> <span data-ttu-id="ab3cf-151">请始终确保大小与正确的 SKU 相搭配。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-151">Always ensure that your sizing applies to the correct SKU.</span></span> <span data-ttu-id="ab3cf-152">功能和部署要求[根据类型而异][type]，可用性根据[区域][region]而异。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-152">Capabilities and deployment requirements [vary by type][type], and availability varies by [region][region].</span></span> <span data-ttu-id="ab3cf-153">还可以从一个 SKU 升级到更大的 SKU。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-153">You can also step up from one SKU to a larger SKU.</span></span>

<span data-ttu-id="ab3cf-154">Microsoft 可帮助建立大型实例设置，但你要负责验证操作系统的配置设置。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-154">Microsoft helps establish the large instance setup, but it is your responsibility to verify the operating system’s configuration settings.</span></span> <span data-ttu-id="ab3cf-155">请务必查看确切 Linux 版本的最新 SAP 说明。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-155">Make sure to review the most current SAP Notes for your exact Linux release.</span></span>

### <a name="storage"></a><span data-ttu-id="ab3cf-156">存储</span><span class="sxs-lookup"><span data-stu-id="ab3cf-156">Storage</span></span>

<span data-ttu-id="ab3cf-157">根据 SAP HANA TDI 的建议实施存储布局。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-157">Storage layout is implemented according to the recommendation of the TDI for SAP HANA.</span></span> <span data-ttu-id="ab3cf-158">HANA 大型实例根据标准的 TDI 规范随附特定的存储配置。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-158">HANA Large Instances come with a specific storage configuration for the standard TDI specifications.</span></span> <span data-ttu-id="ab3cf-159">但是，可以 1 TB 为增量购买更多的存储。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-159">However, you can purchase additional storage in 1 TB increments.</span></span>

<span data-ttu-id="ab3cf-160">为了支持任务关键型环境的要求（包括快速恢复），将使用 NFS 而不是直接附加存储。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-160">To support the requirements of mission-critical environments including fast recovery, NFS is used and not direct attached storage.</span></span> <span data-ttu-id="ab3cf-161">HANA 大型实例的 NFS 存储服务器托管在多租户环境中，其中的租户已通过计算、网络和存储隔离技术进行分离和保护。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-161">The NFS storage server for HANA Large Instances is hosted in a multi-tenant environment, where tenants are segregated and secured using compute, network, and storage isolation.</span></span>

<span data-ttu-id="ab3cf-162">若要支持主站点的高可用性，请使用不同的存储布局。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-162">To support high availability at the primary site, use different storage layouts.</span></span> <span data-ttu-id="ab3cf-163">例如，在多主机横向扩展部署中，存储是共享的。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-163">For example, in a multi-host scale-out, the storage is shared.</span></span> <span data-ttu-id="ab3cf-164">另一种高可用性做法是使用基于应用程序的复制，例如 HSR。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-164">Another high availability option is application-based replication such as HSR.</span></span> <span data-ttu-id="ab3cf-165">但是，对于灾难恢复，应使用基于快照的存储复制。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-165">For DR, however, a snapshot-based storage replication is used.</span></span>

### <a name="networking"></a><span data-ttu-id="ab3cf-166">网络</span><span class="sxs-lookup"><span data-stu-id="ab3cf-166">Networking</span></span>

<span data-ttu-id="ab3cf-167">此体系结构使用虚拟网络和物理网络。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-167">This architecture uses both virtual and physical networks.</span></span> <span data-ttu-id="ab3cf-168">虚拟网络是 Azure IaaS 的一部分，通过 [ExpressRoute][expressroute]] 线路连接到离散的 HANA 大型实例物理网络。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-168">The virtual network is part of Azure IaaS and connects to a discrete HANA Large Instances physical network through [ExpressRoute][expressroute]] circuits.</span></span> <span data-ttu-id="ab3cf-169">跨界网关将 Azure 虚拟网络中的工作负荷连接到本地站点。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-169">A cross-premises gateway connects your workloads in the Azure virtual network to your on-premises sites.</span></span>

<span data-ttu-id="ab3cf-170">出于安全考虑，HANA 大型实例网络相互隔离。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-170">HANA Large Instances networks are isolated from each other for security.</span></span> <span data-ttu-id="ab3cf-171">位于不同区域的实例不会相互通信，专用存储复制除外。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-171">Instances residing in different regions do not communicate with each other, except for the dedicated storage replication.</span></span> <span data-ttu-id="ab3cf-172">但是，若要使用 HSR，必须进行区域间的通信。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-172">However, to use HSR, inter-region communications are required.</span></span> <span data-ttu-id="ab3cf-173">[IP 路由表][ip]或代理可用于实现跨区域 HSR。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-173">[IP routing tables][ip] or proxies can be used to enable cross-regions HSR.</span></span>

<span data-ttu-id="ab3cf-174">连接到一个区域中的 HANA 大型实例的所有 Azure 虚拟网络可以通过 ExpressRoute [跨界连接][cross-connected]到次要区域中的 HANA 大型实例。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-174">All Azure virtual networks that connect to HANA Large Instances in one region can be [cross-connected][cross-connected] via ExpressRoute to HANA Large Instances in a secondary region.</span></span>

<span data-ttu-id="ab3cf-175">在预配期间，默认会包含 HANA 大型实例的 ExpressRoute。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-175">ExpressRoute for HANA Large Instances is included by default during provisioning.</span></span> <span data-ttu-id="ab3cf-176">在设置期间，需要特定的网络布局，包括所需的 CIDR 地址范围和域路由。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-176">For setup, a specific network layout is needed, including required CIDR address ranges and domain routing.</span></span> <span data-ttu-id="ab3cf-177">有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的基础结构和连接][HLI-infrastructure]。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-177">For details, see [SAP HANA (large instances) infrastructure and connectivity on Azure][HLI-infrastructure].</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="ab3cf-178">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="ab3cf-178">Scalability considerations</span></span>

<span data-ttu-id="ab3cf-179">若要纵向扩展或缩减，可以从适用于 HANA 大型实例的多种服务器大小中进行选择。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-179">To scale up or down, you can choose from many sizes of servers that are available for HANA Large Instances.</span></span> <span data-ttu-id="ab3cf-180">这些大小分类为 [I 型和 II 型][classes]，是针对不同工作负荷定制的。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-180">They are categorized as [Type I and Type II][classes] and tailored for different workloads.</span></span> <span data-ttu-id="ab3cf-181">请选择在未来三年能够适应工作负荷增长的大小。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-181">Choose a size that can grow with your workload for the next three years.</span></span> <span data-ttu-id="ab3cf-182">我们还提供满足一年需求的承诺。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-182">One-year commitments are also available.</span></span>

<span data-ttu-id="ab3cf-183">作为一种数据库分区策略，通常会将多主机横向扩展部署用作 BW/4HANA 部署。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-183">A multi-host scale-out deployment is generally used for BW/4HANA deployments as a kind of database partitioning strategy.</span></span> <span data-ttu-id="ab3cf-184">若要横向扩展，请在安装前规划 HANA 表的位置。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-184">To scale out, plan the placement of HANA tables prior to installation.</span></span> <span data-ttu-id="ab3cf-185">从基础结构的角度看，多个主机会连接到共享的存储卷，以便在 HANA 系统中的某个计算工作节点发生故障时，后备主机能够快速接管工作。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-185">From an infrastructure standpoint, multiple hosts are connected to a shared storage volume, enabling quick takeover by standby hosts in case one of the compute worker nodes in the HANA system fails.</span></span>

<span data-ttu-id="ab3cf-186">使用单个“HANA 大型实例”实例，就能将一台刀片服务器中的 S/4HANA 和 SAP Business Suite on HANA 纵向扩展到 20 TB。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-186">S/4HANA and SAP Business Suite on HANA on a single blade can be scaled up to 20 TB with a single HANA Large Instances instance.</span></span>

<span data-ttu-id="ab3cf-187">针对节约型的方案，可以使用 [SAP Quick Sizer][quick-sizer] 来计算在 HANA 的基础上实施 SAP 软件时的内存要求。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-187">For greenfield scenarios, the [SAP Quick Sizer][quick-sizer] is available to calculate memory requirements of the implementation of SAP software on top of HANA.</span></span> <span data-ttu-id="ab3cf-188">HANA 的内存需求将随数据量增长而增加。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-188">Memory requirements for HANA increase as data volume grows.</span></span> <span data-ttu-id="ab3cf-189">基于系统的当前内存消耗量预测未来的消耗量，然后将需求对应到某个 HANA 大型实例大小。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-189">Use your system’s current memory consumption as the basis for predicting future consumption, and then map your demand into one of the HANA Large Instances sizes.</span></span>

<span data-ttu-id="ab3cf-190">对于现有的 SAP 部署，SAP 会提供报告，用于检查现有系统使用的数据并计算 HANA 实例的内存要求。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-190">If you already have SAP deployments, SAP provides reports you can use to check the data used by existing systems and calculate memory requirements for a HANA instance.</span></span> <span data-ttu-id="ab3cf-191">有关示例，请参阅以下 SAP 说明：</span><span class="sxs-lookup"><span data-stu-id="ab3cf-191">For example, see the following SAP Notes:</span></span>

- <span data-ttu-id="ab3cf-192">SAP 说明 [1793345][sap-1793345] - SAP Suite on HANA 的大小</span><span class="sxs-lookup"><span data-stu-id="ab3cf-192">SAP Note [1793345][sap-1793345] - Sizing for SAP Suite on HANA</span></span>
- <span data-ttu-id="ab3cf-193">SAP 说明 [1872170][sap-1872170] - Suite on HANA 和 S/4 HANA 大小报告</span><span class="sxs-lookup"><span data-stu-id="ab3cf-193">SAP Note [1872170][sap-1872170] - Suite on HANA and S/4 HANA sizing report</span></span>
- <span data-ttu-id="ab3cf-194">SAP 说明 [2121330][sap-2121330] - 常见问题解答：SAP BW on HANA 大小报告</span><span class="sxs-lookup"><span data-stu-id="ab3cf-194">SAP Note [2121330][sap-2121330] - FAQ: SAP BW on HANA Sizing Report</span></span>
- <span data-ttu-id="ab3cf-195">SAP 说明 [1736976][sap-1736976] - BW on HANA 的大小报告</span><span class="sxs-lookup"><span data-stu-id="ab3cf-195">SAP Note [1736976][sap-1736976] - Sizing Report for BW on HANA</span></span>
- <span data-ttu-id="ab3cf-196">SAP 说明 [2296290][sap-2296290] - BW on HANA 的新大小报告</span><span class="sxs-lookup"><span data-stu-id="ab3cf-196">SAP Note [2296290][sap-2296290] - New Sizing Report for BW on HANA</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="ab3cf-197">可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="ab3cf-197">Availability considerations</span></span>

<span data-ttu-id="ab3cf-198">资源冗余是高可用性基础结构解决方案中的常见话题。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-198">Resource redundancy is the general theme in highly available infrastructure solutions.</span></span> <span data-ttu-id="ab3cf-199">对于 SLA 不太苛刻的组织而言，单一实例 Azure VM 可以保障运行时间 SLA。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-199">For enterprises that have a less stringent SLA, single-instance Azure VMs offer an uptime SLA.</span></span> <span data-ttu-id="ab3cf-200">有关详细信息，请参阅 [Azure 服务级别协议](https://azure.microsoft.com/support/legal/sla/)。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-200">For more information, see [Azure Service Level Agreement](https://azure.microsoft.com/support/legal/sla/).</span></span>

<span data-ttu-id="ab3cf-201">咨询 SAP、系统集成商和/或 Microsoft，以便正确构建和实施[高可用性和灾难恢复][hli-hadr]策略。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-201">Work with SAP, your system integrator, or Microsoft to properly architect and implement a [high availability and disaster-recovery][hli-hadr] strategy.</span></span> <span data-ttu-id="ab3cf-202">此体系结构遵循 Azure 上的 HANA（大型实例）的[服务级别协议][sla] (SLA)。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-202">This architecture follows the Azure [service-level agreement][sla] (SLA) for HANA on Azure (Large Instances).</span></span> <span data-ttu-id="ab3cf-203">若要评估可用性要求，请考虑任何单一故障点、所需的服务运行时间级别，以及以下常见指标：</span><span class="sxs-lookup"><span data-stu-id="ab3cf-203">To assess your availability requirements, consider any single points of failure, the desired level of uptime for services, and these common metrics:</span></span>

- <span data-ttu-id="ab3cf-204">恢复时间目标 (RTO) 表示 HANA 大型实例服务器处于不可用状态的持续时间。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-204">Recovery Time Objective (RTO) means the duration of time in which the HANA Large Instances server is unavailable.</span></span>

- <span data-ttu-id="ab3cf-205">恢复点目标 (RPO) 表示故障导致客户数据丢失的最长可容许期限。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-205">Recovery Point Objective (RPO) means the maximum tolerable period in which customer data might be lost due to a failure.</span></span>

<span data-ttu-id="ab3cf-206">若要实现高可用性，请在高可用性对中部署多个实例，并在同步模式下使用 HSR，以尽量减少数据丢失和停机时间。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-206">For high availability, deploy more than one instance in a HA Pair and use HSR in a synchronous mode to minimize data loss and downtime.</span></span> <span data-ttu-id="ab3cf-207">除了本地的双节点高可用性设置以外，HSR 还支持多层复制，其中，单独的 Azure 区域中的第三个节点会注册到 HSR 群集对的辅助副本（复制目标）。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-207">In addition to a local, two-node high availability setup, HSR supports multi-tier replication, where a third node in a separate Azure region registers to the secondary replica of the clustered HSR pair as its replication target.</span></span> <span data-ttu-id="ab3cf-208">这就构成了复制菊花链。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-208">This forms a replication daisy chain.</span></span> <span data-ttu-id="ab3cf-209">故障转移到 DR 节点是一个手动过程。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-209">The failover to the DR node is a manual process.</span></span>

<span data-ttu-id="ab3cf-210">如果使用自动故障转移设置了 HANA 大型实例 HSR，则可以请求 Microsoft 服务管理团队为现有的服务器设置 [STONITH 设备][stonith]。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-210">When you set up HANA Large Instances HSR with automatic failover, you can request the Microsoft Service Management team to set up a [STONITH device][stonith] for your existing servers.</span></span>

## <a name="disaster-recovery-considerations"></a><span data-ttu-id="ab3cf-211">灾难恢复注意事项</span><span class="sxs-lookup"><span data-stu-id="ab3cf-211">Disaster recovery considerations</span></span>

<span data-ttu-id="ab3cf-212">此体系结构支持不同 Azure 区域中 HANA 大型实例之间的[灾难恢复][hli-dr]。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-212">This architecture supports [disaster recovery][hli-dr] between HANA Large Instances in different Azure regions.</span></span> <span data-ttu-id="ab3cf-213">可以在 HANA 大型实例上使用两种方法来支持 DR：</span><span class="sxs-lookup"><span data-stu-id="ab3cf-213">There are two ways to support DR with HANA Large Instances:</span></span>

- <span data-ttu-id="ab3cf-214">存储复制。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-214">Storage replication.</span></span> <span data-ttu-id="ab3cf-215">主存储内容会不断复制到指定的 DR HANA 大型实例服务器上提供的远程 DR 存储系统。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-215">The primary storage contents are constantly replicated to the remote DR storage systems that are available on the designated DR HANA Large Instances server.</span></span> <span data-ttu-id="ab3cf-216">在存储复制中，HANA 数据库不会加载到内存中。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-216">In storage replication, the HANA database is not loaded into memory.</span></span> <span data-ttu-id="ab3cf-217">从管理角度看，此 DR 选项更简单。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-217">This DR option is simpler from an administration perspective.</span></span> <span data-ttu-id="ab3cf-218">若要确定此策略是否合适，请根据可用性 SLA 考虑数据库加载时间。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-218">To determine if this is a suitable strategy, consider the database load time against the availability SLA.</span></span> <span data-ttu-id="ab3cf-219">使用存储复制还能执行时间点恢复。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-219">Storage replication also enables you to perform point-in-time recovery.</span></span> <span data-ttu-id="ab3cf-220">如果设置了多用途（成本优化）DR，则必须在 DR 位置购买相同大小的额外存储。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-220">If multi-purpose (cost-optimized) DR is set up, you must purchase additional storage of the same size at the DR location.</span></span> <span data-ttu-id="ab3cf-221">Microsoft 通过 HANA 大型实例产品/服务为 HANA 故障转移提供自助服务[存储快照和故障转移脚本][scripts]。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-221">Microsoft provides self-services [storage snapshot and failover scripts][scripts] for HANA failover as part of the HANA Large Instances offering.</span></span>

- <span data-ttu-id="ab3cf-222">DR 区域中包含第三个副本的多层 HSR（其中的 HANA 数据库会加载到内存中）。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-222">Multi-tier HSR with a third replica in the DR region (where the HANA database is loaded onto memory).</span></span> <span data-ttu-id="ab3cf-223">此选项支持更快的恢复时间，但不支持时间点恢复。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-223">This option supports a faster recovery time but does not support a point-in-time recovery.</span></span> <span data-ttu-id="ab3cf-224">HSR 需要辅助系统。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-224">HSR requires a secondary system.</span></span> <span data-ttu-id="ab3cf-225">DR 站点的 HANA 系统复制通过 nginx 等代理或 IP 表进行处理。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-225">HANA system replication for the DR site is handled through proxies such as nginx or IP tables.</span></span>

> [!NOTE]
> <span data-ttu-id="ab3cf-226">可以在单一实例环境中运行此参考体系结构，以优化其成本。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-226">You can optimize this reference architecture for costs by running in a single-instance environment.</span></span> <span data-ttu-id="ab3cf-227">此[成本优化方案](https://blogs.sap.com/2016/07/19/new-whitepaper-for-high-availability-for-sap-hana-cost-optimized-scenario/)适合用于非生产 HANA 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-227">This [cost-optimized scenario](https://blogs.sap.com/2016/07/19/new-whitepaper-for-high-availability-for-sap-hana-cost-optimized-scenario/) is suitable for non-production HANA workloads.</span></span>

## <a name="backup-considerations"></a><span data-ttu-id="ab3cf-228">备份注意事项</span><span class="sxs-lookup"><span data-stu-id="ab3cf-228">Backup considerations</span></span>

<span data-ttu-id="ab3cf-229">根据业务要求，从[备份和恢复][hli-backup]的多个可用选项中做出选择。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-229">Based on your business requirements, choose from several options available for [backup and recovery][hli-backup].</span></span>

| <span data-ttu-id="ab3cf-230">备份选项</span><span class="sxs-lookup"><span data-stu-id="ab3cf-230">Backup option</span></span>                   | <span data-ttu-id="ab3cf-231">优点</span><span class="sxs-lookup"><span data-stu-id="ab3cf-231">Pros</span></span>                                                                                                   | <span data-ttu-id="ab3cf-232">缺点</span><span class="sxs-lookup"><span data-stu-id="ab3cf-232">Cons</span></span>                                                       |
|---------------------------------|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| <span data-ttu-id="ab3cf-233">HANA 备份</span><span class="sxs-lookup"><span data-stu-id="ab3cf-233">HANA backup</span></span>        | <span data-ttu-id="ab3cf-234">本机到 SAP。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-234">Native to SAP.</span></span> <span data-ttu-id="ab3cf-235">内置的一致性检查。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-235">Built-in consistency check.</span></span>                                                             | <span data-ttu-id="ab3cf-236">备份和恢复时间较长。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-236">Long backup and recovery times.</span></span> <span data-ttu-id="ab3cf-237">消耗存储空间。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-237">Storage space consumption.</span></span> |
| <span data-ttu-id="ab3cf-238">HANA 快照</span><span class="sxs-lookup"><span data-stu-id="ab3cf-238">HANA snapshot</span></span>      | <span data-ttu-id="ab3cf-239">本机到 SAP。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-239">Native to SAP.</span></span> <span data-ttu-id="ab3cf-240">备份和还原速度较快。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-240">Rapid backup and restore.</span></span>                                                               |                                       |
| <span data-ttu-id="ab3cf-241">存储快照</span><span class="sxs-lookup"><span data-stu-id="ab3cf-241">Storage snapshot</span></span>   | <span data-ttu-id="ab3cf-242">HANA 大型实例已随附。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-242">Included with HANA Large Instances.</span></span> <span data-ttu-id="ab3cf-243">已优化 HANA 大型实例的 DR。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-243">Optimized DR for HANA Large Instances.</span></span> <span data-ttu-id="ab3cf-244">支持启动卷备份。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-244">Boot volume backup support.</span></span> | <span data-ttu-id="ab3cf-245">每个卷最多 254 个快照。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-245">Maximum 254 snapshots per volume.</span></span>                          |
| <span data-ttu-id="ab3cf-246">日志备份</span><span class="sxs-lookup"><span data-stu-id="ab3cf-246">Log backup</span></span>         | <span data-ttu-id="ab3cf-247">时间点恢复需要此选项。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-247">Required for point in time recovery.</span></span>                                                                   |                                                            |
| <span data-ttu-id="ab3cf-248">其他备份工具</span><span class="sxs-lookup"><span data-stu-id="ab3cf-248">Other backup tools</span></span> | <span data-ttu-id="ab3cf-249">冗余备份位置。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-249">Redundant backup location.</span></span>                                                                             | <span data-ttu-id="ab3cf-250">额外的许可费用。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-250">Additional licensing costs.</span></span>                                |

## <a name="manageability-considerations"></a><span data-ttu-id="ab3cf-251">可管理性注意事项</span><span class="sxs-lookup"><span data-stu-id="ab3cf-251">Manageability considerations</span></span>

<span data-ttu-id="ab3cf-252">使用 SAP HANA Studio、SAP HANA Cockpit、SAP Solution Manager 和其他本机 Linux 工具来监视 CPU、内存、网络带宽和存储空间等 HANA 大型实例资源。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-252">Monitor HANA Large Instances resources such as CPU, memory, network bandwidth, and storage space using SAP HANA Studio, SAP HANA Cockpit, SAP Solution Manager, and other native Linux tools.</span></span> <span data-ttu-id="ab3cf-253">HANA 大型实例未随附内置的监视工具。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-253">HANA Large Instances does not come with built-in monitoring tools.</span></span> <span data-ttu-id="ab3cf-254">Microsoft 会提供所需的资源用于根据组织的要求进行[故障排除和监视][hli-troubleshoot]，Microsoft 支持团队可以帮助排查技术问题。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-254">Microsoft offers resources to help you [troubleshoot and monitor][hli-troubleshoot] according to your organization’s requirements, and the Microsoft support team can assist you in troubleshooting technical issues.</span></span>

<span data-ttu-id="ab3cf-255">如需更大的计算能力，必须购买更大的 SKU。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-255">If you need more computing capability, you must get a larger SKU.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="ab3cf-256">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="ab3cf-256">Security considerations</span></span>

- <span data-ttu-id="ab3cf-257">默认情况下，HANA 大型实例针对静态数据使用基于 TDE（透明数据加密）的存储加密。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-257">By default, HANA Large Instances use storage encryption based on TDE (transparent data encryption) for the data at rest.</span></span>

- <span data-ttu-id="ab3cf-258">HANA 大型实例与虚拟机之间的传输中数据不会加密。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-258">Data in transit between HANA Large Instances and the virtual machines is not encrypted.</span></span> <span data-ttu-id="ab3cf-259">若要加密数据传输，请启用应用程序特定的加密。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-259">To encrypt the data transfer, enable the application-specific encryption.</span></span> <span data-ttu-id="ab3cf-260">请参阅 SAP 说明 [2159014][sap-2159014] - 常见问题解答：SAP HANA 安全性。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-260">See SAP Note [2159014][sap-2159014] - FAQ: SAP HANA Security.</span></span>

- <span data-ttu-id="ab3cf-261">隔离功能在多租户 HANA 大型实例环境中的租户之间提供安全性。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-261">Isolation provides security between the tenants in the multi-tenant HANA Large Instance environment.</span></span> <span data-ttu-id="ab3cf-262">使用租户自身的 VLAN 来隔离租户。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-262">Tenants are isolated using their own VLAN.</span></span>

- <span data-ttu-id="ab3cf-263">[Azure 网络安全最佳做法][network-best-practices]提供了有用的指导。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-263">[Azure network security best practices][network-best-practices] provide helpful guidance.</span></span>

- <span data-ttu-id="ab3cf-264">与处理任何部署一样，我们建议进行[操作系统强化][os-hardening]。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-264">As with any deployment, [operating system hardening][os-hardening] is recommended.</span></span>

- <span data-ttu-id="ab3cf-265">出于物理安全性考虑，仅限已获授权的人员访问 Azure 数据中心。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-265">For physical security, access to Azure datacenters is limited to authorized personnel only.</span></span> <span data-ttu-id="ab3cf-266">任何客户都不能访问物理服务器。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-266">No customers can access the physical servers.</span></span>

<span data-ttu-id="ab3cf-267">有关详细信息，请参阅 [SAP HANA 安全性 - 概述][sap-security]。（需要创建一个 SAP Service Marketplace 帐户进行访问。）</span><span class="sxs-lookup"><span data-stu-id="ab3cf-267">For more information, see [SAP HANA Security—An Overview][sap-security].(A SAP Service Marketplace account is required for access.)</span></span>

## <a name="communities"></a><span data-ttu-id="ab3cf-268">社区</span><span class="sxs-lookup"><span data-stu-id="ab3cf-268">Communities</span></span>

<span data-ttu-id="ab3cf-269">社区可以解答问题，并帮助设置成功的部署。</span><span class="sxs-lookup"><span data-stu-id="ab3cf-269">Communities can answer questions and help you set up a successful deployment.</span></span> <span data-ttu-id="ab3cf-270">请注意以下几点：</span><span class="sxs-lookup"><span data-stu-id="ab3cf-270">Consider the following:</span></span>

- <span data-ttu-id="ab3cf-271">[在 Microsoft 平台上运行 SAP 应用程序（博客）][running-sap-blog]</span><span class="sxs-lookup"><span data-stu-id="ab3cf-271">[Running SAP Applications on the Microsoft Platform Blog][running-sap-blog]</span></span>
- <span data-ttu-id="ab3cf-272">[Azure 社区支持][azure-forum]</span><span class="sxs-lookup"><span data-stu-id="ab3cf-272">[Azure Community Support][azure-forum]</span></span>
- <span data-ttu-id="ab3cf-273">[SAP 社区][sap-community]</span><span class="sxs-lookup"><span data-stu-id="ab3cf-273">[SAP Community][sap-community]</span></span>
- <span data-ttu-id="ab3cf-274">[Stack Overflow SAP][stack-overflow]</span><span class="sxs-lookup"><span data-stu-id="ab3cf-274">[Stack Overflow SAP][stack-overflow]</span></span>

## <a name="related-resources"></a><span data-ttu-id="ab3cf-275">相关资源</span><span class="sxs-lookup"><span data-stu-id="ab3cf-275">Related resources</span></span>

<span data-ttu-id="ab3cf-276">可以查看以下 [Azure 示例方案](/azure/architecture/example-scenario)，了解使用部分相同技术的具体解决方案：</span><span class="sxs-lookup"><span data-stu-id="ab3cf-276">You may wish to review the following [Azure example scenarios](/azure/architecture/example-scenario) that demonstrate specific solutions using some of the same technologies:</span></span>

- [<span data-ttu-id="ab3cf-277">在 Azure 上使用 Oracle 数据库运行 SAP 生产工作负荷</span><span class="sxs-lookup"><span data-stu-id="ab3cf-277">Running SAP production workloads using an Oracle Database on Azure</span></span>](/azure/architecture/example-scenario/apps/sap-production)
- [<span data-ttu-id="ab3cf-278">Azure 上的 SAP 工作负荷的开发/测试环境</span><span class="sxs-lookup"><span data-stu-id="ab3cf-278">Dev/test environments for SAP workloads on Azure</span></span>](/azure/architecture/example-scenario/apps/sap-dev-test)

<!-- links -->

[azure-forum]: https://azure.microsoft.com/support/forums/
[azure-large-instances]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[classes]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[cross-connected]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#network-considerations-for-disaster-recovery-with-hana-large-instances
[dr-site]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery
[expressroute]: /azure/architecture/reference-architectures/hybrid-networking/expressroute
[filter-network]: https://azure.microsoft.com/blog/multiple-vm-nics-and-network-virtual-appliances-in-azure/
[hli-dr]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#network-considerations-for-disaster-recovery-with-hana-large-instances
[hli-backup]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#backup-and-restore
[hli-hadr]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json
[hli-infrastructure]: /azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity
[hli-overview]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[hli-troubleshoot]: /azure/virtual-machines/workloads/sap/troubleshooting-monitoring
[ip]: https://blogs.msdn.microsoft.com/saponsqlserver/2018/02/10/setting-up-hana-system-replication-on-azure-hana-large-instances/
[network-best-practices]: /azure/security/azure-security-network-security-best-practices
[nfs]: /azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[os-hardening]: /azure/security/azure-security-iaas
[physical]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[planning]: /azure/vpn-gateway/vpn-gateway-plan-design
[protecting-sap]: https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/06/protecting-sap-systems-running-on-vmware-with-azure-site-recovery/
[ref-arch]: /azure/architecture/reference-architectures/
[running-SAP]: https://blogs.msdn.microsoft.com/saponsqlserver/2016/06/07/sap-on-sql-general-update-for-customers-partners-june-2016/
[region]: https://azure.microsoft.com/global-infrastructure/services/
[running-sap-blog]: https://blogs.msdn.microsoft.com/saponsqlserver/2017/05/04/sap-on-azure-general-update-for-customers-partners-april-2017/
[quick-sizer]: https://service.sap.com/quicksizing
[sap-1793345]: https://launchpad.support.sap.com/#/notes/1793345
[sap-1872170]: https://launchpad.support.sap.com/#/notes/1872170
[sap-2121330]: https://launchpad.support.sap.com/#/notes/2121330
[sap-2159014]: https://launchpad.support.sap.com/#/notes/2159014
[sap-1736976]: https://launchpad.support.sap.com/#/notes/1736976
[sap-2296290]: https://launchpad.support.sap.com/#/notes/2296290
[sap-community]: https://www.sap.com/community.html
[sap-security]: https://archive.sap.com/documents/docs/DOC-62943
[scripts]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery
[sku]: /azure/expressroute/expressroute-about-virtual-network-gateways
[sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[stack-overflow]: https://stackoverflow.com/tags/sap/info
[stonith]: /azure/virtual-machines/workloads/sap/ha-setup-with-stonith
[subnet]: /azure/virtual-network/virtual-network-manage-subnet
[swd]: https://help.sap.com/doc/saphelp_nw70ehp2/7.02.16/en-us/48/8fe37933114e6fe10000000a421937/frameset.htm
[type]: /azure/virtual-machines/workloads/sap/hana-installation
[vnet]: /azure/virtual-network/virtual-networks-overview
[visio-download]: https://archcenter.blob.core.windows.net/cdn/sap-reference-architectures.vsdx