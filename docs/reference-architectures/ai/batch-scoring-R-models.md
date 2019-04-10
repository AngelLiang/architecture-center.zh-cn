---
title: 批处理评分与在 Azure 上的 R 模型
description: 执行批处理评分与使用 Azure Batch 和基于零售商店销售预测的数据集的 R 模型。
author: njray
ms.date: 03/29/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 4fa57168c337b01c8e7d0fc86ba54fee59a7ae47
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887928"
---
# <a name="batch-scoring-of-r-machine-learning-models-on-azure"></a>在 Azure 上的 R 机器学习批处理评分模型

此参考体系结构演示如何执行批处理评分与使用 Azure Batch 的 R 模型。 该方案基于零售商店销售预测，但这种体系结构可以归纳为需要使用 R 模型的大型 scaler 预测生成任何方案。 [GitHub][github] 中提供了本体系结构的参考实现。

![体系结构关系图][0]

**场景**：连锁超市需要预测即将到来的季度的销售的产品。 预测可让公司将更好地管理其供应链，并确保它可以满足对其存储的每个产品的需求。 如前一周中的新销售数据变得可用并设置下一季度的市场营销策略的产品，公司将更新其预测每周。 分位的预测会生成来估计单个销售预测的不确定性。

包括以下处理步骤：

1. Azure 逻辑应用会触发一次每周的预测的生成过程。

1. 逻辑应用启动 Azure 容器实例运行 Docker 容器，这会触发批处理群集上的评分作业的计划程序。

1. 批处理群集的节点上并行运行的评分的作业。 每个节点：

    1. 提取工作线程从 Docker Hub 的 Docker 映像并启动容器。

    1. 读取输入的数据，并预先训练的 R 模型从 Azure Blob 存储。

    1. 得分数据来生成预测。

    1. 将预测的结果写入到 blob 存储中。

下图显示一个存储中的四个产品 (Sku) 的预测的销售额。 黑色线条是销售历史记录、 虚线是预测的中值 (q50)、 粉红色带区表示 25 个和 70 第五个百分点值和蓝色带区表示第五个第 90 五个百分点值。

![销售预测][1]

## <a name="architecture"></a>体系结构

该体系结构包括以下组件。

[Azure Batch] [ batch]用于预测的作业以并行方式运行的虚拟机群集上。 作出预测使用预先定型的机器学习模型实现。 在 Azure Batch 中可以自动缩放的基于提交到群集的作业数的 Vm 数。 每个节点上的 R 脚本进行评分的数据和生成预测的 Docker 容器中运行。

[Azure Blob 存储][ blob]用来存储输入的数据、 预先定型的机器学习模型和预测的结果。 它提供为此工作负荷需要的性能非常经济高效的存储。

[Azure 容器实例][ aci]按需提供无服务器计算。 在这种情况下，容器实例部署按计划触发生成预测的批处理作业。 批处理作业触发从 R 脚本使用[doAzureParallel][doAzureParallel]包。 在作业完成后，容器实例自动会关闭。

[Azure 逻辑应用][ logic-apps]通过部署容器实例按计划触发整个工作流。 在逻辑应用中的 Azure 容器实例连接器允许一个实例，以便在触发事件的范围时部署。

## <a name="performance-considerations"></a>性能注意事项

### <a name="containerized-deployment"></a>容器化的部署

使用此体系结构，所有 R 脚本都运行内[Docker](https://www.docker.com/)容器。 这可确保脚本运行在一致的环境中，使用相同的 R 版本和包的版本中，每次。 对于计划程序和辅助角色的容器，使用是单独的 Docker 映像，因为每个都有一组不同的 R 包依赖项。

Azure 容器实例提供无服务器环境来运行计划程序容器。 计划程序容器运行 R 脚本，以触发 Azure Batch 群集上运行的单个评分作业。

每个批次群集节点运行辅助角色容器，它执行评分脚本。

### <a name="parallelizing-the-workload"></a>并行化工作负荷

当批处理评分的 R 模型的数据，请考虑如何并行化工作负荷。 输入的数据必须以某种方式分区，以便计分操作可以分布在群集节点。 尝试不同的方法来发现将工作负荷的最佳选择。 基于的情况，考虑以下方面：

- 可以加载和处理在单个节点的内存中数据量。
- 启动每个批处理作业的开销。
- 加载 R 模型的开销。

在此示例使用方案中，模型对象太大，而它需要仅几秒钟以生成单个产品的预测。 出于此原因，您可以对产品分组并执行每个节点的单个批处理作业。 一个循环内每个作业将按顺序生成产品的预测值。 此方法证明无法并行化此特定工作负荷的最有效方法。 它避免了启动多个较小的批处理作业和重复加载 R 模型的开销。

另一种方法是触发每个产品的一个批处理作业。 Azure Batch 自动窗体的作业队列，并将其推出的节点在群集上执行提交。 使用[自动缩放][ autoscale]可调整的作业数根据群集中的节点数。 如果花费较长的时间才能完成每个评分的操作，还是启动作业和重新加载模型对象的开销，此方法会更合理。 此方法也是实现变得简单，并使你可以灵活地使用自动缩放，一个重要的考虑因素，如果事先不知道的总工作负荷的大小。

## <a name="monitoring-and-logging-considerations"></a>监视和日志记录注意事项

### <a name="monitoring-azure-batch-jobs"></a>监视 Azure Batch 作业

监视和终止批处理作业从**作业**窗格中的 Azure 门户中的 Batch 帐户。 监视 batch 群集，包括各个节点的状态从**池**窗格。

### <a name="logging-with-doazureparallel"></a>使用 doAzureParallel 的日志记录

DoAzureParallel 包会自动收集的每个作业提交 Azure Batch 上的所有 stdout/stderr 的日志。 可以在安装时创建的存储帐户中找到这些选项。 若要查看它们，请使用存储的导航工具如[Azure 存储资源管理器][ storage-explorer]或 Azure 门户。

若要在开发期间快速调试批处理作业，打印在你本地 R 会话中使用的日志[getJobFiles][getJobFiles] doAzureParallel 的函数。

## <a name="cost-considerations"></a>成本注意事项

此参考体系结构中使用的计算资源是开销最高的组件。 对于此方案中，只要该作业是触发，然后关闭作业完成后创建固定大小的群集。 仅当启动、 运行，或关闭群集节点时，才产生成本。 此方法非常适用于在其中生成预测所需的计算资源保持相对稳定作业作业的方案。

在其中完成作业所需的计算量事先不知道的情况下，它可能更适合使用自动缩放。 使用此方法时，群集的大小是根据作业大小增加或减少。 Azure Batch 支持一系列定义群集使用时可设置自动缩放公式[doAzureParallel][doAzureParallel] API。

在某些情况下，作业之间的时间可能会太短而无法关闭和启动该群集。 在这些情况下，请根据作业之间运行的群集。

Azure 批处理和 doAzureParallel 支持低优先级 Vm 的使用。 这些 Vm 提供可观的折扣但正在由其他更高优先级的工作负荷分配量的风险。 因此对于关键生产工作负荷不建议使用这些虚拟机。 但是，它们是非常有用的实验性或开发工作负荷。

## <a name="deployment"></a>部署

若要部署此参考体系结构，请按照中所述的步骤[GitHub][github]存储库。


[0]: ./_images/batch-scoring-r-models.png
[1]: ./_images/sales-forecasts.png
[aci]: /azure/container-instances/container-instances-overview
[autoscale]: /azure/batch/batch-automatic-scaling
[batch]: /azure/batch/batch-technical-overview
[blob]: /azure/storage/blobs/storage-blobs-introduction
[doAzureParallel]: https://github.com/Azure/doAzureParallel/blob/master/docs/32-autoscale.md
[getJobFiles]: /azure/machine-learning/service/how-to-train-ml-models
[github]: https://github.com/Azure/RBatchScoring
[logic-apps]: /azure/logic-apps/logic-apps-overview
[storage-explorer]: /azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows