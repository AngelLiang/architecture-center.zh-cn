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
# <a name="batch-scoring-of-r-machine-learning-models-on-azure"></a><span data-ttu-id="55e9d-103">在 Azure 上的 R 机器学习批处理评分模型</span><span class="sxs-lookup"><span data-stu-id="55e9d-103">Batch scoring of R machine learning models on Azure</span></span>

<span data-ttu-id="55e9d-104">此参考体系结构演示如何执行批处理评分与使用 Azure Batch 的 R 模型。</span><span class="sxs-lookup"><span data-stu-id="55e9d-104">This reference architecture shows how to perform batch scoring with R models using Azure Batch.</span></span> <span data-ttu-id="55e9d-105">该方案基于零售商店销售预测，但这种体系结构可以归纳为需要使用 R 模型的大型 scaler 预测生成任何方案。</span><span class="sxs-lookup"><span data-stu-id="55e9d-105">The scenario is based on retail store sales forecasting, but this architecture can be generalized for any scenario requiring the generation of predictions on a large scaler using R models.</span></span> <span data-ttu-id="55e9d-106">[GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="55e9d-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![体系结构关系图][0]

<span data-ttu-id="55e9d-108">**场景**：连锁超市需要预测即将到来的季度的销售的产品。</span><span class="sxs-lookup"><span data-stu-id="55e9d-108">**Scenario**: A supermarket chain needs to forecast sales of products over the upcoming quarter.</span></span> <span data-ttu-id="55e9d-109">预测可让公司将更好地管理其供应链，并确保它可以满足对其存储的每个产品的需求。</span><span class="sxs-lookup"><span data-stu-id="55e9d-109">The forecast allows the company to manage its supply chain better and ensure it can meet demand for products at each of its stores.</span></span> <span data-ttu-id="55e9d-110">如前一周中的新销售数据变得可用并设置下一季度的市场营销策略的产品，公司将更新其预测每周。</span><span class="sxs-lookup"><span data-stu-id="55e9d-110">The company updates its forecasts every week as new sales data from the previous week becomes available and the product marketing strategy for next quarter is set.</span></span> <span data-ttu-id="55e9d-111">分位的预测会生成来估计单个销售预测的不确定性。</span><span class="sxs-lookup"><span data-stu-id="55e9d-111">Quantile forecasts are generated to estimate the uncertainty of the individual sales forecasts.</span></span>

<span data-ttu-id="55e9d-112">包括以下处理步骤：</span><span class="sxs-lookup"><span data-stu-id="55e9d-112">Processing involves the following steps:</span></span>

1. <span data-ttu-id="55e9d-113">Azure 逻辑应用会触发一次每周的预测的生成过程。</span><span class="sxs-lookup"><span data-stu-id="55e9d-113">An Azure Logic App triggers the forecast generation process once per week.</span></span>

1. <span data-ttu-id="55e9d-114">逻辑应用启动 Azure 容器实例运行 Docker 容器，这会触发批处理群集上的评分作业的计划程序。</span><span class="sxs-lookup"><span data-stu-id="55e9d-114">The logic app starts an Azure Container Instance running the scheduler Docker container, which triggers the scoring jobs on the Batch cluster.</span></span>

1. <span data-ttu-id="55e9d-115">批处理群集的节点上并行运行的评分的作业。</span><span class="sxs-lookup"><span data-stu-id="55e9d-115">Scoring jobs run in parallel across the nodes of the Batch cluster.</span></span> <span data-ttu-id="55e9d-116">每个节点：</span><span class="sxs-lookup"><span data-stu-id="55e9d-116">Each node:</span></span>

    1. <span data-ttu-id="55e9d-117">提取工作线程从 Docker Hub 的 Docker 映像并启动容器。</span><span class="sxs-lookup"><span data-stu-id="55e9d-117">Pulls the worker Docker image from Docker Hub and starts a container.</span></span>

    1. <span data-ttu-id="55e9d-118">读取输入的数据，并预先训练的 R 模型从 Azure Blob 存储。</span><span class="sxs-lookup"><span data-stu-id="55e9d-118">Reads input data and pre-trained R models from Azure Blob storage.</span></span>

    1. <span data-ttu-id="55e9d-119">得分数据来生成预测。</span><span class="sxs-lookup"><span data-stu-id="55e9d-119">Scores the data to produce the forecasts.</span></span>

    1. <span data-ttu-id="55e9d-120">将预测的结果写入到 blob 存储中。</span><span class="sxs-lookup"><span data-stu-id="55e9d-120">Writes the forecast results to blob storage.</span></span>

<span data-ttu-id="55e9d-121">下图显示一个存储中的四个产品 (Sku) 的预测的销售额。</span><span class="sxs-lookup"><span data-stu-id="55e9d-121">The figure below shows the forecasted sales for four products (SKUs) in one store.</span></span> <span data-ttu-id="55e9d-122">黑色线条是销售历史记录、 虚线是预测的中值 (q50)、 粉红色带区表示 25 个和 70 第五个百分点值和蓝色带区表示第五个第 90 五个百分点值。</span><span class="sxs-lookup"><span data-stu-id="55e9d-122">The black line is the sales history, the dashed line is the median (q50) forecast, the pink band represents the twenty-fifth and seventy-fifth percentiles, and the blue band represents the fifth and ninety-fifth percentiles.</span></span>

![销售预测][1]

## <a name="architecture"></a><span data-ttu-id="55e9d-124">体系结构</span><span class="sxs-lookup"><span data-stu-id="55e9d-124">Architecture</span></span>

<span data-ttu-id="55e9d-125">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="55e9d-125">This architecture consists of the following components.</span></span>

<span data-ttu-id="55e9d-126">[Azure Batch] [ batch]用于预测的作业以并行方式运行的虚拟机群集上。</span><span class="sxs-lookup"><span data-stu-id="55e9d-126">[Azure Batch][batch] is used to run forecast generation jobs in parallel on a cluster of virtual machines.</span></span> <span data-ttu-id="55e9d-127">作出预测使用预先定型的机器学习模型实现。 在 Azure Batch 中可以自动缩放的基于提交到群集的作业数的 Vm 数。</span><span class="sxs-lookup"><span data-stu-id="55e9d-127">Predictions are made using pre-trained machine learning models implemented in R. Azure Batch can automatically scale the number of VMs based on the number of jobs submitted to the cluster.</span></span> <span data-ttu-id="55e9d-128">每个节点上的 R 脚本进行评分的数据和生成预测的 Docker 容器中运行。</span><span class="sxs-lookup"><span data-stu-id="55e9d-128">On each node, an R script runs within a Docker container to score data and generate forecasts.</span></span>

<span data-ttu-id="55e9d-129">[Azure Blob 存储][ blob]用来存储输入的数据、 预先定型的机器学习模型和预测的结果。</span><span class="sxs-lookup"><span data-stu-id="55e9d-129">[Azure Blob Storage][blob] is used to store the input data, the pre-trained machine learning models, and the forecast results.</span></span> <span data-ttu-id="55e9d-130">它提供为此工作负荷需要的性能非常经济高效的存储。</span><span class="sxs-lookup"><span data-stu-id="55e9d-130">It delivers very cost-effective storage for the performance that this workload requires.</span></span>

<span data-ttu-id="55e9d-131">[Azure 容器实例][ aci]按需提供无服务器计算。</span><span class="sxs-lookup"><span data-stu-id="55e9d-131">[Azure Container Instances][aci] provide serverless compute on demand.</span></span> <span data-ttu-id="55e9d-132">在这种情况下，容器实例部署按计划触发生成预测的批处理作业。</span><span class="sxs-lookup"><span data-stu-id="55e9d-132">In this case, a container instance is deployed on a schedule to trigger the Batch jobs that generate the forecasts.</span></span> <span data-ttu-id="55e9d-133">批处理作业触发从 R 脚本使用[doAzureParallel] [ doAzureParallel]包。</span><span class="sxs-lookup"><span data-stu-id="55e9d-133">The Batch jobs are triggered from an R script using the [doAzureParallel][doAzureParallel] package.</span></span> <span data-ttu-id="55e9d-134">在作业完成后，容器实例自动会关闭。</span><span class="sxs-lookup"><span data-stu-id="55e9d-134">The container instance automatically shuts down once the jobs have finished.</span></span>

<span data-ttu-id="55e9d-135">[Azure 逻辑应用][ logic-apps]通过部署容器实例按计划触发整个工作流。</span><span class="sxs-lookup"><span data-stu-id="55e9d-135">[Azure Logic Apps][logic-apps] trigger the entire workflow by deploying the container instances on a schedule.</span></span> <span data-ttu-id="55e9d-136">在逻辑应用中的 Azure 容器实例连接器允许一个实例，以便在触发事件的范围时部署。</span><span class="sxs-lookup"><span data-stu-id="55e9d-136">An Azure Container Instances connector in Logic Apps allows an instance to be deployed upon a range of trigger events.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="55e9d-137">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="55e9d-137">Performance considerations</span></span>

### <a name="containerized-deployment"></a><span data-ttu-id="55e9d-138">容器化的部署</span><span class="sxs-lookup"><span data-stu-id="55e9d-138">Containerized deployment</span></span>

<span data-ttu-id="55e9d-139">使用此体系结构，所有 R 脚本都运行内[Docker](https://www.docker.com/)容器。</span><span class="sxs-lookup"><span data-stu-id="55e9d-139">With this architecture, all R scripts run within [Docker](https://www.docker.com/) containers.</span></span> <span data-ttu-id="55e9d-140">这可确保脚本运行在一致的环境中，使用相同的 R 版本和包的版本中，每次。</span><span class="sxs-lookup"><span data-stu-id="55e9d-140">This ensures that the scripts run in a consistent environment, with the same R version and packages versions, every time.</span></span> <span data-ttu-id="55e9d-141">对于计划程序和辅助角色的容器，使用是单独的 Docker 映像，因为每个都有一组不同的 R 包依赖项。</span><span class="sxs-lookup"><span data-stu-id="55e9d-141">Separate Docker images are used for the scheduler and worker containers, because each has a different set of R package dependencies.</span></span>

<span data-ttu-id="55e9d-142">Azure 容器实例提供无服务器环境来运行计划程序容器。</span><span class="sxs-lookup"><span data-stu-id="55e9d-142">Azure Container Instances provides a serverless environment to run the scheduler container.</span></span> <span data-ttu-id="55e9d-143">计划程序容器运行 R 脚本，以触发 Azure Batch 群集上运行的单个评分作业。</span><span class="sxs-lookup"><span data-stu-id="55e9d-143">The scheduler container runs an R script that triggers the individual scoring jobs running on an Azure Batch cluster.</span></span>

<span data-ttu-id="55e9d-144">每个批次群集节点运行辅助角色容器，它执行评分脚本。</span><span class="sxs-lookup"><span data-stu-id="55e9d-144">Each node of the Batch cluster runs the worker container, which executes the scoring script.</span></span>

### <a name="parallelizing-the-workload"></a><span data-ttu-id="55e9d-145">并行化工作负荷</span><span class="sxs-lookup"><span data-stu-id="55e9d-145">Parallelizing the workload</span></span>

<span data-ttu-id="55e9d-146">当批处理评分的 R 模型的数据，请考虑如何并行化工作负荷。</span><span class="sxs-lookup"><span data-stu-id="55e9d-146">When batch scoring data with R models, consider how to parallelize the workload.</span></span> <span data-ttu-id="55e9d-147">输入的数据必须以某种方式分区，以便计分操作可以分布在群集节点。</span><span class="sxs-lookup"><span data-stu-id="55e9d-147">The input data must be partitioned somehow so that the scoring operation can be distributed  across the cluster nodes.</span></span> <span data-ttu-id="55e9d-148">尝试不同的方法来发现将工作负荷的最佳选择。</span><span class="sxs-lookup"><span data-stu-id="55e9d-148">Try different approaches to discover the best choice for distributing your workload.</span></span> <span data-ttu-id="55e9d-149">基于的情况，考虑以下方面：</span><span class="sxs-lookup"><span data-stu-id="55e9d-149">On a case-by-case basis, consider the following:</span></span>

- <span data-ttu-id="55e9d-150">可以加载和处理在单个节点的内存中数据量。</span><span class="sxs-lookup"><span data-stu-id="55e9d-150">How much data can be loaded and processed in the memory of a single node.</span></span>
- <span data-ttu-id="55e9d-151">启动每个批处理作业的开销。</span><span class="sxs-lookup"><span data-stu-id="55e9d-151">The overhead of starting each batch job.</span></span>
- <span data-ttu-id="55e9d-152">加载 R 模型的开销。</span><span class="sxs-lookup"><span data-stu-id="55e9d-152">The overhead of loading the R models.</span></span>

<span data-ttu-id="55e9d-153">在此示例使用方案中，模型对象太大，而它需要仅几秒钟以生成单个产品的预测。</span><span class="sxs-lookup"><span data-stu-id="55e9d-153">In the scenario used for this example, the model objects are large, and it takes only a few seconds to generate a forecast for individual products.</span></span> <span data-ttu-id="55e9d-154">出于此原因，您可以对产品分组并执行每个节点的单个批处理作业。</span><span class="sxs-lookup"><span data-stu-id="55e9d-154">For this reason, you can group the products and execute a single Batch job per node.</span></span> <span data-ttu-id="55e9d-155">一个循环内每个作业将按顺序生成产品的预测值。</span><span class="sxs-lookup"><span data-stu-id="55e9d-155">A loop within each job generates forecasts for the products sequentially.</span></span> <span data-ttu-id="55e9d-156">此方法证明无法并行化此特定工作负荷的最有效方法。</span><span class="sxs-lookup"><span data-stu-id="55e9d-156">This method turns out to be the most efficient way to parallelize this particular workload.</span></span> <span data-ttu-id="55e9d-157">它避免了启动多个较小的批处理作业和重复加载 R 模型的开销。</span><span class="sxs-lookup"><span data-stu-id="55e9d-157">It avoids the overhead of starting many smaller Batch jobs and repeatedly loading the R models.</span></span>

<span data-ttu-id="55e9d-158">另一种方法是触发每个产品的一个批处理作业。</span><span class="sxs-lookup"><span data-stu-id="55e9d-158">An alternative approach is to trigger one Batch job per product.</span></span> <span data-ttu-id="55e9d-159">Azure Batch 自动窗体的作业队列，并将其推出的节点在群集上执行提交。</span><span class="sxs-lookup"><span data-stu-id="55e9d-159">Azure Batch automatically forms a queue of jobs and submits them to be executed on the cluster as nodes become available.</span></span> <span data-ttu-id="55e9d-160">使用[自动缩放][ autoscale]可调整的作业数根据群集中的节点数。</span><span class="sxs-lookup"><span data-stu-id="55e9d-160">Use [automatic scaling][autoscale] to adjust the number of nodes in the cluster depending on the number of jobs.</span></span> <span data-ttu-id="55e9d-161">如果花费较长的时间才能完成每个评分的操作，还是启动作业和重新加载模型对象的开销，此方法会更合理。</span><span class="sxs-lookup"><span data-stu-id="55e9d-161">This approach makes more sense if it takes a relatively long time to complete each scoring operation, justifying the overhead of starting the jobs and reloading the model objects.</span></span> <span data-ttu-id="55e9d-162">此方法也是实现变得简单，并使你可以灵活地使用自动缩放，一个重要的考虑因素，如果事先不知道的总工作负荷的大小。</span><span class="sxs-lookup"><span data-stu-id="55e9d-162">This approach is also simpler to implement and gives you the flexibility to use automatic scaling—an important consideration if the size of the total workload is not known in advance.</span></span>

## <a name="monitoring-and-logging-considerations"></a><span data-ttu-id="55e9d-163">监视和日志记录注意事项</span><span class="sxs-lookup"><span data-stu-id="55e9d-163">Monitoring and logging considerations</span></span>

### <a name="monitoring-azure-batch-jobs"></a><span data-ttu-id="55e9d-164">监视 Azure Batch 作业</span><span class="sxs-lookup"><span data-stu-id="55e9d-164">Monitoring Azure Batch jobs</span></span>

<span data-ttu-id="55e9d-165">监视和终止批处理作业从**作业**窗格中的 Azure 门户中的 Batch 帐户。</span><span class="sxs-lookup"><span data-stu-id="55e9d-165">Monitor and terminate Batch jobs from the **Jobs** pane of the Batch account in the Azure portal.</span></span> <span data-ttu-id="55e9d-166">监视 batch 群集，包括各个节点的状态从**池**窗格。</span><span class="sxs-lookup"><span data-stu-id="55e9d-166">Monitor the batch cluster, including the state of individual nodes, from the **Pools** pane.</span></span>

### <a name="logging-with-doazureparallel"></a><span data-ttu-id="55e9d-167">使用 doAzureParallel 的日志记录</span><span class="sxs-lookup"><span data-stu-id="55e9d-167">Logging with doAzureParallel</span></span>

<span data-ttu-id="55e9d-168">DoAzureParallel 包会自动收集的每个作业提交 Azure Batch 上的所有 stdout/stderr 的日志。</span><span class="sxs-lookup"><span data-stu-id="55e9d-168">The doAzureParallel package automatically collects logs of all stdout/stderr for every job submitted on Azure Batch.</span></span> <span data-ttu-id="55e9d-169">可以在安装时创建的存储帐户中找到这些选项。</span><span class="sxs-lookup"><span data-stu-id="55e9d-169">These can be found in the storage account created at setup.</span></span> <span data-ttu-id="55e9d-170">若要查看它们，请使用存储的导航工具如[Azure 存储资源管理器][ storage-explorer]或 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="55e9d-170">To view them, use a storage navigation tool such as [Azure Storage Explorer][storage-explorer] or Azure portal.</span></span>

<span data-ttu-id="55e9d-171">若要在开发期间快速调试批处理作业，打印在你本地 R 会话中使用的日志[getJobFiles] [ getJobFiles] doAzureParallel 的函数。</span><span class="sxs-lookup"><span data-stu-id="55e9d-171">To quickly debug Batch jobs during development, print logs in your local R session using the [getJobFiles][getJobFiles] function of doAzureParallel.</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="55e9d-172">成本注意事项</span><span class="sxs-lookup"><span data-stu-id="55e9d-172">Cost considerations</span></span>

<span data-ttu-id="55e9d-173">此参考体系结构中使用的计算资源是开销最高的组件。</span><span class="sxs-lookup"><span data-stu-id="55e9d-173">The compute resources used in this reference architecture are the most costly components.</span></span> <span data-ttu-id="55e9d-174">对于此方案中，只要该作业是触发，然后关闭作业完成后创建固定大小的群集。</span><span class="sxs-lookup"><span data-stu-id="55e9d-174">For this scenario, a cluster of fixed size is created whenever the job is triggered and then shut down after the job has completed.</span></span> <span data-ttu-id="55e9d-175">仅当启动、 运行，或关闭群集节点时，才产生成本。</span><span class="sxs-lookup"><span data-stu-id="55e9d-175">Cost is incurred only while the cluster nodes are starting, running, or shutting down.</span></span> <span data-ttu-id="55e9d-176">此方法非常适用于在其中生成预测所需的计算资源保持相对稳定作业作业的方案。</span><span class="sxs-lookup"><span data-stu-id="55e9d-176">This approach is suitable for a scenario where the compute resources required to generate the forecasts remain relatively constant from job to job.</span></span>

<span data-ttu-id="55e9d-177">在其中完成作业所需的计算量事先不知道的情况下，它可能更适合使用自动缩放。</span><span class="sxs-lookup"><span data-stu-id="55e9d-177">In scenarios where the amount of compute required to complete the job is not known in advance, it may be more suitable to use automatic scaling.</span></span> <span data-ttu-id="55e9d-178">使用此方法时，群集的大小是根据作业大小增加或减少。</span><span class="sxs-lookup"><span data-stu-id="55e9d-178">With this approach, the size of the cluster is scaled up or down depending on the size of the job.</span></span> <span data-ttu-id="55e9d-179">Azure Batch 支持一系列定义群集使用时可设置自动缩放公式[doAzureParallel] [ doAzureParallel] API。</span><span class="sxs-lookup"><span data-stu-id="55e9d-179">Azure Batch supports a range of auto-scale formulae which you can set when defining the cluster using the [doAzureParallel][doAzureParallel] API.</span></span>

<span data-ttu-id="55e9d-180">在某些情况下，作业之间的时间可能会太短而无法关闭和启动该群集。</span><span class="sxs-lookup"><span data-stu-id="55e9d-180">For some scenarios, the time between jobs may be too short to shut down and start up the cluster.</span></span> <span data-ttu-id="55e9d-181">在这些情况下，请根据作业之间运行的群集。</span><span class="sxs-lookup"><span data-stu-id="55e9d-181">In these cases, keep the cluster running between jobs if appropriate.</span></span>

<span data-ttu-id="55e9d-182">Azure 批处理和 doAzureParallel 支持低优先级 Vm 的使用。</span><span class="sxs-lookup"><span data-stu-id="55e9d-182">Azure Batch and doAzureParallel support the use of low-priority VMs.</span></span> <span data-ttu-id="55e9d-183">这些 Vm 提供可观的折扣但正在由其他更高优先级的工作负荷分配量的风险。</span><span class="sxs-lookup"><span data-stu-id="55e9d-183">These VMs come with a significant discount but risk being appropriated by other higher priority workloads.</span></span> <span data-ttu-id="55e9d-184">因此对于关键生产工作负荷不建议使用这些虚拟机。</span><span class="sxs-lookup"><span data-stu-id="55e9d-184">The use of these VMs are therefore not recommended for critical production workloads.</span></span> <span data-ttu-id="55e9d-185">但是，它们是非常有用的实验性或开发工作负荷。</span><span class="sxs-lookup"><span data-stu-id="55e9d-185">However, they are very useful for experimental or development workloads.</span></span>

## <a name="deployment"></a><span data-ttu-id="55e9d-186">部署</span><span class="sxs-lookup"><span data-stu-id="55e9d-186">Deployment</span></span>

<span data-ttu-id="55e9d-187">若要部署此参考体系结构，请按照中所述的步骤[GitHub] [ github]存储库。</span><span class="sxs-lookup"><span data-stu-id="55e9d-187">To deploy this reference architecture, follow the steps described in the [GitHub][github] repo.</span></span>


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