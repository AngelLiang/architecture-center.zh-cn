---
title: Azure 上 Python 模型的批量评分
description: 生成一个可缩放的解决方案，以使用 Azure 机器学习服务按计划以并行方式对模型进行批量评分。
author: njray
ms.date: 01/30/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai, AI
ms.openlocfilehash: b7607984bcf2c4bd046421aeb6e9d52dd8e7c18e
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887737"
---
# <a name="batch-scoring-of-python-machine-learning-models-on-azure"></a><span data-ttu-id="47b35-103">在 Azure 上的 Python 机器学习批处理评分模型</span><span class="sxs-lookup"><span data-stu-id="47b35-103">Batch scoring of Python machine learning models on Azure</span></span>

<span data-ttu-id="47b35-104">本参考体系结构演示如何生成一个可缩放的解决方案，以使用 Azure 机器学习服务按计划以并行方式对多个模型进行批量评分。</span><span class="sxs-lookup"><span data-stu-id="47b35-104">This reference architecture shows how to build a scalable solution for batch scoring many models on a schedule in parallel using Azure Machine Learning Service.</span></span> <span data-ttu-id="47b35-105">此解决方案可以用作模板并推广到不同的问题。</span><span class="sxs-lookup"><span data-stu-id="47b35-105">The solution can be used as a template and can generalize to different problems.</span></span>

<span data-ttu-id="47b35-106">[GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="47b35-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![Azure 上 Python 模型的批量评分](./_images/batch-scoring-python.png)

<span data-ttu-id="47b35-108">**场景**：此解决方案在 IoT 设置中监视大量设备的操作，而在该设置中，每个设备都会持续发送传感器读取内容。</span><span class="sxs-lookup"><span data-stu-id="47b35-108">**Scenario**: This solution monitors the operation of a large number of devices in an IoT setting where each device sends sensor readings continuously.</span></span> <span data-ttu-id="47b35-109">假设每个设备与预先训练的异常情况检测模型相关联，这些模型用于预测一系列已在预定义的时间间隔内进行聚合的度量是否对应于某种异常情况。</span><span class="sxs-lookup"><span data-stu-id="47b35-109">Each device is assumed to be associated with pretrained anomaly detection models that need to be used to predict whether a series of measurements, that are aggregated over a predefined time interval, correspond to an anomaly or not.</span></span> <span data-ttu-id="47b35-110">在实际场景中，这可能是传感器读取内容流，该流需要在筛选并聚合后才能用于训练或实时评分。</span><span class="sxs-lookup"><span data-stu-id="47b35-110">In real-world scenarios, this could be a stream of sensor readings that need to be filtered and aggregated before being used in training or real-time scoring.</span></span> <span data-ttu-id="47b35-111">为简单起见，此解决方案使用的数据文件是执行评分作业时使用的文件。</span><span class="sxs-lookup"><span data-stu-id="47b35-111">For simplicity, this solution uses the same data file when executing scoring jobs.</span></span>

<span data-ttu-id="47b35-112">本参考体系结构适用于按计划触发的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="47b35-112">This reference architecture is designed for workloads that are triggered on a schedule.</span></span> <span data-ttu-id="47b35-113">包括以下处理步骤：</span><span class="sxs-lookup"><span data-stu-id="47b35-113">Processing involves the following steps:</span></span>
1.  <span data-ttu-id="47b35-114">将要引入的传感器读数发送到 Azure 事件中心。</span><span class="sxs-lookup"><span data-stu-id="47b35-114">Send sensor readings for ingestion to Azure Event Hubs.</span></span>
2.  <span data-ttu-id="47b35-115">执行流处理并存储原始数据。</span><span class="sxs-lookup"><span data-stu-id="47b35-115">Perform stream processing and store the raw data.</span></span>
3.  <span data-ttu-id="47b35-116">将数据发送到已准备好接管工作的机器学习群集。</span><span class="sxs-lookup"><span data-stu-id="47b35-116">Send the data to a Machine Learning cluster that is ready to start taking work.</span></span> <span data-ttu-id="47b35-117">群集中的每个节点针对特定的传感器运行评分作业。</span><span class="sxs-lookup"><span data-stu-id="47b35-117">Each node in the cluster runs a scoring job for a specific sensor.</span></span> 
4.  <span data-ttu-id="47b35-118">执行评分管道，该管道使用机器学习 Python 脚本并行运行评分作业。</span><span class="sxs-lookup"><span data-stu-id="47b35-118">Execute the scoring pipeline, which runs the scoring jobs in parallel using Machine Learning Python scripts.</span></span> <span data-ttu-id="47b35-119">创建、发布管道，并将其计划为按预定义的时间间隔运行。</span><span class="sxs-lookup"><span data-stu-id="47b35-119">The pipeline is created, published, and scheduled to run on a predefined interval of time.</span></span>
5.  <span data-ttu-id="47b35-120">生成预测结果，并将其存储在 Blob 存储中，以供日后使用。</span><span class="sxs-lookup"><span data-stu-id="47b35-120">Generate predictions and store them in Blob storage for later consumption.</span></span>

## <a name="architecture"></a><span data-ttu-id="47b35-121">体系结构</span><span class="sxs-lookup"><span data-stu-id="47b35-121">Architecture</span></span>

<span data-ttu-id="47b35-122">该体系结构包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="47b35-122">This architecture consists of the following components:</span></span>

<span data-ttu-id="47b35-123">[Azure 事件中心][event-hubs]。</span><span class="sxs-lookup"><span data-stu-id="47b35-123">[Azure Event Hubs][event-hubs].</span></span> <span data-ttu-id="47b35-124">此消息引入服务可以每秒引入数百万条事件消息。</span><span class="sxs-lookup"><span data-stu-id="47b35-124">This message ingestion service can ingest millions of event messages per second.</span></span> <span data-ttu-id="47b35-125">在此体系结构中，传感器将数据流发送到事件中心。</span><span class="sxs-lookup"><span data-stu-id="47b35-125">In this architecture, sensors send a stream of data to the event hub.</span></span>

<span data-ttu-id="47b35-126">[Azure 流分析][stream-analytics]。</span><span class="sxs-lookup"><span data-stu-id="47b35-126">[Azure Stream Analytics][stream-analytics].</span></span> <span data-ttu-id="47b35-127">一个事件处理引擎。</span><span class="sxs-lookup"><span data-stu-id="47b35-127">An event-processing engine.</span></span> <span data-ttu-id="47b35-128">流分析作业从事件中心读取数据流，并执行流处理操作。</span><span class="sxs-lookup"><span data-stu-id="47b35-128">A Stream Analytics job reads the data streams from the event hub and performs stream processing.</span></span>

<span data-ttu-id="47b35-129">[Azure SQL 数据库][sql-database]。</span><span class="sxs-lookup"><span data-stu-id="47b35-129">[Azure SQL Database][sql-database].</span></span> <span data-ttu-id="47b35-130">传感器读数中的数据加载到 SQL 数据库中。</span><span class="sxs-lookup"><span data-stu-id="47b35-130">Data from the sensor readings is loaded into SQL Database.</span></span> <span data-ttu-id="47b35-131">SQL 是众所周知的用于存储已处理的流式传输数据（表格式的结构化数据）的存储；但也可以使用其他数据存储。</span><span class="sxs-lookup"><span data-stu-id="47b35-131">SQL is a familiar way to store the processed, streamed data (which is tabular and structured), but other data stores can be used.</span></span>

<span data-ttu-id="47b35-132">[Azure 机器学习服务][amls]。</span><span class="sxs-lookup"><span data-stu-id="47b35-132">[Azure Machine Learning Service][amls].</span></span> <span data-ttu-id="47b35-133">机器学习是用于大规模训练、评分、部署和管理机器学习模型的云服务。</span><span class="sxs-lookup"><span data-stu-id="47b35-133">Machine Learning is a cloud service for training, scoring, deploying, and managing machine learning models at scale.</span></span> <span data-ttu-id="47b35-134">在批量评分的上下文中，机器学习通过自动缩放选项来按需创建虚拟机群集，该群集中的每个节点针对特定的传感器运行评分作业。</span><span class="sxs-lookup"><span data-stu-id="47b35-134">In the context of batch scoring, Machine Learning creates a cluster of virtual machines on demand with an automatic scaling option, where each node in the cluster runs a scoring job for a specific sensor.</span></span> <span data-ttu-id="47b35-135">评分作业以机器学习管理的排队 Python 脚本步骤的形式并行执行。</span><span class="sxs-lookup"><span data-stu-id="47b35-135">The scoring jobs are executed in parallel as Python-script steps that are queued and managed by Machine Learning.</span></span> <span data-ttu-id="47b35-136">这些步骤是创建、发布并计划为按预定义的时间间隔运行的机器学习管道的一部分。</span><span class="sxs-lookup"><span data-stu-id="47b35-136">These steps are part of a Machine Learning pipeline that is created, published, and scheduled to run on a predefined interval of time.</span></span>

<span data-ttu-id="47b35-137">[Azure Blob 存储][storage]。</span><span class="sxs-lookup"><span data-stu-id="47b35-137">[Azure Blob Storage][storage].</span></span> <span data-ttu-id="47b35-138">Blob 容器用于存储预先训练的模型、数据和输出预测。</span><span class="sxs-lookup"><span data-stu-id="47b35-138">Blob containers are used to store the pretrained models, the data, and the output predictions.</span></span> <span data-ttu-id="47b35-139">模型上传到 Blob 存储的 [01_create_resources.ipynb][create-resources] 笔记本中。</span><span class="sxs-lookup"><span data-stu-id="47b35-139">The models are uploaded to Blob storage in the [01_create_resources.ipynb][create-resources] notebook.</span></span> <span data-ttu-id="47b35-140">这些[单类 SVM][one-class-svm] 模型进行训练时所使用的数据代表不同设备的不同传感器的值。</span><span class="sxs-lookup"><span data-stu-id="47b35-140">These [one-class SVM][one-class-svm] models are trained on data that represents values of different sensors for different devices.</span></span> <span data-ttu-id="47b35-141">此解决方案假定数据值按固定的时间间隔聚合。</span><span class="sxs-lookup"><span data-stu-id="47b35-141">This solution assumes that the data values are aggregated over a fixed interval of time.</span></span>

<span data-ttu-id="47b35-142">[Azure 容器注册表][acr]。</span><span class="sxs-lookup"><span data-stu-id="47b35-142">[Azure Container Registry][acr].</span></span> <span data-ttu-id="47b35-143">Python 评分[脚本][pyscript]在 Docker 容器（此类容器在群集的每个节点上创建）中运行，可在其中读取相关的传感器数据、生成预测并将其存储在 Blob 存储中。</span><span class="sxs-lookup"><span data-stu-id="47b35-143">The scoring Python [script][pyscript] runs in Docker containers that are created on each node of the cluster, where it reads the relevant sensor data, generates predictions and stores them in Blob storage.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="47b35-144">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="47b35-144">Performance considerations</span></span>

<span data-ttu-id="47b35-145">对于标准 Python 模型，仅使用 CPU 通常就足以处理工作负荷。</span><span class="sxs-lookup"><span data-stu-id="47b35-145">For standard Python models, it's generally accepted that CPUs are sufficient to handle the workload.</span></span> <span data-ttu-id="47b35-146">此体系结构使用 CPU。</span><span class="sxs-lookup"><span data-stu-id="47b35-146">This architecture uses CPUs.</span></span> <span data-ttu-id="47b35-147">但是，对于[深度学习工作负荷][deep]，Gpu 通常优于由相当长的 Cpu&mdash;相当大的 Cpu 群集通常需要以获取可比较的性能。</span><span class="sxs-lookup"><span data-stu-id="47b35-147">However, for [deep learning workloads][deep], GPUs generally outperform CPUs by a considerable amount &mdash; a sizeable cluster of CPUs is usually needed to get comparable performance.</span></span>

### <a name="parallelizing-across-vms-versus-cores"></a><span data-ttu-id="47b35-148">在 Vm 上与内核并行执行</span><span class="sxs-lookup"><span data-stu-id="47b35-148">Parallelizing across VMs versus cores</span></span>

<span data-ttu-id="47b35-149">以批处理模式运行许多模型的评分过程时，作业需跨 VM 实现并行化。</span><span class="sxs-lookup"><span data-stu-id="47b35-149">When running scoring processes of many models in batch mode, the jobs need to be parallelized across VMs.</span></span> <span data-ttu-id="47b35-150">可使用两种方法：</span><span class="sxs-lookup"><span data-stu-id="47b35-150">Two approaches are possible:</span></span>

* <span data-ttu-id="47b35-151">使用低成本 VM 创建较大的群集。</span><span class="sxs-lookup"><span data-stu-id="47b35-151">Create a larger cluster using low-cost VMs.</span></span>

* <span data-ttu-id="47b35-152">使用高性能 VM 创建较小的群集，每个 VM 上提供的核心数较多。</span><span class="sxs-lookup"><span data-stu-id="47b35-152">Create a smaller cluster using high performing VMs with more cores available on each.</span></span>

<span data-ttu-id="47b35-153">通常情况下，为标准 Python 模型评分不像为深度学习模型评分那样苛刻，一个小的群集就可以有效地处理大量的排队模型。</span><span class="sxs-lookup"><span data-stu-id="47b35-153">In general, scoring of standard Python models is not as demanding as scoring of deep learning models, and a small cluster should be able to handle a large number of queued models efficiently.</span></span> <span data-ttu-id="47b35-154">可以在数据集大小增加时增加群集节点的数目。</span><span class="sxs-lookup"><span data-stu-id="47b35-154">You can increase the number of cluster nodes as the dataset sizes increase.</span></span>

<span data-ttu-id="47b35-155">在此方案中，为方便起见，将在单个机器学习管道步骤中提交一个评分任务。</span><span class="sxs-lookup"><span data-stu-id="47b35-155">For convenience in this scenario, one scoring task is submitted within a single Machine Learning pipeline step.</span></span> <span data-ttu-id="47b35-156">不过，在同一管道步骤中为多个数据区块评分可能更有效。</span><span class="sxs-lookup"><span data-stu-id="47b35-156">However, it can be more efficient to score multiple data chunks within the same pipeline step.</span></span> <span data-ttu-id="47b35-157">在这种情况下，请编写自定义代码，以便在多个数据集中进行读取，并在执行单个步骤期间为这些数据集执行评分脚本。</span><span class="sxs-lookup"><span data-stu-id="47b35-157">In those cases, write custom code to read in multiple datasets and execute the scoring script for those during a single-step execution.</span></span>

## <a name="management-considerations"></a><span data-ttu-id="47b35-158">管理注意事项</span><span class="sxs-lookup"><span data-stu-id="47b35-158">Management considerations</span></span>

- <span data-ttu-id="47b35-159">**监视作业**。</span><span class="sxs-lookup"><span data-stu-id="47b35-159">**Monitor jobs**.</span></span> <span data-ttu-id="47b35-160">监视正在运行的作业的进度很重要，但在活动节点群集间进行监视可能是一项挑战。</span><span class="sxs-lookup"><span data-stu-id="47b35-160">It's important to monitor the progress of running jobs, but it can be a challenge to monitor across a cluster of active nodes.</span></span> <span data-ttu-id="47b35-161">若要检查群集中节点的状态，请使用 [Azure 门户][portal]管理[机器学习工作区][ml-workspace]。</span><span class="sxs-lookup"><span data-stu-id="47b35-161">To inspect the state of the nodes in the cluster, use the [Azure Portal][portal] to manage the [machine learning workspace][ml-workspace].</span></span> <span data-ttu-id="47b35-162">如果节点处于非活动状态或作业失败，则错误日志将保存到 Blob 存储，并且还可以在“管道”部分访问这些日志。</span><span class="sxs-lookup"><span data-stu-id="47b35-162">If a node is inactive or a job has failed, the error logs are saved to blob storage, and are also accessible in the Pipelines section.</span></span> <span data-ttu-id="47b35-163">若要进行更全面的监视，请将日志连接到 [Application Insights][app-insights]，或运行单独的进程来轮询群集及其作业的状态。</span><span class="sxs-lookup"><span data-stu-id="47b35-163">For richer monitoring, connect logs to [Application Insights][app-insights], or run separate processes to poll for the state of the cluster and its jobs.</span></span>
-   <span data-ttu-id="47b35-164">**日志记录**。</span><span class="sxs-lookup"><span data-stu-id="47b35-164">**Logging**.</span></span> <span data-ttu-id="47b35-165">机器学习服务将所有 stdout/stderr 记录到关联的 Azure 存储帐户中。</span><span class="sxs-lookup"><span data-stu-id="47b35-165">Machine Learning Service logs all stdout/stderr to the associated Azure Storage account.</span></span> <span data-ttu-id="47b35-166">若要轻松查看日志文件，请使用 [Azure 存储资源管理器][explorer]等存储导航工具。</span><span class="sxs-lookup"><span data-stu-id="47b35-166">To easily view the log files, use a storage navigation tool such as [Azure Storage Explorer][explorer].</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="47b35-167">成本注意事项</span><span class="sxs-lookup"><span data-stu-id="47b35-167">Cost considerations</span></span>

<span data-ttu-id="47b35-168">在此体系结构中使用的最昂贵组件是计算资源。</span><span class="sxs-lookup"><span data-stu-id="47b35-168">The most expensive components used in this reference architecture are the compute resources.</span></span> <span data-ttu-id="47b35-169">计算群集大小根据队列中的作业增加和减少。</span><span class="sxs-lookup"><span data-stu-id="47b35-169">The compute cluster size scales up and down depending on the jobs in the queue.</span></span> <span data-ttu-id="47b35-170">可以修改计算的预配配置，通过 Python SDK 以编程方式启用自动缩放。</span><span class="sxs-lookup"><span data-stu-id="47b35-170">Enable automatic scaling programmatically through the Python SDK by modifying the compute’s provisioning configuration.</span></span> <span data-ttu-id="47b35-171">或者使用 [Azure CLI][cli]设置群集的自动缩放参数。</span><span class="sxs-lookup"><span data-stu-id="47b35-171">Or use the [Azure CLI][cli] to set the automatic scaling parameters of the cluster.</span></span>

<span data-ttu-id="47b35-172">对于无需立即处理的工作，可配置自动缩放公式，使默认状态（最小值）为零节点群集。</span><span class="sxs-lookup"><span data-stu-id="47b35-172">For work that doesn't require immediate processing, configure the automatic scaling formula so the default state (minimum) is a cluster of zero nodes.</span></span> <span data-ttu-id="47b35-173">通过此配置，群集从零节点开始，仅在队列中检测到作业时才会增加。</span><span class="sxs-lookup"><span data-stu-id="47b35-173">With this configuration, the cluster starts with zero nodes and only scales up when it detects jobs in the queue.</span></span> <span data-ttu-id="47b35-174">如果批量评分进程一天只运行几次或更少，则此设置可大幅节省成本。</span><span class="sxs-lookup"><span data-stu-id="47b35-174">If the batch scoring process happens only a few times a day or less, this setting enables significant cost savings.</span></span>

<span data-ttu-id="47b35-175">自动缩放可能不适用于彼此发生时间太接近的批处理作业。</span><span class="sxs-lookup"><span data-stu-id="47b35-175">Automatic scaling may not be appropriate for batch jobs that happen too close to each other.</span></span> <span data-ttu-id="47b35-176">群集启动和停止所需的时间也会产生成本，因此如果批处理工作负荷在上一个作业结束后的几分钟内开始，则保持群集在作业之间运行可能更具成本效益。</span><span class="sxs-lookup"><span data-stu-id="47b35-176">The time that it takes for a cluster to spin up and spin down also incurs a cost, so if a batch workload begins only a few minutes after the previous job ends, it might be more cost effective to keep the cluster running between jobs.</span></span> <span data-ttu-id="47b35-177">这取决于评分过程是计划高频率运行（例如，每小时运行一次）还是低频率运行（例如，每月运行一次）。</span><span class="sxs-lookup"><span data-stu-id="47b35-177">That depends on whether scoring processes are scheduled to run at a high frequency (every hour, for example), or less frequently (once a month, for example).</span></span>


## <a name="deployment"></a><span data-ttu-id="47b35-178">部署</span><span class="sxs-lookup"><span data-stu-id="47b35-178">Deployment</span></span>

<span data-ttu-id="47b35-179">若要部署这个用于参考的体系结构，请按照 [GitHub 存储库][github]中所述的步骤进行操作。</span><span class="sxs-lookup"><span data-stu-id="47b35-179">To deploy this reference architecture, follow the steps described in the [GitHub repo][github].</span></span>

[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[automatic-scaling]: /azure/batch/batch-automatic-scaling
[azure-files]: /azure/storage/files/storage-files-introduction
[cli]: /cli/azure
[create-resources]: https://github.com/Microsoft/AMLBatchScoringPipeline/blob/master/01_create_resources.ipynb
[deep]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[event-hubs]: /azure/event-hubs/event-hubs-geo-dr
[explorer]: https://azure.microsoft.com/en-us/features/storage-explorer/
[github]: https://github.com/Microsoft/AMLBatchScoringPipeline
[one-class-svm]: http://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html
[portal]: https://portal.azure.com
[ml-workspace]: /azure/machine-learning/studio/create-workspace
[python-script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/batchai/predict.py
[pyscript]: https://github.com/Microsoft/AMLBatchScoringPipeline/blob/master/scripts/predict.py
[storage]: /azure/storage/blobs/storage-blobs-overview
[stream-analytics]: /azure/stream-analytics/
[sql-database]: /azure/sql-database/
[app-insights]: /azure/application-insights/app-insights-overview
