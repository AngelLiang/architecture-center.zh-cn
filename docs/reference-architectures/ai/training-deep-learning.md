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
# <a name="distributed-training-of-deep-learning-models-on-azure"></a><span data-ttu-id="b9e14-103">Azure 上深度学习模型的分布式训练</span><span class="sxs-lookup"><span data-stu-id="b9e14-103">Distributed training of deep learning models on Azure</span></span>

<span data-ttu-id="b9e14-104">本参考体系结构演示如何在支持 GPU 的 VM 群集之间展开深度学习模型的分布式训练。</span><span class="sxs-lookup"><span data-stu-id="b9e14-104">This reference architecture shows how to conduct distributed training of deep learning models across clusters of GPU-enabled VMs.</span></span> <span data-ttu-id="b9e14-105">该方案属于图像分类，但也可以针对其他深度学习方案（例如分段与对象检测）通用化该解决方案。</span><span class="sxs-lookup"><span data-stu-id="b9e14-105">The scenario is image classification, but the solution can be generalized for other deep learning scenarios such as segmentation and object detection.</span></span>

<span data-ttu-id="b9e14-106">[GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="b9e14-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![分布式深度学习的体系结构][0]

<span data-ttu-id="b9e14-108">**场景**：图像分类是计算机影像中广泛应用的技术，其一般处理方式是训练卷积神经网络 (CNN)。</span><span class="sxs-lookup"><span data-stu-id="b9e14-108">**Scenario**: Image classification is a widely applied technique in computer vision, often tackled by training a convolutional neural network (CNN).</span></span> <span data-ttu-id="b9e14-109">对于包含大型数据集的特大模型，使用单个 GPU 进行训练可能需要花费数周甚至数月之久。</span><span class="sxs-lookup"><span data-stu-id="b9e14-109">For particularly large models with large datasets, the training process can take weeks or months on a single GPU.</span></span> <span data-ttu-id="b9e14-110">在某些情况下，由于模型过大，以致 GPU 无法容纳合理的批大小。</span><span class="sxs-lookup"><span data-stu-id="b9e14-110">In some situations, the models are so large that it's not possible to fit reasonable batch sizes onto the GPU.</span></span> <span data-ttu-id="b9e14-111">在这些情况下使用分布式训练可以缩短训练时间。</span><span class="sxs-lookup"><span data-stu-id="b9e14-111">Using distributed training in these situations can shorten the training time.</span></span>

<span data-ttu-id="b9e14-112">此具体方案使用 [Horovod][horovod] 基于 [Imagenet 数据集][imagenet]与合成数据训练 [ResNet50 CNN 模型][resnet]。</span><span class="sxs-lookup"><span data-stu-id="b9e14-112">In this specific scenario, a [ResNet50 CNN model][resnet] is trained using [Horovod][horovod] on the [Imagenet dataset][imagenet] and on synthetic data.</span></span> <span data-ttu-id="b9e14-113">本参考实现演示如何使用三个最流行的深度学习框架完成此任务：TensorFlow、Keras 和 PyTorch。</span><span class="sxs-lookup"><span data-stu-id="b9e14-113">The reference implementation shows how to accomplish this task using three of the most popular deep learning frameworks: TensorFlow, Keras, and PyTorch.</span></span>

<span data-ttu-id="b9e14-114">有多种方法能够以分布方式训练深度学习模型，包括基于同步或异步更新的数据并行方法和模型并行方法。</span><span class="sxs-lookup"><span data-stu-id="b9e14-114">There are several ways to train a deep learning model in a distributed fashion, including data-parallel and model-parallel approaches based on synchronous or asynchronous updates.</span></span> <span data-ttu-id="b9e14-115">目前，最常见的方案是基于同步更新的数据并行方法。</span><span class="sxs-lookup"><span data-stu-id="b9e14-115">Currently the most common scenario is data parallel with synchronous updates.</span></span> <span data-ttu-id="b9e14-116">这种方法最容易实现，足以满足大多数用例的需求。</span><span class="sxs-lookup"><span data-stu-id="b9e14-116">This approach is the easiest to implement and is sufficient for most use cases.</span></span>

<span data-ttu-id="b9e14-117">在基于同步更新的数据并行分布式训练中，模型跨 *n* 个硬件设备进行复制。</span><span class="sxs-lookup"><span data-stu-id="b9e14-117">In data-parallel distributed training with synchronous updates, the model is replicated across *n* hardware devices.</span></span> <span data-ttu-id="b9e14-118">训练样本的小批分割成 *n* 个微批。</span><span class="sxs-lookup"><span data-stu-id="b9e14-118">A mini-batch of training samples is divided into *n* micro-batches.</span></span> <span data-ttu-id="b9e14-119">每个设备对微批执行向前和向后传递。</span><span class="sxs-lookup"><span data-stu-id="b9e14-119">Each device performs the forward and backward passes for a micro-batch.</span></span> <span data-ttu-id="b9e14-120">设备完成该过程后，将与其他设备共享更新。</span><span class="sxs-lookup"><span data-stu-id="b9e14-120">When a device finishes the process, it shares the updates with the other devices.</span></span> <span data-ttu-id="b9e14-121">这些值用于计算整个小批的更新权重，权重将在模型之间同步。</span><span class="sxs-lookup"><span data-stu-id="b9e14-121">These values are used to calculate the updated weights of the entire mini-batch, and the weights are synchronized across the models.</span></span> <span data-ttu-id="b9e14-122">[GitHub][github] 存储库中介绍了此方案。</span><span class="sxs-lookup"><span data-stu-id="b9e14-122">This scenario is covered in the [GitHub][github] repository.</span></span>

![数据并行分布式训练][1]

<span data-ttu-id="b9e14-124">本体系结构还可用于模型并行和异步更新。</span><span class="sxs-lookup"><span data-stu-id="b9e14-124">This architecture can also be used for model-parallel and asynchronous updates.</span></span> <span data-ttu-id="b9e14-125">在模型并行分布式训练中，模型将分割到 *n* 个硬件设备，每个设备包含模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="b9e14-125">In model-parallel distributed training, the model is divided across *n* hardware devices, with each device holding a part of the model.</span></span> <span data-ttu-id="b9e14-126">在最简单的实现中，每个设备可以包含一个网络层，在向前和向后传递期间，信息将在设备之间传递。</span><span class="sxs-lookup"><span data-stu-id="b9e14-126">In the simplest implementation, each device may hold a layer of the network, and information is passed between devices during the forward and backwards pass.</span></span> <span data-ttu-id="b9e14-127">可以这种方式训练大型神经网络，但代价是性能下降，因为一个设备需要持续等待另一个设备完成向前或向后传递。</span><span class="sxs-lookup"><span data-stu-id="b9e14-127">Larger neural networks can be trained this way, but at the cost of performance, since devices are constantly waiting for each other to complete either the forward or backwards pass.</span></span> <span data-ttu-id="b9e14-128">某些高级技术使用合成渐变，可在一定程度上缓解此问题。</span><span class="sxs-lookup"><span data-stu-id="b9e14-128">Some advanced techniques try to partially alleviate this issue by using synthetic gradients.</span></span>

<span data-ttu-id="b9e14-129">训练步骤如下：</span><span class="sxs-lookup"><span data-stu-id="b9e14-129">The steps for training are:</span></span>

1. <span data-ttu-id="b9e14-130">创建要在群集上运行的、用于训练模型的脚本，然后将其传输到文件存储。</span><span class="sxs-lookup"><span data-stu-id="b9e14-130">Create scripts that will run on the cluster and train your model, then transfer them to file storage.</span></span>

1. <span data-ttu-id="b9e14-131">将数据写入 Blob 存储。</span><span class="sxs-lookup"><span data-stu-id="b9e14-131">Write the data to Blob Storage.</span></span>

1. <span data-ttu-id="b9e14-132">创建 Batch AI 文件服务器，并将 Blob 存储中的数据下载到其中。</span><span class="sxs-lookup"><span data-stu-id="b9e14-132">Create a Batch AI file server and download the data from Blob Storage onto it.</span></span>

1. <span data-ttu-id="b9e14-133">为每个深度学习框架创建 Docker 容器，并将其传输到容器注册表（Docker 中心）。</span><span class="sxs-lookup"><span data-stu-id="b9e14-133">Create the Docker containers for each deep learning framework and transfer them to a container registry (Docker Hub).</span></span>

1. <span data-ttu-id="b9e14-134">创建一个也要装载 Batch AI 文件服务器的 Batch AI 池。</span><span class="sxs-lookup"><span data-stu-id="b9e14-134">Create a Batch AI pool that also mounts the Batch AI file server.</span></span>

1. <span data-ttu-id="b9e14-135">提交作业。</span><span class="sxs-lookup"><span data-stu-id="b9e14-135">Submit jobs.</span></span> <span data-ttu-id="b9e14-136">每个作业提取相应的 Docker 映像和脚本。</span><span class="sxs-lookup"><span data-stu-id="b9e14-136">Each pulls in the appropriate Docker image and scripts.</span></span>

1. <span data-ttu-id="b9e14-137">完成该作业后，将所有结果写入文件存储。</span><span class="sxs-lookup"><span data-stu-id="b9e14-137">Once the job is completed, write all the results to Files storage.</span></span>

## <a name="architecture"></a><span data-ttu-id="b9e14-138">体系结构</span><span class="sxs-lookup"><span data-stu-id="b9e14-138">Architecture</span></span>

<span data-ttu-id="b9e14-139">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="b9e14-139">This architecture consists of the following components.</span></span>

<span data-ttu-id="b9e14-140">在此体系结构中，**[Azure Batch AI][batch-ai]** 根据需求扩展和缩减资源，发挥着核心的作用。</span><span class="sxs-lookup"><span data-stu-id="b9e14-140">**[Azure Batch AI][batch-ai]** plays the central role in this architecture by scaling resources up and down according to need.</span></span> <span data-ttu-id="b9e14-141">Batch AI 是可以帮助预配和管理 VM 群集、计划作业、收集结果、缩放资源、处理故障以及创建相应存储的服务。</span><span class="sxs-lookup"><span data-stu-id="b9e14-141">Batch AI is a service that helps provision and manage clusters of VMs, schedule jobs, gather results, scale resources, handle failures, and create appropriate storage.</span></span> <span data-ttu-id="b9e14-142">它允许使用支持 GPU 的 VM 来运行深度学习工作负荷。</span><span class="sxs-lookup"><span data-stu-id="b9e14-142">It supports GPU-enabled VMs for deep learning workloads.</span></span> <span data-ttu-id="b9e14-143">Python SDK 和命令行接口 (CLI) 适用于 Batch AI。</span><span class="sxs-lookup"><span data-stu-id="b9e14-143">A Python SDK and a command-line interface (CLI) are available for Batch AI.</span></span>

> [!NOTE]
> <span data-ttu-id="b9e14-144">Azure Batch AI 将在 2019 年 3 月停用，[Azure 机器学习服务][amls]中现已推出其大规模训练和评分功能。</span><span class="sxs-lookup"><span data-stu-id="b9e14-144">The Azure Batch AI service is retiring March 2019, and its at-scale training and scoring capabilities are now available in [Azure Machine Learning Service][amls].</span></span> <span data-ttu-id="b9e14-145">本参考体系结构即将更新为使用机器学习。机器学习提供名为 [Azure 机器学习计算][aml-compute]的托管计算目标，用于对机器学习模型进行训练、部署和评分。</span><span class="sxs-lookup"><span data-stu-id="b9e14-145">This reference architecture will be updated soon to use Machine Learning, which offers a managed compute target called [Azure Machine Learning Compute][aml-compute] for training, deploying, and scoring machine learning models.</span></span>

<span data-ttu-id="b9e14-146">**[Blob 存储][azure-blob]** 用于暂存数据。</span><span class="sxs-lookup"><span data-stu-id="b9e14-146">**[Blob storage][azure-blob]** is used to stage the data.</span></span> <span data-ttu-id="b9e14-147">在训练期间，此数据将下载到 Batch AI 文件服务器。</span><span class="sxs-lookup"><span data-stu-id="b9e14-147">This data is downloaded to a Batch AI file server during training.</span></span>

<span data-ttu-id="b9e14-148">**[Azure 文件][files]** 用于存储脚本、日志和最终训练结果。</span><span class="sxs-lookup"><span data-stu-id="b9e14-148">**[Azure Files][files]** is used to store the scripts, logs, and the final results from the training.</span></span> <span data-ttu-id="b9e14-149">文件存储适合用于存储日志和脚本，但其性能不如 Blob 存储，因此，不应将它用于数据密集型任务。</span><span class="sxs-lookup"><span data-stu-id="b9e14-149">File storage works well for storing logs and scripts, but is not as performant as Blob Storage, so it shouldn't be used for data-intensive tasks.</span></span>

<span data-ttu-id="b9e14-150">**[Batch AI 文件服务器][batch-ai-files]** 是本体系结构中用来存储训练数据的单节点 NFS 共享。</span><span class="sxs-lookup"><span data-stu-id="b9e14-150">**[Batch AI file server][batch-ai-files]** is a single-node NFS share used in this architecture to store the training data.</span></span> <span data-ttu-id="b9e14-151">Batch AI 创建 NFS 共享并将其装载到群集上。</span><span class="sxs-lookup"><span data-stu-id="b9e14-151">Batch AI creates an NFS share and mounts it on the cluster.</span></span> <span data-ttu-id="b9e14-152">Batch AI 文件服务器可提供必要的吞吐量，建议使用它来向群集提供数据。</span><span class="sxs-lookup"><span data-stu-id="b9e14-152">Batch AI file servers are the recommended way to serve data to the cluster with the necessary throughput.</span></span>

<span data-ttu-id="b9e14-153">**[Docker 中心][docker]** 用于存储 Batch AI 运行训练所需的 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="b9e14-153">**[Docker Hub][docker]** is used to store the Docker image that Batch AI uses to run the training.</span></span> <span data-ttu-id="b9e14-154">之所以为本体系结构选择 Docker 中心，是因为它易于使用，并且是 Docker 用户的默认映像存储库。</span><span class="sxs-lookup"><span data-stu-id="b9e14-154">Docker Hub was chosen for this architecture because it's easy to use and is the default image repository for Docker users.</span></span> <span data-ttu-id="b9e14-155">也可使用 [Azure 容器注册表][acr]。</span><span class="sxs-lookup"><span data-stu-id="b9e14-155">[Azure Container Registry][acr] can also be used.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="b9e14-156">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="b9e14-156">Performance considerations</span></span>

<span data-ttu-id="b9e14-157">Azure 提供四个适合用于训练深度学习模型的[支持 GPU 的 VM 类型][gpu]。</span><span class="sxs-lookup"><span data-stu-id="b9e14-157">Azure provides four [GPU-enabled VM types][gpu] suitable for training deep learning models.</span></span> <span data-ttu-id="b9e14-158">下面按从低到高的顺序提供了这些 VM 类型的价格和速度：</span><span class="sxs-lookup"><span data-stu-id="b9e14-158">They range in price and speed from low to high as follows:</span></span>

| <span data-ttu-id="b9e14-159">**Azure VM 系列**</span><span class="sxs-lookup"><span data-stu-id="b9e14-159">**Azure VM series**</span></span> | <span data-ttu-id="b9e14-160">**NVIDIA GPU**</span><span class="sxs-lookup"><span data-stu-id="b9e14-160">**NVIDIA GPU**</span></span> |
|---------------------|----------------|
| <span data-ttu-id="b9e14-161">NC</span><span class="sxs-lookup"><span data-stu-id="b9e14-161">NC</span></span>                  | <span data-ttu-id="b9e14-162">K80</span><span class="sxs-lookup"><span data-stu-id="b9e14-162">K80</span></span>            |
| <span data-ttu-id="b9e14-163">ND</span><span class="sxs-lookup"><span data-stu-id="b9e14-163">ND</span></span>                  | <span data-ttu-id="b9e14-164">P40</span><span class="sxs-lookup"><span data-stu-id="b9e14-164">P40</span></span>            |
| <span data-ttu-id="b9e14-165">NCv2</span><span class="sxs-lookup"><span data-stu-id="b9e14-165">NCv2</span></span>                | <span data-ttu-id="b9e14-166">P100</span><span class="sxs-lookup"><span data-stu-id="b9e14-166">P100</span></span>           |
| <span data-ttu-id="b9e14-167">NCv3</span><span class="sxs-lookup"><span data-stu-id="b9e14-167">NCv3</span></span>                | <span data-ttu-id="b9e14-168">V100</span><span class="sxs-lookup"><span data-stu-id="b9e14-168">V100</span></span>           |

<span data-ttu-id="b9e14-169">我们建议在横向扩展之前先纵向扩展训练。例如，在试用 K80 群集之前，先试用单个 V100。</span><span class="sxs-lookup"><span data-stu-id="b9e14-169">We recommended scaling up your training before scaling out. For example, try a single V100 before trying a cluster of K80s.</span></span>

<span data-ttu-id="b9e14-170">下图根据在 Batch AI 中使用 TensorFlow 和 Horovod 执行的[基准测试][benchmark]，显示了不同 GPU 类型的性能差异。</span><span class="sxs-lookup"><span data-stu-id="b9e14-170">The following graph shows the performance differences for different GPU types based on [benchmarking tests][benchmark] carried out using TensorFlow and Horovod on Batch AI.</span></span> <span data-ttu-id="b9e14-171">该图显示了 32 个 GPU 群集在使用不同 GPU 类型和 MPI 版本的各个模型中的吞吐量。</span><span class="sxs-lookup"><span data-stu-id="b9e14-171">The graph shows throughput of 32 GPU clusters across various models, on different GPU types and MPI versions.</span></span> <span data-ttu-id="b9e14-172">模型是在 TensorFlow 1.9 中实现的</span><span class="sxs-lookup"><span data-stu-id="b9e14-172">Models were implemented in TensorFlow 1.9</span></span>

![GPU 群集上的 TensorFlow 模型的吞吐量结果][2]

<span data-ttu-id="b9e14-174">上表中所示的每个 VM 系列包含采用 InfiniBand 的配置。</span><span class="sxs-lookup"><span data-stu-id="b9e14-174">Each VM series shown in the previous table includes a configuration with InfiniBand.</span></span> <span data-ttu-id="b9e14-175">运行分布式训练时使用 InfiniBand 配置可以加快节点之间的通信速度。</span><span class="sxs-lookup"><span data-stu-id="b9e14-175">Use the InfiniBand configurations when you run distributed training, for faster communication between nodes.</span></span> <span data-ttu-id="b9e14-176">对于利用 InfiniBand 的框架，InfiniBand 还可提高其训练缩放效率。</span><span class="sxs-lookup"><span data-stu-id="b9e14-176">InfiniBand also increases the scaling efficiency of the training for the frameworks that can take advantage of it.</span></span> <span data-ttu-id="b9e14-177">有关详细信息，请参阅 Infiniband [基准比较][benchmark]。</span><span class="sxs-lookup"><span data-stu-id="b9e14-177">For details, see the Infiniband [benchmark comparison][benchmark].</span></span>

<span data-ttu-id="b9e14-178">尽管 Batch AI 可以使用 [blobfuse][blobfuse] 适配器装载 Blob 存储，我们不建议以这种方式为分布式训练使用 Blob 存储，因为性能并不足以处理所需的吞吐量。</span><span class="sxs-lookup"><span data-stu-id="b9e14-178">Although Batch AI can mount Blob storage using the [blobfuse][blobfuse] adapter, we don't recommend using Blob Storage this way for distributed training, because the performance isn't good enough to handle the necessary throughput.</span></span> <span data-ttu-id="b9e14-179">应该如体系结构示意图中所示，将数据移到 Batch AI 文件服务器。</span><span class="sxs-lookup"><span data-stu-id="b9e14-179">Move the data to a Batch AI file server instead, as shown in the architecture diagram.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="b9e14-180">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="b9e14-180">Scalability considerations</span></span>

<span data-ttu-id="b9e14-181">由于网络开销，分布式训练的缩放效率始终小于 100% &mdash; 在设备之间同步整个模型成了一个瓶颈。</span><span class="sxs-lookup"><span data-stu-id="b9e14-181">The scaling efficiency of distributed training is always less than 100 percent due to network overhead &mdash; syncing the entire model between devices becomes a bottleneck.</span></span> <span data-ttu-id="b9e14-182">因此，分布式训练最适合用于无法在单个 GPU 中使用合理批大小训练的大型模型，或者用于解决无法通过简单并行方式分布模型来解决的问题。</span><span class="sxs-lookup"><span data-stu-id="b9e14-182">Therefore, distributed training is most suited for large models that cannot be trained using a reasonable batch size on a single GPU, or for problems that cannot be addressed by distributing the model in a simple, parallel way.</span></span>

<span data-ttu-id="b9e14-183">不建议将分布式训练用于运行超参数搜索。</span><span class="sxs-lookup"><span data-stu-id="b9e14-183">Distributed training is not recommended for running hyperparameter searches.</span></span> <span data-ttu-id="b9e14-184">与单独训练多个模型配置相比，其缩放效率会影响性能，并降低分布式方法的效率。</span><span class="sxs-lookup"><span data-stu-id="b9e14-184">The scaling efficiency affects performance and makes a distributed approach less efficient than training multiple model configurations separately.</span></span>

<span data-ttu-id="b9e14-185">提高缩放效率的方法之一是增加批大小。</span><span class="sxs-lookup"><span data-stu-id="b9e14-185">One way to increase scaling efficiency is to increase the batch size.</span></span> <span data-ttu-id="b9e14-186">但是，增加批大小必须谨慎，因为在不调整其他参数的情况下增加批大小可能会损害模型的最终性能。</span><span class="sxs-lookup"><span data-stu-id="b9e14-186">That must be done carefully, however, because increasing the batch size without adjusting the other parameters can hurt the model's final performance.</span></span>

## <a name="storage-considerations"></a><span data-ttu-id="b9e14-187">存储注意事项</span><span class="sxs-lookup"><span data-stu-id="b9e14-187">Storage considerations</span></span>

<span data-ttu-id="b9e14-188">训练深度学习模型时，一个经常被忽视的问题是数据的存储位置。</span><span class="sxs-lookup"><span data-stu-id="b9e14-188">When training deep learning models, an often-overlooked aspect is where the data is stored.</span></span> <span data-ttu-id="b9e14-189">如果存储速度太慢，以致无法跟上 GPU 的需求，则训练性能可能会下降。</span><span class="sxs-lookup"><span data-stu-id="b9e14-189">If the storage is too slow to keep up with the demands of the GPUs, training performance can degrade.</span></span>

<span data-ttu-id="b9e14-190">Batch AI 支持多种存储解决方案。</span><span class="sxs-lookup"><span data-stu-id="b9e14-190">Batch AI supports many storage solutions.</span></span> <span data-ttu-id="b9e14-191">本体系结构使用 Batch AI 文件服务器，因为该服务器在易用性与性能之间提供最佳平衡。</span><span class="sxs-lookup"><span data-stu-id="b9e14-191">This architecture uses a Batch AI file server, because it provides the best tradeoff between ease of use and performance.</span></span> <span data-ttu-id="b9e14-192">为获得最佳性能，请在本地加载数据。</span><span class="sxs-lookup"><span data-stu-id="b9e14-192">For best performance, load the data locally.</span></span> <span data-ttu-id="b9e14-193">但是，此操作可能比较繁琐，因为所有节点必须从 Blob 存储下载数据，下载 ImageNet 数据集时，可能需要耗费几个小时。</span><span class="sxs-lookup"><span data-stu-id="b9e14-193">However, this can be cumbersome, because all the nodes must download the data from Blob Storage, and with the ImageNet dataset, this can take hours.</span></span> <span data-ttu-id="b9e14-194">[Azure 高级 Blob 存储][blob]（受限公共预览版）是另一个值得考虑的选项。</span><span class="sxs-lookup"><span data-stu-id="b9e14-194">[Azure Premium Blob Storage][blob] (limited public preview) is another good option to consider.</span></span>

<span data-ttu-id="b9e14-195">请不要将 Blob 和文件存储装载为分布式训练的数据存储。</span><span class="sxs-lookup"><span data-stu-id="b9e14-195">Do not mount Blob and File storage as data stores for distributed training.</span></span> <span data-ttu-id="b9e14-196">它们的速度太慢，因此会降低训练性能。</span><span class="sxs-lookup"><span data-stu-id="b9e14-196">They are too slow and will hinder training performance.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="b9e14-197">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="b9e14-197">Security considerations</span></span>

### <a name="restrict-access-to-azure-blob-storage"></a><span data-ttu-id="b9e14-198">限制对 Azure Blob 存储的访问</span><span class="sxs-lookup"><span data-stu-id="b9e14-198">Restrict access to Azure Blob Storage</span></span>

<span data-ttu-id="b9e14-199">本体系结构使用[存储帐户密钥][security-guide]访问 Blob 存储。</span><span class="sxs-lookup"><span data-stu-id="b9e14-199">This architecture uses [storage account keys][security-guide] to access the Blob storage.</span></span> <span data-ttu-id="b9e14-200">为进一步控制和保护，请考虑改用共享访问签名 (SAS)。</span><span class="sxs-lookup"><span data-stu-id="b9e14-200">For further control and protection, consider using a shared access signature (SAS) instead.</span></span> <span data-ttu-id="b9e14-201">SAS 授予对存储中对象的有限访问权限，而无需对帐户密钥进行硬编码或以纯文本形式保存。</span><span class="sxs-lookup"><span data-stu-id="b9e14-201">This grants limited access to objects in storage, without needing to hard-code the account keys or save them in plaintext.</span></span> <span data-ttu-id="b9e14-202">使用 SAS 还有助于确保存储帐户具有适当的监管，并且仅向有意拥有访问权限的人员授予该访问权限。</span><span class="sxs-lookup"><span data-stu-id="b9e14-202">Using a SAS also helps to ensure that the storage account has proper governance, and that access is granted only to the people intended to have it.</span></span>

<span data-ttu-id="b9e14-203">在具有更多敏感数据的情况下，请确保所有存储密钥都受到保护，因为这些密钥可授予对工作负载的所有输入和输出数据的完全访问权限。</span><span class="sxs-lookup"><span data-stu-id="b9e14-203">For scenarios with more sensitive data, make sure that all of your storage keys are protected, because these keys grant full access to all input and output data from the workload.</span></span>

### <a name="encrypt-data-at-rest-and-in-motion"></a><span data-ttu-id="b9e14-204">加密静态和动态数据</span><span class="sxs-lookup"><span data-stu-id="b9e14-204">Encrypt data at rest and in motion</span></span>

<span data-ttu-id="b9e14-205">在使用敏感数据的方案中，请加密静态数据 &mdash; 即存储中的数据。</span><span class="sxs-lookup"><span data-stu-id="b9e14-205">In scenarios that use sensitive data, encrypt the data at rest &mdash; that is, the data in storage.</span></span> <span data-ttu-id="b9e14-206">每次将数据从一个位置移到另一位置时，请使用 SSL 来保护数据传输。</span><span class="sxs-lookup"><span data-stu-id="b9e14-206">Each time data moves from one location to the next, use SSL to secure the data transfer.</span></span> <span data-ttu-id="b9e14-207">有关详细信息，请参阅 [Azure 存储安全指南][security-guide]。</span><span class="sxs-lookup"><span data-stu-id="b9e14-207">For more information, see the [Azure Storage security guide][security-guide].</span></span>

### <a name="secure-data-in-a-virtual-network"></a><span data-ttu-id="b9e14-208">保护虚拟网络中的数据</span><span class="sxs-lookup"><span data-stu-id="b9e14-208">Secure data in a virtual network</span></span>

<span data-ttu-id="b9e14-209">对于生产部署，请考虑将 Batch AI 群集部署到指定的虚拟网络子网中。</span><span class="sxs-lookup"><span data-stu-id="b9e14-209">For production deployments, consider deploying the Batch AI cluster into a subnet of a virtual network that you specify.</span></span> <span data-ttu-id="b9e14-210">这样，群集中的计算节点便可安全地与其他虚拟机通信，或者与本地网络通信。</span><span class="sxs-lookup"><span data-stu-id="b9e14-210">This allows the compute nodes in the cluster to communicate securely with other virtual machines or with an on-premises network.</span></span> <span data-ttu-id="b9e14-211">还可以在虚拟网络中结合 Blob 存储使用[服务终结点][endpoints]来授予访问权限，或者在虚拟网络中结合 Batch AI 使用单节点 NFS。</span><span class="sxs-lookup"><span data-stu-id="b9e14-211">You can also use [service endpoints][endpoints] with blob storage to grant access from a virtual network or use a single-node NFS inside the virtual network with Batch AI.</span></span>

## <a name="monitoring-considerations"></a><span data-ttu-id="b9e14-212">监视注意事项</span><span class="sxs-lookup"><span data-stu-id="b9e14-212">Monitoring considerations</span></span>

<span data-ttu-id="b9e14-213">运行作业时，监视进度并确保操作按预期进行，这点至关重要。</span><span class="sxs-lookup"><span data-stu-id="b9e14-213">While running your job, it's important to monitor the progress and make sure that things are working as expected.</span></span> <span data-ttu-id="b9e14-214">然而，在活动节点群集间进行监视可能是一项挑战。</span><span class="sxs-lookup"><span data-stu-id="b9e14-214">However, it can be a challenge to monitor across a cluster of active nodes.</span></span>

<span data-ttu-id="b9e14-215">可以通过 Azure 门户或者 [Azure CLI][cli] 和 Python SDK 管理 Batch AI 文件服务器。</span><span class="sxs-lookup"><span data-stu-id="b9e14-215">The Batch AI file servers can be managed through the Azure portal or though the [Azure CLI][cli] and Python SDK.</span></span> <span data-ttu-id="b9e14-216">若要大致了解群集的总体状态，请在 Azure 门户中导航到“Batch AI”，以检查群集节点的状态。</span><span class="sxs-lookup"><span data-stu-id="b9e14-216">To get a sense of the overall state of the cluster, navigate to **Batch AI** in the Azure portal to inspect the state of the cluster nodes.</span></span> <span data-ttu-id="b9e14-217">如果某个节点处于非活动状态或作业失败，则错误日志将保存到 Blob 存储，并且可以在 Azure 门户中的“作业”下访问这些日志。</span><span class="sxs-lookup"><span data-stu-id="b9e14-217">If a node is inactive or a job fails, the error logs are saved to blob storage, and are also accessible in the Azure Portal under **Jobs**.</span></span>

<span data-ttu-id="b9e14-218">通过将日志连接到 [Azure Application Insights][ai]，或者通过运行单独的进程来轮询 Batch AI 群集及其作业的状态，可进一步进行监视。</span><span class="sxs-lookup"><span data-stu-id="b9e14-218">Enrich monitoring by connecting logs to [Azure Application Insights][ai] or by running separate processes that poll for the state of the Batch AI cluster and its jobs.</span></span>

<span data-ttu-id="b9e14-219">Batch AI 自动将所有 stdout/stderr 记录到关联的 Blob 存储帐户中。</span><span class="sxs-lookup"><span data-stu-id="b9e14-219">Batch AI automatically logs all stdout/stderr to the associate Blob storage account.</span></span> <span data-ttu-id="b9e14-220">使用 [Azure 存储资源管理器][storage-explorer]等存储导航工具可以更轻松地浏览日志文件。</span><span class="sxs-lookup"><span data-stu-id="b9e14-220">Use a storage navigation tool such as [Azure Storage Explorer][storage-explorer] for an easier experience when navigating log files.</span></span>

<span data-ttu-id="b9e14-221">此外，还可以流式传输每个作业的日志。</span><span class="sxs-lookup"><span data-stu-id="b9e14-221">It is also possible to stream the logs for each job.</span></span> <span data-ttu-id="b9e14-222">有关此选项的详细信息，请参阅 [GitHub][github] 中的开发步骤。</span><span class="sxs-lookup"><span data-stu-id="b9e14-222">For details about this option, see the development steps on [GitHub][github].</span></span>

## <a name="deployment"></a><span data-ttu-id="b9e14-223">部署</span><span class="sxs-lookup"><span data-stu-id="b9e14-223">Deployment</span></span>

<span data-ttu-id="b9e14-224">[GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="b9e14-224">The reference implementation of this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="b9e14-225">请遵循该文章中的步骤在支持 GPU 的 VM 群集之间展开深度学习模型的分布式训练。</span><span class="sxs-lookup"><span data-stu-id="b9e14-225">Follow the steps described there to conduct distributed training of deep learning models across clusters of GPU-enabled VMs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b9e14-226">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b9e14-226">Next steps</span></span>

<span data-ttu-id="b9e14-227">本体系结构的输出是保存到 Blob 存储中的已训练模型。</span><span class="sxs-lookup"><span data-stu-id="b9e14-227">The output from this architecture is a trained model that is saved to blob storage.</span></span> <span data-ttu-id="b9e14-228">可将此模型操作化，以用于实时评分或批处理评分。</span><span class="sxs-lookup"><span data-stu-id="b9e14-228">You can operationalize this model for either real-time scoring or batch scoring.</span></span> <span data-ttu-id="b9e14-229">有关详细信息，请参阅以下参考体系结构：</span><span class="sxs-lookup"><span data-stu-id="b9e14-229">For more information, see the following reference architectures:</span></span>

- <span data-ttu-id="b9e14-230">[Azure 上 Python Scikit-Learn 和深度学习模型的实时评分][real-time-scoring]</span><span class="sxs-lookup"><span data-stu-id="b9e14-230">[Real-time scoring of Python Scikit-Learn and deep learning models on Azure][real-time-scoring]</span></span>
- <span data-ttu-id="b9e14-231">[Azure 上深度学习模型的批处理评分][batch-scoring]</span><span class="sxs-lookup"><span data-stu-id="b9e14-231">[Batch scoring on Azure for deep learning models][batch-scoring]</span></span>

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