---
title: 计算机辅助工程服务
titleSuffix: Azure Example Scenarios
description: 为 Azure 上的计算机辅助工程 (CAE) 提供软件即服务 (SaaS) 平台。
author: alexbuckgit
ms.date: 08/22/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: HPC
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-hpc-saas.png
ms.openlocfilehash: bd38bd0042fceeab6efe04d7b6d1477d17ada7f5
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249632"
---
# <a name="a-computer-aided-engineering-service-on-azure"></a>Azure 上的计算机辅助工程服务

本示例方案演示如何交付基于 Azure 的高性能计算 (HPC) 功能构建的软件即服务 (SaaS) 平台。 本方案基于某个工程软件解决方案。 但是，体系结构与需要 HPC 资源的其他行业（例如图像渲染、复杂建模和金融风险计算）相关。

本示例演示某家工程软件提供商如何向工程公司和制造企业交付计算机辅助工程 (CAE) 应用程序。 CAE 解决方案可以实现创新、减少开发时间，并在整个产品设计生命周期内降低成本。 这些解决方案需要大量的计算资源，并且经常要处理大量数据。 本地 HPC 设备或高端工作站的高企成本往往让小型工程公司、创业者和学生觉得这些技术高不可攀。

该公司希望通过构建以基于云的 HPC 技术作为后盾的 SaaS 平台，来拓展其应用程序的市场。 其客户应可根据需求支付计算资源费用，并可以运用其他公司不能优惠提供的大规模计算能力。

该公司的目标包括：

- 利用 Azure 中的 HPC 功能可以加速产品设计和测试过程。
- 使用最新的硬件创新可以运行复杂的仿真，同时可将较简单仿真的成本降到最低。
- 可在 Web 浏览器中实现逼真的可视化和渲染，而无需高端的工程工作站。

## <a name="relevant-use-cases"></a>相关用例

其他相关用例包括：

- 基因组学研究
- 天气仿真
- 计算化学应用

## <a name="architecture"></a>体系结构

![支持 HPC 功能的 SaaS 解决方案的体系结构][architecture]

- 用户可以使用 [Apache Guacamole 服务](https://guacamole.apache.org/)，在浏览器中通过基于 HTML5 的 RDP 连接来访问 NV 系列虚拟机 (VM)。 这些 VM 实例为渲染和协作任务提供强大的 GPU。 用户可以编辑其设计和查看结果，而无需访问高端移动计算设备或便携式计算机。 计划程序基于用户定义的试探法运转其他 VM。
- 在桌面 CAD 会话中，用户可以提交工作负荷，以便在可用的 HPC 群集节点上执行。 这些工作负荷执行压力分析或计算流体动力学计算等任务，消除了对专用本地计算群集的需求。 可以基于用户对计算资源的现有需求，将这些群集节点配置为根据负载或队列深度进行自动缩放。
- Azure Kubernetes 服务 (AKS) 用于托管提供给最终用户的 Web 资源。

### <a name="components"></a>组件

- [H 系列虚拟机](/azure/virtual-machines/linux/sizes-hpc)用于运行计算密集型仿真，例如分子建模和计算流体动力学。 该解决方案还利用了远程直接内存访问 (RDMA) 连接和 InfiniBand 网络等技术。
- [NV 系列虚拟机](/azure/virtual-machines/windows/sizes-gpu)在标准的 Web 浏览器中为工程师提供高端工作站功能。 这些虚拟机配备了支持高级渲染并可运行单精度工作负荷的 NVIDIA Tesla M60 GPU。
- 运行 CentOS 的[常规用途虚拟机](/azure/virtual-machines/linux/sizes-general)可以处理更多传统工作负荷，例如 Web 应用程序。
- [应用程序网关](/azure/application-gateway/overview)可对传入 Web 服务器的请求进行负载均衡。
- [Azure Kubernetes 服务 (AKS)](/azure/aks/intro-kubernetes) 用于针对不需要 HPC 高端功能或 GPU 虚拟机的仿真，以较低的成本运行可缩放的工作负荷。
- [Altair PBS Works Suite](https://www.pbsworks.com/PBSProduct.aspx?n=PBS-Works-Suite&c=Overview-and-Capabilities) 协调 HPC 工作流，确保可以提供足够的虚拟机实例来处理当前负载。 当需求减少时，它还可以解除分配虚拟机以降低成本。
- [Blob 存储](/azure/storage/blobs/storage-blobs-introduction)存储用于支持计划作业的文件。

### <a name="alternatives"></a>备选项

- [Azure CycleCloud](/azure/cyclecloud/overview) 简化了 HPC 群集的创建、管理、操作和优化。 它提供高级策略和监管功能。 CycleCloud 支持任何作业计划程序或软件堆栈。
- [HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options) 可为基于 Windows Server 的工作负荷创建和管理 Azure HPC 群集。 HPC Pack 不适用于基于 Linux 的工作负荷。
- [Azure Automation State Configuration](/azure/automation/automation-dsc-overview) 提供基础结构即代码方法用于定义要部署的虚拟机和软件。 可以基于提交到作业队列的作业数，使用计算节点的自动缩放规则将虚拟机部署为虚拟机规模集的一部分。 需要新虚拟机时，可以使用 Azure 映像库中最新的修补映像预配该虚拟机，然后通过 PowerShell DSC 配置脚本安装并配置所需的软件。
- [Azure Functions](/azure/azure-functions/functions-overview)

## <a name="considerations"></a>注意事项

- 尽管使用基础结构即代码方法能够很好地管理虚拟机生成定义，但使用脚本预配新虚拟机可能需要花费很长的时间。 此解决方案提供一种折衷方式：使用 DSC 脚本定期创建一个黄金映像，然后可以使用该映像来预配新虚拟机，其速度比使用 DSC 按需生成完整的 VM 更快。 Azure DevOps Services 或其他 CI/CD 工具可以使用 DSC 脚本定期刷新黄金映像。
- 在总体解决方案成本与快速提供计算资源之间进行平衡一个重要考虑因素。 预配 N 系列虚拟机实例池并将其置入解除分配状态可以降低运营成本。 需要额外的虚拟机时，重新分配现有实例需要在另一台主机上打开虚拟机，但可以消除 OS 识别并安装 GPU 驱动程序所需的 PCI 总线检测时间，因为在重启时，取消预配后再重新预配的虚拟机将为 GPU 保留相同的 PCI 总线。
- 原始体系结构完全依赖于 Azure 虚拟机来运行仿真。 为了降低不需要所有虚拟机功能的工作负荷所产生的成本，这些工作负荷已容器化并已部署到 Azure Kubernetes 服务 (AKS) 中。
- 公司员工对开源技术已有一定的技能。 他们可以利用这些技能基于 Linux 和 Kubernetes 等技术生成解决方案。

## <a name="pricing"></a>定价

为了帮助你了解运行本方案所产生的成本，我们在[成本计算器示例][calculator]中预配置了多个所需的服务。 解决方案的成本取决于满足要求所需的服务数量和规模。

以下因素与本解决方案的绝大部分成本相关：

- 预配更多的实例时，Azure 虚拟机的成本将呈线性增加。 已解除分配的虚拟机只会产生存储成本，而不产生计算成本。 今后在需求提高时，可以重新分配这些已解除分配的虚拟机。
- Azure Kubernetes 服务成本取决于选择用来支持工作负荷的 VM 类型。 成本根据群集中的 VM 数呈线性增加。

## <a name="next-steps"></a>后续步骤

- 阅读 [Altair 客户案例][source-document]。 此示例方案基于其体系结构的版本。
- 查看 Azure 中提供的其他[大型计算解决方案](https://azure.microsoft.com/solutions/big-compute)。

<!-- links -->
[architecture]: ./media/architecture-hpc-saas.png
[source-document]: https://customers.microsoft.com/story/altair-manufacturing-azure
[calculator]: https://azure.com/e/3cb9ccdc893f41ffbcdb00c328178ccf
