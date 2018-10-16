---
title: 在 Azure 上运行计算流体动力学 (CFD) 仿真
description: 在 Azure 上执行计算流体动力学 (CFD) 仿真。
author: mikewarr
ms.date: 09/20/2018
ms.openlocfilehash: 5734e6fe707e3beb5e23f2ad2b4344ba289803bb
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818560"
---
# <a name="running-computational-fluid-dynamics-cfd-simulations-on-azure"></a><span data-ttu-id="e495f-103">在 Azure 上运行计算流体动力学 (CFD) 仿真</span><span class="sxs-lookup"><span data-stu-id="e495f-103">Running computational fluid dynamics (CFD) simulations on Azure</span></span>

<span data-ttu-id="e495f-104">计算流体动力学 (CFD) 仿真需要大量的计算时间，并需要专业化的硬件。</span><span class="sxs-lookup"><span data-stu-id="e495f-104">Computational Fluid Dynamics (CFD) simulations require significant compute time along with specialized hardware.</span></span> <span data-ttu-id="e495f-105">随着群集用量的增加，仿真时间和总体网格用量也会增加，导致备用容量出现问题，排队时间延长。</span><span class="sxs-lookup"><span data-stu-id="e495f-105">As cluster usage increases, simulation times and overall grid use grow, leading to issues with spare capacity and long queue times.</span></span> <span data-ttu-id="e495f-106">添加物理硬件可能费用高昂，并且不一定与企业经历的用量高峰与低谷相一致。</span><span class="sxs-lookup"><span data-stu-id="e495f-106">Adding physical hardware can be expensive, and may not align to the usage peaks and valleys that a business goes through.</span></span> <span data-ttu-id="e495f-107">利用 Azure 可以化解其中的许多难题，而且不需要额外的资本性支出。</span><span class="sxs-lookup"><span data-stu-id="e495f-107">By taking advantage of Azure, many of these challenges can be overcome with no capital expenditure.</span></span>

<span data-ttu-id="e495f-108">Azure 提供所需的硬件用于在 GPU 和 CPU 虚拟机上运行 CFD 作业。</span><span class="sxs-lookup"><span data-stu-id="e495f-108">Azure provides the hardware you need to run your CFD jobs on both GPU and CPU virtual machines.</span></span> <span data-ttu-id="e495f-109">支持 RDMA（远程直接内存访问）的 VM 大小提供基于 FDR InfiniBand 的网络功能，可以实现低延迟 MPI（消息传递接口）通信。</span><span class="sxs-lookup"><span data-stu-id="e495f-109">RDMA (Remote Direct Memory Access) enabled VM sizes have FDR InfiniBand-based networking which allows for low latency MPI (Message Passing Interface) communication.</span></span> <span data-ttu-id="e495f-110">结合提供企业级群集文件系统的 Avere vFXT，客户可以确保为 Azure 中的读取操作提供最大吞吐量。</span><span class="sxs-lookup"><span data-stu-id="e495f-110">Combined with the Avere vFXT, which provides an enterprise-scale clustered file system, customers can ensure maximum throughput for read operations in Azure.</span></span>

<span data-ttu-id="e495f-111">为了简化 HPC 群集的创建、管理和优化，可以使用 Azure CycleCloud 来预配群集以及协调混合方案与云方案中的数据。</span><span class="sxs-lookup"><span data-stu-id="e495f-111">To simplify the creation, management, and optimization of HPC clusters, Azure CycleCloud can be used to provision clusters and orchestrate data in both hybrid and cloud scenarios.</span></span> <span data-ttu-id="e495f-112">通过监视挂起的作业，CycleCloud 可以连接到所选的工作负荷计划程序，自动按需启动计算资源，而你只需支付所用资源的费用。</span><span class="sxs-lookup"><span data-stu-id="e495f-112">By monitoring the pending jobs, CycleCloud will automatically launch on-demand compute, where you only pay for what you use, connected to the workload scheduler of your choice.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="e495f-113">相关用例</span><span class="sxs-lookup"><span data-stu-id="e495f-113">Relevant use cases</span></span>

<span data-ttu-id="e495f-114">对于以下可以使用 CFD 应用程序的行业，请考虑此方案：</span><span class="sxs-lookup"><span data-stu-id="e495f-114">Consider this scenario for these industries where CFD applications could be used:</span></span>

* <span data-ttu-id="e495f-115">航空</span><span class="sxs-lookup"><span data-stu-id="e495f-115">Aeronautics</span></span>
* <span data-ttu-id="e495f-116">汽车</span><span class="sxs-lookup"><span data-stu-id="e495f-116">Automotive</span></span>
* <span data-ttu-id="e495f-117">建筑 HVAC</span><span class="sxs-lookup"><span data-stu-id="e495f-117">Building HVAC</span></span>
* <span data-ttu-id="e495f-118">石油和天然气</span><span class="sxs-lookup"><span data-stu-id="e495f-118">Oil and gas</span></span>
* <span data-ttu-id="e495f-119">生命科学</span><span class="sxs-lookup"><span data-stu-id="e495f-119">Life sciences</span></span>

## <a name="architecture"></a><span data-ttu-id="e495f-120">体系结构</span><span class="sxs-lookup"><span data-stu-id="e495f-120">Architecture</span></span>

![体系结构关系图][architecture]

<span data-ttu-id="e495f-122">此图从较高层面显示了针对 Azure 中的按需节点提供作业监视的典型混合设计的概况：</span><span class="sxs-lookup"><span data-stu-id="e495f-122">This diagram shows a high-level overview of a typical hybrid design providing job monitoring of the on-demand nodes in Azure:</span></span>

1. <span data-ttu-id="e495f-123">连接到 Azure CycleCloud 服务器以配置群集。</span><span class="sxs-lookup"><span data-stu-id="e495f-123">Connect to the Azure CycleCloud server to configure the cluster.</span></span>
2. <span data-ttu-id="e495f-124">使用用于 MPI 的支持 RDMA 的计算机配置和创建群集头节点。</span><span class="sxs-lookup"><span data-stu-id="e495f-124">Configure and create the cluster head node, using RDMA enabled machines for MPI.</span></span>
3. <span data-ttu-id="e495f-125">添加和配置本地头节点。</span><span class="sxs-lookup"><span data-stu-id="e495f-125">Add and configure the on-premises head node.</span></span>
4. <span data-ttu-id="e495f-126">如果资源不足，Azure CycleCloud 将纵向扩展（或缩减）Azure 中的计算资源。</span><span class="sxs-lookup"><span data-stu-id="e495f-126">If there are insufficient resources, Azure CycleCloud will scale up (or down) compute resources in Azure.</span></span> <span data-ttu-id="e495f-127">可以定义预先确定的限制来防止过度分配。</span><span class="sxs-lookup"><span data-stu-id="e495f-127">A predetermined limit can be defined to prevent over allocation.</span></span>
5. <span data-ttu-id="e495f-128">分配给执行节点的任务。</span><span class="sxs-lookup"><span data-stu-id="e495f-128">Tasks allocated to the execute nodes.</span></span>
6. <span data-ttu-id="e495f-129">在 Azure 中缓存的、来自本地 NFS 服务器的数据。</span><span class="sxs-lookup"><span data-stu-id="e495f-129">Data cached in Azure from on-premises NFS server.</span></span>
7. <span data-ttu-id="e495f-130">在 Azure 中缓存的、从 Avere vFXT 读入的数据。</span><span class="sxs-lookup"><span data-stu-id="e495f-130">Data read in from the Avere vFXT for Azure cache.</span></span>
8. <span data-ttu-id="e495f-131">中继到 Azure CycleCloud 服务器的作业和任务信息。</span><span class="sxs-lookup"><span data-stu-id="e495f-131">Job and task information relayed to the Azure CycleCloud server.</span></span>

### <a name="components"></a><span data-ttu-id="e495f-132">组件</span><span class="sxs-lookup"><span data-stu-id="e495f-132">Components</span></span>

* <span data-ttu-id="e495f-133">[Azure CycleCloud][cyclecloud] 是用于在 Azure 中创建、管理、操作和优化 HPC 与大型计算群集的工具。</span><span class="sxs-lookup"><span data-stu-id="e495f-133">[Azure CycleCloud][cyclecloud] a tool for creating, managing, operating, and optimizing HPC and Big Compute clusters in Azure.</span></span>
* <span data-ttu-id="e495f-134">[Avere vFXT on Azure][avere] 用于提供针对云生成的企业级群集文件系统。</span><span class="sxs-lookup"><span data-stu-id="e495f-134">[Avere vFXT on Azure][avere] is used to provide an enterprise-scale clustered file system built for the cloud.</span></span>
* <span data-ttu-id="e495f-135">[Azure 虚拟机 (VM)][vms] 用于创建一组静态计算实例。</span><span class="sxs-lookup"><span data-stu-id="e495f-135">[Azure Virtual Machines (VMs)][vms] are used to create a static set of compute instances.</span></span>
* <span data-ttu-id="e495f-136">[虚拟机规模集][vmss]提供一组可由 Azure CycleCloud 纵向扩展或缩减的相同 VM。</span><span class="sxs-lookup"><span data-stu-id="e495f-136">[Virtual Machine Scale Sets (virtual machine scale set)][vmss] provide a group of identical VMs capable of being scaled up or down by Azure CycleCloud.</span></span>
* <span data-ttu-id="e495f-137">[Azure 存储帐户](/azure/storage/common/storage-introduction)用于同步和数据保留。</span><span class="sxs-lookup"><span data-stu-id="e495f-137">[Azure Storage accounts](/azure/storage/common/storage-introduction) are used for synchronization and data retention.</span></span>
* <span data-ttu-id="e495f-138">[虚拟网络](/azure/virtual-network/virtual-networks-overview)允许许多类型的 Azure 资源（例如 Azure 虚拟机 (VM)）以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。</span><span class="sxs-lookup"><span data-stu-id="e495f-138">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) enable many types of Azure resources, such as Azure Virtual Machines (VMs), to securely communicate with each other, the internet, and on-premises networks.</span></span>

### <a name="alternatives"></a><span data-ttu-id="e495f-139">备选项</span><span class="sxs-lookup"><span data-stu-id="e495f-139">Alternatives</span></span>

<span data-ttu-id="e495f-140">客户还可以完全使用 Azure CycleCloud 在 Azure 中创建网格。</span><span class="sxs-lookup"><span data-stu-id="e495f-140">Customers can also use Azure CycleCloud to create a grid entirely in Azure.</span></span> <span data-ttu-id="e495f-141">在此设置中，Azure CycleCloud 服务器在 Azure 订阅中运行。</span><span class="sxs-lookup"><span data-stu-id="e495f-141">In this setup, the Azure CycleCloud server is run within your Azure subscription.</span></span>

<span data-ttu-id="e495f-142">使用不需要管理工作负荷计划程序的新式应用程序方法时，[Azure Batch][batch] 可能有所帮助。</span><span class="sxs-lookup"><span data-stu-id="e495f-142">For a modern application approach where management of a workload scheduler is not needed, [Azure Batch][batch] can help.</span></span> <span data-ttu-id="e495f-143">Azure Batch 可在云中高效运行大规模并行和高性能计算 (HPC) 应用程序。</span><span class="sxs-lookup"><span data-stu-id="e495f-143">Azure Batch can run large-scale parallel and high-performance computing (HPC) applications efficiently in the cloud.</span></span> <span data-ttu-id="e495f-144">使用 Azure Batch 可以定义 Azure 计算资源，以并行执行或大规模执行应用程序，无需手动配置或管理基础结构。</span><span class="sxs-lookup"><span data-stu-id="e495f-144">Azure Batch allows you to define the Azure compute resources to execute your applications in parallel or at scale without manually configuring or managing infrastructure.</span></span> <span data-ttu-id="e495f-145">Azure Batch 按要求计划计算密集型任务并动态添加或删除计算资源。</span><span class="sxs-lookup"><span data-stu-id="e495f-145">Azure Batch schedules compute-intensive tasks and dynamically adds and removes compute resources based on your requirements.</span></span>

### <a name="scalability-and-security"></a><span data-ttu-id="e495f-146">可伸缩性和安全性</span><span class="sxs-lookup"><span data-stu-id="e495f-146">Scalability, and Security</span></span>

<span data-ttu-id="e495f-147">可以手动或使用自动缩放来缩放 Azure CycleCloud 上的执行节点。</span><span class="sxs-lookup"><span data-stu-id="e495f-147">Scaling the execute nodes on Azure CycleCloud can be accomplished either manually or using autoscaling.</span></span> <span data-ttu-id="e495f-148">有关详细信息，请参阅 [CycleCloud 自动缩放][cycle-scale]。</span><span class="sxs-lookup"><span data-stu-id="e495f-148">For more information, see [CycleCloud Autoscaling][cycle-scale].</span></span>

<span data-ttu-id="e495f-149">有关设计安全解决方案的一般性指导，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="e495f-149">For general guidance on designing secure solutions, see the [Azure security documentation][security].</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="e495f-150">部署此方案</span><span class="sxs-lookup"><span data-stu-id="e495f-150">Deploy this scenario</span></span>

<span data-ttu-id="e495f-151">在 Azure 中部署之前，需要满足一些先决条件。</span><span class="sxs-lookup"><span data-stu-id="e495f-151">Before deploying in Azure, some pre-requisites are required.</span></span> <span data-ttu-id="e495f-152">在部署资源管理器模板之前，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="e495f-152">Follow these steps before deploying the Resource Manager template:</span></span>
1. <span data-ttu-id="e495f-153">创建一个[服务主体][cycle-svcprin]用于检索 appId、displayName、名称、密码和租户。</span><span class="sxs-lookup"><span data-stu-id="e495f-153">Create a [service principal][cycle-svcprin] for retrieving the appId, displayName, name, password, and tenant.</span></span>
2. <span data-ttu-id="e495f-154">生成一个 [SSH 密钥对][cycle-ssh]，以安全登录到 CycleCloud 服务器。</span><span class="sxs-lookup"><span data-stu-id="e495f-154">Generate an [SSH key pair][cycle-ssh] to sign in securely to the CycleCloud server.</span></span>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FCycleCloudCommunity%2Fcyclecloud_arm%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

3. <span data-ttu-id="e495f-155">[登录到 CycleCloud 服务器][cycle-login]，以配置和创建新群集。</span><span class="sxs-lookup"><span data-stu-id="e495f-155">[Log into the CycleCloud server][cycle-login] to configure and create a new cluster.</span></span>
4. <span data-ttu-id="e495f-156">[创建群集][cycle-create]。</span><span class="sxs-lookup"><span data-stu-id="e495f-156">[Create a cluster][cycle-create].</span></span>

<span data-ttu-id="e495f-157">Avere 缓存是一个可选解决方案，它能大幅提高应用程序作业数据的读取吞吐量。</span><span class="sxs-lookup"><span data-stu-id="e495f-157">The Avere Cache is an optional solution that can drastically increase read throughput for the application job data.</span></span> <span data-ttu-id="e495f-158">Avere vFXT for Azure 解决了在云中运行这些企业 HPC 应用程序，同时利用本地或 Azure Blob 存储中存储的数据的问题。</span><span class="sxs-lookup"><span data-stu-id="e495f-158">Avere vFXT for Azure solves the problem of running these enterprise HPC applications in the cloud while leveraging data stored on-premises or in Azure Blob storage.</span></span>

<span data-ttu-id="e495f-159">对于正在计划使用包含本地存储和云计算的混合基础设施的组织而言，可以使用存储在 NAS 设备中的数据将 HPC 应用程序“迸发”到 Azure 中，并按需运转虚拟 CPU。</span><span class="sxs-lookup"><span data-stu-id="e495f-159">For organizations that are planning for a hybrid infrastructure with both on-premises storage and cloud computing, HPC applications can “burst” into Azure using data stored in NAS devices and spin up virtual CPUs as needed.</span></span> <span data-ttu-id="e495f-160">数据集永远不会完全移动到云中。</span><span class="sxs-lookup"><span data-stu-id="e495f-160">The data set is never moved completely into the cloud.</span></span> <span data-ttu-id="e495f-161">在处理期间，使用 Avere 群集暂时缓存请求的字节。</span><span class="sxs-lookup"><span data-stu-id="e495f-161">The requested bytes are temporarily cached using an Avere cluster during processing.</span></span>

<span data-ttu-id="e495f-162">若要设置和配置 Avere vFXT 安装，请遵循 [Avere 设置和配置指南][avere]。</span><span class="sxs-lookup"><span data-stu-id="e495f-162">To set up and configure an Avere vFXT installation, follow the [Avere Setup and Configuration guide][avere].</span></span>

## <a name="pricing"></a><span data-ttu-id="e495f-163">定价</span><span class="sxs-lookup"><span data-stu-id="e495f-163">Pricing</span></span>

<span data-ttu-id="e495f-164">使用 CycleCloud 服务器运行 HPC 实施项目的成本取决于多种因素。</span><span class="sxs-lookup"><span data-stu-id="e495f-164">The cost of running an HPC implementation using CycleCloud server will vary depending on a number of factors.</span></span> <span data-ttu-id="e495f-165">例如，CycleCloud 根据所用的计算时间收费，其前提是按典型方式定量分配和运行主服务器与 CycleCloud 服务器。</span><span class="sxs-lookup"><span data-stu-id="e495f-165">For example, CycleCloud is charged by the amount of compute time that is used, with the Master and CycleCloud server typically being constantly allocated and running.</span></span> <span data-ttu-id="e495f-166">运行执行节点产生的费用取决于这些节点的运行时间及其大小。</span><span class="sxs-lookup"><span data-stu-id="e495f-166">The cost of running the Execute nodes will depend on how long these are up and running as well as what size is used.</span></span> <span data-ttu-id="e495f-167">此外，还会收取普通的 Azure 存储和网络费用。</span><span class="sxs-lookup"><span data-stu-id="e495f-167">The normal Azure charges for storage and networking also apply.</span></span>

<span data-ttu-id="e495f-168">此方案显示了如何在 Azure 中运行 CFD 应用程序，其中的计算机所需的 RDMA 功能只在特定 VM 大小上提供。</span><span class="sxs-lookup"><span data-stu-id="e495f-168">This scenario shows how CFD applications can be run in Azure, so the machines will require RDMA functionality, which is only available on specific VM sizes.</span></span> <span data-ttu-id="e495f-169">以下示例显示了在数据传出量为 1 TB 的情况下，在一个月中每天连续分配八小时的规模集所产生的费用。</span><span class="sxs-lookup"><span data-stu-id="e495f-169">The following are examples of costs that could be incurred for a scale set that is allocated continuously for eight hours per day for one month, with data egress of 1 TB.</span></span> <span data-ttu-id="e495f-170">其中还包括 Azure CycleCloud 服务器和 Avere vFXT for Azure 安装的价格：</span><span class="sxs-lookup"><span data-stu-id="e495f-170">It also includes pricing for the Azure CycleCloud server and the Avere vFXT for Azure install:</span></span>

* <span data-ttu-id="e495f-171">区域：北欧</span><span class="sxs-lookup"><span data-stu-id="e495f-171">Region: North Europe</span></span>
* <span data-ttu-id="e495f-172">Azure CycleCloud 服务器：1 个标准 D3（4 个 CPU，14 GB 内存，32 GB 标准 HDD）</span><span class="sxs-lookup"><span data-stu-id="e495f-172">Azure CycleCloud Server: 1 x Standard D3 (4 x CPUs, 14 GB Memory, Standard HDD 32 GB)</span></span>
* <span data-ttu-id="e495f-173">Azure CycleCloud 主服务器：1 个标准 D12 v（4 个 CPU，28 GB 内存，32 GB 标准 HDD）</span><span class="sxs-lookup"><span data-stu-id="e495f-173">Azure CycleCloud Master Server: 1 x Standard D12 v (4 x CPUs, 28 GB Memory, Standard HDD 32 GB)</span></span>
* <span data-ttu-id="e495f-174">Azure CycleCloud 节点阵列：10 个标准 H16r（16 个 CPU，112 GB 内存）</span><span class="sxs-lookup"><span data-stu-id="e495f-174">Azure CycleCloud Node Array: 10 x Standard H16r (16 x CPUs, 112 GB Memory)</span></span>
* <span data-ttu-id="e495f-175">Azure 群集上的 Avere vFXT：3 个 x D16s v3（200 GB OS 磁盘，1 TB 高级 SSD 数据磁盘）</span><span class="sxs-lookup"><span data-stu-id="e495f-175">Avere vFXT on Azure Cluster: 3 x D16s v3 (200 GB OS, Premium SSD 1-TB data disk)</span></span>
* <span data-ttu-id="e495f-176">数据传出量：1 TB</span><span class="sxs-lookup"><span data-stu-id="e495f-176">Data Egress: 1 TB</span></span>

<span data-ttu-id="e495f-177">对于上面列出的硬件，请查看此[价格估算][pricing]。</span><span class="sxs-lookup"><span data-stu-id="e495f-177">Review this [price estimate][pricing] for the hardware listed above.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e495f-178">后续步骤</span><span class="sxs-lookup"><span data-stu-id="e495f-178">Next Steps</span></span>

<span data-ttu-id="e495f-179">部署示例后，详细了解 [Azure CycleCloud][cyclecloud]。</span><span class="sxs-lookup"><span data-stu-id="e495f-179">Once you've deployed the sample, learn more about [Azure CycleCloud][cyclecloud].</span></span>

## <a name="related-resources"></a><span data-ttu-id="e495f-180">相关资源</span><span class="sxs-lookup"><span data-stu-id="e495f-180">Related resources</span></span>

* <span data-ttu-id="e495f-181">[支持 RDMA 的计算机实例][rdma]</span><span class="sxs-lookup"><span data-stu-id="e495f-181">[RDMA Capable Machine Instances][rdma]</span></span>
* <span data-ttu-id="e495f-182">[自定义 RDMA 实例 VM][rdma-custom]</span><span class="sxs-lookup"><span data-stu-id="e495f-182">[Customizing an RDMA Instance VM][rdma-custom]</span></span>

<!-- links -->
[architecture]: ./media/architecture-hpc-cfd.png
[calculator]: https://azure.com/e/
[availability]: /azure/architecture/checklist/availability
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[vmss]: /azure/virtual-machine-scale-sets/overview
[cyclecloud]: /azure/cyclecloud/
[rdma]: /azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances
[gpu]: /azure/virtual-machines/windows/sizes-gpu
[hpcsizes]: /azure/virtual-machines/windows/sizes-hpc
[vms]: /azure/virtual-machines/
[low-pri]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority
[batch]: /azure/batch/
[avere]: https://github.com/Azure/Avere/blob/master/README.md
[cycle-prereq]: /azure/cyclecloud/quickstart-install-cyclecloud#prerequisites
[cycle-svcprin]: /azure/cyclecloud/quickstart-install-cyclecloud#service-principal
[cycle-ssh]: /azure/cyclecloud/quickstart-install-cyclecloud#ssh-keypair
[cycle-login]: /azure/cyclecloud/quickstart-install-cyclecloud#log-into-the-cyclecloud-application-server
[cycle-create]: /azure/cyclecloud/quickstart-create-and-run-cluster
[rdma]: /azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances
[rdma-custom]: /azure/virtual-machines/linux/classic/rdma-cluster#customize-the-vm
[pricing]: https://azure.com/e/53030a04a2ab47a289156e2377a4247a
[cycle-scale]: /azure/cyclecloud/autoscale
