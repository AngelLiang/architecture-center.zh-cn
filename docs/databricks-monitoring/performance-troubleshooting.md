---
title: Azure Databricks 使用 Azure Monitor 的性能故障排除
titleSuffix: ''
description: 使用 Grafana 仪表板以对 Azure Databricks 中的性能问题进行故障排除
author: petertaylor9999
ms.date: 04/02/2019
ms.topic: ''
ms.service: ''
ms.subservice: ''
ms.openlocfilehash: 49ec63d0c45ab388ca83b3ab0562428327539619
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59740368"
---
# <a name="troubleshoot-performance-bottlenecks-in-azure-databricks"></a><span data-ttu-id="54698-103">对 Azure Databricks 中的性能瓶颈进行故障排除</span><span class="sxs-lookup"><span data-stu-id="54698-103">Troubleshoot performance bottlenecks in Azure Databricks</span></span>

<span data-ttu-id="54698-104">本文介绍如何使用监视仪表板以在 Azure Databricks 上的 Spark 作业中找出性能瓶颈。</span><span class="sxs-lookup"><span data-stu-id="54698-104">This article describes how to use monitoring dashboards to find performance bottlenecks in Spark jobs on Azure Databricks.</span></span>

<span data-ttu-id="54698-105">[Azure Databricks](/azure/azure-databricks/)是[Apache Spark](https://spark.apache.org/)– 基于分析服务，轻松地快速开发和部署大数据分析。</span><span class="sxs-lookup"><span data-stu-id="54698-105">[Azure Databricks](/azure/azure-databricks/) is an [Apache Spark](https://spark.apache.org/)–based analytics service that makes it easy to rapidly develop and deploy big data analytics.</span></span> <span data-ttu-id="54698-106">运行生产 Azure Databricks 工作负荷时，监视和故障排除性能问题是一项非常关键。</span><span class="sxs-lookup"><span data-stu-id="54698-106">Monitoring and troubleshooting performance issues is a critical when operating production Azure Databricks workloads.</span></span> <span data-ttu-id="54698-107">若要标识的常见性能问题，最好使用监视根据遥测数据的可视化效果。</span><span class="sxs-lookup"><span data-stu-id="54698-107">To identify common performance issues, it's helpful to use monitoring visualizations based on telemetry data.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54698-108">必备组件</span><span class="sxs-lookup"><span data-stu-id="54698-108">Prerequisites</span></span>

<span data-ttu-id="54698-109">若要设置 Grafana 仪表板在本文中所示：</span><span class="sxs-lookup"><span data-stu-id="54698-109">To set up the Grafana dashboards shown in this article:</span></span>

- <span data-ttu-id="54698-110">将遥测发送到 Log Analytics 工作区中，使用 Azure Databricks 监视库将 Databricks 群集配置。</span><span class="sxs-lookup"><span data-stu-id="54698-110">Configure your Databricks cluster to send telemetry to a Log Analytics workspace, using the Azure Databricks Monitoring Library.</span></span> <span data-ttu-id="54698-111">有关详细信息，请参阅[配置 Azure Databricks 以发送到 Azure Monitor 指标](./configure-cluster.md)。</span><span class="sxs-lookup"><span data-stu-id="54698-111">For details, see [Configure Azure Databricks to send metrics to Azure Monitor](./configure-cluster.md).</span></span>

- <span data-ttu-id="54698-112">在虚拟机中部署 Grafana。</span><span class="sxs-lookup"><span data-stu-id="54698-112">Deploy Grafana in a virtual machine.</span></span> <span data-ttu-id="54698-113">请参阅[使用仪表板直观显示 Azure Databricks 指标](./dashboards.md)。</span><span class="sxs-lookup"><span data-stu-id="54698-113">See [Use dashboards to visualize Azure Databricks metrics](./dashboards.md).</span></span>

<span data-ttu-id="54698-114">部署 Grafana 仪表板包含一系列时间序列的可视化效果。</span><span class="sxs-lookup"><span data-stu-id="54698-114">The Grafana dashboard that is deployed includes a set of time-series visualizations.</span></span> <span data-ttu-id="54698-115">每个关系图是指标的 Apache Spark 作业，相关的作业和任务组成每个阶段的阶段的时间序列绘图。</span><span class="sxs-lookup"><span data-stu-id="54698-115">Each graph is time-series plot of metrics related to an Apache Spark job, the stages of the job, and tasks that make up each stage.</span></span>

## <a name="azure-databricks-performance-overview"></a><span data-ttu-id="54698-116">Azure Databricks 性能概述</span><span class="sxs-lookup"><span data-stu-id="54698-116">Azure Databricks performance overview</span></span>

<span data-ttu-id="54698-117">Azure Databricks 基于 Apache Spark 的常规用途的分布式计算系统。</span><span class="sxs-lookup"><span data-stu-id="54698-117">Azure Databricks is based on Apache Spark, a general-purpose distributed computing system.</span></span> <span data-ttu-id="54698-118">应用程序代码，称为**作业**，由群集管理器协调的 Apache Spark 群集上执行。</span><span class="sxs-lookup"><span data-stu-id="54698-118">Application code, known as a **job**, executes on an Apache Spark cluster, coordinated by the cluster manager.</span></span> <span data-ttu-id="54698-119">一般情况下，作业是的最高级别的计算单位。</span><span class="sxs-lookup"><span data-stu-id="54698-119">In general, a job is the highest-level unit of computation.</span></span> <span data-ttu-id="54698-120">作业代表由 Spark 应用程序执行完成操作。</span><span class="sxs-lookup"><span data-stu-id="54698-120">A job represents the complete operation performed by the Spark application.</span></span> <span data-ttu-id="54698-121">典型的操作包括从源读取数据、 应用数据转换，并将结果写入存储或另一个目标。</span><span class="sxs-lookup"><span data-stu-id="54698-121">A typical operation includes reading data from a source, applying data transformations, and writing the results to storage or another destination.</span></span>

<span data-ttu-id="54698-122">作业可分解**阶段**。</span><span class="sxs-lookup"><span data-stu-id="54698-122">Jobs are broken down into **stages**.</span></span> <span data-ttu-id="54698-123">作业将推进阶段通过按顺序，这意味着更高版本阶段必须等待较早的阶段，才能完成。</span><span class="sxs-lookup"><span data-stu-id="54698-123">The job advances through the stages sequentially, which means that later stages must wait for earlier stages to complete.</span></span> <span data-ttu-id="54698-124">阶段包含完全相同的组**任务**，可以并行执行的 Spark 群集的多个节点上。</span><span class="sxs-lookup"><span data-stu-id="54698-124">Stages contain groups of identical **tasks** that can be executed in parallel on multiple nodes of the Spark cluster.</span></span> <span data-ttu-id="54698-125">任务是执行数据的子集上发生的最高粒度单位。</span><span class="sxs-lookup"><span data-stu-id="54698-125">Tasks are the most granular unit of execution taking place on a subset of the data.</span></span>

<span data-ttu-id="54698-126">后续部分介绍可用于性能故障排除某些仪表板可视化效果。</span><span class="sxs-lookup"><span data-stu-id="54698-126">The next sections describe some dashboard visualizations that are useful for performance troubleshooting.</span></span>

## <a name="job-and-stage-latency"></a><span data-ttu-id="54698-127">作业和阶段延迟</span><span class="sxs-lookup"><span data-stu-id="54698-127">Job and stage latency</span></span>

<span data-ttu-id="54698-128">作业滞后时间是从作业执行的持续时间，直到它完成启动时。</span><span class="sxs-lookup"><span data-stu-id="54698-128">Job latency is the duration of a job execution from when it starts until it completes.</span></span> <span data-ttu-id="54698-129">它会显示为百分位数的作业执行的每个群集和应用程序 ID，以允许离群值的可视化效果。</span><span class="sxs-lookup"><span data-stu-id="54698-129">It is shown as percentiles of a job execution per cluster and application ID, to allow the visualization of outliers.</span></span> <span data-ttu-id="54698-130">下图显示了作业历史记录，第 90 百分位达到 50 秒，即使第 50 百分位持续了大约 10 秒。</span><span class="sxs-lookup"><span data-stu-id="54698-130">The following graph shows a job history where the 90th percentile reached 50 seconds, even though the 50th percentile was consistently around 10 seconds.</span></span>

![图中显示了作业滞后时间](./_images/grafana-job-latency.png)

<span data-ttu-id="54698-132">调查作业执行的群集和应用程序，寻找延迟峰值。</span><span class="sxs-lookup"><span data-stu-id="54698-132">Investigate job execution by cluster and application, looking for spikes in latency.</span></span> <span data-ttu-id="54698-133">一旦识别出群集和应用程序具有高延迟，继续调查阶段延迟。</span><span class="sxs-lookup"><span data-stu-id="54698-133">Once clusters and applications with high latency are identified, move on to investigate stage latency.</span></span>

<span data-ttu-id="54698-134">阶段延迟也显示为百分点值以允许离群值的可视化效果。</span><span class="sxs-lookup"><span data-stu-id="54698-134">Stage latency is also shown as percentiles to allow the visualization of outliers.</span></span> <span data-ttu-id="54698-135">按群集、 应用程序和阶段名称划分阶段延迟。</span><span class="sxs-lookup"><span data-stu-id="54698-135">Stage latency is broken out by cluster, application, and stage name.</span></span> <span data-ttu-id="54698-136">标识在图中要确定哪些任务会阻碍着设备得到的阶段完成的任务延迟峰值。</span><span class="sxs-lookup"><span data-stu-id="54698-136">Identify spikes in task latency in the graph to determine which tasks are holding back completion of the stage.</span></span>

![显示阶段延迟图表](./_images/grafana-stage-latency.png)

<span data-ttu-id="54698-138">群集吞吐量图显示了作业、 阶段和每分钟完成的任务数。</span><span class="sxs-lookup"><span data-stu-id="54698-138">The cluster throughput graph shows the number of jobs, stages, and tasks completed per minute.</span></span> <span data-ttu-id="54698-139">这可帮助你了解相对数量的阶段和每个作业的任务方面的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="54698-139">This helps you to understand the workload in terms of the relative number of stages and tasks per job.</span></span> <span data-ttu-id="54698-140">在这里可以看到阶段的数目时约有 12 个作业每分钟数，范围介于 2 到 6 之间&ndash;24 每分钟。</span><span class="sxs-lookup"><span data-stu-id="54698-140">Here you can see that the number of jobs per minute ranges between 2 and 6, while the number of stages is about 12 &ndash; 24 per minute.</span></span>

![关系图显示群集吞吐量](./_images/grafana-cluster-throughput.png)

## <a name="sum-of-task-execution-latency"></a><span data-ttu-id="54698-142">任务执行延迟的总和</span><span class="sxs-lookup"><span data-stu-id="54698-142">Sum of task execution latency</span></span>

<span data-ttu-id="54698-143">此可视化效果显示每个主机群集上运行的任务执行延迟的总和。</span><span class="sxs-lookup"><span data-stu-id="54698-143">This visualization shows the sum of task execution latency per host running on a cluster.</span></span> <span data-ttu-id="54698-144">使用此关系图中检测到群集，或每个执行器的任务 misallocation 由于减的前提下主机缓慢运行的任务。</span><span class="sxs-lookup"><span data-stu-id="54698-144">Use this graph to detect tasks that run slowly due to the host slowing down on a cluster, or a misallocation of tasks per executor.</span></span> <span data-ttu-id="54698-145">在下图中，大多数主机具有大约 30 秒的总和。</span><span class="sxs-lookup"><span data-stu-id="54698-145">In the following graph, most of the hosts have a sum of about 30 seconds.</span></span> <span data-ttu-id="54698-146">但是，两个主机必须将鼠标悬停在大约 10 分钟的总和。</span><span class="sxs-lookup"><span data-stu-id="54698-146">However, two of the hosts have sums that hover around 10 minutes.</span></span> <span data-ttu-id="54698-147">主机运行速度缓慢，或者 misallocated 的每个执行器的任务数。</span><span class="sxs-lookup"><span data-stu-id="54698-147">Either the hosts are running slow or the number of tasks per executor is misallocated.</span></span>

![每个主机的任务执行的图形显示总和](./_images/grafana-sum-task-exec.png)

<span data-ttu-id="54698-149">每个执行器的任务数显示两个执行器分配数不相称的任务，从而产生瓶颈。</span><span class="sxs-lookup"><span data-stu-id="54698-149">The number of tasks per executor shows that two executors are assigned a disproportionate number of tasks, causing a bottleneck.</span></span>

![图中显示了每个执行器的任务](./_images/grafana-tasks-per-exec.png)

## <a name="task-metrics-per-stage"></a><span data-ttu-id="54698-151">每个阶段的任务度量值</span><span class="sxs-lookup"><span data-stu-id="54698-151">Task metrics per stage</span></span>

<span data-ttu-id="54698-152">任务度量值可视化提供用于执行任务的成本明细。</span><span class="sxs-lookup"><span data-stu-id="54698-152">The task metrics visualization gives the cost breakdown for a task execution.</span></span> <span data-ttu-id="54698-153">可以使用它所花费的相对时间，请参阅上序列化和反序列化等任务。</span><span class="sxs-lookup"><span data-stu-id="54698-153">You can use it see the relative time spent on tasks such as serialization and deserialization.</span></span> <span data-ttu-id="54698-154">此数据可能会显示优化机会&mdash;例如，通过使用[将变量广播](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#broadcast-variables)以避免传送的数据。</span><span class="sxs-lookup"><span data-stu-id="54698-154">This data might show opportunities to optimize &mdash; for example, by using [broadcast variables](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#broadcast-variables) to avoid shipping data.</span></span> <span data-ttu-id="54698-155">任务度量值还显示一个任务，无序播放数据大小和随机读取和写入时间。</span><span class="sxs-lookup"><span data-stu-id="54698-155">The task metrics also show the shuffle data size for a task, and the shuffle read and write times.</span></span> <span data-ttu-id="54698-156">如果这些值较高，则表示大量的数据是通过网络移动。</span><span class="sxs-lookup"><span data-stu-id="54698-156">If these values are high, it means that a lot of data is moving across the network.</span></span>

<span data-ttu-id="54698-157">另一个任务指标是计划程序延迟，测量其所需的时间计划的任务。</span><span class="sxs-lookup"><span data-stu-id="54698-157">Another task metric is the scheduler delay, which measures how long it takes to schedule a task.</span></span> <span data-ttu-id="54698-158">理想情况下，此值应为低与执行器计算时间相比较，指的是时间所用实际执行的任务。</span><span class="sxs-lookup"><span data-stu-id="54698-158">Ideally, this value should be low compared to the executor compute time, which is the time spent actually executing the task.</span></span>

<span data-ttu-id="54698-159">以下关系图显示了计划程序延迟时间 (3.7 s)，超过了执行器计算时间 (1.1 s)。</span><span class="sxs-lookup"><span data-stu-id="54698-159">The following graph shows a scheduler delay time (3.7 s) that exceeds the executor compute time (1.1 s).</span></span> <span data-ttu-id="54698-160">这意味着花费在等待任务进行计划比执行实际工作的时间。</span><span class="sxs-lookup"><span data-stu-id="54698-160">That means more time is spent waiting for tasks to be scheduled than doing the actual work.</span></span>

![显示每个阶段的任务度量值图表](./_images/grafana-metrics-per-stage.png)

<span data-ttu-id="54698-162">在这种情况下，问题被引起太多分区，这会导致大量开销。</span><span class="sxs-lookup"><span data-stu-id="54698-162">In this case, the problem was caused by having too many partitions, which caused a lot of overhead.</span></span> <span data-ttu-id="54698-163">减少分区数目降低计划程序延迟时间。</span><span class="sxs-lookup"><span data-stu-id="54698-163">Reducing the number of partitions lowered the scheduler delay time.</span></span> <span data-ttu-id="54698-164">下一步图所示，大多数情况下执行任务所用。</span><span class="sxs-lookup"><span data-stu-id="54698-164">The next graph shows that most of the time is spent executing the task.</span></span>

![显示每个阶段的任务度量值图表](./_images/grafana-metrics-per-stage2.png)

## <a name="streaming-throughput-and-latency"></a><span data-ttu-id="54698-166">流式处理吞吐量和延迟</span><span class="sxs-lookup"><span data-stu-id="54698-166">Streaming throughput and latency</span></span>

<span data-ttu-id="54698-167">流式处理吞吐量直接与结构化流式处理。</span><span class="sxs-lookup"><span data-stu-id="54698-167">Streaming throughput is directly related to structured streaming.</span></span> <span data-ttu-id="54698-168">有两个重要的指标与流式处理吞吐量相关联：第二个和已处理的行，每秒每个输入的行数。</span><span class="sxs-lookup"><span data-stu-id="54698-168">There are two important metrics associated with streaming throughput: Input rows per second and processed rows per second.</span></span> <span data-ttu-id="54698-169">如果每秒的输入的行 outpaces 每秒处理的行，则意味着落后流处理系统。</span><span class="sxs-lookup"><span data-stu-id="54698-169">If input rows per second outpaces processed rows per second, it means the stream processing system is falling behind.</span></span> <span data-ttu-id="54698-170">此外，如果输入的数据来自事件中心或 Kafka，然后每秒的输入的行应跟上数据引入速率在前端。</span><span class="sxs-lookup"><span data-stu-id="54698-170">Also, if the input data comes from Event Hubs or Kafka, then input rows per second should keep up with the data ingestion rate at the front end.</span></span>

<span data-ttu-id="54698-171">两个作业可以有类似但截然不同的流式处理度量值的群集吞吐量。</span><span class="sxs-lookup"><span data-stu-id="54698-171">Two jobs can have similar cluster throughput but very different streaming metrics.</span></span> <span data-ttu-id="54698-172">下面的屏幕截图显示了两个不同的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="54698-172">The following screenshot shows two different workloads.</span></span> <span data-ttu-id="54698-173">它们是在群集吞吐量 （作业、 阶段和每分钟的任务） 方面类似。</span><span class="sxs-lookup"><span data-stu-id="54698-173">They are similar in terms of cluster throughput (jobs, stages, and tasks per minute).</span></span> <span data-ttu-id="54698-174">但第二次运行处理 12000 行/sec，对比 4,000 行数/秒。</span><span class="sxs-lookup"><span data-stu-id="54698-174">But the second run processes 12,000 rows/sec versus 4,000 rows/sec.</span></span>

![关系图显示流式处理吞吐量](./_images/grafana-streaming-throughput.png)

<span data-ttu-id="54698-176">流式处理吞吐量通常是更好的业务指标比群集吞吐量，因为它测量处理的数据记录数。</span><span class="sxs-lookup"><span data-stu-id="54698-176">Streaming throughput is often a better business metric than cluster throughput, because it measures the number of data records that are processed.</span></span>

## <a name="resource-consumption-per-executor"></a><span data-ttu-id="54698-177">每个执行器的资源消耗</span><span class="sxs-lookup"><span data-stu-id="54698-177">Resource consumption per executor</span></span>

<span data-ttu-id="54698-178">这些指标有助于了解每个执行器执行的工作。</span><span class="sxs-lookup"><span data-stu-id="54698-178">These metrics help to understand the work that each executor performs.</span></span>

<span data-ttu-id="54698-179">**百分比指标**测量上各种操作，表示与整体执行器计算时间所用时间的比率为花费多少时间的执行器。</span><span class="sxs-lookup"><span data-stu-id="54698-179">**Percentage metrics** measure how much time an executor spends on various things, expressed as a ratio of time spent versus the overall executor compute time.</span></span> <span data-ttu-id="54698-180">指标包括：</span><span class="sxs-lookup"><span data-stu-id="54698-180">The metrics are:</span></span>

- <span data-ttu-id="54698-181">%序列化时间</span><span class="sxs-lookup"><span data-stu-id="54698-181">% Serialize time</span></span>
- <span data-ttu-id="54698-182">%反序列化时间</span><span class="sxs-lookup"><span data-stu-id="54698-182">% Deserialize time</span></span>
- <span data-ttu-id="54698-183">%Cpu 执行器时间</span><span class="sxs-lookup"><span data-stu-id="54698-183">% CPU executor time</span></span>
- <span data-ttu-id="54698-184">JVM 时间百分比</span><span class="sxs-lookup"><span data-stu-id="54698-184">% JVM time</span></span>

<span data-ttu-id="54698-185">这些可视化效果显示多少每个指标可能会导致总体执行器处理。</span><span class="sxs-lookup"><span data-stu-id="54698-185">These visualizations show how much each of these metrics contributes to overall executor processing.</span></span>

![显示百分比指标图表](./_images/grafana-percentage.png)

<span data-ttu-id="54698-187">**无序播放指标**混排在执行器之间的数据与相关度量值。</span><span class="sxs-lookup"><span data-stu-id="54698-187">**Shuffle metrics** are metrics related to data shuffling across the executors.</span></span>

- <span data-ttu-id="54698-188">无序播放 I/O</span><span class="sxs-lookup"><span data-stu-id="54698-188">Shuffle I/O</span></span>
- <span data-ttu-id="54698-189">无序播放内存</span><span class="sxs-lookup"><span data-stu-id="54698-189">Shuffle memory</span></span>
- <span data-ttu-id="54698-190">文件系统使用情况</span><span class="sxs-lookup"><span data-stu-id="54698-190">File system usage</span></span>
- <span data-ttu-id="54698-191">磁盘使用情况</span><span class="sxs-lookup"><span data-stu-id="54698-191">Disk usage</span></span>

## <a name="common-performance-bottlenecks"></a><span data-ttu-id="54698-192">常见的性能瓶颈</span><span class="sxs-lookup"><span data-stu-id="54698-192">Common performance bottlenecks</span></span>

<span data-ttu-id="54698-193">在 Spark 中的两个常见性能瓶颈*任务延迟*和一个*非最佳随机选择分区计数*。</span><span class="sxs-lookup"><span data-stu-id="54698-193">Two common performance bottlenecks in Spark are *task stragglers* and a *non-optimal shuffle partition count*.</span></span>

### <a name="task-stragglers"></a><span data-ttu-id="54698-194">任务延迟</span><span class="sxs-lookup"><span data-stu-id="54698-194">Task stragglers</span></span>

<span data-ttu-id="54698-195">在作业中的阶段用阻止后续阶段中的早期阶段按顺序执行。</span><span class="sxs-lookup"><span data-stu-id="54698-195">The stages in a job are executed sequentially, with earlier stages blocking later stages.</span></span> <span data-ttu-id="54698-196">如果一个任务执行随机选择分区慢于其他任务，在群集中的所有任务必须都等待速度缓慢的任务来保持同步，才能结束阶段。</span><span class="sxs-lookup"><span data-stu-id="54698-196">If one task executes a shuffle partition more slowly than other tasks, all tasks in the cluster must wait for the slow task to catch up before the stage can end.</span></span> <span data-ttu-id="54698-197">这可能由于以下原因：</span><span class="sxs-lookup"><span data-stu-id="54698-197">This can happen for the following reasons:</span></span>

1. <span data-ttu-id="54698-198">主机组的运行速度缓慢。</span><span class="sxs-lookup"><span data-stu-id="54698-198">A host or group of hosts are running slow.</span></span> <span data-ttu-id="54698-199">症状：高的任务、 阶段或作业延迟和低群集吞吐量。</span><span class="sxs-lookup"><span data-stu-id="54698-199">Symptoms: High task, stage, or job latency and low cluster throughput.</span></span> <span data-ttu-id="54698-200">不会均匀分布任务延迟，每个主机的总和。</span><span class="sxs-lookup"><span data-stu-id="54698-200">The summation of tasks latencies per host won't be evenly distributed.</span></span> <span data-ttu-id="54698-201">但是，资源消耗将均匀分布在执行程序。</span><span class="sxs-lookup"><span data-stu-id="54698-201">However, resource consumption will be evenly distributed across executors.</span></span>

1. <span data-ttu-id="54698-202">任务有一个成本高昂的聚合来执行 （数据倾斜）。</span><span class="sxs-lookup"><span data-stu-id="54698-202">Tasks have an expensive aggregation to execute (data skewing).</span></span> <span data-ttu-id="54698-203">症状：均匀地分布高的任务，阶段，或作业和低群集吞吐量，但每个主机的延迟的总和。</span><span class="sxs-lookup"><span data-stu-id="54698-203">Symptoms: High task, stage, or job and low cluster throughput, but the summation of latencies per host is evenly distributed.</span></span> <span data-ttu-id="54698-204">资源消耗将均匀分布在各执行器。</span><span class="sxs-lookup"><span data-stu-id="54698-204">Resource consumption will be evenly distributed across executors.</span></span>

1. <span data-ttu-id="54698-205">如果分区是大小不等，更大的分区可能会导致不均衡的任务执行 （分区倾斜）。</span><span class="sxs-lookup"><span data-stu-id="54698-205">If partitions are of unequal size, a larger partition may cause unbalanced task execution (partition skewing).</span></span> <span data-ttu-id="54698-206">症状：执行器资源消耗较高相比其他执行器在群集上运行。</span><span class="sxs-lookup"><span data-stu-id="54698-206">Symptoms: Executor resource consumption is high compared to other executors running on the cluster.</span></span> <span data-ttu-id="54698-207">在该执行器上运行的所有任务将运行缓慢，并在管道中保存阶段执行。</span><span class="sxs-lookup"><span data-stu-id="54698-207">All tasks running on that executor will run slow and hold the stage execution in the pipeline.</span></span> <span data-ttu-id="54698-208">这些阶段被称为*暂存屏障*。</span><span class="sxs-lookup"><span data-stu-id="54698-208">Those stages are said to be *stage barriers*.</span></span>

### <a name="non-optimal-shuffle-partition-count"></a><span data-ttu-id="54698-209">非最佳随机选择分区计数</span><span class="sxs-lookup"><span data-stu-id="54698-209">Non-optimal shuffle partition count</span></span>

<span data-ttu-id="54698-210">在结构化流式处理查询的执行器任务赋值类型为群集的资源密集型操作。</span><span class="sxs-lookup"><span data-stu-id="54698-210">During a structured streaming query, the assignment of a task to an executor is a resource-intensive operation for the cluster.</span></span> <span data-ttu-id="54698-211">如果无序播放数据不是最佳的大小，为任务的延迟量吞吐量和延迟会对产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="54698-211">If the shuffle data isn't the optimal size, the amount of delay for a task will negatively impact throughput and latency.</span></span> <span data-ttu-id="54698-212">如果有过少的分区，在群集中的内核数将没有得到充分利用这可能会导致在处理效率低下。</span><span class="sxs-lookup"><span data-stu-id="54698-212">If there are too few partitions, the cores in the cluster will be underutilized which can result in processing inefficiency.</span></span> <span data-ttu-id="54698-213">相反，如果有太多的分区，是管理的大量开销少量的任务。</span><span class="sxs-lookup"><span data-stu-id="54698-213">Conversely, if there are too many partitions, there's a great deal of management overhead for a small number of tasks.</span></span>

<span data-ttu-id="54698-214">使用资源消耗指标值分区倾斜和 misallocation 的执行程序在群集上进行故障排除。</span><span class="sxs-lookup"><span data-stu-id="54698-214">Use the resource consumption metrics to troubleshoot partition skewing and misallocation of executors on the cluster.</span></span> <span data-ttu-id="54698-215">如果分区有偏差，将在群集上运行其他执行器相比提升执行器资源。</span><span class="sxs-lookup"><span data-stu-id="54698-215">If a partition is skewed, executor resources will be elevated in comparison to other executors running on the cluster.</span></span>

<span data-ttu-id="54698-216">例如下, 图显示了使用混排在前两个执行器上的内存为 90 X 大于其他执行程序：</span><span class="sxs-lookup"><span data-stu-id="54698-216">For example, the following graph shows that the memory used by shuffling on the first two executors is 90X bigger than the other executors:</span></span>

![显示百分比指标图表](./_images/grafana-shuffle-memory.png)