---
title: 大型机迁移：从大型机迁移到 Azure
description: 将应用程序从大型机环境迁移到 Azure，这是经过验证的、高度可用且可缩放的基础结构，适用于当前在大型机上运行的系统。
author: njray
ms.date: 12/26/2018
ms.openlocfilehash: 9243f757182f95cc227fd6cd7a5374f9c1371ccc
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243108"
---
# <a name="make-the-switch-from-mainframes-to-azure"></a><span data-ttu-id="1f3e4-103">从大型机迁移到 Azure</span><span class="sxs-lookup"><span data-stu-id="1f3e4-103">Make the switch from mainframes to Azure</span></span>

<span data-ttu-id="1f3e4-104">作为运行传统大型机应用程序的替代平台，Azure 在高可用性环境中可提供超大规模计算和存储。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-104">As an alternative platform for running traditional mainframe applications, Azure offers hyperscale compute and storage in a high availability environment.</span></span> <span data-ttu-id="1f3e4-105">用户无需花费大型机环境的相关成本，就能够获得基于云的新式平台的价值及灵活性。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-105">You get the value and agility of a modern, cloud-based platform without the costs associated with a mainframe environment.</span></span>

<span data-ttu-id="1f3e4-106">本部分提供从大型机平台迁移到 Azure 的技术指导。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-106">This section provides technical guidance for making the switch from a mainframe platform to Azure.</span></span>

![大型机和 Azure](../../_images/mainframe-migration/make-the-switch.png)

## <a name="mips-vs-vcpus"></a><span data-ttu-id="1f3e4-108">MIPS 与 vCPU</span><span class="sxs-lookup"><span data-stu-id="1f3e4-108">MIPS vs. vCPUs</span></span>

<span data-ttu-id="1f3e4-109">没有现成的通用映射公式用于确定运行大型机工作负载所需的虚拟中央处理单元 (vCPU) 数量。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-109">There is no universal mapping formula that exists for determining the amount of virtual central processing units (vCPUs) needed to run mainframe workloads.</span></span> <span data-ttu-id="1f3e4-110">但是，映射到 Azure 上的 vCPU 指标通常为每秒百万条指令 (MIPS)。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-110">However, the metric of a million instructions per second (MIPS) is often mapped to vCPUs on Azure.</span></span> <span data-ttu-id="1f3e4-111">MIPS 通过为给定计算机提供每秒周期数的恒定值来测量大型机的总体计算能力。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-111">MIPS measures the overall compute power of a mainframe by providing a constant value of the number of cycles per second for a given machine.</span></span>

<span data-ttu-id="1f3e4-112">一个小型组织可能需要不到 500 MIPS，而一个大型组织通常使用量超过 5,000 MIPS。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-112">A small organization might require less than 500 MIPS, while a large organization typically uses more than 5,000 MIPS.</span></span> <span data-ttu-id="1f3e4-113">单个 MIPS 的费用是 1,000 美元，如果一个大型组织每年需要部署 5,000-MIPS 的基础设施，则需要花费约 500 万美元。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-113">At $1,000 per single MIPS, a large organization spends approximately $5 million annually to deploy a 5,000-MIPS infrastructure.</span></span> <span data-ttu-id="1f3e4-114">这种规模的典型 Azure 部署的年成本估算约为 MIPS 基础设施成本的十分之一。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-114">The annual cost estimate for a typical Azure deployment of this scale is approximately one-tenth the cost of a MIPS infrastructure.</span></span> <span data-ttu-id="1f3e4-115">有关详细信息，请参阅[揭秘大型机到 Azure 的迁移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration)白皮书中的“表 4”。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-115">For details, see Table 4 in the [Demystifying Mainframe-to-Azure Migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration) white paper.</span></span>

<span data-ttu-id="1f3e4-116">要准确计算 Azure 上 vCPU 的 MIPS，需要确定 vCPU 的类型，以及当前运行的确切工作负载。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-116">An accurate calculation of MIPS to vCPUs with Azure depends on the type of vCPU and the exact workload you are running.</span></span> <span data-ttu-id="1f3e4-117">但是，基准研究为估计所需 vCPU 的数量和类型提供了良好的基础。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-117">However, benchmark studies provide a good basis for estimating the number and type of vCPUs you will need.</span></span> <span data-ttu-id="1f3e4-118">一份近期的 [HPE zREF 基准](https://h20195.www2.hpe.com/v2/getpdf.aspx/4aa4-2452enw.pdf)提供了以下估计：</span><span class="sxs-lookup"><span data-stu-id="1f3e4-118">A recent [HPE zREF benchmark](https://h20195.www2.hpe.com/v2/getpdf.aspx/4aa4-2452enw.pdf) provides the following estimates:</span></span>

- <span data-ttu-id="1f3e4-119">用于在线 (CICS) 作业的 HP Proliant 服务器上运行的每个基于 Intel 的核心为 288 MIPS。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-119">288 MIPS per Intel-based core running on HP Proliant servers for online (CICS) jobs.</span></span>

- <span data-ttu-id="1f3e4-120">用于 COBOL 批处理作业的每个 Intel 核心为 170 MIPS。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-120">170 MIPS per Intel core for COBOL batch jobs.</span></span>

<span data-ttu-id="1f3e4-121">本指南估计用于在线处理的每个 vCPU 为 200 MIPS，用于批处理的每个 vCPU 为 100 MIPS。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-121">This guide estimates 200 MIPS per vCPU for online processing and 100 MIPS per vCPU for batch processing.</span></span>

> [!NOTE]
> <span data-ttu-id="1f3e4-122">随着新的虚拟机 (VM) 系列在 Azure 中推出，这些估计值可能会发生变化。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-122">These estimates are subject to change as new virtual machine (VM) series become available in Azure.</span></span>

## <a name="high-availability-and-failover"></a><span data-ttu-id="1f3e4-123">高可用性和故障转移</span><span class="sxs-lookup"><span data-stu-id="1f3e4-123">High availability and failover</span></span>

<span data-ttu-id="1f3e4-124">当使用大型机耦合和并行系统综合体时，大型机系统通常提供 5 个 9 的可用性 (99.999%)。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-124">Mainframe systems often offer five 9s availability (99.999 percent) when mainframe coupling and Parallel Sysplex are used.</span></span> <span data-ttu-id="1f3e4-125">然而，系统运营商仍需要安排停机时间来维护和加载初始程序 (IPL)。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-125">Yet system operators still need to schedule downtime for maintenance and initial program loads (IPLs).</span></span> <span data-ttu-id="1f3e4-126">实际可用性接近 2 到 3 个 9，与基于 Intel 的高端服务器相当。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-126">The actual availability approaches two or three 9s, on par with high end, Intel-based servers.</span></span>

<span data-ttu-id="1f3e4-127">相比之下，Azure 提供基于承诺的服务级别协议 (SLA)，默认为多个 9 可用性，通过本地或基于地理位置的服务复制进行优化。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-127">By comparison, Azure offers commitment-based service level agreements (SLAs), where multiple 9s availability is the default, optimized with local or geo-based replication of services.</span></span>

<span data-ttu-id="1f3e4-128">Azure 通过从本地或其他地理区域的多个存储设备复制数据来提供额外的可用性。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-128">Azure provides additional availability by replicating data from multiple storage devices, either locally or in other geographic regions.</span></span> <span data-ttu-id="1f3e4-129">如果发生基于 Azure 的故障，计算资源可以访问本地或区域级别的复制数据。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-129">In the event of an Azure-based failure, compute resources can access the replicated data on either the local or regional level.</span></span>

<span data-ttu-id="1f3e4-130">使用 Azure 平台即服务 (PaaS) 资源（如 [Azure SQL 数据库](/azure/sql-database/sql-database-technical-overview)和 [Azure Cosmos 数据库](/azure/cosmos-db/introduction)）时，Azure 可自动处理故障转移。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-130">When you use Azure platform as a service (PaaS) resources, such as [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) and [Azure Cosmos Database](/azure/cosmos-db/introduction), Azure can automatically handle failovers.</span></span> <span data-ttu-id="1f3e4-131">使用 Azure 基础结构即服务 (IaaS) 时，故障转移依赖于特定的系统功能，例如 SQL Server AlwaysOn 功能、故障转移群集实例和可用性组。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-131">When you use Azure infrastructure as a service (IaaS), failover relies on specific system functionality, such as SQL Server AlwaysOn features, failover clustering instances, and availability groups.</span></span>

## <a name="scalability"></a><span data-ttu-id="1f3e4-132">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="1f3e4-132">Scalability</span></span>

<span data-ttu-id="1f3e4-133">大型机通常可以纵向扩展，而云环境可以横向扩展。大型机可以通过使用耦合设备 (CF) 横向扩展，但硬件和存储的成本很高，使得大型机的横向扩展成本非常高。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-133">Mainframes typically scale up, while cloud environments scale out. Mainframes can scale out with the use of a coupling facility (CF), but the high costs of hardware and storage makes mainframes very expensive to scale out.</span></span>

<span data-ttu-id="1f3e4-134">另外，CF 提供紧密耦合的计算，而 Azure 的横向扩展功能是松散耦合的。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-134">In addition, a CF offers tightly coupled compute, whereas the scale-out features of Azure are loosely coupled.</span></span> <span data-ttu-id="1f3e4-135">云可以纵向扩展以匹配确切的用户规范，计算能力、存储和服务在基于使用情况的计费模型下按需扩展。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-135">The cloud can scale up or down to match exact user specifications, with compute power, storage, and services scaling on demand under a usage-based billing model.</span></span>

## <a name="backup-and-recovery"></a><span data-ttu-id="1f3e4-136">备份和恢复</span><span class="sxs-lookup"><span data-stu-id="1f3e4-136">Backup and recovery</span></span>

<span data-ttu-id="1f3e4-137">大型机客户通常通过维护灾难恢复站点或使用独立的大型机提供商来应对灾难事故。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-137">Mainframe customers typically maintain disaster recovery sites or make use or an independent mainframe provider for disaster contingencies.</span></span> <span data-ttu-id="1f3e4-138">与灾难恢复站点的同步通常通过数据的脱机副本完成。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-138">Synchronization with a disaster recovery site is usually done through offline copies of data.</span></span> <span data-ttu-id="1f3e4-139">两种选择都会产生高昂的成本。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-139">Both options incur high costs.</span></span>

<span data-ttu-id="1f3e4-140">通过大型机耦合设备也可以获得自动化异地冗余（虽然费用很高），并且通常保留用于任务关键型系统。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-140">Automated geo-redundancy is also available through the mainframe coupling facility, albeit at great expense, and is usually reserved for mission-critical systems.</span></span> <span data-ttu-id="1f3e4-141">相比之下，Azure 在本地或区域级别或通过异地冗余为[备份](/azure/backup/backup-introduction-to-azure-backup)、[恢复](/azure/site-recovery/site-recovery-overview)和[冗余](/azure/storage/common/storage-redundancy)提供了易于实现且经济高效的选项。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-141">In contrast, Azure has easy-to-implement and cost-effective options for [backup](/azure/backup/backup-introduction-to-azure-backup), [recovery](/azure/site-recovery/site-recovery-overview), and [redundancy](/azure/storage/common/storage-redundancy) at local or regional levels, or via geo-redundancy.</span></span>

## <a name="storage"></a><span data-ttu-id="1f3e4-142">存储</span><span class="sxs-lookup"><span data-stu-id="1f3e4-142">Storage</span></span>

<span data-ttu-id="1f3e4-143">在了解大型机的工作方式时会涉及理解各种交叉的术语。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-143">Part of understanding how mainframes work involves decoding various overlapping terms.</span></span> <span data-ttu-id="1f3e4-144">例如，中央存储、实际内存、实际存储和主存储，通常都指的是直接连接到大型机处理器的存储。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-144">For example, central storage, real memory, real storage, and main storage all generally refer to storage attached directly to the mainframe processor.</span></span>

<span data-ttu-id="1f3e4-145">大型机硬件包括处理器和许多其他设备，如直接访问存储设备 (DASD)、磁带驱动器和几种类型的用户控制台。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-145">Mainframe hardware includes processors and many other devices, such as direct access storage devices (DASDs), magnetic tape drives, and several types of user consoles.</span></span> <span data-ttu-id="1f3e4-146">磁带和 DASD 用于系统功能和用户程序。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-146">Tapes and DASDs are used for system functions and by user programs.</span></span>

<span data-ttu-id="1f3e4-147">大型机的物理存储类型包括：</span><span class="sxs-lookup"><span data-stu-id="1f3e4-147">Types of physical storage for mainframes include:</span></span>

- <span data-ttu-id="1f3e4-148">中央存储：直接位于大型机处理器上，也称为处理器或实际存储。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-148">Central storage: Located directly on the mainframe processor, this is also known as processor or real storage.</span></span>

- <span data-ttu-id="1f3e4-149">辅助存储：与大型机分开放置，此类型包括 DASD 上的存储，也称为分页存储。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-149">Auxiliary storage: Located separately from the mainframe, this type includes storage on DASDs and is also known as paging storage.</span></span>

<span data-ttu-id="1f3e4-150">云提供一系列灵活的可扩展选项，用户只需支付所需的选项。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-150">The cloud offers a range of flexible, scalable options, and you will pay only for those options that you need.</span></span> <span data-ttu-id="1f3e4-151">[Azure 存储](/azure/storage/common/storage-introduction)为数据对象提供可大规模缩放的对象存储，为云提供文件系统服务、可靠的消息传送存储，以及 NoSQL 存储。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-151">[Azure Storage](/azure/storage/common/storage-introduction) offers a massively scalable object store for data objects, a file system service for the cloud, a reliable messaging store, and a NoSQL store.</span></span> <span data-ttu-id="1f3e4-152">对于 VM，托管和非托管磁盘提供持久且安全的磁盘存储。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-152">For VMs, managed and unmanaged disks provide persistent, secure disk storage.</span></span>

## <a name="mainframe-development-and-testing"></a><span data-ttu-id="1f3e4-153">大型机开发和测试</span><span class="sxs-lookup"><span data-stu-id="1f3e4-153">Mainframe development and testing</span></span>

<span data-ttu-id="1f3e4-154">大型机迁移项目的主要驱动力是应用程序开发的变化。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-154">A major driver in mainframe migration projects is the changing face of application development.</span></span> <span data-ttu-id="1f3e4-155">组织希望他们的开发环境更加灵活，能够响应各种业务需求。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-155">Organizations want their development environment to be more agile and responsive to business needs.</span></span>

<span data-ttu-id="1f3e4-156">大型机通常采用独立逻辑分区 (LPAR) 进行开发和测试，例如 QA 和暂存 LPAR。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-156">Mainframes typically have separate logical partitions (LPARs) for development and testing, such as QA and staging LPARs.</span></span> <span data-ttu-id="1f3e4-157">大型机开发解决方案包括编译器（COBOL、PL/I、汇编程序）和编辑器。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-157">Mainframe development solutions include compilers (COBOL, PL/I, Assembler) and editors.</span></span> <span data-ttu-id="1f3e4-158">最常见的是用于在 IBM 大型机上运行的 z/OS 操作系统的交互式系统生产力工具 (ISPF)。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-158">The most common is the Interactive System Productivity Facility (ISPF) for the z/OS operating system that runs on IBM mainframes.</span></span> <span data-ttu-id="1f3e4-159">其他还包括 ROSCOE 编程工具 (RPF) 和 Computer Associates 工具，如 CA Librarian 和 CA-Panvalet。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-159">Others include ROSCOE Programming Facility (RPF) and Computer Associates tools, such as CA Librarian and CA-Panvalet.</span></span>

<span data-ttu-id="1f3e4-160">仿真环境和编译器在 x86 平台上可用，因此，开发和测试通常可以是从大型机迁移到 Azure 的第一批工作负载。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-160">Emulation environments and compilers are available on x86 platforms, so development and testing can typically be among the first workloads to migrate from a mainframe to Azure.</span></span> <span data-ttu-id="1f3e4-161">[Azure 中的 DevOps 工具](https://azure.microsoft.com/solutions/devops/)的可用性和广泛使用正在加速开发和测试环境的迁移。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-161">The availability and widespread use of [DevOps tools in Azure](https://azure.microsoft.com/solutions/devops/) is accelerating the migration of development and testing environments.</span></span>

<span data-ttu-id="1f3e4-162">在 Azure 上开发和测试解决方案并准备好部署到大型机时，需要将代码复制到大型机并在那里进行编译。</span><span class="sxs-lookup"><span data-stu-id="1f3e4-162">When solutions are developed and tested on Azure and are ready for deployment to the mainframe, you will need to copy the code to the mainframe and compile it there.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1f3e4-163">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1f3e4-163">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1f3e4-164">大型机应用程序迁移</span><span class="sxs-lookup"><span data-stu-id="1f3e4-164">Mainframe application migration</span></span>](application-strategies.md)
