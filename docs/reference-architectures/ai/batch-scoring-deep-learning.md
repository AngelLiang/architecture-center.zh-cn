---
title: 针对深度学习模型的批处理计分
titleSuffix: Azure Reference Architectures
description: 这个用于参考的体系结构显示如何使用 Azure Batch AI 将神经样式传输应用于视频。
author: jiata
ms.date: 10/02/2018
ms.custom: azcat-ai
ms.openlocfilehash: 0396903a39d00a4131df65872a63f4b3fde8dce7
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53119874"
---
# <a name="batch-scoring-on-azure-for-deep-learning-models"></a>Azure 上针对深度学习模型的批处理计分

这个用于参考的体系结构显示如何使用 Azure Batch AI 将神经样式传输应用于视频。 样式传输是一种深度学习技术，它以另一个图像的样式构成现有图像。 可概括此体系结构，将它用于任何为深度学习使用批处理计分的场景。 [**部署此解决方案**](#deploy-the-solution)。

![使用 Azure Batch AI 的深度学习模型的体系结构图](./_images/batch-ai-deep-learning.png)

**场景**：媒体组织的一个视频需要改变样式，使其看起来像一幅特定的画。 该组织希望能以自动化的方式及时将这种样式应用于视频的所有帧。 有关神经样式传输算法的详细背景信息，请参阅[使用卷积神经网络的图像样式传输][image-style-transfer] (PDF)。

| 样式图像： | 输入/内容视频： | 输出视频： |
|--------|--------|---------|
| <img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/style_image.jpg" width="300"> | [<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video.mp4 "输入视频") 单击以观看视频 | [<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video.mp4 "输出视频") 单击以观看视频 |

这个用于参考的体系结构专为 Azure 存储中存在新媒体时而触发的工作负载而设计。 包括以下处理步骤：

1. 将选定的样式图像（如梵高的画作）和样式传输脚本上传到 Blob 存储。
1. 创建已准备好开始工作的自动缩放 Batch AI 群集。
1. 将视频文件拆分为单独的帧并将这些帧上传到 Blob 存储中。
1. 上传所有帧后，将触发器文件上传到 Blob 存储中。
1. 此文件触发逻辑应用，该应用创建在 Azure 容器实例中运行的容器。
1. 容器运行创建 Batch AI 作业的脚本。 每个作业在 Batch AI 群集的节点间并行应用神经样式传输。
1. 生成图像后，将它们保存回 Blob 存储。
1. 下载生成的帧，并将图像拼结成视频。

## <a name="architecture"></a>体系结构

该体系结构包括以下组件。

### <a name="compute"></a>计算

[Azure Batch AI][batch-ai] 用于运行神经样式传输算法。 Batch AI 通过在支持 GPU 的 VM 上提供为深度学习框架预先配置的容器化环境，支持深度学习工作负载。 Batch AI 还可将计算群集连接到 Blob 存储。

### <a name="storage"></a>存储

[Blob 存储][blob-storage]用于存储所有图像（输入图像、样式图像和输出图像）以及从 Batch AI 生成的所有日志。 Blob 存储通过 [blobfuse][blobfuse] 与 Batch AI 集成，blobfuse 是由 Blob 存储支持的开源虚拟文件系统。 对于此工作负载所需的性能而言，Blob 存储也非常经济高效。

### <a name="trigger--scheduling"></a>触发/计划

[Azure 逻辑应用][logic-apps]用于触发工作流。 逻辑应用检测到已将 BLOb 添加到容器中时，便会触发 Batch AI 进程。 逻辑应用非常适合这个用于参考的体系结构，因为它是检测 Blob 存储更改的简单方法，并提供了更改触发器的简单进程。

[Azure 容器实例][container-instances]用于运行创建 Batch AI 作业的 Python 脚本。 在 Docker 容器中运行这些脚本是按需运行它们的便捷方式。 对于此体系结构，我们使用容器实例，因为它有一个预构建的逻辑应用连接器，该连接器允许逻辑应用触发 Batch AI 作业。 容器实例可快速启动无状态进程。

[DockerHub][dockerhub] 用于存储容器实例用于建进执行作业创程的 Docker 映像。 选择 DockerHub 用于此体系结构，因为它易于使用，并且是 Docker 用户的默认映像存储库。 也可使用 [Azure 容器注册表][container-registry]。

### <a name="data-preparation"></a>数据准备工作

这个用于参考的体系结构使用“树上的猩猩”视频片段。 可从[此处][source-video]下载该片段，然后按照以下步骤针对工作流进行处理：

1. 使用 [AzCopy][azcopy] 从公共 blob 下载视频。
2. 使用 [FFmpeg][ffmpeg] 提取音频文件，以便稍后可将音频文件拼结回输出视频。
3. 使用 FFmpeg 将视频分成单个帧。 独立地并行处理这些帧。
4. 使用 AzCopy 将各个帧复制到 blob 容器中。

在此阶段，视频片段的形式可用于神经样式传输。

## <a name="performance-considerations"></a>性能注意事项

### <a name="gpu-vs-cpu"></a>GPU 与 CPU

对于深度学习工作负载，GPU 通常会远远超出 CPU，以至于通常需要相当大的 CPU 群集才可获得可比的性能。 虽然在此体系结构中可选择只使用 CPU，但 GPU 将提供更好的成本/性能配置文件。 我们建议使用最新的 [NCv3 系列] VM 大小 GPU 的 GPU 优化 VM。

默认情况下，并非所有区域都启用 GPU。 确保选择启用了 GPU 的区域。 此外，对于 GPU 优化的 VM，订阅的内核默认配额为零。 可通过打开支持请求来提高此配额。 确保订阅有足够的配额来运行工作负载。

### <a name="parallelizing-across-vms-vs-cores"></a>跨 VM 和内核并行执行

将样式传输进程作为批处理作业运行时，主要在 GPU 上运行的作业必须在 VM 间并行化。 可使用两种方法：可使用具有单个 GPU 的 VM 创建更大的群集，也可使用具有许多 GPU 的 VM 创建较小的群集。

对于此工作负载，这两个选项的性能相当。 使用更少的 VM 且每个 VM 具有更多的 GPU，可帮助减少数据移动。 但是，此工作负载的每个作业的数据量并不是很大，因此 blob 存储不会受到太多限制。

### <a name="images-batch-size-per-batch-ai-job"></a>每个 Batch AI 作业的图像批大小

另一个必须配置的参数是每个 Batch AI 作业要处理的图像数。 一方面，你希望确保工作广泛分部在节点间，如果作业失败，不必重试太多图像。 这意味着具有多个 Batch AI 作业，因此每个作业要处理的图像数量很少。 另一方面，如果每个作业处理的图像太少，则安装/启动时间会不成比例地变长。 可将作业数设置为等于群集中的最大节点数。 这在没有作业失败时最有效，因为它可以最大限度地降低安装/启动成本。 但是，如果作业失败，则可能需要重新处理大量图像。

### <a name="file-servers"></a>文件服务器

使用 Batch AI 时，可根据场景所需的吞吐量选择多个存储选项。 对于吞吐量需求低的工作负载，使用 blob 存储（通过 blobfuse）就足够了。 或者，Batch AI 还支持 Batch AI 文件服务器（托管的单节点 NFS），可自动将其安装在群集节点上，为作业提供可集中访问的存储位置。 大多数情况下，一个工作区只需要一个文件服务器，可将训练作业的数据分为不同的目录。 如果单节点 NFS 不适用于工作负载，Batch AI 支持其他存储选项，包括 Azure 文件或自定义解决方案，如 Gluster 或 Lustre 文件系统。

## <a name="security-considerations"></a>安全注意事项

### <a name="restricting-access-to-azure-blob-storage"></a>限制对 Azure Blob 存储的访问

在这个用于参考的体系结构中，Azure Blob 存储是需要保护的主要存储组件。 GitHub 存储库中显示的基线部署使用存储帐户密钥来访问 Blob 存储。 为进一步控制和保护，请考虑改用共享访问签名 (SAS)。 这允许对存储中的对象进行有限的访问，而无需对帐户密钥进行硬编码或以纯文本形式保存。 此方法特别有用，因为帐户密钥在逻辑应用的设计器界面中以纯文本形式可见。 使用 SAS 还有助于确保存储帐户具有适当监管，并且仅向有意拥有访问权限的人员授予该访问权限。

在具有更多敏感数据的情况下，请确保所有存储密钥都受到保护，因为这些密钥可授予对工作负载的所有输入和输出数据的完全访问权限。

### <a name="data-encryption-and-data-movement"></a>数据加密和数据移动

这个用于参考的体系结构使用样式传输作为批处理计分进程的示例。 在数据敏感性更强的情况下，存储中的数据应静态加密。 每次将数据从一个位置移动到另一位置时，都使用 SSL 来保护数据传输。 有关详细信息，请参阅 [Azure 存储安全指南][storage-security]。

### <a name="securing-data-in-a-virtual-network"></a>保护虚拟网络中的数据

部署 Batch AI 群集时，可将群集配置为在虚拟网络的子网内进行预配。 此操作允许群集中的计算节点安全地与其他虚拟机通信，甚至与本地网络通信。 还可结合使用[服务终结点][service-endpoints]与 Blob 存储以授予虚拟网络中的访问权限，或结合使用 VNET 中的单节点 NFS 和 Batch AI 以确保数据始终受到保护。

### <a name="protecting-against-malicious-activity"></a>防止恶意活动

在存在多个用户的情况下，确保敏感数据免受恶意活动的影响。 如果其他用户可访问此部署以自定义输入数据，请注意以下预防措施和注意事项：

- 使用 RBAC 限制用户只能访问他们所需的资源。
- 预配两个单独的存储帐户。 将输入和输出数据存储在第一个帐户中。 可授予外部用户访问此帐户的权限。 将可执行脚本和输出日志文件存储在另一帐户中。 外部用户不应有权访问此帐户。 这可确保外部用户无法修改任何可执行文件（注入恶意代码），并且无法访问可能包含敏感信息的日志文件。
- 恶意用户可对作业队列执行 DDoS 攻击或在作业队列中注入格式错误的有害消息，从而导致系统锁定或导致出列错误。

## <a name="monitoring-and-logging"></a>监视和日志记录

### <a name="monitoring-batch-ai-jobs"></a>监视 Batch AI 作业

运行作业时，监视进度并确保操作按预期进行，这点至关重要。 然而，在活动节点群集间进行监视可能是一项挑战。

要了解群集的整体状态，请转到 Azure 门户的“Batch AI”边栏选项卡以检查群集中节点的状态。 如果节点处于非活动状态或作业失败，则错误日志将保存到 Blob 存储，并且还可在 Azure 门户的“作业”边栏选项卡中访问。

通过将日志连接到 Application Insights，或通过运行单独的进程来轮询 Batch AI 群集及其作业的状态，可进一步进行监视。

### <a name="logging-in-batch-ai"></a>Batch AI 中的日志记录

Batch AI 自动将所有 stdout/stderr 记录到关联的 Blob 存储帐户中。 使用存储资源管理器等存储导航工具可更轻松地浏览日志文件。

这个用于参考的体系结构的部署步骤还展示了如何设置更简单的日志记录系统，以便将不同作业中的所有日志保存到 Blob 容器中的同一目录中，如下所示。 使用这些日志可监视每个作业和每个图像处理所需的时间。 通过此可更好地了解如何进一步优化进程。

![适用于 Azure Batch AI 的日志记录的屏幕截图](./_images/batch-ai-logging.png)

## <a name="cost-considerations"></a>成本注意事项

与存储和计划组件相比，这个用于参考的体系结构中使用的计算资源在成本方面遥遥领先。 其中一个主要挑战是在支持 GPU 的计算机群集中有效地并行化工作。

Batch AI 群集大小可根据队列中的作业自动增加和减少。 可通过以下两种方式之一使用 Batch AI 启用自动缩放。 可通过编程方式执行此操作，可在[部署步骤][deployment]中的 `.env` 文件中进行配置，也可在创建群集后直接在门户中更改缩放公式。

对于无需立即处理的工作，可配置自动缩放公式，使默认状态（最小值）为零节点群集。 通过此配置，群集从零节点开始，仅在队列中检测到作业时才会增加。 如果批处理计分进程一天只进行几次或更少，则此设置可显著节约成本。

自动缩放可能不适用于彼此发生时间太接近的批处理作业。 群集启动和停止所需的时间也会产生成本，因此如果批工作负载在上一个作业结束后的几分钟内开始，则保持群集在作业之间运行可能更具成本效益。

## <a name="deploy-the-solution"></a>部署解决方案

若要部署这个用于参考的体系结构，请按照 [GitHub 存储库][deployment]中所述的步骤进行操作。

<!-- links -->

[azcopy]: /azure/storage/common/storage-use-azcopy-linux
[batch-ai]: /azure/batch-ai/
[blobfuse]: https://github.com/Azure/azure-storage-fuse
[blob-storage]: /azure/storage/blobs/storage-blobs-introduction
[container-instances]: /azure/container-instances/
[container-registry]: /azure/container-registry/
[deployment]: https://github.com/Azure/batch-scoring-for-dl-models
[dockerhub]: https://hub.docker.com/
[ffmpeg]: https://www.ffmpeg.org/
[image-style-transfer]: https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Gatys_Image_Style_Transfer_CVPR_2016_paper.pdf
[logic-apps]: /azure/logic-apps/
[service-endpoints]: /azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network
[source-video]: https://happypathspublic.blob.core.windows.net/videos/orangutan.mp4
[storage-security]: /azure/storage/common/storage-security-guide
[vm-sizes-gpu]: /azure/virtual-machines/windows/sizes-gpu