---
title: 针对深度学习模型的批处理计分
titleSuffix: Azure Reference Architectures
description: 本参考体系结构演示如何使用 Azure 机器学习将神经样式传输应用于视频。
author: jiata
ms.date: 02/06/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 85d04f179b988fd5b00b361149f2170d13608e6d
ms.sourcegitcommit: 700a4f6ce61b1ebe68e227fc57443e49282e35aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55887380"
---
# <a name="batch-scoring-on-azure-for-deep-learning-models"></a>Azure 上针对深度学习模型的批处理计分

本参考体系结构演示如何使用 Azure 机器学习将神经样式传输应用于视频。 样式传输是一种深度学习技术，它以另一个图像的样式构成现有图像。 可概括此体系结构，将它用于任何为深度学习使用批处理计分的场景。 [**部署此解决方案**](#deploy-the-solution)。

![使用 Azure 机器学习的深度学习模型的体系结构图](./_images/aml-scoring-deep-learning.png)

**场景**：媒体组织的一个视频需要改变样式，使其看起来像一幅特定的画。 该组织希望能以自动化的方式及时将这种样式应用于视频的所有帧。 有关神经样式传输算法的详细背景信息，请参阅[使用卷积神经网络的图像样式传输][image-style-transfer] (PDF)。

| 样式图像： | 输入/内容视频： | 输出视频： |
|--------|--------|---------|
| <img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/style_image.jpg" width="300"> | [<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video.mp4 "输入视频") 单击以观看视频 | [<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video.mp4 "输出视频") 单击以观看视频 |

这个用于参考的体系结构专为 Azure 存储中存在新媒体时而触发的工作负载而设计。

包括以下处理步骤：

1. 将视频文件上传到存储。
1. 视频文件触发逻辑应用，以将请求发送到 Azure 机器学习管道发布的终结点。
1. 该管道处理视频、应用采用 MPI 的样式传输，并对视频进行后处理。
1. 管道完成后，输出将保存到 Blob 存储。

## <a name="architecture"></a>体系结构

该体系结构包括以下组件。

### <a name="compute"></a>计算

**[Azure 机器学习服务][amls]** 使用 Azure 机器学习管道创建可再现的且易于管理的计算序列。 此外，它还提供名为 [Azure 机器学习计算][aml-compute]的托管计算目标（管道计算可在其上运行），用于对机器学习模型进行训练、部署和评分。 

### <a name="storage"></a>存储

**[Blob 存储][blob-storage]** 用于存储所有图像（输入图像、样式图像和输出图像）。 Azure 机器学习服务与 Blob 存储相集成，因此，用户无需手动在计算平台和 Blob 存储之间移动数据。 对于此工作负载所需的性能而言，Blob 存储也非常经济高效。

### <a name="trigger--scheduling"></a>触发/计划

[Azure 逻辑应用][logic-apps]用于触发工作流。 当逻辑应用检测到已将 Blob 添加到容器中时，会触发 Azure 机器学习管道。 逻辑应用非常适合这个用于参考的体系结构，因为它是检测 Blob 存储更改的简单方法，并提供了更改触发器的简单进程。

### <a name="preprocessing-and-postprocessing-our-data"></a>预处理和后处理数据

这个用于参考的体系结构使用“树上的猩猩”视频片段。 可从[此处][source-video]下载该视频片段。

1. 使用 [FFmpeg][ffmpeg] 从视频片段中提取音频文件，以便稍后可将音频文件拼结回到输出视频。
1. 使用 FFmpeg 将视频分成单个帧。 独立地并行处理这些帧。
1. 此时，我们可以同时将神经样式传输应用到每个帧。
1. 处理每个帧之后，需要使用 FFmpeg 将这些帧重新拼接到一起。
1. 最后，将音频文件重新附加到重新拼接的视频片段。

## <a name="performance-considerations"></a>性能注意事项

### <a name="gpu-vs-cpu"></a>GPU 与 CPU

对于深度学习工作负载，GPU 通常会远远超出 CPU，以至于通常需要相当大的 CPU 群集才可获得可比的性能。 虽然在此体系结构中可选择只使用 CPU，但 GPU 将提供更好的成本/性能配置文件。 我们建议使用最新的 [NCv3 系列] VM 大小 GPU 的 GPU 优化 VM。

默认情况下，并非所有区域都启用 GPU。 确保选择启用了 GPU 的区域。 此外，对于 GPU 优化的 VM，订阅的内核默认配额为零。 可通过打开支持请求来提高此配额。 确保订阅有足够的配额来运行工作负载。

### <a name="parallelizing-across-vms-vs-cores"></a>跨 VM 和内核并行执行

将样式传输进程作为批处理作业运行时，主要在 GPU 上运行的作业必须在 VM 间并行化。 可使用两种方法：可使用具有单个 GPU 的 VM 创建更大的群集，也可使用具有许多 GPU 的 VM 创建较小的群集。

对于此工作负载，这两个选项的性能相当。 使用更少的 VM 且每个 VM 具有更多的 GPU，可帮助减少数据移动。 但是，此工作负载的每个作业的数据量并不是很大，因此 blob 存储不会受到太多限制。

### <a name="mpi-step"></a>MPI 步骤 

在 Azure 机器学习中创建管道时，用于执行并行计算的步骤之一是 MPI 步骤。 MPI 步骤有助于在可用节点之间均匀拆分数据。 只有在所有请求的节点均已准备就绪时，MPI 步骤才会执行。 如果某个节点发生故障或被抢占（如果是低优先级虚拟机），则必须重新运行 MPI 步骤。 

## <a name="security-considerations"></a>安全注意事项

### <a name="restricting-access-to-azure-blob-storage"></a>限制对 Azure Blob 存储的访问

在这个用于参考的体系结构中，Azure Blob 存储是需要保护的主要存储组件。 GitHub 存储库中显示的基线部署使用存储帐户密钥来访问 Blob 存储。 为进一步控制和保护，请考虑改用共享访问签名 (SAS)。 这允许对存储中的对象进行有限的访问，而无需对帐户密钥进行硬编码或以纯文本形式保存。 此方法特别有用，因为帐户密钥在逻辑应用的设计器界面中以纯文本形式可见。 使用 SAS 还有助于确保存储帐户具有适当监管，并且仅向有意拥有访问权限的人员授予该访问权限。

在具有更多敏感数据的情况下，请确保所有存储密钥都受到保护，因为这些密钥可授予对工作负载的所有输入和输出数据的完全访问权限。

### <a name="data-encryption-and-data-movement"></a>数据加密和数据移动

这个用于参考的体系结构使用样式传输作为批处理计分进程的示例。 在数据敏感性更强的情况下，存储中的数据应静态加密。 每次将数据从一个位置移动到另一位置时，都使用 SSL 来保护数据传输。 有关详细信息，请参阅 [Azure 存储安全指南][storage-security]。

### <a name="securing-your-computation-in-a-virtual-network"></a>保护虚拟网络中的计算

部署机器学习计算群集时，可将群集配置为在[虚拟网络][virtual-network]的子网中进行预配。 这样，群集中的计算节点便可安全地与其他虚拟机通信。 

### <a name="protecting-against-malicious-activity"></a>防止恶意活动

在存在多个用户的情况下，确保敏感数据免受恶意活动的影响。 如果其他用户可访问此部署以自定义输入数据，请注意以下预防措施和注意事项：

- 使用 RBAC 限制用户只能访问他们所需的资源。
- 预配两个单独的存储帐户。 将输入和输出数据存储在第一个帐户中。 可授予外部用户访问此帐户的权限。 将可执行脚本和输出日志文件存储在另一帐户中。 外部用户不应有权访问此帐户。 这可确保外部用户无法修改任何可执行文件（注入恶意代码），并且无法访问可能包含敏感信息的日志文件。
- 恶意用户可对作业队列执行 DDoS 攻击或在作业队列中注入格式错误的有害消息，从而导致系统锁定或导致出列错误。

## <a name="monitoring-and-logging"></a>监视和日志记录

### <a name="monitoring-batch-jobs"></a>监视 Batch 作业

运行作业时，监视进度并确保操作按预期进行，这点至关重要。 然而，在活动节点群集间进行监视可能是一项挑战。

若要了解群集的整体状态，请转到 Azure 门户的“机器学习”边栏选项卡，检查群集中节点的状态。 如果节点处于非活动状态或作业失败，则错误日志将保存到 Blob 存储，并且还可在 Azure 门户中访问。

通过将日志连接到 Application Insights，或通过运行单独的进程来轮询群集及其作业的状态，可进一步进行监视。

### <a name="logging-with-azure-machine-learning"></a>使用 Azure 机器学习进行日志记录

Azure 机器学习自动将所有 stdout/stderr 记录到关联的 Blob 存储帐户中。 除非另有指定，否则，Azure 机器学习工作区会自动预配一个存储帐户，并将日志转储到其中。 还可以使用存储资源管理器等存储导航工具来更轻松地浏览日志文件。

## <a name="cost-considerations"></a>成本注意事项

与存储和计划组件相比，这个用于参考的体系结构中使用的计算资源在成本方面遥遥领先。 其中一个主要挑战是在支持 GPU 的计算机群集中有效地并行化工作。

机器学习计算群集大小可根据队列中的作业自动增加和减少。 可以通过设置最小和最大节点数，以编程方式启用自动缩放。

对于无需立即处理的工作，可配置自动缩放，使默认状态（最小值）为零节点群集。 通过此配置，群集从零节点开始，仅在队列中检测到作业时才会增加。 如果批处理计分进程一天只进行几次或更少，则此设置可显著节约成本。

自动缩放可能不适用于彼此发生时间太接近的批处理作业。 群集启动和停止所需的时间也会产生成本，因此如果批工作负载在上一个作业结束后的几分钟内开始，则保持群集在作业之间运行可能更具成本效益。

机器学习计算还支持低优先级虚拟机。 这样，便可以在享受折扣的虚拟机上运行计算，但需要注意，这些虚拟机随时可能会被占用。 低优先级虚拟机非常适合用于非关键型批量评分工作负荷。

## <a name="deploy-the-solution"></a>部署解决方案

若要部署这个用于参考的体系结构，请按照 [GitHub 存储库][deployment]中所述的步骤进行操作。

> [!NOTE]
> 也可以使用 Azure Kubernetes 服务部署适用于深度学习模型的批量评分体系结构。 遵循此 [Github 存储库][deployment2]中所述的步骤。


<!-- links -->

[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[azcopy]: /azure/storage/common/storage-use-azcopy-linux
[blob-storage]: /azure/storage/blobs/storage-blobs-introduction
[container-instances]: /azure/container-instances/
[container-registry]: /azure/container-registry/
[deployment]: https://github.com/Azure/Batch-Scoring-Deep-Learning-Models-With-AML
[deployment2]: https://github.com/Azure/Batch-Scoring-Deep-Learning-Models-With-AKS
[ffmpeg]: https://www.ffmpeg.org/
[image-style-transfer]: https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Gatys_Image_Style_Transfer_CVPR_2016_paper.pdf
[logic-apps]: /azure/logic-apps/
[source-video]: https://happypathspublic.blob.core.windows.net/videos/orangutan.mp4
[storage-security]: /azure/storage/common/storage-security-guide
[vm-sizes-gpu]: /azure/virtual-machines/windows/sizes-gpu
[virtual-network]: /azure/machine-learning/service/how-to-enable-virtual-network
