---
title: Azure 上深度学习模型的分布式训练
description: 本参考体系结构演示如何使用 Azure Batch AI 在支持 GPU 的 VM 群集之间展开深度学习模型的分布式训练。
author: njray
ms.date: 01/14/19
ms.custom: azcat-ai
ms.openlocfilehash: 800defeb851f5a31dc730038c3699e1a3d54b923
ms.sourcegitcommit: d5ea427c25f9f7799cc859b99f328739ca2d8c1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54307743"
---
# <a name="distributed-training-of-deep-learning-models-on-azure"></a>Azure 上深度学习模型的分布式训练

本参考体系结构演示如何在支持 GPU 的 VM 群集之间展开深度学习模型的分布式训练。 该方案属于图像分类，但也可以针对其他深度学习方案（例如分段与对象检测）通用化该解决方案。

[GitHub][github] 中提供了本体系结构的参考实现。

![分布式深度学习的体系结构][0]

**场景**：图像分类是计算机影像中广泛应用的技术，其一般处理方式是训练卷积神经网络 (CNN)。 对于包含大型数据集的特大模型，使用单个 GPU 进行训练可能需要花费数周甚至数月之久。 在某些情况下，由于模型过大，以致 GPU 无法容纳合理的批大小。 在这些情况下使用分布式训练可以缩短训练时间。

此具体方案使用 [Horovod][horovod] 基于 [Imagenet 数据集][imagenet]与合成数据训练 [ResNet50 CNN 模型][resnet]。 本参考实现演示如何使用三个最流行的深度学习框架完成此任务：TensorFlow、Keras 和 PyTorch。

有多种方法能够以分布方式训练深度学习模型，包括基于同步或异步更新的数据并行方法和模型并行方法。 目前，最常见的方案是基于同步更新的数据并行方法。 这种方法最容易实现，足以满足大多数用例的需求。

在基于同步更新的数据并行分布式训练中，模型跨 *n* 个硬件设备进行复制。 训练样本的小批分割成 *n* 个微批。 每个设备对微批执行向前和向后传递。 设备完成该过程后，将与其他设备共享更新。 这些值用于计算整个小批的更新权重，权重将在模型之间同步。 [GitHub][github] 存储库中介绍了此方案。

![数据并行分布式训练][1]

本体系结构还可用于模型并行和异步更新。 在模型并行分布式训练中，模型将分割到 *n* 个硬件设备，每个设备包含模型的一部分。 在最简单的实现中，每个设备可以包含一个网络层，在向前和向后传递期间，信息将在设备之间传递。 可以这种方式训练大型神经网络，但代价是性能下降，因为一个设备需要持续等待另一个设备完成向前或向后传递。 某些高级技术使用合成渐变，可在一定程度上缓解此问题。

训练步骤如下：

1. 创建要在群集上运行的、用于训练模型的脚本，然后将其传输到文件存储。

1. 将数据写入 Blob 存储。

1. 创建 Batch AI 文件服务器，并将 Blob 存储中的数据下载到其中。

1. 为每个深度学习框架创建 Docker 容器，并将其传输到容器注册表（Docker 中心）。

1. 创建一个也要装载 Batch AI 文件服务器的 Batch AI 池。

1. 提交作业。 每个作业提取相应的 Docker 映像和脚本。

1. 完成该作业后，将所有结果写入文件存储。

## <a name="architecture"></a>体系结构

该体系结构包括以下组件。

在此体系结构中，**[Azure Batch AI][batch-ai]** 根据需求扩展和缩减资源，发挥着核心的作用。 Batch AI 是可以帮助预配和管理 VM 群集、计划作业、收集结果、缩放资源、处理故障以及创建相应存储的服务。 它允许使用支持 GPU 的 VM 来运行深度学习工作负荷。 Python SDK 和命令行接口 (CLI) 适用于 Batch AI。

> [!NOTE]
> Azure Batch AI 将在 2019 年 3 月停用，[Azure 机器学习服务][amls]中现已推出其大规模训练和评分功能。 本参考体系结构即将更新为使用机器学习。机器学习提供名为 [Azure 机器学习计算][aml-compute]的托管计算目标，用于对机器学习模型进行训练、部署和评分。

**[Blob 存储][azure-blob]** 用于暂存数据。 在训练期间，此数据将下载到 Batch AI 文件服务器。

**[Azure 文件][files]** 用于存储脚本、日志和最终训练结果。 文件存储适合用于存储日志和脚本，但其性能不如 Blob 存储，因此，不应将它用于数据密集型任务。

**[Batch AI 文件服务器][batch-ai-files]** 是本体系结构中用来存储训练数据的单节点 NFS 共享。 Batch AI 创建 NFS 共享并将其装载到群集上。 Batch AI 文件服务器可提供必要的吞吐量，建议使用它来向群集提供数据。

**[Docker 中心][docker]** 用于存储 Batch AI 运行训练所需的 Docker 映像。 之所以为本体系结构选择 Docker 中心，是因为它易于使用，并且是 Docker 用户的默认映像存储库。 也可使用 [Azure 容器注册表][acr]。

## <a name="performance-considerations"></a>性能注意事项

Azure 提供四个适合用于训练深度学习模型的[支持 GPU 的 VM 类型][gpu]。 下面按从低到高的顺序提供了这些 VM 类型的价格和速度：

| **Azure VM 系列** | **NVIDIA GPU** |
|---------------------|----------------|
| NC                  | K80            |
| ND                  | P40            |
| NCv2                | P100           |
| NCv3                | V100           |

我们建议在横向扩展之前先纵向扩展训练。例如，在试用 K80 群集之前，先试用单个 V100。

下图根据在 Batch AI 中使用 TensorFlow 和 Horovod 执行的[基准测试][benchmark]，显示了不同 GPU 类型的性能差异。 该图显示了 32 个 GPU 群集在使用不同 GPU 类型和 MPI 版本的各个模型中的吞吐量。 模型是在 TensorFlow 1.9 中实现的

![GPU 群集上的 TensorFlow 模型的吞吐量结果][2]

上表中所示的每个 VM 系列包含采用 InfiniBand 的配置。 运行分布式训练时使用 InfiniBand 配置可以加快节点之间的通信速度。 对于利用 InfiniBand 的框架，InfiniBand 还可提高其训练缩放效率。 有关详细信息，请参阅 Infiniband [基准比较][benchmark]。

尽管 Batch AI 可以使用 [blobfuse][blobfuse] 适配器装载 Blob 存储，我们不建议以这种方式为分布式训练使用 Blob 存储，因为性能并不足以处理所需的吞吐量。 应该如体系结构示意图中所示，将数据移到 Batch AI 文件服务器。

## <a name="scalability-considerations"></a>可伸缩性注意事项

由于网络开销，分布式训练的缩放效率始终小于 100% &mdash; 在设备之间同步整个模型成了一个瓶颈。 因此，分布式训练最适合用于无法在单个 GPU 中使用合理批大小训练的大型模型，或者用于解决无法通过简单并行方式分布模型来解决的问题。

不建议将分布式训练用于运行超参数搜索。 与单独训练多个模型配置相比，其缩放效率会影响性能，并降低分布式方法的效率。

提高缩放效率的方法之一是增加批大小。 但是，增加批大小必须谨慎，因为在不调整其他参数的情况下增加批大小可能会损害模型的最终性能。

## <a name="storage-considerations"></a>存储注意事项

训练深度学习模型时，一个经常被忽视的问题是数据的存储位置。 如果存储速度太慢，以致无法跟上 GPU 的需求，则训练性能可能会下降。

Batch AI 支持多种存储解决方案。 本体系结构使用 Batch AI 文件服务器，因为该服务器在易用性与性能之间提供最佳平衡。 为获得最佳性能，请在本地加载数据。 但是，此操作可能比较繁琐，因为所有节点必须从 Blob 存储下载数据，下载 ImageNet 数据集时，可能需要耗费几个小时。 [Azure 高级 Blob 存储][blob]（受限公共预览版）是另一个值得考虑的选项。

请不要将 Blob 和文件存储装载为分布式训练的数据存储。 它们的速度太慢，因此会降低训练性能。

## <a name="security-considerations"></a>安全注意事项

### <a name="restrict-access-to-azure-blob-storage"></a>限制对 Azure Blob 存储的访问

本体系结构使用[存储帐户密钥][security-guide]访问 Blob 存储。 为进一步控制和保护，请考虑改用共享访问签名 (SAS)。 SAS 授予对存储中对象的有限访问权限，而无需对帐户密钥进行硬编码或以纯文本形式保存。 使用 SAS 还有助于确保存储帐户具有适当的监管，并且仅向有意拥有访问权限的人员授予该访问权限。

在具有更多敏感数据的情况下，请确保所有存储密钥都受到保护，因为这些密钥可授予对工作负载的所有输入和输出数据的完全访问权限。

### <a name="encrypt-data-at-rest-and-in-motion"></a>加密静态和动态数据

在使用敏感数据的方案中，请加密静态数据 &mdash; 即存储中的数据。 每次将数据从一个位置移到另一位置时，请使用 SSL 来保护数据传输。 有关详细信息，请参阅 [Azure 存储安全指南][security-guide]。

### <a name="secure-data-in-a-virtual-network"></a>保护虚拟网络中的数据

对于生产部署，请考虑将 Batch AI 群集部署到指定的虚拟网络子网中。 这样，群集中的计算节点便可安全地与其他虚拟机通信，或者与本地网络通信。 还可以在虚拟网络中结合 Blob 存储使用[服务终结点][endpoints]来授予访问权限，或者在虚拟网络中结合 Batch AI 使用单节点 NFS。

## <a name="monitoring-considerations"></a>监视注意事项

运行作业时，监视进度并确保操作按预期进行，这点至关重要。 然而，在活动节点群集间进行监视可能是一项挑战。

可以通过 Azure 门户或者 [Azure CLI][cli] 和 Python SDK 管理 Batch AI 文件服务器。 若要大致了解群集的总体状态，请在 Azure 门户中导航到“Batch AI”，以检查群集节点的状态。 如果某个节点处于非活动状态或作业失败，则错误日志将保存到 Blob 存储，并且可以在 Azure 门户中的“作业”下访问这些日志。

通过将日志连接到 [Azure Application Insights][ai]，或者通过运行单独的进程来轮询 Batch AI 群集及其作业的状态，可进一步进行监视。

Batch AI 自动将所有 stdout/stderr 记录到关联的 Blob 存储帐户中。 使用 [Azure 存储资源管理器][storage-explorer]等存储导航工具可以更轻松地浏览日志文件。

此外，还可以流式传输每个作业的日志。 有关此选项的详细信息，请参阅 [GitHub][github] 中的开发步骤。

## <a name="deployment"></a>部署

[GitHub][github] 中提供了本体系结构的参考实现。 请遵循该文章中的步骤在支持 GPU 的 VM 群集之间展开深度学习模型的分布式训练。

## <a name="next-steps"></a>后续步骤

本体系结构的输出是保存到 Blob 存储中的已训练模型。 可将此模型操作化，以用于实时评分或批处理评分。 有关详细信息，请参阅以下参考体系结构：

- [Azure 上 Python Scikit-Learn 和深度学习模型的实时评分][real-time-scoring]
- [Azure 上深度学习模型的批处理评分][batch-scoring]

[0]: ./_images/distributed_dl_architecture.png
[1]: ./_images/distributed_dl_flow.png
[2]: ./_images/distributed_dl_tests.png
[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[azure-blob]: /azure/storage/blobs/storage-blobs-introduction
[batch-ai]: /azure/batch-ai/overview
[batch-ai-files]: /azure/batch-ai/resource-concepts#file-server
[batch-scoring]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[benchmark]: https://github.com/msalvaris/BatchAIHorovodBenchmark
[blob]: https://azure.microsoft.com/en-gb/blog/introducing-azure-premium-blob-storage-limited-public-preview/
[blobfuse]: https://github.com/Azure/azure-storage-fuse
[cli]: https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md
[docker]: https://hub.docker.com/
[endpoints]: /azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network
[files]: /azure/storage/files/storage-files-introduction
[github]: https://github.com/Azure/DistributedDeepLearning/
[gpu]: /azure/virtual-machines/windows/sizes-gpu
[horovod]: https://github.com/uber/horovod
[imagenet]: http://www.image-net.org/
[real-time-scoring]: /azure/architecture/reference-architectures/ai/realtime-scoring-python
[resnet]: https://arxiv.org/abs/1512.03385
[security-guide]: /azure/storage/common/storage-security-guide
[storage-explorer]: /azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows
[tutorial]: https://github.com/Azure/DistributedDeepLearning