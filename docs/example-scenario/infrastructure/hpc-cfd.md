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
# <a name="running-computational-fluid-dynamics-cfd-simulations-on-azure"></a>在 Azure 上运行计算流体动力学 (CFD) 仿真

计算流体动力学 (CFD) 仿真需要大量的计算时间，并需要专业化的硬件。 随着群集用量的增加，仿真时间和总体网格用量也会增加，导致备用容量出现问题，排队时间延长。 添加物理硬件可能费用高昂，并且不一定与企业经历的用量高峰与低谷相一致。 利用 Azure 可以化解其中的许多难题，而且不需要额外的资本性支出。

Azure 提供所需的硬件用于在 GPU 和 CPU 虚拟机上运行 CFD 作业。 支持 RDMA（远程直接内存访问）的 VM 大小提供基于 FDR InfiniBand 的网络功能，可以实现低延迟 MPI（消息传递接口）通信。 结合提供企业级群集文件系统的 Avere vFXT，客户可以确保为 Azure 中的读取操作提供最大吞吐量。

为了简化 HPC 群集的创建、管理和优化，可以使用 Azure CycleCloud 来预配群集以及协调混合方案与云方案中的数据。 通过监视挂起的作业，CycleCloud 可以连接到所选的工作负荷计划程序，自动按需启动计算资源，而你只需支付所用资源的费用。

## <a name="relevant-use-cases"></a>相关用例

对于以下可以使用 CFD 应用程序的行业，请考虑此方案：

* 航空
* 汽车
* 建筑 HVAC
* 石油和天然气
* 生命科学

## <a name="architecture"></a>体系结构

![体系结构关系图][architecture]

此图从较高层面显示了针对 Azure 中的按需节点提供作业监视的典型混合设计的概况：

1. 连接到 Azure CycleCloud 服务器以配置群集。
2. 使用用于 MPI 的支持 RDMA 的计算机配置和创建群集头节点。
3. 添加和配置本地头节点。
4. 如果资源不足，Azure CycleCloud 将纵向扩展（或缩减）Azure 中的计算资源。 可以定义预先确定的限制来防止过度分配。
5. 分配给执行节点的任务。
6. 在 Azure 中缓存的、来自本地 NFS 服务器的数据。
7. 在 Azure 中缓存的、从 Avere vFXT 读入的数据。
8. 中继到 Azure CycleCloud 服务器的作业和任务信息。

### <a name="components"></a>组件

* [Azure CycleCloud][cyclecloud] 是用于在 Azure 中创建、管理、操作和优化 HPC 与大型计算群集的工具。
* [Avere vFXT on Azure][avere] 用于提供针对云生成的企业级群集文件系统。
* [Azure 虚拟机 (VM)][vms] 用于创建一组静态计算实例。
* [虚拟机规模集][vmss]提供一组可由 Azure CycleCloud 纵向扩展或缩减的相同 VM。
* [Azure 存储帐户](/azure/storage/common/storage-introduction)用于同步和数据保留。
* [虚拟网络](/azure/virtual-network/virtual-networks-overview)允许许多类型的 Azure 资源（例如 Azure 虚拟机 (VM)）以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。

### <a name="alternatives"></a>备选项

客户还可以完全使用 Azure CycleCloud 在 Azure 中创建网格。 在此设置中，Azure CycleCloud 服务器在 Azure 订阅中运行。

使用不需要管理工作负荷计划程序的新式应用程序方法时，[Azure Batch][batch] 可能有所帮助。 Azure Batch 可在云中高效运行大规模并行和高性能计算 (HPC) 应用程序。 使用 Azure Batch 可以定义 Azure 计算资源，以并行执行或大规模执行应用程序，无需手动配置或管理基础结构。 Azure Batch 按要求计划计算密集型任务并动态添加或删除计算资源。

### <a name="scalability-and-security"></a>可伸缩性和安全性

可以手动或使用自动缩放来缩放 Azure CycleCloud 上的执行节点。 有关详细信息，请参阅 [CycleCloud 自动缩放][cycle-scale]。

有关设计安全解决方案的一般性指导，请参阅 [Azure 安全性文档][security]。

## <a name="deploy-this-scenario"></a>部署此方案

在 Azure 中部署之前，需要满足一些先决条件。 在部署资源管理器模板之前，请执行以下步骤：
1. 创建一个[服务主体][cycle-svcprin]用于检索 appId、displayName、名称、密码和租户。
2. 生成一个 [SSH 密钥对][cycle-ssh]，以安全登录到 CycleCloud 服务器。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FCycleCloudCommunity%2Fcyclecloud_arm%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

3. [登录到 CycleCloud 服务器][cycle-login]，以配置和创建新群集。
4. [创建群集][cycle-create]。

Avere 缓存是一个可选解决方案，它能大幅提高应用程序作业数据的读取吞吐量。 Avere vFXT for Azure 解决了在云中运行这些企业 HPC 应用程序，同时利用本地或 Azure Blob 存储中存储的数据的问题。

对于正在计划使用包含本地存储和云计算的混合基础设施的组织而言，可以使用存储在 NAS 设备中的数据将 HPC 应用程序“迸发”到 Azure 中，并按需运转虚拟 CPU。 数据集永远不会完全移动到云中。 在处理期间，使用 Avere 群集暂时缓存请求的字节。

若要设置和配置 Avere vFXT 安装，请遵循 [Avere 设置和配置指南][avere]。

## <a name="pricing"></a>定价

使用 CycleCloud 服务器运行 HPC 实施项目的成本取决于多种因素。 例如，CycleCloud 根据所用的计算时间收费，其前提是按典型方式定量分配和运行主服务器与 CycleCloud 服务器。 运行执行节点产生的费用取决于这些节点的运行时间及其大小。 此外，还会收取普通的 Azure 存储和网络费用。

此方案显示了如何在 Azure 中运行 CFD 应用程序，其中的计算机所需的 RDMA 功能只在特定 VM 大小上提供。 以下示例显示了在数据传出量为 1 TB 的情况下，在一个月中每天连续分配八小时的规模集所产生的费用。 其中还包括 Azure CycleCloud 服务器和 Avere vFXT for Azure 安装的价格：

* 区域：北欧
* Azure CycleCloud 服务器：1 个标准 D3（4 个 CPU，14 GB 内存，32 GB 标准 HDD）
* Azure CycleCloud 主服务器：1 个标准 D12 v（4 个 CPU，28 GB 内存，32 GB 标准 HDD）
* Azure CycleCloud 节点阵列：10 个标准 H16r（16 个 CPU，112 GB 内存）
* Azure 群集上的 Avere vFXT：3 个 x D16s v3（200 GB OS 磁盘，1 TB 高级 SSD 数据磁盘）
* 数据传出量：1 TB

对于上面列出的硬件，请查看此[价格估算][pricing]。

## <a name="next-steps"></a>后续步骤

部署示例后，详细了解 [Azure CycleCloud][cyclecloud]。

## <a name="related-resources"></a>相关资源

* [支持 RDMA 的计算机实例][rdma]
* [自定义 RDMA 实例 VM][rdma-custom]

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
