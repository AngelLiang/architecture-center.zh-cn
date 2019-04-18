---
title: 在 Azure 中的可靠性监视的应用程序运行状况
description: 如何使用监视来改善应用程序在 Azure 中的可靠性
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: 46f9f3fb623a2facf4b9f9c6ec57376ae59e41dc
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646497"
---
# <a name="monitoring-application-health-for-reliability-in-azure"></a>在 Azure 中的可靠性监视的应用程序运行状况

监视和诊断对于复原能力至关重要。 如果出现故障，则需要知道*的*失败，它*时*它失败&mdash;并*为什么*。

*监视*不是与相同*故障检测*。 例如，你的应用程序可以检测暂时性错误，然后重试，避免停机时间。 但它还应该记录重试操作，以便可以监视错误率，若要获取应用程序运行状况的整体视图。

监视和诊断过程视为包含四个不同的阶段的管道：检测、 集合和存储、 分析和诊断和可视化效果和警报。

## <a name="instrumentation"></a>检测

因此，检测是关键，它并不可行直接，监视应用程序。 多个虚拟机 (Vm)，这将添加和删除随着时间的推移可能会运行大规模分布式的系统。 同样，云应用程序可能使用大量的数据存储和单个用户操作可能跨越多个子系统。

提供丰富的检测：

- 对于失败的可能但尚未发生，提供足够的数据来确定原因，缓解局面，并确保系统保持可用。
- 针对已发生的故障，应用程序应该向用户返回相应的错误消息，但应尝试继续运行，尽管功能降低。

监视系统应该捕获相关详细信息，以便可以有效地还原应用程序，并且如有必要，设计人员和开发人员可以修改系统，以防再次发生这种情况。

监视的原始数据可以来自各种源，包括：

- 应用程序日志，例如由[Azure Application Insights](/azure/azure-monitor/app/app-insights-overview)服务。
- 操作系统性能指标收集[Azure 监视代理](/azure/azure-monitor/platform/agents-overview)。
- [Azure 资源](/azure/azure-monitor/platform/metrics-supported)，包括度量值收集的 Azure Monitor。
- [Azure 服务运行状况](/azure/service-health/service-health-overview)，它提供了一个仪表板，以帮助您跟踪活动事件。
- [Azure AD 日志](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) 内置于 Azure 平台。

大多数 Azure 服务都指标和诊断，可以配置它们以分析并确定问题的原因。 若要了解详细信息，请参阅[由 Azure Monitor 监视数据收集](/azure/azure-monitor/platform/data-collection)。

## <a name="collection-and-storage"></a>收集和存储

原始检测数据可以保存在各种位置和格式，包括：

- 应用程序跟踪日志
- IIS 日志
- 性能计数器

这些不同的源收集、 合并，并放置在可靠的数据存储在 Azure 中，如 Application Insights、 Azure Monitor 指标、 服务运行状况、 存储帐户和 Azure Log Analytics 中。

## <a name="analysis-and-diagnosis"></a>分析和诊断

分析数据整合到这些数据存储区，用于解决问题，获得应用程序运行状况的总体视图。 通常情况下，你可以[搜索并分析](/azure/azure-monitor/log-query/log-query-overview)Application Insights 和 Log Analytics 使用 Kusto 查询或使用预配置视图关系图中的数据 [管理解决方案](/azure/azure-monitor/insights/solutions-inventory)。 或使用 Azure 顾问查看关于建议，并将重点放[复原](/azure/advisor/advisor-high-availability-recommendations)并[性能](/azure/advisor/advisor-performance-recommendations)。

## <a name="visualization-and-alerts"></a>可视化和警报

简化的运算符，从而发现问题或趋势快速，例如一个仪表板或电子邮件警报的格式呈现遥测数据。

使用获取的应用程序状态的完整堆栈视图 [Azure 仪表板](/azure/azure-portal/azure-portal-dashboards)创建监视从 Application Insights，Log Analytics、 Azure Monitor 指标和服务运行状况的关系图的合并的视图。 并使用 [Azure Monitor 警报](/azure/azure-monitor/platform/alerts-overview)Log Analytics 和 Application Insights 中创建服务运行状况、 资源运行状况、 Azure Monitor 指标通知日志。

有关监视和诊断的详细信息，请参阅[监视和诊断](../best-practices/monitoring.md)。

## <a name="health-probes-and-check-functions"></a>运行状况探测，并检查函数

运行状况和性能的应用程序可能会随着时间推移，降低和下降直到应用程序失败，可能不明显。

实施探测或检查函数，并定期从应用程序外部运行。 这些检查可以很简单，作为测量响应时间为作为一个整体应用程序、 应用程序的各个部分、 针对特定服务的应用程序使用，或单独的组件。

检查函数，可以运行以确保它们产生有效的结果、 测量滞后时间和检查可用性，并从系统中提取信息的过程。

## <a name="long-running-workflow-failures"></a>长时间运行工作流失败

长时间运行的工作流通常包括多个步骤，其中每个应是独立。

跟踪长时间运行的进程来最大程度减少整个工作流需要回滚，或将需要执行多个补偿事务的可能性的进度。

>[!TIP]
> 监视和管理长时间运行工作流的进度实现一种模式，例如[计划程序代理监督程序](../patterns/scheduler-agent-supervisor.md)。

## <a name="application-logs"></a>应用程序日志

应用程序日志是诊断数据的重要来源。 若要深入了解在需要时大多数，请执行应用程序日志记录的最佳做法。

### <a name="log-data-in-the-production-environment"></a>在生产环境中的日志数据

应用程序运行在生产环境中，以便有足够的信息来诊断生产状态中的问题的原因时捕获可靠的遥测数据。

### <a name="log-events-at-service-boundaries"></a>在服务边界处的日志事件

包含跨服务边界流动的关联 ID。 如果某个事务流经多个服务，其中的一个失败的关联 ID 可帮助您跟踪请求跨应用程序和查明事务失败的原因。

### <a name="use-semantic-structured-logging"></a>使用语义 （结构化） 的日志记录

使用结构化日志很方便地自动执行的消耗和分析日志数据，这是在云规模尤其重要。 通常情况下，我们建议将 Azure 资源指标和诊断数据存储在 Log Analytics 工作区中，而不存储帐户中。 这样一来，可以使用 Kusto 查询以获取所需快速和结构化格式的数据。 此外可以使用 Azure Monitor Api 和 Azure Log Analytics Api。

### <a name="use-asynchronous-logging"></a>使用异步日志记录

同步日志记录操作有时会阻止应用程序代码，导致请求一样写入日志的备份。 使用异步日志记录以在应用程序日志记录期间保持可用性。

### <a name="separate-application-logging-from-auditing"></a>从审核的单独的应用程序日志记录

审核记录通常维护符合性或法规要求，并且必须完成。 若要避免删除的事务，独立于诊断日志的审核日志的保留。

## <a name="remote-call-statistics"></a>远程调用统计信息

跟踪和报告远程调用实时统计信息，并可以方便地查看此信息，使运营团队获得应用程序的运行状况的即时视图。 汇总远程调用指标，例如延迟、 吞吐量和 99 和 95 百分位中的错误。

## <a name="transient-exceptions-and-retries"></a>暂时性异常和重试次数

跟踪一段时间来发现问题或应用程序的重试逻辑中的故障数暂时性异常和重试。 随着时间的推移增加异常的趋势可能表示该服务有问题，可能会失败。 有关详细信息，请参阅[重试服务特定指南](../best-practices/retry-service-specific.md)。

## <a name="early-warning-system"></a>早期警告系统

监视警告符号可能需要主动干预的应用程序。 评估应用程序和其依赖项的总体运行状况的工具帮助您快速识别时系统或其组件突然变得不可用。 使用它们来实现了早期系统警告。

1. 标识应用程序的运行状况，例如暂时性异常和远程调用延迟的关键性能指标。
1. 在它们变得严重并且需要恢复响应之前先行确定问题的级别上设置阈值。
1. 达到阈值时，将警报发送到操作员。

请考虑[Microsoft System Center 2016](https://www.microsoft.com/cloud-platform/system-center)或第三方工具，提供监视功能。 大多数监视解决方案跟踪关键的性能计数器和服务可用性。 [Azure 资源运行状况](/azure/service-health/resource-health-checks-resource-types)提供一些内置的运行状况状态检查，可以帮助诊断 Azure 服务的限制。

## <a name="subscription-and-service-limitations"></a>订阅和服务限制

Azure 订阅在特定的资源类型，例如资源组、 内核数和存储帐户的数目受到限制。 若要确保你的应用程序不会遇到 Azure 订阅限制，请创建轮询接近其限制和配额的服务的警报。

解决警报具有以下订阅限制。

### <a name="individual-services"></a>单个服务

各项 Azure 服务存在消耗量限制对存储、 吞吐量、 连接、 请求数 / 秒和其他指标。 如果它尝试使用的资源超过这些限制，这样会导致服务限制，并可能停机，你的应用程序将失败。

根据特定服务和应用程序的需求，您可以经常使其保持在这些限制通过纵向扩展 （例如选择另一个定价层） 或横向扩展 （添加新的实例）。

### <a name="azure-storage-scalability-and-performance-targets"></a>Azure 存储可伸缩性和性能目标

Azure 允许每个订阅的存储帐户的最大数量。 如果你的应用程序需要更多存储帐户不是当前在你的订阅中可用，请使用其他存储帐户创建新的订阅。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](/azure/azure-subscription-service-limits/#storage-limits)。

如果你超出 Azure 存储可伸缩性和性能目标，你的应用程序会受到存储限制。 有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标](/azure/storage/storage-scalability-targets/)。

### <a name="scalability-targets-for-virtual-machine-disks"></a>虚拟机磁盘的可伸缩性目标

Azure 基础结构即服务 (IaaS) VM 支持附加多个数据磁盘，具体取决于多种因素，包括 VM 大小和存储帐户的类型。 如果应用程序超过虚拟机磁盘的可伸缩性目标，请预配更多的存储帐户并在其中创建虚拟机磁盘。 有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)。

### <a name="vm-size"></a>VM 大小

如果实际 CPU、 内存、 磁盘和 Vm 的 I/O 接近的 VM 大小的限制，你的应用程序可能会遇到容量问题。 若要更正问题，请增加 VM 大小。 中介绍了 VM 大小[在 Azure 中的虚拟机的大小](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

如果你的工作负荷量随着时间的推移，请考虑使用虚拟机规模集自动缩放 VM 实例数。 否则，您需要手动增加或减少 Vm 的数目。 有关详细信息，请参阅[虚拟机规模集概述](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/)。

### <a name="azure-sql-database"></a>Azure SQL 数据库

如果你的 Azure SQL 数据库层不足够用来处理应用程序的数据库事务单位 (DTU) 要求，你的数据的使用将受到限制。 有关选择正确的服务计划的详细信息，请参阅[购买模型的 Azure SQL 数据库](/azure/sql-database/sql-database-service-tiers/)。

## <a name="third-party-services"></a>第三方服务

如果你的应用程序在第三方服务上具有依赖项，确定如何对这些服务可能会失败和效果故障将具有对应用程序。

监视和诊断，可能不包括第三方服务。 登录到这些服务的调用，并将它们与应用程序的运行状况和使用的唯一标识符的诊断日志记录相关联。 有关监视和诊断的成熟做法的详细信息，请参阅[监视和诊断指南](../best-practices/monitoring.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [规划灾难恢复](./disaster-recovery.md)
