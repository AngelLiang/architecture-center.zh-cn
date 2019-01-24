---
title: 3D 视频渲染
titleSuffix: Azure Example Scenarios
description: 使用 Azure Batch 服务在 Azure 中运行本机 HPC 工作负荷。
author: adamboeglin
ms.date: 07/13/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack
ms.openlocfilehash: ffb400f542b94ed02d1398b2e5e909d79708248b
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485700"
---
# <a name="3d-video-rendering-on-azure"></a><span data-ttu-id="29738-103">Azure 上的 3D 视频渲染</span><span class="sxs-lookup"><span data-stu-id="29738-103">3D video rendering on Azure</span></span>

<span data-ttu-id="29738-104">3D 视频渲染是很费时的过程，需要大量的 CPU 时间才能完成。</span><span class="sxs-lookup"><span data-stu-id="29738-104">3D video rendering is a time consuming process that requires a significant amount of CPU time to complete.</span></span> <span data-ttu-id="29738-105">在一台计算机中，从静态资产生成视频文件可能需要数小时甚至数天，具体取决于所要生成的视频的长度和复杂程度。</span><span class="sxs-lookup"><span data-stu-id="29738-105">On a single machine, the process of generating a video file from static assets can take hours or even days depending on the length and complexity of the video you are producing.</span></span> <span data-ttu-id="29738-106">许多公司会购买昂贵的高端台式机来执行这些任务，或者会投资可以向其提交作业的大型渲染场。</span><span class="sxs-lookup"><span data-stu-id="29738-106">Many companies will purchase either expensive high end desktop computers to perform these tasks, or invest in large render farms that they can submit jobs to.</span></span> <span data-ttu-id="29738-107">但是，如果使用 Azure Batch，则可根据需要随意启用或关闭该功能，根本不需要任何资本投资。</span><span class="sxs-lookup"><span data-stu-id="29738-107">However, by taking advantage of Azure Batch, that power is available to you when you need it and shuts itself down when you don't, all without any capital investment.</span></span>

<span data-ttu-id="29738-108">不管是选择 Windows Server 还是选择 Linux 计算节点，都可以通过 Batch 获得始终如一的管理体验并进行作业计划。</span><span class="sxs-lookup"><span data-stu-id="29738-108">Batch gives you a consistent management experience and job scheduling, whether you select Windows Server or Linux compute nodes.</span></span> <span data-ttu-id="29738-109">有了 Batch，你就可以使用现有的 Windows 或 Linux 应用程序（包括 AutoDesk Maya 和 Blender）在 Azure 中运行大规模的渲染作业。</span><span class="sxs-lookup"><span data-stu-id="29738-109">With Batch, you can use your existing Windows or Linux applications, including AutoDesk Maya and Blender, to run large-scale render jobs in Azure.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="29738-110">相关用例</span><span class="sxs-lookup"><span data-stu-id="29738-110">Relevant use cases</span></span>

<span data-ttu-id="29738-111">其他相关用例包括：</span><span class="sxs-lookup"><span data-stu-id="29738-111">Other relevant use cases include:</span></span>

- <span data-ttu-id="29738-112">3D 建模</span><span class="sxs-lookup"><span data-stu-id="29738-112">3D modeling</span></span>
- <span data-ttu-id="29738-113">Visual FX (VFX) 渲染</span><span class="sxs-lookup"><span data-stu-id="29738-113">Visual FX (VFX) rendering</span></span>
- <span data-ttu-id="29738-114">视频转码</span><span class="sxs-lookup"><span data-stu-id="29738-114">Video transcoding</span></span>
- <span data-ttu-id="29738-115">图像处理、颜色校正和重设大小</span><span class="sxs-lookup"><span data-stu-id="29738-115">Image processing, color correction, and resizing</span></span>

## <a name="architecture"></a><span data-ttu-id="29738-116">体系结构</span><span class="sxs-lookup"><span data-stu-id="29738-116">Architecture</span></span>

![从体系结构的角度概要说明使用 Azure Batch 的云原生 HPC 解决方案中涉及的组件][architecture]

<span data-ttu-id="29738-118">本方案演示了一个使用 Azure Batch 的工作流。</span><span class="sxs-lookup"><span data-stu-id="29738-118">This scenario shows a workflow that uses Azure Batch.</span></span> <span data-ttu-id="29738-119">数据流如下所示：</span><span class="sxs-lookup"><span data-stu-id="29738-119">The data flows as follows:</span></span>

1. <span data-ttu-id="29738-120">将输入文件和处理这些文件的应用程序上传到 Azure 存储帐户。</span><span class="sxs-lookup"><span data-stu-id="29738-120">Upload input files and the applications to process those files to your Azure Storage account.</span></span>
2. <span data-ttu-id="29738-121">创建一个包含 Batch 帐户中的计算节点的 Batch 池、一个用于在池中运行工作负荷的作业，以及作业中的任务。</span><span class="sxs-lookup"><span data-stu-id="29738-121">Create a Batch pool of compute nodes in your Batch account, a job to run the workload on the pool, and tasks in the job.</span></span>
3. <span data-ttu-id="29738-122">将输入文件和应用程序下载到 Batch。</span><span class="sxs-lookup"><span data-stu-id="29738-122">Download input files and the applications to Batch.</span></span>
4. <span data-ttu-id="29738-123">监视任务执行情况。</span><span class="sxs-lookup"><span data-stu-id="29738-123">Monitor task execution.</span></span>
5. <span data-ttu-id="29738-124">上传任务输出。</span><span class="sxs-lookup"><span data-stu-id="29738-124">Upload task output.</span></span>
6. <span data-ttu-id="29738-125">下载输出文件。</span><span class="sxs-lookup"><span data-stu-id="29738-125">Download output files.</span></span>

<span data-ttu-id="29738-126">若要简化此过程，也可使用 [Maya 和 3ds Max 的 Batch 插件][batch-plugins]</span><span class="sxs-lookup"><span data-stu-id="29738-126">To simplify this process, you could also use the [Batch Plugins for Maya and 3ds Max][batch-plugins]</span></span>

### <a name="components"></a><span data-ttu-id="29738-127">组件</span><span class="sxs-lookup"><span data-stu-id="29738-127">Components</span></span>

<span data-ttu-id="29738-128">Azure Batch 基于以下 Azure 技术：</span><span class="sxs-lookup"><span data-stu-id="29738-128">Azure Batch builds upon the following Azure technologies:</span></span>

- <span data-ttu-id="29738-129">[虚拟网络](/azure/virtual-network/virtual-networks-overview)用于头节点和计算资源。</span><span class="sxs-lookup"><span data-stu-id="29738-129">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) are used for both the head node and the compute resources.</span></span>
- <span data-ttu-id="29738-130">[Azure 存储帐户](/azure/storage/common/storage-introduction)用于同步和数据保留。</span><span class="sxs-lookup"><span data-stu-id="29738-130">[Azure Storage accounts](/azure/storage/common/storage-introduction) are used for synchronization and data retention.</span></span>
- <span data-ttu-id="29738-131">[虚拟机规模集][vmss]由 CycleCloud 用于计算资源。</span><span class="sxs-lookup"><span data-stu-id="29738-131">[Virtual Machine Scale Sets][vmss] are used by CycleCloud for compute resources.</span></span>

## <a name="considerations"></a><span data-ttu-id="29738-132">注意事项</span><span class="sxs-lookup"><span data-stu-id="29738-132">Considerations</span></span>

### <a name="machine-sizes-available-for-azure-batch"></a><span data-ttu-id="29738-133">适用于 Azure Batch 的计算机大小</span><span class="sxs-lookup"><span data-stu-id="29738-133">Machine Sizes available for Azure Batch</span></span>

<span data-ttu-id="29738-134">虽然大多数渲染客户会选择 CPU 功率高的资源，但是其他使用虚拟机规模集的工作负荷可能会按其他标准来选择 VM，具体取决于许多因素：</span><span class="sxs-lookup"><span data-stu-id="29738-134">While most rendering customers will choose resources with high CPU power, other workloads using virtual machine scale sets may choose VMs differently and will depend on a number of factors:</span></span>

- <span data-ttu-id="29738-135">所运行的应用程序是否进行内存绑定？</span><span class="sxs-lookup"><span data-stu-id="29738-135">Is the application being run memory bound?</span></span>
- <span data-ttu-id="29738-136">应用程序是否需要使用 GPU？</span><span class="sxs-lookup"><span data-stu-id="29738-136">Does the application need to use GPUs?</span></span>
- <span data-ttu-id="29738-137">对于紧密耦合的作业，作业类型是否只能采用不得已的并行方式？或者需要 Infiniband 连接？</span><span class="sxs-lookup"><span data-stu-id="29738-137">Are the job types embarrassingly parallel or require infiniband connectivity for tightly coupled jobs?</span></span>
- <span data-ttu-id="29738-138">需要通过快速 I/O 来访问计算节点上的存储。</span><span class="sxs-lookup"><span data-stu-id="29738-138">Require fast I/O to access storage on the compute Nodes.</span></span>

<span data-ttu-id="29738-139">Azure 有各种 VM 大小，每种都符合上述应用程序要求，某些是针对 HPC 的，但即使是最小的，也可以提供有效的网格实现：</span><span class="sxs-lookup"><span data-stu-id="29738-139">Azure has a wide range of VM sizes that can address each and every one of the above application requirements, some are specific to HPC, but even the smallest sizes can be utilized to provide an effective grid implementation:</span></span>

- <span data-ttu-id="29738-140">[HPC VM 大小][compute-hpc]：考虑到渲染受 CPU 限制，Microsoft 通常会建议使用 Azure H 系列 VM。</span><span class="sxs-lookup"><span data-stu-id="29738-140">[HPC VM sizes][compute-hpc] Due to the CPU bound nature of rendering, Microsoft typically suggests the Azure H-Series VMs.</span></span> <span data-ttu-id="29738-141">此类 VM 是专门针对高端计算需求构建的，提供 8 核和 16 核 vCPU 大小，采用 DDR4 内存、SSD 临时存储以及 Haswell E5 Intel 技术。</span><span class="sxs-lookup"><span data-stu-id="29738-141">This type of VM is built specifically for high end computational needs, they have 8 and 16 core vCPU sizes available, and features DDR4 memory, SSD temporary storage, and Haswell E5 Intel technology.</span></span>
- <span data-ttu-id="29738-142">[GPU VM 大小][compute-gpu]：GPU 优化 VM 大小是具有单个或多个 NVIDIA GPU 的专用虚拟机。</span><span class="sxs-lookup"><span data-stu-id="29738-142">[GPU VM sizes][compute-gpu] GPU optimized VM sizes are specialized virtual machines available with single or multiple NVIDIA GPUs.</span></span> <span data-ttu-id="29738-143">这些大小是针对计算密集型、图形密集型和可视化工作负荷设计的。</span><span class="sxs-lookup"><span data-stu-id="29738-143">These sizes are designed for compute-intensive, graphics-intensive, and visualization workloads.</span></span>
- <span data-ttu-id="29738-144">NC、NCv2、NCv3 和 ND 大小针对计算密集型和网络密集型应用程序和算法进行了优化，包括基于 CUDA 和 OpenCL 的应用程序和模拟、AI 以及深度学习。</span><span class="sxs-lookup"><span data-stu-id="29738-144">NC, NCv2, NCv3, and ND sizes are optimized for compute-intensive and network-intensive applications and algorithms, including CUDA and OpenCL-based applications and simulations, AI, and Deep Learning.</span></span> <span data-ttu-id="29738-145">NV 大小已针对远程可视化效果、流式处理、游戏、编码和 VDI 方案进行了优化和设计，使用 OpenGL 和 DirectX 之类的框架。</span><span class="sxs-lookup"><span data-stu-id="29738-145">NV sizes are optimized and designed for remote visualization, streaming, gaming, encoding, and VDI scenarios utilizing frameworks such as OpenGL and DirectX.</span></span>
- <span data-ttu-id="29738-146">[内存优化型 VM 大小][compute-memory]：需要更多内存时，内存优化型 VM 大小的内存/CPU 比率更高。</span><span class="sxs-lookup"><span data-stu-id="29738-146">[Memory optimized VM sizes][compute-memory] When more memory is required, the memory optimized VM sizes offer a higher memory-to-CPU ratio.</span></span>
- <span data-ttu-id="29738-147">[常规用途 VM 大小][compute-general]：常规用途 VM 大小也可使用，提供均衡的 CPU/内存比率。</span><span class="sxs-lookup"><span data-stu-id="29738-147">[General purposes VM sizes][compute-general] General-purpose VM sizes are also available and provide balanced CPU-to-memory ratio.</span></span>

### <a name="alternatives"></a><span data-ttu-id="29738-148">备选项</span><span class="sxs-lookup"><span data-stu-id="29738-148">Alternatives</span></span>

<span data-ttu-id="29738-149">如果需要对 Azure 中的渲染环境进行更多的控制，或者需要混合实现，则可使用 CycleCloud 计算功能来协调云中的 IaaS 网格。</span><span class="sxs-lookup"><span data-stu-id="29738-149">If you require more control over your rendering environment in Azure or need a hybrid implementation, then CycleCloud computing can help orchestrate an IaaS grid in the cloud.</span></span> <span data-ttu-id="29738-150">它使用与 Azure Batch 相同的基础 Azure 技术，使生成和维护 IaaS 网格变得很高效。</span><span class="sxs-lookup"><span data-stu-id="29738-150">Using the same underlying Azure technologies as Azure Batch, it makes building and maintaining an IaaS grid an efficient process.</span></span> <span data-ttu-id="29738-151">若要查找更多信息并了解设计原则，请查看以下链接：</span><span class="sxs-lookup"><span data-stu-id="29738-151">To find out more and learn about the design principles use the following link:</span></span>

<span data-ttu-id="29738-152">有关 Azure 中提供的所有 HPC 解决方案的完整概述，请参阅[使用 Azure VM 的 HPC、Batch 和大计算解决方案][hpc-alt-solutions]一文</span><span class="sxs-lookup"><span data-stu-id="29738-152">For a complete overview of all the HPC solutions that are available to you in Azure, see the article [HPC, Batch, and Big Compute solutions using Azure VMs][hpc-alt-solutions]</span></span>

### <a name="availability"></a><span data-ttu-id="29738-153">可用性</span><span class="sxs-lookup"><span data-stu-id="29738-153">Availability</span></span>

<span data-ttu-id="29738-154">可以通过一系列的服务、工具和 API 监视 Azure Batch 组件。</span><span class="sxs-lookup"><span data-stu-id="29738-154">Monitoring of the Azure Batch components is available through a range of services, tools, and APIs.</span></span> <span data-ttu-id="29738-155">监视功能在[监视 Batch 解决方案][batch-monitor]一文中有进一步的介绍。</span><span class="sxs-lookup"><span data-stu-id="29738-155">Monitoring is discussed further in the [Monitor Batch solutions][batch-monitor] article.</span></span>

### <a name="scalability"></a><span data-ttu-id="29738-156">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="29738-156">Scalability</span></span>

<span data-ttu-id="29738-157">Azure Batch 帐户中的池可以通过手动干预进行缩放，也可以通过基于 Azure Batch 指标的公式进行自动缩放。</span><span class="sxs-lookup"><span data-stu-id="29738-157">Pools within an Azure Batch account can either scale through manual intervention or, by using a formula based on Azure Batch metrics, be scaled automatically.</span></span> <span data-ttu-id="29738-158">有关可伸缩性的详细信息，请参阅[创建用于缩放 Batch 池中的节点的自动缩放公式][batch-scaling]一文。</span><span class="sxs-lookup"><span data-stu-id="29738-158">For more information on scalability, see the article [Create an automatic scaling formula for scaling nodes in a Batch pool][batch-scaling].</span></span>

### <a name="security"></a><span data-ttu-id="29738-159">安全</span><span class="sxs-lookup"><span data-stu-id="29738-159">Security</span></span>

<span data-ttu-id="29738-160">若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="29738-160">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="29738-161">复原</span><span class="sxs-lookup"><span data-stu-id="29738-161">Resiliency</span></span>

<span data-ttu-id="29738-162">虽然 Azure Batch 中目前没有故障转移功能，但建议你执行以下步骤，确保发生计划外中断时的可用性：</span><span class="sxs-lookup"><span data-stu-id="29738-162">While there is currently no failover capability in Azure Batch, we recommend using the following steps to ensure availability if there is an unplanned outage:</span></span>

- <span data-ttu-id="29738-163">使用备用的存储帐户在备用的 Azure 位置创建一个 Azure Batch 帐户</span><span class="sxs-lookup"><span data-stu-id="29738-163">Create an Azure Batch account in an alternate Azure location with an alternate Storage Account</span></span>
- <span data-ttu-id="29738-164">使用同一名称创建相同的节点池，不分配任何节点</span><span class="sxs-lookup"><span data-stu-id="29738-164">Create the same node pools with the same name, with zero nodes allocated</span></span>
- <span data-ttu-id="29738-165">确保通过备用存储帐户创建并更新应用程序</span><span class="sxs-lookup"><span data-stu-id="29738-165">Ensure Applications are created and updated to the alternate Storage Account</span></span>
- <span data-ttu-id="29738-166">将输入文件上传到备用的 Azure Batch 帐户，提交作业时也提交到该帐户</span><span class="sxs-lookup"><span data-stu-id="29738-166">Upload input files and submit jobs to the alternate Azure Batch account</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="29738-167">部署方案</span><span class="sxs-lookup"><span data-stu-id="29738-167">Deploy the scenario</span></span>

### <a name="create-an-azure-batch-account-and-pools-manually"></a><span data-ttu-id="29738-168">手动创建 Azure Batch 帐户和池</span><span class="sxs-lookup"><span data-stu-id="29738-168">Create an Azure Batch account and pools manually</span></span>

<span data-ttu-id="29738-169">本方案演示了 Azure Batch 工作方式，并将 Azure Batch Labs 以示例 SaaS 解决方案（可以为你自己的客户开发）的方式进行了演示：</span><span class="sxs-lookup"><span data-stu-id="29738-169">This scenario demonstrates how Azure Batch works while showcasing Azure Batch Labs as an example SaaS solution that can be developed for your own customers:</span></span>

<span data-ttu-id="29738-170">[Azure Batch Masterclass][batch-labs-masterclass]</span><span class="sxs-lookup"><span data-stu-id="29738-170">[Azure Batch Masterclass][batch-labs-masterclass]</span></span>

### <a name="deploy-the-components"></a><span data-ttu-id="29738-171">部署组件</span><span class="sxs-lookup"><span data-stu-id="29738-171">Deploy the components</span></span>

<span data-ttu-id="29738-172">此模板将部署：</span><span class="sxs-lookup"><span data-stu-id="29738-172">The template will deploy:</span></span>

- <span data-ttu-id="29738-173">一个新的 Azure Batch 帐户</span><span class="sxs-lookup"><span data-stu-id="29738-173">A new Azure Batch account</span></span>
- <span data-ttu-id="29738-174">一个存储帐户</span><span class="sxs-lookup"><span data-stu-id="29738-174">A storage account</span></span>
- <span data-ttu-id="29738-175">一个与 Batch 帐户关联的节点池</span><span class="sxs-lookup"><span data-stu-id="29738-175">A node pool associated with the batch account</span></span>
- <span data-ttu-id="29738-176">节点池将会配置为对 Canonical Ubuntu 映像使用 A2 v2 VM</span><span class="sxs-lookup"><span data-stu-id="29738-176">The node pool will be configured to use A2 v2 VMs with Canonical Ubuntu images</span></span>
- <span data-ttu-id="29738-177">节点池一开始包含零个 VM，需通过手动缩放来添加 VM</span><span class="sxs-lookup"><span data-stu-id="29738-177">The node pool will contain zero VMs initially and will require you to manually scale to add VMs</span></span>

<!-- markdownlint-disable MD033 -->

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fhpc%2Fbatchcreatewithpools.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>
<!-- markdownlint-enable MD033 -->

<span data-ttu-id="29738-178">[详细了解资源管理器模板][azure-arm-templates]</span><span class="sxs-lookup"><span data-stu-id="29738-178">[Learn more about Resource Manager templates][azure-arm-templates]</span></span>

## <a name="pricing"></a><span data-ttu-id="29738-179">定价</span><span class="sxs-lookup"><span data-stu-id="29738-179">Pricing</span></span>

<span data-ttu-id="29738-180">Azure Batch 的使用费用取决于用于池的 VM 大小以及这些 VM 的分配和运行时长。创建 Azure Batch 帐户没有相关费用。</span><span class="sxs-lookup"><span data-stu-id="29738-180">The cost of using Azure Batch will depend on the VM sizes that are used for the pools and how long these VMs are allocated and running, there is no cost associated with an Azure Batch account creation.</span></span> <span data-ttu-id="29738-181">存储和数据出口应考虑在内，因为这些需额外付费。</span><span class="sxs-lookup"><span data-stu-id="29738-181">Storage and data egress should be taken into account as these will apply additional costs.</span></span>

<span data-ttu-id="29738-182">下面以示例方式说明了一个在 8 小时内完成的作业在使用不同数目的服务器时可能会产生的费用：</span><span class="sxs-lookup"><span data-stu-id="29738-182">The following are examples of costs that could be incurred for a job that completes in 8 hours using a different number of servers:</span></span>

- <span data-ttu-id="29738-183">100 台高性能 CPU VM：[成本估算][hpc-est-high]</span><span class="sxs-lookup"><span data-stu-id="29738-183">100 High-Performance CPU VMs: [Cost Estimate][hpc-est-high]</span></span>

  <span data-ttu-id="29738-184">100 x H16m（16 核，225 GB RAM，高级存储 512 GB），2 TB Blob 存储，1 TB 出口</span><span class="sxs-lookup"><span data-stu-id="29738-184">100 x H16m (16 cores, 225 GB RAM, Premium Storage 512 GB), 2 TB Blob Storage, 1-TB egress</span></span>

- <span data-ttu-id="29738-185">50 台高性能 CPU VM：[成本估算][hpc-est-med]</span><span class="sxs-lookup"><span data-stu-id="29738-185">50 High-Performance CPU VMs: [Cost Estimate][hpc-est-med]</span></span>

  <span data-ttu-id="29738-186">50 x H16m（16 核，225 GB RAM，高级存储 512 GB），2 TB Blob 存储，1 TB 出口</span><span class="sxs-lookup"><span data-stu-id="29738-186">50 x H16m (16 cores, 225 GB RAM, Premium Storage 512 GB), 2 TB Blob Storage, 1-TB egress</span></span>

- <span data-ttu-id="29738-187">10 台高性能 CPU VM：[成本估算][hpc-est-low]</span><span class="sxs-lookup"><span data-stu-id="29738-187">10 High-Performance CPU VMs: [Cost Estimate][hpc-est-low]</span></span>

  <span data-ttu-id="29738-188">10 x H16m（16 核，225 GB RAM，高级存储 512 GB），2 TB Blob 存储，1 TB 出口</span><span class="sxs-lookup"><span data-stu-id="29738-188">10 x H16m (16 cores, 225 GB RAM, Premium Storage 512 GB), 2 TB Blob Storage, 1-TB egress</span></span>

### <a name="pricing-for-low-priority-vms"></a><span data-ttu-id="29738-189">低优先级 VM 的定价</span><span class="sxs-lookup"><span data-stu-id="29738-189">Pricing for low-priority VMs</span></span>

<span data-ttu-id="29738-190">Azure Batch 也支持在节点池中使用低优先级 VM，这可能会节省大量费用。</span><span class="sxs-lookup"><span data-stu-id="29738-190">Azure Batch also supports the use of low-priority VMs in the node pools, which can potentially provide a substantial cost saving.</span></span> <span data-ttu-id="29738-191">有关详细信息，包括在标准 VM 和低优先级 VM 之间的价格比较，请参阅 [Azure Batch 定价][batch-pricing]。</span><span class="sxs-lookup"><span data-stu-id="29738-191">For more information, including a price comparison between standard VMs and low-priority VMs, see [Azure Batch Pricing][batch-pricing].</span></span>

> [!NOTE]
> <span data-ttu-id="29738-192">低优先级 VM 仅适用于某些应用程序和工作负荷。</span><span class="sxs-lookup"><span data-stu-id="29738-192">Low-priority VMs are only suitable for certain applications and workloads.</span></span>

## <a name="related-resources"></a><span data-ttu-id="29738-193">相关资源</span><span class="sxs-lookup"><span data-stu-id="29738-193">Related resources</span></span>

<span data-ttu-id="29738-194">[Azure Batch 概述][batch-overview]</span><span class="sxs-lookup"><span data-stu-id="29738-194">[Azure Batch Overview][batch-overview]</span></span>

<span data-ttu-id="29738-195">[Azure Batch 文档][batch-doc]</span><span class="sxs-lookup"><span data-stu-id="29738-195">[Azure Batch Documentation][batch-doc]</span></span>

<span data-ttu-id="29738-196">[在 Azure Batch 上使用容器][batch-containers]</span><span class="sxs-lookup"><span data-stu-id="29738-196">[Using containers on Azure Batch][batch-containers]</span></span>

<!-- links -->
[architecture]: ./media/architecture-video-rendering.png
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[resiliency]: /azure/architecture/resiliency/
[scalability]: /azure/architecture/checklist/scalability
[vmss]: /azure/virtual-machine-scale-sets/overview
[storage]: https://azure.microsoft.com/services/storage/
[batch]: https://azure.microsoft.com/services/batch/
[batch-arch]: https://azure.microsoft.com/solutions/architecture/big-compute-with-azure-batch/
[compute-hpc]: /azure/virtual-machines/windows/sizes-hpc
[compute-gpu]: /azure/virtual-machines/windows/sizes-gpu
[compute-compute]: /azure/virtual-machines/windows/sizes-compute
[compute-memory]: /azure/virtual-machines/windows/sizes-memory
[compute-general]: /azure/virtual-machines/windows/sizes-general
[compute-storage]: /azure/virtual-machines/windows/sizes-storage
[compute-acu]: /azure/virtual-machines/windows/acu
[compute=benchmark]: /azure/virtual-machines/windows/compute-benchmark-scores
[hpc-est-high]: https://azure.com/e/9ac25baf44ef49c3a6b156935ee9544c
[hpc-est-med]: https://azure.com/e/0286f1d6f6784310af4dcda5aec8c893
[hpc-est-low]: https://azure.com/e/e39afab4e71949f9bbabed99b428ba4a
[batch-labs-masterclass]: https://github.com/azurebigcompute/BigComputeLabs/tree/master/Azure%20Batch%20Masterclass%20Labs
[batch-scaling]: /azure/batch/batch-automatic-scaling
[hpc-alt-solutions]: /azure/virtual-machines/linux/high-performance-computing?toc=%2fazure%2fbatch%2ftoc.json
[batch-monitor]: /azure/batch/monitoring-overview
[batch-pricing]: https://azure.microsoft.com/pricing/details/batch/
[batch-doc]: /azure/batch/
[batch-overview]: https://azure.microsoft.com/services/batch/
[batch-containers]: https://github.com/Azure/batch-shipyard
[azure-arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[batch-plugins]: /azure/batch/batch-rendering-service#options-for-submitting-a-render-job