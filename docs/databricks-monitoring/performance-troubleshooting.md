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
# <a name="troubleshoot-performance-bottlenecks-in-azure-databricks"></a>对 Azure Databricks 中的性能瓶颈进行故障排除

本文介绍如何使用监视仪表板以在 Azure Databricks 上的 Spark 作业中找出性能瓶颈。

[Azure Databricks](/azure/azure-databricks/)是[Apache Spark](https://spark.apache.org/)– 基于分析服务，轻松地快速开发和部署大数据分析。 运行生产 Azure Databricks 工作负荷时，监视和故障排除性能问题是一项非常关键。 若要标识的常见性能问题，最好使用监视根据遥测数据的可视化效果。

## <a name="prerequisites"></a>必备组件

若要设置 Grafana 仪表板在本文中所示：

- 将遥测发送到 Log Analytics 工作区中，使用 Azure Databricks 监视库将 Databricks 群集配置。 有关详细信息，请参阅[配置 Azure Databricks 以发送到 Azure Monitor 指标](./configure-cluster.md)。

- 在虚拟机中部署 Grafana。 请参阅[使用仪表板直观显示 Azure Databricks 指标](./dashboards.md)。

部署 Grafana 仪表板包含一系列时间序列的可视化效果。 每个关系图是指标的 Apache Spark 作业，相关的作业和任务组成每个阶段的阶段的时间序列绘图。

## <a name="azure-databricks-performance-overview"></a>Azure Databricks 性能概述

Azure Databricks 基于 Apache Spark 的常规用途的分布式计算系统。 应用程序代码，称为**作业**，由群集管理器协调的 Apache Spark 群集上执行。 一般情况下，作业是的最高级别的计算单位。 作业代表由 Spark 应用程序执行完成操作。 典型的操作包括从源读取数据、 应用数据转换，并将结果写入存储或另一个目标。

作业可分解**阶段**。 作业将推进阶段通过按顺序，这意味着更高版本阶段必须等待较早的阶段，才能完成。 阶段包含完全相同的组**任务**，可以并行执行的 Spark 群集的多个节点上。 任务是执行数据的子集上发生的最高粒度单位。

后续部分介绍可用于性能故障排除某些仪表板可视化效果。

## <a name="job-and-stage-latency"></a>作业和阶段延迟

作业滞后时间是从作业执行的持续时间，直到它完成启动时。 它会显示为百分位数的作业执行的每个群集和应用程序 ID，以允许离群值的可视化效果。 下图显示了作业历史记录，第 90 百分位达到 50 秒，即使第 50 百分位持续了大约 10 秒。

![图中显示了作业滞后时间](./_images/grafana-job-latency.png)

调查作业执行的群集和应用程序，寻找延迟峰值。 一旦识别出群集和应用程序具有高延迟，继续调查阶段延迟。

阶段延迟也显示为百分点值以允许离群值的可视化效果。 按群集、 应用程序和阶段名称划分阶段延迟。 标识在图中要确定哪些任务会阻碍着设备得到的阶段完成的任务延迟峰值。

![显示阶段延迟图表](./_images/grafana-stage-latency.png)

群集吞吐量图显示了作业、 阶段和每分钟完成的任务数。 这可帮助你了解相对数量的阶段和每个作业的任务方面的工作负荷。 在这里可以看到阶段的数目时约有 12 个作业每分钟数，范围介于 2 到 6 之间&ndash;24 每分钟。

![关系图显示群集吞吐量](./_images/grafana-cluster-throughput.png)

## <a name="sum-of-task-execution-latency"></a>任务执行延迟的总和

此可视化效果显示每个主机群集上运行的任务执行延迟的总和。 使用此关系图中检测到群集，或每个执行器的任务 misallocation 由于减的前提下主机缓慢运行的任务。 在下图中，大多数主机具有大约 30 秒的总和。 但是，两个主机必须将鼠标悬停在大约 10 分钟的总和。 主机运行速度缓慢，或者 misallocated 的每个执行器的任务数。

![每个主机的任务执行的图形显示总和](./_images/grafana-sum-task-exec.png)

每个执行器的任务数显示两个执行器分配数不相称的任务，从而产生瓶颈。

![图中显示了每个执行器的任务](./_images/grafana-tasks-per-exec.png)

## <a name="task-metrics-per-stage"></a>每个阶段的任务度量值

任务度量值可视化提供用于执行任务的成本明细。 可以使用它所花费的相对时间，请参阅上序列化和反序列化等任务。 此数据可能会显示优化机会&mdash;例如，通过使用[将变量广播](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#broadcast-variables)以避免传送的数据。 任务度量值还显示一个任务，无序播放数据大小和随机读取和写入时间。 如果这些值较高，则表示大量的数据是通过网络移动。

另一个任务指标是计划程序延迟，测量其所需的时间计划的任务。 理想情况下，此值应为低与执行器计算时间相比较，指的是时间所用实际执行的任务。

以下关系图显示了计划程序延迟时间 (3.7 s)，超过了执行器计算时间 (1.1 s)。 这意味着花费在等待任务进行计划比执行实际工作的时间。

![显示每个阶段的任务度量值图表](./_images/grafana-metrics-per-stage.png)

在这种情况下，问题被引起太多分区，这会导致大量开销。 减少分区数目降低计划程序延迟时间。 下一步图所示，大多数情况下执行任务所用。

![显示每个阶段的任务度量值图表](./_images/grafana-metrics-per-stage2.png)

## <a name="streaming-throughput-and-latency"></a>流式处理吞吐量和延迟

流式处理吞吐量直接与结构化流式处理。 有两个重要的指标与流式处理吞吐量相关联：第二个和已处理的行，每秒每个输入的行数。 如果每秒的输入的行 outpaces 每秒处理的行，则意味着落后流处理系统。 此外，如果输入的数据来自事件中心或 Kafka，然后每秒的输入的行应跟上数据引入速率在前端。

两个作业可以有类似但截然不同的流式处理度量值的群集吞吐量。 下面的屏幕截图显示了两个不同的工作负荷。 它们是在群集吞吐量 （作业、 阶段和每分钟的任务） 方面类似。 但第二次运行处理 12000 行/sec，对比 4,000 行数/秒。

![关系图显示流式处理吞吐量](./_images/grafana-streaming-throughput.png)

流式处理吞吐量通常是更好的业务指标比群集吞吐量，因为它测量处理的数据记录数。

## <a name="resource-consumption-per-executor"></a>每个执行器的资源消耗

这些指标有助于了解每个执行器执行的工作。

**百分比指标**测量上各种操作，表示与整体执行器计算时间所用时间的比率为花费多少时间的执行器。 指标包括：

- %序列化时间
- %反序列化时间
- %Cpu 执行器时间
- JVM 时间百分比

这些可视化效果显示多少每个指标可能会导致总体执行器处理。

![显示百分比指标图表](./_images/grafana-percentage.png)

**无序播放指标**混排在执行器之间的数据与相关度量值。

- 无序播放 I/O
- 无序播放内存
- 文件系统使用情况
- 磁盘使用情况

## <a name="common-performance-bottlenecks"></a>常见的性能瓶颈

在 Spark 中的两个常见性能瓶颈*任务延迟*和一个*非最佳随机选择分区计数*。

### <a name="task-stragglers"></a>任务延迟

在作业中的阶段用阻止后续阶段中的早期阶段按顺序执行。 如果一个任务执行随机选择分区慢于其他任务，在群集中的所有任务必须都等待速度缓慢的任务来保持同步，才能结束阶段。 这可能由于以下原因：

1. 主机组的运行速度缓慢。 症状：高的任务、 阶段或作业延迟和低群集吞吐量。 不会均匀分布任务延迟，每个主机的总和。 但是，资源消耗将均匀分布在执行程序。

1. 任务有一个成本高昂的聚合来执行 （数据倾斜）。 症状：均匀地分布高的任务，阶段，或作业和低群集吞吐量，但每个主机的延迟的总和。 资源消耗将均匀分布在各执行器。

1. 如果分区是大小不等，更大的分区可能会导致不均衡的任务执行 （分区倾斜）。 症状：执行器资源消耗较高相比其他执行器在群集上运行。 在该执行器上运行的所有任务将运行缓慢，并在管道中保存阶段执行。 这些阶段被称为*暂存屏障*。

### <a name="non-optimal-shuffle-partition-count"></a>非最佳随机选择分区计数

在结构化流式处理查询的执行器任务赋值类型为群集的资源密集型操作。 如果无序播放数据不是最佳的大小，为任务的延迟量吞吐量和延迟会对产生负面影响。 如果有过少的分区，在群集中的内核数将没有得到充分利用这可能会导致在处理效率低下。 相反，如果有太多的分区，是管理的大量开销少量的任务。

使用资源消耗指标值分区倾斜和 misallocation 的执行程序在群集上进行故障排除。 如果分区有偏差，将在群集上运行其他执行器相比提升执行器资源。

例如下, 图显示了使用混排在前两个执行器上的内存为 90 X 大于其他执行程序：

![显示百分比指标图表](./_images/grafana-shuffle-memory.png)