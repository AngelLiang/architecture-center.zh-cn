---
title: Azure 上 Python 模型的批量评分
description: 生成一个可缩放的解决方案，以便使用 Azure Batch AI 按计划以并行方式对模型进行批量评分。
author: njray
ms.date: 12/13/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai, AI
ms.openlocfilehash: a291821860a8e503ba4c6173ac6d8fd449d6ebf3
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485360"
---
# <a name="batch-scoring-of-python-models-on-azure"></a><span data-ttu-id="6b704-103">Azure 上 Python 模型的批量评分</span><span class="sxs-lookup"><span data-stu-id="6b704-103">Batch scoring of Python models on Azure</span></span>

<span data-ttu-id="6b704-104">此参考体系结构演示如何生成一个可缩放的解决方案，以便使用 Azure Batch AI 按计划以并行方式对多个模型进行批量评分。</span><span class="sxs-lookup"><span data-stu-id="6b704-104">This reference architecture shows how to build a scalable solution for batch scoring many models on a schedule in parallel using Azure Batch AI.</span></span> <span data-ttu-id="6b704-105">此解决方案可以用作模板并推广到不同的问题。</span><span class="sxs-lookup"><span data-stu-id="6b704-105">The solution can be used as a template and can generalize to different problems.</span></span>

<span data-ttu-id="6b704-106"> [GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="6b704-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![Azure 上 Python 模型的批量评分](./_images/batch-scoring-python.png)

<span data-ttu-id="6b704-108">**场景**：此解决方案在 IoT 设置中监视大量设备的操作，而在该设置中，每个设备都会持续发送传感器读取内容。</span><span class="sxs-lookup"><span data-stu-id="6b704-108">**Scenario**: This solution monitors the operation of a large number of devices in an IoT setting where each device sends sensor readings continuously.</span></span> <span data-ttu-id="6b704-109">假定每个设备都已预先训练异常情况检测模型，这些模型需要用来预测一系列已在预定义的时间间隔内进行聚合的度量是否对应于某个异常情况。</span><span class="sxs-lookup"><span data-stu-id="6b704-109">Each device is assumed to have pre-trained anomaly detection models that need to be used to predict whether a series of measurements, that are aggregated over a predefined time interval, correspond to an anomaly or not.</span></span> <span data-ttu-id="6b704-110">在实际场景中，这可能是传感器读取内容流，该流需要在筛选并聚合后才能用于训练或实时评分。</span><span class="sxs-lookup"><span data-stu-id="6b704-110">In real-world scenarios, this could be a stream of sensor readings that need to be filtered and aggregated before being used in training or real-time scoring.</span></span> <span data-ttu-id="6b704-111">为了简单起见，解决方案使用的数据文件是执行评分作业时使用的文件。</span><span class="sxs-lookup"><span data-stu-id="6b704-111">For simplicity, the solution uses the same data file when executing scoring jobs.</span></span>

## <a name="architecture"></a><span data-ttu-id="6b704-112">体系结构</span><span class="sxs-lookup"><span data-stu-id="6b704-112">Architecture</span></span>

<span data-ttu-id="6b704-113">该体系结构包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="6b704-113">This architecture consists of the following components:</span></span>

<span data-ttu-id="6b704-114">[Azure 事件中心][event-hubs]。</span><span class="sxs-lookup"><span data-stu-id="6b704-114">[Azure Event Hubs][event-hubs].</span></span> <span data-ttu-id="6b704-115">此消息引入服务可以每秒引入数百万条事件消息。</span><span class="sxs-lookup"><span data-stu-id="6b704-115">This message ingestion service can ingest millions of event messages per second.</span></span> <span data-ttu-id="6b704-116">在此体系结构中，传感器将数据流发送到事件中心。</span><span class="sxs-lookup"><span data-stu-id="6b704-116">In this architecture, sensors send a stream of data to the event hub.</span></span>

<span data-ttu-id="6b704-117">[Azure 流分析][stream-analytics]。</span><span class="sxs-lookup"><span data-stu-id="6b704-117">[Azure Stream Analytics][stream-analytics].</span></span> <span data-ttu-id="6b704-118">一个事件处理引擎。</span><span class="sxs-lookup"><span data-stu-id="6b704-118">An event-processing engine.</span></span> <span data-ttu-id="6b704-119">流分析作业从事件中心读取数据流，并执行流处理操作。</span><span class="sxs-lookup"><span data-stu-id="6b704-119">A Stream Analytics job reads the data streams from the event hub and performs stream processing.</span></span>

<span data-ttu-id="6b704-120">[Azure Batch AI][batch-ai]。</span><span class="sxs-lookup"><span data-stu-id="6b704-120">[Azure Batch AI][batch-ai].</span></span> <span data-ttu-id="6b704-121">此分布式计算引擎用于在 Azure 中大规模训练和测试机器学习和 AI 模型。</span><span class="sxs-lookup"><span data-stu-id="6b704-121">This distributed computing engine is used to train and test machine learning and AI models at scale in Azure.</span></span> <span data-ttu-id="6b704-122">Batch AI 通过自动缩放选项来按需创建虚拟机，该选项允许 Batch AI 群集中的每个节点针对特定的传感器运行评分作业。</span><span class="sxs-lookup"><span data-stu-id="6b704-122">Batch AI creates virtual machines on demand with an automatic scaling option, where each node in the Batch AI cluster runs a scoring job for a specific sensor.</span></span> <span data-ttu-id="6b704-123">评分的 Python [脚本][python-script]运行在 Docker 容器（此类容器在群集的每个节点上创建）中，可以在其中读取相关的传感器数据、生成预测并将其存储在 Blob 存储中。</span><span class="sxs-lookup"><span data-stu-id="6b704-123">The scoring Python [script][python-script] runs in Docker containers that are created on each node of the cluster, where it reads the relevant sensor data, generates predictions and stores them in Blob storage.</span></span>

<span data-ttu-id="6b704-124">[Azure Blob 存储][storage]。</span><span class="sxs-lookup"><span data-stu-id="6b704-124">[Azure Blob Storage][storage].</span></span> <span data-ttu-id="6b704-125">Blob 容器用于存储预先训练的模型、数据和输出预测。</span><span class="sxs-lookup"><span data-stu-id="6b704-125">Blob containers are used to store the pretrained models, the data, and the output predictions.</span></span> <span data-ttu-id="6b704-126">模型上传到 Blob 存储的 [create\_resources.ipynb][create-resources] 笔记本中。</span><span class="sxs-lookup"><span data-stu-id="6b704-126">The models are uploaded to Blob storage in the [create\_resources.ipynb][create-resources] notebook.</span></span> <span data-ttu-id="6b704-127">这些[单类 SVM][one-class-svm] 模型进行训练时所使用的数据代表不同设备的不同传感器的值。</span><span class="sxs-lookup"><span data-stu-id="6b704-127">These [one-class SVM][one-class-svm] models are trained on data that represents values of different sensors for different devices.</span></span> <span data-ttu-id="6b704-128">此解决方案假定数据值按固定的时间间隔聚合。</span><span class="sxs-lookup"><span data-stu-id="6b704-128">This solution assumes that the data values are aggregated over a fixed interval of time.</span></span>

<span data-ttu-id="6b704-129">[Azure 逻辑应用][logic-apps]。</span><span class="sxs-lookup"><span data-stu-id="6b704-129">[Azure Logic Apps][logic-apps].</span></span> <span data-ttu-id="6b704-130">此解决方案创建一个按小时运行 Batch AI 作业的逻辑应用。</span><span class="sxs-lookup"><span data-stu-id="6b704-130">This solution creates a Logic App that runs hourly Batch AI jobs.</span></span> <span data-ttu-id="6b704-131">可以通过逻辑应用轻松地为解决方案创建运行时工作流和计划。</span><span class="sxs-lookup"><span data-stu-id="6b704-131">Logic Apps provides an easy way to create the runtime workflow and scheduling for the solution.</span></span> <span data-ttu-id="6b704-132">Batch AI 作业使用 Python [脚本][script]提交，该脚本也在 Docker 容器中运行。</span><span class="sxs-lookup"><span data-stu-id="6b704-132">The Batch AI jobs are submitted using a Python [script][script] that also runs in a Docker container.</span></span>

<span data-ttu-id="6b704-133">[Azure 容器注册表][acr]。</span><span class="sxs-lookup"><span data-stu-id="6b704-133">[Azure Container Registry][acr].</span></span> <span data-ttu-id="6b704-134">Docker 映像在 Batch AI 和逻辑应用中使用，在 [create\_resources.ipynb][create-resources] 笔记本中创建，然后推送到容器注册表。</span><span class="sxs-lookup"><span data-stu-id="6b704-134">Docker images are used in both Batch AI and Logic Apps and are created in the [create\_resources.ipynb][create-resources] notebook, then pushed to Container Registry.</span></span> <span data-ttu-id="6b704-135">这样就可以通过其他 Azure 服务（此解决方案中的逻辑应用和 Batch AI）方便地托管映像并实例化容器。</span><span class="sxs-lookup"><span data-stu-id="6b704-135">This provides a convenient way to host images and instantiate containers through other Azure services—Logic Apps and Batch AI in this solution.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="6b704-136">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="6b704-136">Performance considerations</span></span>

<span data-ttu-id="6b704-137">对于标准 Python 模型，仅使用 CPU 通常就足以处理工作负荷。</span><span class="sxs-lookup"><span data-stu-id="6b704-137">For standard Python models, it's generally accepted that CPUs are sufficient to handle the workload.</span></span> <span data-ttu-id="6b704-138">此体系结构使用 CPU。</span><span class="sxs-lookup"><span data-stu-id="6b704-138">This architecture uses CPUs.</span></span> <span data-ttu-id="6b704-139">但是，对于[深度学习工作负荷][deep]，GPU 通常会远远超出 CPU，通常需要相当大的 CPU 群集才可获得可比的性能。</span><span class="sxs-lookup"><span data-stu-id="6b704-139">However, for [deep learning workloads][deep], GPUs generally outperform CPUs by a considerable amount—a sizeable cluster of CPUs is usually needed to get comparable performance.</span></span>

### <a name="parallelizing-across-vms-vs-cores"></a><span data-ttu-id="6b704-140">跨 VM 和内核并行执行</span><span class="sxs-lookup"><span data-stu-id="6b704-140">Parallelizing across VMs vs cores</span></span>

<span data-ttu-id="6b704-141">以批处理模式运行许多模型的评分过程时，作业需跨 VM 实现并行化。</span><span class="sxs-lookup"><span data-stu-id="6b704-141">When running scoring processes of many models in batch mode, the jobs need to be parallelized across VMs.</span></span> <span data-ttu-id="6b704-142">可使用两种方法：</span><span class="sxs-lookup"><span data-stu-id="6b704-142">Two approaches are possible:</span></span>

* <span data-ttu-id="6b704-143">使用低成本 VM 创建较大的群集。</span><span class="sxs-lookup"><span data-stu-id="6b704-143">Create a larger cluster using low-cost VMs.</span></span>

* <span data-ttu-id="6b704-144">使用高性能 VM 创建较小的群集，每个 VM 上提供的核心数较多。</span><span class="sxs-lookup"><span data-stu-id="6b704-144">Create a smaller cluster using high performing VMs with more cores available on each.</span></span>

<span data-ttu-id="6b704-145">通常情况下，为标准 Python 模型评分不像为深度学习模型评分那样苛刻，一个小的群集就可以有效地处理大量的排队模型。</span><span class="sxs-lookup"><span data-stu-id="6b704-145">In general, scoring of standard Python models is not as demanding as scoring of deep learning models, and a small cluster should be able to handle a large number of queued models efficiently.</span></span> <span data-ttu-id="6b704-146">可以在数据集大小增加时增加群集节点的数目。</span><span class="sxs-lookup"><span data-stu-id="6b704-146">You can increase the number of cluster nodes as the dataset sizes increase.</span></span>

<span data-ttu-id="6b704-147">在此方案中，为了方便起见，在单个 Batch AI 作业中提交一个评分任务。</span><span class="sxs-lookup"><span data-stu-id="6b704-147">For convenience in this scenario, one scoring task is submitted within a single Batch AI job.</span></span> <span data-ttu-id="6b704-148">不过，在同一 Batch AI 作业中为多个数据区块评分可能更有效。</span><span class="sxs-lookup"><span data-stu-id="6b704-148">However, it can be more efficient to score multiple data chunks within the same Batch AI job.</span></span> <span data-ttu-id="6b704-149">在这些情况下，请编写自定义代码，以便在多个数据集中进行读取，并在执行单个 Batch AI 作业期间为这些数据集执行评分脚本。</span><span class="sxs-lookup"><span data-stu-id="6b704-149">In those cases, write custom code to read in multiple datasets and execute the scoring script for those during a single Batch AI job execution.</span></span>

### <a name="file-servers"></a><span data-ttu-id="6b704-150">文件服务器</span><span class="sxs-lookup"><span data-stu-id="6b704-150">File servers</span></span>

<span data-ttu-id="6b704-151">使用 Batch AI 时，可根据场景所需的吞吐量选择多个存储选项。</span><span class="sxs-lookup"><span data-stu-id="6b704-151">When using Batch AI, you can choose multiple storage options depending on the throughput needed for your scenario.</span></span> <span data-ttu-id="6b704-152">对于吞吐量需求低的工作负载，使用 Blob 存储就足够了。</span><span class="sxs-lookup"><span data-stu-id="6b704-152">For workloads with low throughput requirements, using blob storage should be enough.</span></span> <span data-ttu-id="6b704-153">或者，Batch AI 还支持 [Batch AI 文件服务器][bai-file-server]（托管的单节点 NFS），可自动将其安装在群集节点上，为作业提供可集中访问的存储位置。</span><span class="sxs-lookup"><span data-stu-id="6b704-153">Alternatively, Batch AI also supports a [Batch AI File Server][bai-file-server], a managed, single-node NFS, which can be automatically mounted on cluster nodes to provide a centrally accessible storage location for jobs.</span></span> <span data-ttu-id="6b704-154">大多数情况下，一个工作区只需要一个文件服务器；可将训练作业的数据分为不同的目录。</span><span class="sxs-lookup"><span data-stu-id="6b704-154">For most cases, only one file server is needed in a workspace, and you can separate data for your training jobs into different directories.</span></span>

<span data-ttu-id="6b704-155">如果单节点 NFS 不适用于工作负荷，Batch AI 会支持其他存储选项，包括 [Azure 文件][azure-files]和自定义解决方案，如 Gluster 或 Lustre 文件系统。</span><span class="sxs-lookup"><span data-stu-id="6b704-155">If a single-node NFS isn't appropriate for your workloads, Batch AI supports other storage options, including [Azure Files][azure-files] and custom solutions such as a Gluster or Lustre file system.</span></span>

## <a name="management-considerations"></a><span data-ttu-id="6b704-156">管理注意事项</span><span class="sxs-lookup"><span data-stu-id="6b704-156">Management considerations</span></span>

### <a name="monitoring-batch-ai-jobs"></a><span data-ttu-id="6b704-157">监视 Batch AI 作业</span><span class="sxs-lookup"><span data-stu-id="6b704-157">Monitoring Batch AI jobs</span></span>

<span data-ttu-id="6b704-158">监视正在运行的作业的进度很重要，但在活动节点群集间进行监视可能是一项挑战。</span><span class="sxs-lookup"><span data-stu-id="6b704-158">It's important to monitor the progress of running jobs, but it can be a challenge to monitor across a cluster of active nodes.</span></span> <span data-ttu-id="6b704-159">若要了解群集的整体状态，请转到 [Azure 门户][portal]的“Batch AI”边栏选项卡，检查群集中节点的状态。</span><span class="sxs-lookup"><span data-stu-id="6b704-159">To get a sense of the overall state of the cluster, go to the **Batch AI** blade of the [Azure Portal][portal] to inspect the state of the nodes in the cluster.</span></span> <span data-ttu-id="6b704-160">如果节点处于非活动状态或作业失败，则错误日志将保存到 Blob 存储，并且还可在门户的“作业”边栏选项卡中访问。</span><span class="sxs-lookup"><span data-stu-id="6b704-160">If a node is inactive or a job has failed, the error logs are saved to blob storage, and are also accessible in the **Jobs** blade of the portal.</span></span>

<span data-ttu-id="6b704-161">若要进行内容更丰富的监视，请将日志连接到 [Application Insights][ai]，或通过运行单独的进程来轮询 Batch AI 群集及其作业的状态。</span><span class="sxs-lookup"><span data-stu-id="6b704-161">For richer monitoring, connect logs to [Application Insights][ai], or run separate processes to poll for the state of the Batch AI cluster and its jobs.</span></span>

### <a name="logging-in-batch-ai"></a><span data-ttu-id="6b704-162">Batch AI 中的日志记录</span><span class="sxs-lookup"><span data-stu-id="6b704-162">Logging in Batch AI</span></span>

<span data-ttu-id="6b704-163">Batch AI 将所有 stdout/stderr 记录到关联的 Azure 存储帐户中。</span><span class="sxs-lookup"><span data-stu-id="6b704-163">Batch AI logs all stdout/stderr to the associated Azure storage account.</span></span> <span data-ttu-id="6b704-164">若要轻松地导航日志文件，请使用 [Azure 存储资源管理器][explorer]等存储导航工具。</span><span class="sxs-lookup"><span data-stu-id="6b704-164">For easy navigation of the log files, use a storage navigation tool such as [Azure Storage Explorer][explorer].</span></span>

<span data-ttu-id="6b704-165">部署此参考体系结构时，可以选择设置更简单的日志记录系统。</span><span class="sxs-lookup"><span data-stu-id="6b704-165">When you deploy this reference architecture, you have the option to set up a simpler logging system.</span></span> <span data-ttu-id="6b704-166">使用此选项时，可以将不同作业中的所有日志保存到 Blob 容器中的同一目录中，如下所示。</span><span class="sxs-lookup"><span data-stu-id="6b704-166">With this option, all the logs across the different jobs are saved to the same directory in your blob container as shown below.</span></span> <span data-ttu-id="6b704-167">使用这些日志可监视每个作业和每个图像处理所需的时间，以便更好地了解如何优化此过程。</span><span class="sxs-lookup"><span data-stu-id="6b704-167">Use these logs to monitor how long it takes for each job and each image to process, so you have a better sense of how to optimize the process.</span></span>

![Azure 存储资源管理器](./_images/batch-scoring-python-monitor.png)

## <a name="cost-considerations"></a><span data-ttu-id="6b704-169">成本注意事项</span><span class="sxs-lookup"><span data-stu-id="6b704-169">Cost considerations</span></span>

<span data-ttu-id="6b704-170">在此体系结构中使用的最昂贵组件是计算资源。</span><span class="sxs-lookup"><span data-stu-id="6b704-170">The most expensive components used in this reference architecture are the compute resources.</span></span>

<span data-ttu-id="6b704-171">Batch AI 群集大小根据队列中的作业增加和减少。</span><span class="sxs-lookup"><span data-stu-id="6b704-171">The Batch AI cluster size scales up and down depending on the jobs in the queue.</span></span> <span data-ttu-id="6b704-172">可通过以下两种方式之一使用 Batch AI 启用[自动缩放][automatic-scaling]。</span><span class="sxs-lookup"><span data-stu-id="6b704-172">You can enable [automatic scaling][automatic-scaling] with Batch AI in one of two ways.</span></span> <span data-ttu-id="6b704-173">可通过编程方式执行此操作（可在[部署步骤][github]中的 .env 文件中进行配置），也可在创建群集后直接在门户中更改缩放公式。</span><span class="sxs-lookup"><span data-stu-id="6b704-173">You can do so programmatically, which can be configured in the .env file that is part of the [deployment steps][github], or you can change the scale formula directly in the portal after the cluster is created.</span></span>

<span data-ttu-id="6b704-174">对于无需立即处理的工作，可配置自动缩放公式，使默认状态（最小值）为零节点群集。</span><span class="sxs-lookup"><span data-stu-id="6b704-174">For work that doesn't require immediate processing, configure the automatic scaling formula so the default state (minimum) is a cluster of zero nodes.</span></span> <span data-ttu-id="6b704-175">通过此配置，群集从零节点开始，仅在队列中检测到作业时才会增加。</span><span class="sxs-lookup"><span data-stu-id="6b704-175">With this configuration, the cluster starts with zero nodes and only scales up when it detects jobs in the queue.</span></span> <span data-ttu-id="6b704-176">如果批处理计分进程一天只进行几次或更少，则此设置可显著节约成本。</span><span class="sxs-lookup"><span data-stu-id="6b704-176">If the batch scoring process only happens a few times a day or less, this setting enables significant cost savings.</span></span>

<span data-ttu-id="6b704-177">自动缩放可能不适用于彼此发生时间太接近的批处理作业。</span><span class="sxs-lookup"><span data-stu-id="6b704-177">Automatic scaling may not be appropriate for batch jobs that happen too close to each other.</span></span> <span data-ttu-id="6b704-178">群集启动和停止所需的时间也会产生成本，因此如果批工作负载在上一个作业结束后的几分钟内开始，则保持群集在作业之间运行可能更具成本效益。</span><span class="sxs-lookup"><span data-stu-id="6b704-178">The time that it takes for a cluster to spin up and spin down also incur a cost, so if a batch workload begins only a few minutes after the previous job ends, it might be more cost effective to keep the cluster running between jobs.</span></span> <span data-ttu-id="6b704-179">这取决于评分过程是计划高频率运行（例如，每小时运行一次）还是低频率运行（例如，每月运行一次）。</span><span class="sxs-lookup"><span data-stu-id="6b704-179">That depends on whether scoring processes are scheduled to run at a high frequency (every hour, for example), or less frequently (once a month, for example).</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="6b704-180">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="6b704-180">Deploy the solution</span></span>

<span data-ttu-id="6b704-181">[GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="6b704-181">The reference implementation of this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="6b704-182">按照其中的设置步骤生成一个可缩放的解决方案，以便使用 Batch AI 以并行方式对多个模型进行评分。</span><span class="sxs-lookup"><span data-stu-id="6b704-182">Follow the setup steps there to build a scalable solution for scoring many models in parallel using Batch AI.</span></span>

[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[automatic-scaling]: /azure/batch/batch-automatic-scaling
[azure-files]: /azure/storage/files/storage-files-introduction
[batch-ai]: /azure/batch-ai/
[bai-file-server]: /azure/batch-ai/resource-concepts#file-server
[create-resources]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/create_resources.ipynb
[deep]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[event-hubs]: /azure/event-hubs/event-hubs-geo-dr
[explorer]: https://azure.microsoft.com/en-us/features/storage-explorer/
[github]: https://github.com/Azure/BatchAIAnomalyDetection
[logic-apps]: /azure/logic-apps/logic-apps-overview
[one-class-svm]: http://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html
[portal]: https://portal.azure.com
[python-script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/batchai/predict.py
[script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/sched/submit_jobs.py
[storage]: /azure/storage/blobs/storage-blobs-overview
[stream-analytics]: /azure/stream-analytics/
