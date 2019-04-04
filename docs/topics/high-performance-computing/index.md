---
title: Azure 上的高性能计算 (HPC)
description: 有关如何在 Azure 上生成运行的 HPC 工作负荷的指南
author: adamboeglin
ms.date: 2/4/2019
ms.openlocfilehash: 5263dd3a06e5244bf804df4be6ec57d789574f76
ms.sourcegitcommit: ea97ac004c38c6b456794c1a8eef29f8d2b77d50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58489192"
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD026 -->

# <a name="high-performance-computing-hpc-on-azure"></a><span data-ttu-id="06d54-103">Azure 上的高性能计算 (HPC)</span><span class="sxs-lookup"><span data-stu-id="06d54-103">High Performance Computing (HPC) on Azure</span></span>

## <a name="introduction-to-hpc"></a><span data-ttu-id="06d54-104">HPC 简介</span><span class="sxs-lookup"><span data-stu-id="06d54-104">Introduction to HPC</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.youtube.com/embed/rKURT32faJk]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="06d54-105">高性能计算 (HPC) 也称“大型计算”，使用大量基于 CPU 或 GPU 的计算机来解决复杂的数学任务。</span><span class="sxs-lookup"><span data-stu-id="06d54-105">High Performance Computing (HPC), also called "Big Compute", uses a large number of CPU or GPU-based computers to solve complex mathematical tasks.</span></span>

<span data-ttu-id="06d54-106">许多行业使用 HPC 解决某些最困难的问题。</span><span class="sxs-lookup"><span data-stu-id="06d54-106">Many industries use HPC to solve some of their most difficult problems.</span></span>  <span data-ttu-id="06d54-107">其中包括下述工作负荷：</span><span class="sxs-lookup"><span data-stu-id="06d54-107">These include workloads such as:</span></span>

- <span data-ttu-id="06d54-108">基因组学</span><span class="sxs-lookup"><span data-stu-id="06d54-108">Genomics</span></span>
- <span data-ttu-id="06d54-109">石油和天然气模拟</span><span class="sxs-lookup"><span data-stu-id="06d54-109">Oil and gas simulations</span></span>
- <span data-ttu-id="06d54-110">财务</span><span class="sxs-lookup"><span data-stu-id="06d54-110">Finance</span></span>
- <span data-ttu-id="06d54-111">半导体设计</span><span class="sxs-lookup"><span data-stu-id="06d54-111">Semiconductor design</span></span>
- <span data-ttu-id="06d54-112">工程</span><span class="sxs-lookup"><span data-stu-id="06d54-112">Engineering</span></span>
- <span data-ttu-id="06d54-113">天气建模</span><span class="sxs-lookup"><span data-stu-id="06d54-113">Weather modeling</span></span>

### <a name="how-is-hpc-different-on-the-cloud"></a><span data-ttu-id="06d54-114">云上的 HPC 有何不同？</span><span class="sxs-lookup"><span data-stu-id="06d54-114">How is HPC different on the cloud?</span></span>

<span data-ttu-id="06d54-115">本地和云中的 HPC 系统的一大区别是能否根据需要动态添加和删除资源。</span><span class="sxs-lookup"><span data-stu-id="06d54-115">One of the primary differences between an on-premise HPC system and one in the cloud is the ability for resources to dynamically be added and removed as they're needed.</span></span>  <span data-ttu-id="06d54-116">动态缩放消除了计算容量这一瓶颈，允许客户根据作业要求调整其基础结构的大小。</span><span class="sxs-lookup"><span data-stu-id="06d54-116">Dynamic scaling removes compute capacity as a bottleneck and instead allow customers to right size their infrastructure for the requirements of their jobs.</span></span>

<span data-ttu-id="06d54-117">以下文章更详细地介绍了此动态缩放功能。</span><span class="sxs-lookup"><span data-stu-id="06d54-117">The following articles provide more detail about this dynamic scaling capability.</span></span>

- [<span data-ttu-id="06d54-118">大计算的体系结构样式</span><span class="sxs-lookup"><span data-stu-id="06d54-118">Big Compute Architecture Style</span></span>](/azure/architecture/guide/architecture-styles/big-compute?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-119">有关自动缩放的最佳做法</span><span class="sxs-lookup"><span data-stu-id="06d54-119">Autoscaling best practices</span></span>](/azure/architecture/best-practices/auto-scaling?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="implementation-checklist"></a><span data-ttu-id="06d54-120">实现清单</span><span class="sxs-lookup"><span data-stu-id="06d54-120">Implementation checklist</span></span>

<span data-ttu-id="06d54-121">若要在 Azure 上实现自己的 HPC 解决方案，请确保参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="06d54-121">As you're looking to implement your own HPC solution on Azure, ensure you're reviewed the following topics:</span></span>

<!-- markdownlint-disable MD032 -->

> [!div class="checklist"]
> - <span data-ttu-id="06d54-122">按要求选择相应的[体系结构](#infrastructure)</span><span class="sxs-lookup"><span data-stu-id="06d54-122">Choose the appropriate [architecture](#infrastructure) based on your requirements</span></span>
> - <span data-ttu-id="06d54-123">了解适用于工作负荷的具体[计算](#compute)选项</span><span class="sxs-lookup"><span data-stu-id="06d54-123">Know which [compute](#compute) options is right for your workload</span></span>
> - <span data-ttu-id="06d54-124">根据需求确定适当的[存储](#storage)解决方案</span><span class="sxs-lookup"><span data-stu-id="06d54-124">Identify the right [storage](#storage) solution that meets your needs</span></span>
> - <span data-ttu-id="06d54-125">确定如何[管理](#management)所有资源</span><span class="sxs-lookup"><span data-stu-id="06d54-125">Decide how you're going to [manage](#management) all your resources</span></span>
> - <span data-ttu-id="06d54-126">优化用于云的[应用程序](#hpc-applications)</span><span class="sxs-lookup"><span data-stu-id="06d54-126">Optimize your [application](#hpc-applications) for the cloud</span></span>
> - <span data-ttu-id="06d54-127">[保护](#security)基础结构</span><span class="sxs-lookup"><span data-stu-id="06d54-127">[Secure](#security) your Infrastructure</span></span>

<!-- markdownlint-enable MD032 -->

## <a name="infrastructure"></a><span data-ttu-id="06d54-128">基础结构</span><span class="sxs-lookup"><span data-stu-id="06d54-128">Infrastructure</span></span>

<span data-ttu-id="06d54-129">有许多基础结构组件是构建 HPC 系统所必需的。</span><span class="sxs-lookup"><span data-stu-id="06d54-129">There are a number of infrastructure components necessary to build an HPC system.</span></span>  <span data-ttu-id="06d54-130">不管你选择如何管理 HPC 工作负荷，都需要使用计算、存储和网络基础组件。</span><span class="sxs-lookup"><span data-stu-id="06d54-130">Compute, Storage, and Networking provide the underlying components, no matter how you choose to manage your HPC workloads.</span></span>

### <a name="example-hpc-architectures"></a><span data-ttu-id="06d54-131">示例 HPC 体系结构</span><span class="sxs-lookup"><span data-stu-id="06d54-131">Example HPC architectures</span></span>

<span data-ttu-id="06d54-132">可以通过许多不同的方式在 Azure 上设计和实现 HPC 体系结构。</span><span class="sxs-lookup"><span data-stu-id="06d54-132">There are a number of different ways to design and implement your HPC architecture on Azure.</span></span>  <span data-ttu-id="06d54-133">HPC 应用程序可扩展到数千个计算核心，扩展本地群集或作为 100% 的云原生解决方案来运行。</span><span class="sxs-lookup"><span data-stu-id="06d54-133">HPC applications can scale to thousands of compute cores, extend on-premises clusters, or run as a 100% cloud native solution.</span></span>

<span data-ttu-id="06d54-134">以下方案概述了生成 HPC 解决方案的一些常见方式。</span><span class="sxs-lookup"><span data-stu-id="06d54-134">The following scenarios outline a few of the common ways HPC solutions are built.</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/apps/hpc-saas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/apps/media/architecture-hpc-saas.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="06d54-135">Azure 上的计算机辅助工程服务</span><span class="sxs-lookup"><span data-stu-id="06d54-135">Computer-aided engineering services on Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="06d54-136">为 Azure 上的计算机辅助工程 (CAE) 提供软件即服务 (SaaS) 平台。</span><span class="sxs-lookup"><span data-stu-id="06d54-136">Provide a software-as-a-service (SaaS) platform for computer-aided engineering (CAE) on Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/infrastructure/hpc-cfd?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/infrastructure/media/architecture-hpc-cfd.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="06d54-137">Azure 上的计算流体动力学 (CFD) 模拟</span><span class="sxs-lookup"><span data-stu-id="06d54-137">Computational fluid dynamics (CFD) simulations on Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="06d54-138">在 Azure 上执行计算流体动力学 (CFD) 模拟。</span><span class="sxs-lookup"><span data-stu-id="06d54-138">Execute computational fluid dynamics (CFD) simulations on Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/infrastructure/video-rendering?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/infrastructure/media/architecture-video-rendering.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="06d54-139">Azure 上的 3D 视频渲染</span><span class="sxs-lookup"><span data-stu-id="06d54-139">3D video rendering on Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="06d54-140">使用 Azure Batch 服务在 Azure 中运行本机 HPC 工作负荷</span><span class="sxs-lookup"><span data-stu-id="06d54-140">Run native HPC workloads in Azure using the Azure Batch service</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

### <a name="compute"></a><span data-ttu-id="06d54-141">计算</span><span class="sxs-lookup"><span data-stu-id="06d54-141">Compute</span></span>

<span data-ttu-id="06d54-142">Azure 提供一系列已针对 CPU 和 GPU 密集型工作负荷进行优化的虚拟机大小。</span><span class="sxs-lookup"><span data-stu-id="06d54-142">Azure offers a range of sizes that are optimized for both CPU & GPU intensive workloads.</span></span>

#### <a name="cpu-based-virtual-machines"></a><span data-ttu-id="06d54-143">基于 CPU 的虚拟机</span><span class="sxs-lookup"><span data-stu-id="06d54-143">CPU-based virtual machines</span></span>

- [<span data-ttu-id="06d54-144">Linux VM</span><span class="sxs-lookup"><span data-stu-id="06d54-144">Linux VMs</span></span>](/azure/virtual-machines/linux/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- <span data-ttu-id="06d54-145">[Windows VM](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 的 VM</span><span class="sxs-lookup"><span data-stu-id="06d54-145">[Windows VM's](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) VMs</span></span>
  
#### <a name="gpu-enabled-virtual-machines"></a><span data-ttu-id="06d54-146">支持 GPU 的虚拟机</span><span class="sxs-lookup"><span data-stu-id="06d54-146">GPU-enabled virtual machines</span></span>

<span data-ttu-id="06d54-147">N 系列的 VM 具备为计算密集型或图形密集型应用程序（包括人工智能 (AI) 学习和可视化）设计的 NVIDIA GPU。</span><span class="sxs-lookup"><span data-stu-id="06d54-147">N-series VMs feature NVIDIA GPUs designed for compute-intensive or graphics-intensive applications including artificial intelligence (AI) learning and visualization.</span></span>

- [<span data-ttu-id="06d54-148">Linux VM</span><span class="sxs-lookup"><span data-stu-id="06d54-148">Linux VMs</span></span>](/azure/virtual-machines/linux/sizes-gpu?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-149">Windows VM</span><span class="sxs-lookup"><span data-stu-id="06d54-149">Windows VMs</span></span>](/azure/virtual-machines/windows/sizes-gpu?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

### <a name="storage"></a><span data-ttu-id="06d54-150">存储</span><span class="sxs-lookup"><span data-stu-id="06d54-150">Storage</span></span>

<span data-ttu-id="06d54-151">大规模的批处理和 HPC 工作负荷具有超过传统云文件系统功能的数据存储和访问需求。</span><span class="sxs-lookup"><span data-stu-id="06d54-151">Large-scale Batch and HPC workloads have demands for data storage and access that exceed the capabilities of traditional cloud file systems.</span></span>  <span data-ttu-id="06d54-152">可以通过许多解决方案来管理 Azure 上的 HPC 应用程序的速度和容量需求</span><span class="sxs-lookup"><span data-stu-id="06d54-152">There are a number of solutions to manage both the speed and capacity needs of HPC applications on Azure</span></span>

- <span data-ttu-id="06d54-153">[Avere vFXT](https://azure.microsoft.com/services/storage/avere-vfxt/)：数据存储速度更快，访问更方便，适合在边缘设备上进行高性能计算</span><span class="sxs-lookup"><span data-stu-id="06d54-153">[Avere vFXT](https://azure.microsoft.com/services/storage/avere-vfxt/) for faster, more accessible data storage for high-performance computing at the edge</span></span>
- [<span data-ttu-id="06d54-154">BeeGFS</span><span class="sxs-lookup"><span data-stu-id="06d54-154">BeeGFS</span></span>](https://azure.microsoft.com/resources/implement-glusterfs-on-azure/)
- [<span data-ttu-id="06d54-155">存储优化虚拟机</span><span class="sxs-lookup"><span data-stu-id="06d54-155">Storage Optimized Virtual Machines</span></span>](/azure/virtual-machines/windows/sizes-storage?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-156">Blob、表和队列存储</span><span class="sxs-lookup"><span data-stu-id="06d54-156">Blob, table, and queue storage</span></span>](/azure/storage/storage-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-157">Azure SMB 文件存储</span><span class="sxs-lookup"><span data-stu-id="06d54-157">Azure SMB File storage</span></span>](/azure/storage/files/storage-files-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-158">Intel Cloud Edition Lustre</span><span class="sxs-lookup"><span data-stu-id="06d54-158">Intel Cloud Edition Lustre</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/intel.intel-cloud-edition-gs)

<span data-ttu-id="06d54-159">若要详细了解 Azure 上的 Lustre、GlusterFS 和 BeeGFS 的比较结果，请参阅 [Parallel Files Systems on Azure eBook](https://blogs.msdn.microsoft.com/azurecat/2018/06/11/azurecat-ebook-parallel-virtual-file-systems-on-microsoft-azure/)（Azure 电子书上的并行文件系统）</span><span class="sxs-lookup"><span data-stu-id="06d54-159">For more information comparing Lustre, GlusterFS, and BeeGFS on Azure, review the [Parallel Files Systems on Azure eBook](https://blogs.msdn.microsoft.com/azurecat/2018/06/11/azurecat-ebook-parallel-virtual-file-systems-on-microsoft-azure/)</span></span>

### <a name="networking"></a><span data-ttu-id="06d54-160">网络</span><span class="sxs-lookup"><span data-stu-id="06d54-160">Networking</span></span>

<span data-ttu-id="06d54-161">H16r、H16mr、A8、A9 VM 可以连接到高吞吐量后端 RDMA 网络。</span><span class="sxs-lookup"><span data-stu-id="06d54-161">H16r, H16mr, A8, and A9 VMs can connect to a high throughput back-end RDMA network.</span></span> <span data-ttu-id="06d54-162">此网络可以提高在 Microsoft MPI 或 Intel MPI 下运行的紧密耦合的并行应用程序的性能。</span><span class="sxs-lookup"><span data-stu-id="06d54-162">This network can improve the performance of tightly coupled parallel applications running under Microsoft MPI or Intel MPI.</span></span>

- [<span data-ttu-id="06d54-163">支持 RDMA 的实例</span><span class="sxs-lookup"><span data-stu-id="06d54-163">RDMA Capable Instances</span></span>](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context#rdma-capable-instances)
- [<span data-ttu-id="06d54-164">虚拟网络</span><span class="sxs-lookup"><span data-stu-id="06d54-164">Virtual Network</span></span>](/azure/virtual-network/virtual-networks-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-165">ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="06d54-165">ExpressRoute</span></span>](/azure/expressroute/expressroute-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="management"></a><span data-ttu-id="06d54-166">管理</span><span class="sxs-lookup"><span data-stu-id="06d54-166">Management</span></span>

### <a name="do-it-yourself"></a><span data-ttu-id="06d54-167">DIY</span><span class="sxs-lookup"><span data-stu-id="06d54-167">Do-it-yourself</span></span>

<span data-ttu-id="06d54-168">在 Azure 上从头开头构建 HPC 系统可以为你带来极大的灵活性，但通常需要进行很密集的维护。</span><span class="sxs-lookup"><span data-stu-id="06d54-168">Building an HPC system from scratch on Azure offers a significant amount of flexibility, but is often very maintenance intensive.</span></span>  

1. <span data-ttu-id="06d54-169">在 Azure 虚拟机或[虚拟机规模集](/azure/virtual-machine-scale-sets/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)中设置自己的群集环境。</span><span class="sxs-lookup"><span data-stu-id="06d54-169">Set up your own cluster environment in Azure virtual machines or [virtual machine scale sets](/azure/virtual-machine-scale-sets/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context).</span></span>
2. <span data-ttu-id="06d54-170">使用 Azure 资源管理器模板部署先进的[工作负荷管理器](#workload-managers)、基础结构和[应用程序](#hpc-applications)。</span><span class="sxs-lookup"><span data-stu-id="06d54-170">Use Azure Resource Manager templates to deploy leading [workload managers](#workload-managers), infrastructure, and [applications](#hpc-applications).</span></span>
3. <span data-ttu-id="06d54-171">选择包括 MPI 或 GPU 工作负荷专用硬件与网络连接的 HPC 和 GPU [VM 大小](#compute)。</span><span class="sxs-lookup"><span data-stu-id="06d54-171">Choose HPC and GPU [VM sizes](#compute) that include specialized hardware and network connections for MPI or GPU workloads.</span></span>
4. <span data-ttu-id="06d54-172">为 I/O 密集型工作负载添加[高性能存储](#storage)。</span><span class="sxs-lookup"><span data-stu-id="06d54-172">Add [high performance storage](#storage) for I/O-intensive workloads.</span></span>

### <a name="hybrid-and-cloud-bursting"></a><span data-ttu-id="06d54-173">混合和云突发</span><span class="sxs-lookup"><span data-stu-id="06d54-173">Hybrid and cloud Bursting</span></span>

<span data-ttu-id="06d54-174">若要将现有的本地 HPC 系统连接到 Azure，可以参考许多有用的资源。</span><span class="sxs-lookup"><span data-stu-id="06d54-174">If you have an existing on-premise HPC system that you'd like to connect to Azure, there are a number of resources to help get you started.</span></span>

<span data-ttu-id="06d54-175">首先，请参阅文档中的[将本地网络连接到 Azure 的选项](/azure/architecture/reference-architectures/hybrid-networking/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)一文。</span><span class="sxs-lookup"><span data-stu-id="06d54-175">First, review the [Options for connecting an on-premises network to Azure](/azure/architecture/reference-architectures/hybrid-networking/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) article in the documentation.</span></span>  <span data-ttu-id="06d54-176">可以在其中获取下述连接选项的相关信息：</span><span class="sxs-lookup"><span data-stu-id="06d54-176">From there, you may want information on these connectivity options:</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/vpn?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/vpn.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="06d54-177">使用 VPN 网关将本地网络连接到 Azure</span><span class="sxs-lookup"><span data-stu-id="06d54-177">Connect an on-premises network to Azure using a VPN gateway</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="06d54-178">此参考体系结构演示如何使用站点到站点虚拟专用网络 (VPN) 将本地网络扩展到 Azure。</span><span class="sxs-lookup"><span data-stu-id="06d54-178">This reference architecture shows how to extend an on-premises network to Azure, using a site-to-site virtual private network (VPN).</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/expressroute?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/expressroute.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="06d54-179">使用 ExpressRoute 将本地网络连接到 Azure</span><span class="sxs-lookup"><span data-stu-id="06d54-179">Connect an on-premises network to Azure using ExpressRoute</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="06d54-180">ExpressRoute 连接通过第三方连接提供商使用私有的专用连接。</span><span class="sxs-lookup"><span data-stu-id="06d54-180">ExpressRoute connections use a private, dedicated connection through a third-party connectivity provider.</span></span> <span data-ttu-id="06d54-181">该专用连接将本地网络扩展到 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="06d54-181">The private connection extends your on-premises network into Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/expressroute-vpn-failover?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/expressroute-vpn-failover.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="06d54-182">使用 ExpressRoute 和 VPN 故障转移将本地网络连接到 Azure</span><span class="sxs-lookup"><span data-stu-id="06d54-182">Connect an on-premises network to Azure using ExpressRoute with VPN failover</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="06d54-183">实施这样一个高度可用且安全的站点到站点网络体系结构：跨 Azure 虚拟网络，以及使用 ExpressRoute 和 VPN 网关故障转移建立连接的本地网络。</span><span class="sxs-lookup"><span data-stu-id="06d54-183">Implement a highly available and secure site-to-site network architecture that spans an Azure virtual network and an on-premises network connected using ExpressRoute with VPN gateway failover.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

<span data-ttu-id="06d54-184">安全地建立网络连接以后，即可通过现有[工作负荷管理器](#workload-managers)的突发功能根据需要使用云计算资源。</span><span class="sxs-lookup"><span data-stu-id="06d54-184">Once network connectivity is securely established, you can start using cloud compute resources on-demand with the bursting capabilities of your existing [workload manager](#workload-managers).</span></span>

### <a name="marketplace-solutions"></a><span data-ttu-id="06d54-185">市场解决方案</span><span class="sxs-lookup"><span data-stu-id="06d54-185">Marketplace solutions</span></span>

<span data-ttu-id="06d54-186">[Azure 市场](https://azuremarketplace.microsoft.com/marketplace/)中提供许多工作负荷管理器。</span><span class="sxs-lookup"><span data-stu-id="06d54-186">There are a number of workload managers offered in the [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).</span></span>

- [<span data-ttu-id="06d54-187">RogueWave 的基于 CentOS 的 HPC</span><span class="sxs-lookup"><span data-stu-id="06d54-187">RogueWave CentOS-based HPC</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
- [<span data-ttu-id="06d54-188">SUSE Linux Enterprise Server for HPC</span><span class="sxs-lookup"><span data-stu-id="06d54-188">SUSE Linux Enterprise Server for HPC</span></span>](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
- [<span data-ttu-id="06d54-189">TIBCO Grid Server Engine</span><span class="sxs-lookup"><span data-stu-id="06d54-189">TIBCO Grid Server Engine</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
- [<span data-ttu-id="06d54-190">适用于 Windows 和 Linux 的 Azure 数据科学 VM</span><span class="sxs-lookup"><span data-stu-id="06d54-190">Azure Data Science VM for Windows and Linux</span></span>](/azure/machine-learning/data-science-virtual-machine/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-191">D3View</span><span class="sxs-lookup"><span data-stu-id="06d54-191">D3View</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
- [<span data-ttu-id="06d54-192">UberCloud</span><span class="sxs-lookup"><span data-stu-id="06d54-192">UberCloud</span></span>](https://azure.microsoft.com/search/marketplace/?q=ubercloud)

### <a name="azure-batch"></a><span data-ttu-id="06d54-193">Azure 批处理</span><span class="sxs-lookup"><span data-stu-id="06d54-193">Azure Batch</span></span>

<span data-ttu-id="06d54-194">[Azure Batch](/azure/batch/batch-technical-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)是一个平台服务，适用于在云中有效运行大规模并行和高性能计算 (HPC) 应用程序。</span><span class="sxs-lookup"><span data-stu-id="06d54-194">[Azure Batch](/azure/batch/batch-technical-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) is a platform service for running large-scale parallel and high-performance computing (HPC) applications efficiently in the cloud.</span></span> <span data-ttu-id="06d54-195">Azure Batch 可以计划要在托管的虚拟机池上运行的计算密集型工作，并且可以自动缩放计算资源以符合作业的需求。</span><span class="sxs-lookup"><span data-stu-id="06d54-195">Azure Batch schedules compute-intensive work to run on a managed pool of virtual machines, and can automatically scale compute resources to meet the needs of your jobs.</span></span>

<span data-ttu-id="06d54-196">SaaS 提供商或开发商可以使用 Batch SDK 和工具将 HPC 应用程序或容器工作负荷与 Azure 集成，将数据暂存到 Azure，并生成作业执行管道。</span><span class="sxs-lookup"><span data-stu-id="06d54-196">SaaS providers or developers can use the Batch SDKs and tools to integrate HPC applications or container workloads with Azure, stage data to Azure, and build job execution pipelines.</span></span>

### <a name="azure-cyclecloud"></a><span data-ttu-id="06d54-197">Azure CycleCloud</span><span class="sxs-lookup"><span data-stu-id="06d54-197">Azure CycleCloud</span></span>

<span data-ttu-id="06d54-198">[Azure CycleCloud](/azure/cyclecloud/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 提供的在 Azure 上使用任何计划程序（例如 Slurm、Grid Engine、HPC Pack、HTCondor、LSF、PBS Pro 或 Symphony）管理 HPC 工作负荷的方法是最简单的</span><span class="sxs-lookup"><span data-stu-id="06d54-198">[Azure CycleCloud](/azure/cyclecloud/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) Provides the simplest way to manage HPC workloads using any scheduler (like Slurm, Grid Engine, HPC Pack, HTCondor, LSF, PBS Pro, or Symphony), on Azure</span></span>

<span data-ttu-id="06d54-199">CycleCloud 允许执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="06d54-199">CycleCloud allows you to:</span></span>

- <span data-ttu-id="06d54-200">部署全部群集和其他资源，包括计划程序、计算 VM、存储、网络和缓存</span><span class="sxs-lookup"><span data-stu-id="06d54-200">Deploy full clusters and other resources, including scheduler, compute VMs, storage, networking, and cache</span></span>
- <span data-ttu-id="06d54-201">协调作业、数据和云工作流</span><span class="sxs-lookup"><span data-stu-id="06d54-201">Orchestrate job, data, and cloud workflows</span></span>
- <span data-ttu-id="06d54-202">允许管理员完全控制运行作业的具体用户、位置和成本</span><span class="sxs-lookup"><span data-stu-id="06d54-202">Give admins full control over which users can run jobs, as well as where and at what cost</span></span>
- <span data-ttu-id="06d54-203">通过高级策略和治理功能（例如成本控制、Active Directory 集成、监视和报告）自定义并优化群集</span><span class="sxs-lookup"><span data-stu-id="06d54-203">Customize and optimize clusters through advanced policy and governance features, including cost controls, Active Directory integration, monitoring, and reporting</span></span>
- <span data-ttu-id="06d54-204">无需修改即可使用当前的作业计划程序和应用程序</span><span class="sxs-lookup"><span data-stu-id="06d54-204">Use your current job scheduler and applications without modification</span></span>
- <span data-ttu-id="06d54-205">利用内置的自动缩放和经过测试的引用体系结构处理广泛的 HPC 工作负荷和行业工作负荷</span><span class="sxs-lookup"><span data-stu-id="06d54-205">Take advantage of built-in autoscaling and battle-tested reference architectures for a wide range of HPC workloads and industries</span></span>

### <a name="workload-managers"></a><span data-ttu-id="06d54-206">工作负荷管理器</span><span class="sxs-lookup"><span data-stu-id="06d54-206">Workload managers</span></span>

<span data-ttu-id="06d54-207">下面是可在 Azure 基础结构中运行的群集和工作负荷管理器示例。</span><span class="sxs-lookup"><span data-stu-id="06d54-207">The following are examples of cluster and workload managers that can run in Azure infrastructure.</span></span> <span data-ttu-id="06d54-208">在 Azure VM 中创建独立的群集，或从本地群集迸发到 Azure VM。</span><span class="sxs-lookup"><span data-stu-id="06d54-208">Create stand-alone clusters in Azure VMs or burst to Azure VMs from an on-premises cluster.</span></span>

- [<span data-ttu-id="06d54-209">Alces Flight Compute</span><span class="sxs-lookup"><span data-stu-id="06d54-209">Alces Flight Compute</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
- [<span data-ttu-id="06d54-210">TIBCO DataSynapse GridServer</span><span class="sxs-lookup"><span data-stu-id="06d54-210">TIBCO DataSynapse GridServer</span></span>](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/)
- [<span data-ttu-id="06d54-211">Bright Cluster Manager</span><span class="sxs-lookup"><span data-stu-id="06d54-211">Bright Cluster Manager</span></span>](http://www.brightcomputing.com/technology-partners/microsoft)
- [<span data-ttu-id="06d54-212">IBM Spectrum Symphony 和 Symphony LSF</span><span class="sxs-lookup"><span data-stu-id="06d54-212">IBM Spectrum Symphony and Symphony LSF</span></span>](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
- [<span data-ttu-id="06d54-213">PBS Pro</span><span class="sxs-lookup"><span data-stu-id="06d54-213">PBS Pro</span></span>](http://pbspro.org)
- [<span data-ttu-id="06d54-214">Altair</span><span class="sxs-lookup"><span data-stu-id="06d54-214">Altair</span></span>](http://www.altair.com/)
- [<span data-ttu-id="06d54-215">Rescale</span><span class="sxs-lookup"><span data-stu-id="06d54-215">Rescale</span></span>](https://www.rescale.com/azure/)
- [<span data-ttu-id="06d54-216">Microsoft HPC Pack</span><span class="sxs-lookup"><span data-stu-id="06d54-216">Microsoft HPC Pack</span></span>](https://technet.microsoft.com/library/mt744885.aspx)
  - [<span data-ttu-id="06d54-217">适用于 Windows 的 HPC Pack</span><span class="sxs-lookup"><span data-stu-id="06d54-217">HPC Pack for Windows</span></span>](/azure/virtual-machines/windows/hpcpack-cluster-options?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
  - [<span data-ttu-id="06d54-218">适用于 Linux 的 HPC Pack</span><span class="sxs-lookup"><span data-stu-id="06d54-218">HPC Pack for Linux</span></span>](/azure/virtual-machines/linux/hpcpack-cluster-options?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

#### <a name="containers"></a><span data-ttu-id="06d54-219">容器</span><span class="sxs-lookup"><span data-stu-id="06d54-219">Containers</span></span>

<span data-ttu-id="06d54-220">也可使用容器来管理某些 HPC 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="06d54-220">Containers can also be used to manage some HPC workloads.</span></span>  <span data-ttu-id="06d54-221">可以使用 Azure Kubernetes 服务 (AKS) 之类的服务在 Azure 中轻松地部署托管的 Kubernetes 群集。</span><span class="sxs-lookup"><span data-stu-id="06d54-221">Services like the Azure Kubernetes Service (AKS) makes it simple to deploy a managed Kubernetes cluster in Azure.</span></span>

- [<span data-ttu-id="06d54-222">Azure Kubernetes 服务 (AKS)</span><span class="sxs-lookup"><span data-stu-id="06d54-222">Azure Kubernetes Service (AKS)</span></span>](/azure/aks/intro-kubernetes?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-223">容器注册表</span><span class="sxs-lookup"><span data-stu-id="06d54-223">Container Registry</span></span>](/azure/container-registry/container-registry-intro?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="cost-management"></a><span data-ttu-id="06d54-224">成本管理</span><span class="sxs-lookup"><span data-stu-id="06d54-224">Cost management</span></span>

<span data-ttu-id="06d54-225">可以通过多种不同的方式管理 Azure 上的 HPC 成本。</span><span class="sxs-lookup"><span data-stu-id="06d54-225">Managing your HPC cost on Azure can be done through a few different ways.</span></span>  <span data-ttu-id="06d54-226">确保已查看 [Azure 购买选项](https://azure.microsoft.com/pricing/purchase-options/)，找出最适合自己组织的方法。</span><span class="sxs-lookup"><span data-stu-id="06d54-226">Ensure you've reviewed the [Azure purchasing options](https://azure.microsoft.com/pricing/purchase-options/) to find the method that works best for your organization.</span></span>

<span data-ttu-id="06d54-227">[低优先级 VM](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) 可以利用未使用的容量，同时大幅降低成本。</span><span class="sxs-lookup"><span data-stu-id="06d54-227">[Low priority VMs](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) allow you to take advantage of our unutilized capacity at a significant cost savings.</span></span>

## <a name="security"></a><span data-ttu-id="06d54-228">安全</span><span class="sxs-lookup"><span data-stu-id="06d54-228">Security</span></span>

<span data-ttu-id="06d54-229">若要大致了解 Azure 上有关安全性的最佳做法，请参阅 [Azure 安全性文档](/azure/security/azure-security?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)。</span><span class="sxs-lookup"><span data-stu-id="06d54-229">For an overview of security best practices on Azure, review the [Azure Security Documentation](/azure/security/azure-security?context=/azure/architecture/topics/high-performance-computing/context/hpc-context).</span></span>  

<span data-ttu-id="06d54-230">除了[云突发](#hybrid-and-cloud-bursting)部分提供的网络配置，可能还需要实现一项中心辐射型配置，以便隔离计算资源：</span><span class="sxs-lookup"><span data-stu-id="06d54-230">In addition to the network configurations available in the [Cloud Bursting](#hybrid-and-cloud-bursting) section, you may want to implement a hub/spoke configuration to isolate your compute resources:</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/hub-spoke.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="06d54-231">在 Azure 中实现中心辐射型网络拓扑</span><span class="sxs-lookup"><span data-stu-id="06d54-231">Implement a hub-spoke network topology in Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="06d54-232">中心是 Azure 中的一个虚拟网络 (VNet)，充当到本地网络的连接的中心点。</span><span class="sxs-lookup"><span data-stu-id="06d54-232">The hub is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="06d54-233">分支是与中心对等互连的 VNet，可用于隔离工作负荷。</span><span class="sxs-lookup"><span data-stu-id="06d54-233">The spokes are VNets that peer with the hub, and can be used to isolate workloads.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/shared-services?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/shared-services.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="06d54-234">在 Azure 中使用共享服务实现中心辐射型网络拓扑</span><span class="sxs-lookup"><span data-stu-id="06d54-234">Implement a hub-spoke network topology with shared services in Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="06d54-235">此参考体系结构在中心辐射型参考体系结构的基础上生成，在中心包括了可供所有分支使用的共享服务。</span><span class="sxs-lookup"><span data-stu-id="06d54-235">This reference architecture builds on the hub-spoke reference architecture to include shared services in the hub that can be consumed by all spokes.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="hpc-applications"></a><span data-ttu-id="06d54-236">HPC 应用程序</span><span class="sxs-lookup"><span data-stu-id="06d54-236">HPC applications</span></span>

<span data-ttu-id="06d54-237">在 Azure 中运行自定义 HPC 应用程序或商业 HPC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="06d54-237">Run custom or commercial HPC applications in Azure.</span></span> <span data-ttu-id="06d54-238">本部分中的几个示例已成为使用更多 VM 或计算核心高效进行缩放的基准。</span><span class="sxs-lookup"><span data-stu-id="06d54-238">Several examples in this section are benchmarked to scale efficiently with additional VMs or compute cores.</span></span> <span data-ttu-id="06d54-239">请访问 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace)获取随时可供部署的解决方案。</span><span class="sxs-lookup"><span data-stu-id="06d54-239">Visit the [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) for ready-to-deploy solutions.</span></span>

> [!NOTE]
> <span data-ttu-id="06d54-240">请向商业应用程序的供应商咨询有关在云中运行的许可或其他限制。</span><span class="sxs-lookup"><span data-stu-id="06d54-240">Check with the vendor of any commercial application for licensing or other restrictions for running in the cloud.</span></span> <span data-ttu-id="06d54-241">并非所有供应商都提供即用即付许可。</span><span class="sxs-lookup"><span data-stu-id="06d54-241">Not all vendors offer pay-as-you-go licensing.</span></span> <span data-ttu-id="06d54-242">可能需要云中有一个用于自己的解决方案的许可服务器，或连接到本地许可证服务器。</span><span class="sxs-lookup"><span data-stu-id="06d54-242">You might need a licensing server in the cloud for your solution, or connect to an on-premises license server.</span></span>

### <a name="engineering-applications"></a><span data-ttu-id="06d54-243">工程应用程序</span><span class="sxs-lookup"><span data-stu-id="06d54-243">Engineering applications</span></span>

- [<span data-ttu-id="06d54-244">Altair RADIOSS</span><span class="sxs-lookup"><span data-stu-id="06d54-244">Altair RADIOSS</span></span>](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
- [<span data-ttu-id="06d54-245">ANSYS CFD</span><span class="sxs-lookup"><span data-stu-id="06d54-245">ANSYS CFD</span></span>](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
- [<span data-ttu-id="06d54-246">MATLAB 分布式计算服务器</span><span class="sxs-lookup"><span data-stu-id="06d54-246">MATLAB Distributed Computing Server</span></span>](/azure/virtual-machines/windows/matlab-mdcs-cluster?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-247">StarCCM +</span><span class="sxs-lookup"><span data-stu-id="06d54-247">StarCCM+</span></span>](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
- [<span data-ttu-id="06d54-248">OpenFOAM</span><span class="sxs-lookup"><span data-stu-id="06d54-248">OpenFOAM</span></span>](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

### <a name="graphics-and-rendering"></a><span data-ttu-id="06d54-249">图形和渲染</span><span class="sxs-lookup"><span data-stu-id="06d54-249">Graphics and rendering</span></span>

- <span data-ttu-id="06d54-250">Azure Batch 上的 [Autodesk Maya、3ds Max 和 Arnold](/azure/batch/batch-rendering-service?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)</span><span class="sxs-lookup"><span data-stu-id="06d54-250">[Autodesk Maya, 3ds Max, and Arnold](/azure/batch/batch-rendering-service?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) on Azure Batch</span></span>

### <a name="ai-and-deep-learning"></a><span data-ttu-id="06d54-251">AI 和深度学习</span><span class="sxs-lookup"><span data-stu-id="06d54-251">AI and deep learning</span></span>

- [<span data-ttu-id="06d54-252">Microsoft 认知工具包</span><span class="sxs-lookup"><span data-stu-id="06d54-252">Microsoft Cognitive Toolkit</span></span>](/cognitive-toolkit/cntk-on-azure)
- [<span data-ttu-id="06d54-253">深度学习 VM</span><span class="sxs-lookup"><span data-stu-id="06d54-253">Deep Learning VM</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
- [<span data-ttu-id="06d54-254">用于深入学习的 Batch Shipyard 窍门</span><span class="sxs-lookup"><span data-stu-id="06d54-254">Batch Shipyard recipes for deep learning</span></span>](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

### <a name="mpi-providers"></a><span data-ttu-id="06d54-255">MPI 提供程序</span><span class="sxs-lookup"><span data-stu-id="06d54-255">MPI Providers</span></span>

- [<span data-ttu-id="06d54-256">Microsoft MPI</span><span class="sxs-lookup"><span data-stu-id="06d54-256">Microsoft MPI</span></span>](/message-passing-interface/microsoft-mpi)

## <a name="remote-visualization"></a><span data-ttu-id="06d54-257">远程可视化</span><span class="sxs-lookup"><span data-stu-id="06d54-257">Remote visualization</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/infrastructure/linux-vdi-citrix?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/infrastructure/media/azure-citrix-sample-diagram.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="06d54-258">将 Citrix 用于 Linux 虚拟桌面</span><span class="sxs-lookup"><span data-stu-id="06d54-258">Linux virtual desktops with Citrix</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="06d54-259">在 Azure 上使用 Citrix 为 Linux 桌面构建 VDI 环境。</span><span class="sxs-lookup"><span data-stu-id="06d54-259">Build a VDI environment for Linux Desktops using Citrix on Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="performance-benchmarks"></a><span data-ttu-id="06d54-260">性能基准测试</span><span class="sxs-lookup"><span data-stu-id="06d54-260">Performance Benchmarks</span></span>

- [<span data-ttu-id="06d54-261">计算基准测试</span><span class="sxs-lookup"><span data-stu-id="06d54-261">Compute benchmarks</span></span>](/azure/virtual-machines/windows/compute-benchmark-scores?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="customer-stories"></a><span data-ttu-id="06d54-262">客户案例</span><span class="sxs-lookup"><span data-stu-id="06d54-262">Customer stories</span></span>

<span data-ttu-id="06d54-263">许多客户在将 Azure 用于其 HPC 工作负荷时都很成功。</span><span class="sxs-lookup"><span data-stu-id="06d54-263">There are a number of customers who have seen great success by using Azure for their HPC workloads.</span></span>  <span data-ttu-id="06d54-264">可以在下面找到多个这样的客户案例研究：</span><span class="sxs-lookup"><span data-stu-id="06d54-264">You can find a few of these customer case studies below:</span></span>

- [<span data-ttu-id="06d54-265">ANEO</span><span class="sxs-lookup"><span data-stu-id="06d54-265">ANEO</span></span>](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu)
- [<span data-ttu-id="06d54-266">AXA Global P&C</span><span class="sxs-lookup"><span data-stu-id="06d54-266">AXA Global P&C</span></span>](https://customers.microsoft.com/story/axa-global-p-and-c)
- [<span data-ttu-id="06d54-267">Axioma</span><span class="sxs-lookup"><span data-stu-id="06d54-267">Axioma</span></span>](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
- [<span data-ttu-id="06d54-268">d3View</span><span class="sxs-lookup"><span data-stu-id="06d54-268">d3View</span></span>](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
- [<span data-ttu-id="06d54-269">EFS</span><span class="sxs-lookup"><span data-stu-id="06d54-269">EFS</span></span>](https://customers.microsoft.com/story/efs-professionalservices-azure)
- [<span data-ttu-id="06d54-270">Hymans Robertson</span><span class="sxs-lookup"><span data-stu-id="06d54-270">Hymans Robertson</span></span>](https://customers.microsoft.com/story/hymans-robertson)
- [<span data-ttu-id="06d54-271">MetLife</span><span class="sxs-lookup"><span data-stu-id="06d54-271">MetLife</span></span>](https://enterprise.microsoft.com/customer-story/industries/insurance/metlife/)
- [<span data-ttu-id="06d54-272">Microsoft Research</span><span class="sxs-lookup"><span data-stu-id="06d54-272">Microsoft Research</span></span>](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
- [<span data-ttu-id="06d54-273">Milliman</span><span class="sxs-lookup"><span data-stu-id="06d54-273">Milliman</span></span>](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
- [<span data-ttu-id="06d54-274">Mitsubishi UFJ Securities International</span><span class="sxs-lookup"><span data-stu-id="06d54-274">Mitsubishi UFJ Securities International</span></span>](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
- [<span data-ttu-id="06d54-275">NeuroInitiative</span><span class="sxs-lookup"><span data-stu-id="06d54-275">NeuroInitiative</span></span>](https://customers.microsoft.com/story/neuroinitiative-health-provider-azure)
- [<span data-ttu-id="06d54-276">Schlumberger</span><span class="sxs-lookup"><span data-stu-id="06d54-276">Schlumberger</span></span>](https://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
- [<span data-ttu-id="06d54-277">Towers Watson</span><span class="sxs-lookup"><span data-stu-id="06d54-277">Towers Watson</span></span>](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)

## <a name="other-important-information"></a><span data-ttu-id="06d54-278">其他重要信息</span><span class="sxs-lookup"><span data-stu-id="06d54-278">Other important information</span></span>

- <span data-ttu-id="06d54-279">确保在尝试运行大规模工作负荷之前已提高 [vCPU 配额](/azure/virtual-machines/linux/quotas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)。</span><span class="sxs-lookup"><span data-stu-id="06d54-279">Ensure your [vCPU quota](/azure/virtual-machines/linux/quotas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) has been increased before attempting to run large-scale workloads.</span></span>

## <a name="next-steps"></a><span data-ttu-id="06d54-280">后续步骤</span><span class="sxs-lookup"><span data-stu-id="06d54-280">Next steps</span></span>

<span data-ttu-id="06d54-281">有关最新公告，请参阅：</span><span class="sxs-lookup"><span data-stu-id="06d54-281">For the latest announcements, see:</span></span>

- [<span data-ttu-id="06d54-282">Microsoft HPC 和 Batch 团队博客</span><span class="sxs-lookup"><span data-stu-id="06d54-282">Microsoft HPC and Batch team blog</span></span>](http://blogs.technet.com/b/windowshpc/)
- <span data-ttu-id="06d54-283">访问 [Azure 博客](https://azure.microsoft.com/blog/tag/hpc/)。</span><span class="sxs-lookup"><span data-stu-id="06d54-283">Visit the [Azure blog](https://azure.microsoft.com/blog/tag/hpc/).</span></span>

### <a name="microsoft-batch-examples"></a><span data-ttu-id="06d54-284">Microsoft Batch 示例</span><span class="sxs-lookup"><span data-stu-id="06d54-284">Microsoft Batch Examples</span></span>

<span data-ttu-id="06d54-285">这些教程将详细介绍如何在 Microsoft Batch 上运行应用程序</span><span class="sxs-lookup"><span data-stu-id="06d54-285">These tutorials will provide you with details on running applications on Microsoft Batch</span></span>

- [<span data-ttu-id="06d54-286">开始使用 Batch 进行开发</span><span class="sxs-lookup"><span data-stu-id="06d54-286">Get started developing with Batch</span></span>](/azure/batch/quick-run-dotnet?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-287">使用 Azure Batch 代码示例</span><span class="sxs-lookup"><span data-stu-id="06d54-287">Use Azure Batch code samples</span></span>](https://github.com/Azure/azure-batch-samples)
- [<span data-ttu-id="06d54-288">将低优先级 VM 与 Batch 配合使用</span><span class="sxs-lookup"><span data-stu-id="06d54-288">Use low-priority VMs with Batch</span></span>](/azure/batch/batch-low-pri-vms?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="06d54-289">使用 Batch Shipyard 运行容器化的 HPC 工作负荷</span><span class="sxs-lookup"><span data-stu-id="06d54-289">Run containerized HPC workloads with Batch Shipyard</span></span>](https://github.com/Azure/batch-shipyard)
- [<span data-ttu-id="06d54-290">在 Batch 上运行并行 R 工作负荷</span><span class="sxs-lookup"><span data-stu-id="06d54-290">Run parallel R workloads on Batch</span></span>](https://github.com/Azure/doAzureParallel)
- [<span data-ttu-id="06d54-291">在 Batch 上运行按需 Spark 作业</span><span class="sxs-lookup"><span data-stu-id="06d54-291">Run on-demand Spark jobs on Batch</span></span>](https://github.com/Azure/aztk)
- [<span data-ttu-id="06d54-292">使用 Batch 池中的计算密集型 VM</span><span class="sxs-lookup"><span data-stu-id="06d54-292">Use compute-intensive VMs in Batch pools</span></span>](/azure/batch/batch-pool-compute-intensive-sizes?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)