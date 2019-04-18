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
# <a name="monitoring-application-health-for-reliability-in-azure"></a><span data-ttu-id="ee9c5-103">在 Azure 中的可靠性监视的应用程序运行状况</span><span class="sxs-lookup"><span data-stu-id="ee9c5-103">Monitoring application health for reliability in Azure</span></span>

<span data-ttu-id="ee9c5-104">监视和诊断对于复原能力至关重要。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-104">Monitoring and diagnostics are crucial for resiliency.</span></span> <span data-ttu-id="ee9c5-105">如果出现故障，则需要知道*的*失败，它*时*它失败&mdash;并*为什么*。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-105">If something fails, you need to know *that* it failed, *when* it failed &mdash; and *why*.</span></span>

<span data-ttu-id="ee9c5-106">*监视*不是与相同*故障检测*。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-106">*Monitoring* is not the same as *failure detection*.</span></span> <span data-ttu-id="ee9c5-107">例如，你的应用程序可以检测暂时性错误，然后重试，避免停机时间。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-107">For example, your application might detect a transient error and retry, avoiding downtime.</span></span> <span data-ttu-id="ee9c5-108">但它还应该记录重试操作，以便可以监视错误率，若要获取应用程序运行状况的整体视图。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-108">But it should also log the retry operation so that you can monitor the error rate to get an overall picture of application health.</span></span>

<span data-ttu-id="ee9c5-109">监视和诊断过程视为包含四个不同的阶段的管道：检测、 集合和存储、 分析和诊断和可视化效果和警报。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-109">Think of the monitoring and diagnostics process as a pipeline with four distinct stages: Instrumentation, collection and storage, analysis and diagnosis, and visualization and alerts.</span></span>

## <a name="instrumentation"></a><span data-ttu-id="ee9c5-110">检测</span><span class="sxs-lookup"><span data-stu-id="ee9c5-110">Instrumentation</span></span>

<span data-ttu-id="ee9c5-111">因此，检测是关键，它并不可行直接，监视应用程序。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-111">It's not practical to monitor your application directly, so instrumentation is key.</span></span> <span data-ttu-id="ee9c5-112">多个虚拟机 (Vm)，这将添加和删除随着时间的推移可能会运行大规模分布式的系统。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-112">A large-scale distributed system might run on dozens of virtual machines (VMs), which are added and removed over time.</span></span> <span data-ttu-id="ee9c5-113">同样，云应用程序可能使用大量的数据存储和单个用户操作可能跨越多个子系统。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-113">Likewise, a cloud application might use a number of data stores and a single user action might span multiple subsystems.</span></span>

<span data-ttu-id="ee9c5-114">提供丰富的检测：</span><span class="sxs-lookup"><span data-stu-id="ee9c5-114">Provide rich instrumentation:</span></span>

- <span data-ttu-id="ee9c5-115">对于失败的可能但尚未发生，提供足够的数据来确定原因，缓解局面，并确保系统保持可用。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-115">For failures that are likely but have not yet occurred, provide enough data to determine the cause, mitigate the situation, and ensure that the system remains available.</span></span>
- <span data-ttu-id="ee9c5-116">针对已发生的故障，应用程序应该向用户返回相应的错误消息，但应尝试继续运行，尽管功能降低。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-116">For failures that have already occurred, the application should return an appropriate error message to the user but should attempt to continue running, albeit with reduced functionality.</span></span>

<span data-ttu-id="ee9c5-117">监视系统应该捕获相关详细信息，以便可以有效地还原应用程序，并且如有必要，设计人员和开发人员可以修改系统，以防再次发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-117">Monitoring systems should capture comprehensive details so that applications can be restored efficiently and, if necessary, designers and developers can modify the system to prevent the situation from recurring.</span></span>

<span data-ttu-id="ee9c5-118">监视的原始数据可以来自各种源，包括：</span><span class="sxs-lookup"><span data-stu-id="ee9c5-118">The raw data for monitoring can come from a variety of sources, including:</span></span>

- <span data-ttu-id="ee9c5-119">应用程序日志，例如由[Azure Application Insights](/azure/azure-monitor/app/app-insights-overview)服务。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-119">Application logs, such as those produced by the [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview) service.</span></span>
- <span data-ttu-id="ee9c5-120">操作系统性能指标收集[Azure 监视代理](/azure/azure-monitor/platform/agents-overview)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-120">Operating system performance metrics collected by [Azure monitoring agents](/azure/azure-monitor/platform/agents-overview).</span></span>
- <span data-ttu-id="ee9c5-121">[Azure 资源](/azure/azure-monitor/platform/metrics-supported)，包括度量值收集的 Azure Monitor。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-121">[Azure resources](/azure/azure-monitor/platform/metrics-supported), including metrics collected by Azure Monitor.</span></span>
- <span data-ttu-id="ee9c5-122">[Azure 服务运行状况](/azure/service-health/service-health-overview)，它提供了一个仪表板，以帮助您跟踪活动事件。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-122">[Azure Service Health](/azure/service-health/service-health-overview), which offers a dashboard to help you track active events.</span></span>
- <span data-ttu-id="ee9c5-123">[Azure AD 日志](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) 内置于 Azure 平台。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-123">[Azure AD logs](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) built into the Azure platform.</span></span>

<span data-ttu-id="ee9c5-124">大多数 Azure 服务都指标和诊断，可以配置它们以分析并确定问题的原因。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-124">Most Azure services have metrics and diagnostics that you can configure to analyze and determine the cause of problems.</span></span> <span data-ttu-id="ee9c5-125">若要了解详细信息，请参阅[由 Azure Monitor 监视数据收集](/azure/azure-monitor/platform/data-collection)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-125">To learn more, see [Monitoring data collected by Azure Monitor](/azure/azure-monitor/platform/data-collection).</span></span>

## <a name="collection-and-storage"></a><span data-ttu-id="ee9c5-126">收集和存储</span><span class="sxs-lookup"><span data-stu-id="ee9c5-126">Collection and storage</span></span>

<span data-ttu-id="ee9c5-127">原始检测数据可以保存在各种位置和格式，包括：</span><span class="sxs-lookup"><span data-stu-id="ee9c5-127">Raw instrumentation data can be held in various locations and formats, including:</span></span>

- <span data-ttu-id="ee9c5-128">应用程序跟踪日志</span><span class="sxs-lookup"><span data-stu-id="ee9c5-128">Application trace logs</span></span>
- <span data-ttu-id="ee9c5-129">IIS 日志</span><span class="sxs-lookup"><span data-stu-id="ee9c5-129">IIS logs</span></span>
- <span data-ttu-id="ee9c5-130">性能计数器</span><span class="sxs-lookup"><span data-stu-id="ee9c5-130">Performance counters</span></span>

<span data-ttu-id="ee9c5-131">这些不同的源收集、 合并，并放置在可靠的数据存储在 Azure 中，如 Application Insights、 Azure Monitor 指标、 服务运行状况、 存储帐户和 Azure Log Analytics 中。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-131">These disparate sources are collected, consolidated, and placed in reliable data stores in Azure, such as Application Insights, Azure Monitor metrics, Service Health, storage accounts, and Azure Log Analytics.</span></span>

## <a name="analysis-and-diagnosis"></a><span data-ttu-id="ee9c5-132">分析和诊断</span><span class="sxs-lookup"><span data-stu-id="ee9c5-132">Analysis and diagnosis</span></span>

<span data-ttu-id="ee9c5-133">分析数据整合到这些数据存储区，用于解决问题，获得应用程序运行状况的总体视图。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-133">Analyze data consolidated in these data stores to troubleshoot issues and gain an overall view of application health.</span></span> <span data-ttu-id="ee9c5-134">通常情况下，你可以[搜索并分析](/azure/azure-monitor/log-query/log-query-overview)Application Insights 和 Log Analytics 使用 Kusto 查询或使用预配置视图关系图中的数据 [管理解决方案](/azure/azure-monitor/insights/solutions-inventory)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-134">Generally, you can [search for and analyze](/azure/azure-monitor/log-query/log-query-overview) the data in Application Insights and Log Analytics using Kusto queries or view preconfigured graphs using [management solutions](/azure/azure-monitor/insights/solutions-inventory).</span></span> <span data-ttu-id="ee9c5-135">或使用 Azure 顾问查看关于建议，并将重点放[复原](/azure/advisor/advisor-high-availability-recommendations)并[性能](/azure/advisor/advisor-performance-recommendations)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-135">Or use Azure Advisor to view recommendations with a focus on [resiliency](/azure/advisor/advisor-high-availability-recommendations) and [performance](/azure/advisor/advisor-performance-recommendations).</span></span>

## <a name="visualization-and-alerts"></a><span data-ttu-id="ee9c5-136">可视化和警报</span><span class="sxs-lookup"><span data-stu-id="ee9c5-136">Visualization and alerts</span></span>

<span data-ttu-id="ee9c5-137">简化的运算符，从而发现问题或趋势快速，例如一个仪表板或电子邮件警报的格式呈现遥测数据。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-137">Present telemetry data in a format that makes it easy for an operator to notice problems or trends quickly, such as a dashboard or email alert.</span></span>

<span data-ttu-id="ee9c5-138">使用获取的应用程序状态的完整堆栈视图 [Azure 仪表板](/azure/azure-portal/azure-portal-dashboards)创建监视从 Application Insights，Log Analytics、 Azure Monitor 指标和服务运行状况的关系图的合并的视图。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-138">Get a full-stack view of application state by using [Azure dashboards](/azure/azure-portal/azure-portal-dashboards) to create a consolidated view of monitoring graphs from Application Insights, Log Analytics, Azure Monitor metrics, and Service Health.</span></span> <span data-ttu-id="ee9c5-139">并使用 [Azure Monitor 警报](/azure/azure-monitor/platform/alerts-overview)Log Analytics 和 Application Insights 中创建服务运行状况、 资源运行状况、 Azure Monitor 指标通知日志。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-139">And use [Azure Monitor alerts](/azure/azure-monitor/platform/alerts-overview) to create notifications on Service Health, resource health, Azure Monitor metrics, logs in Log Analytics, and Application Insights.</span></span>

<span data-ttu-id="ee9c5-140">有关监视和诊断的详细信息，请参阅[监视和诊断](../best-practices/monitoring.md)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-140">For more information about monitoring and diagnostics, see [Monitoring and diagnostics](../best-practices/monitoring.md).</span></span>

## <a name="health-probes-and-check-functions"></a><span data-ttu-id="ee9c5-141">运行状况探测，并检查函数</span><span class="sxs-lookup"><span data-stu-id="ee9c5-141">Health probes and check functions</span></span>

<span data-ttu-id="ee9c5-142">运行状况和性能的应用程序可能会随着时间推移，降低和下降直到应用程序失败，可能不明显。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-142">The health and performance of an application can degrade over time, and degradation might not be noticeable until the application fails.</span></span>

<span data-ttu-id="ee9c5-143">实施探测或检查函数，并定期从应用程序外部运行。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-143">Implement probes or check functions, and run them regularly from outside the application.</span></span> <span data-ttu-id="ee9c5-144">这些检查可以很简单，作为测量响应时间为作为一个整体应用程序、 应用程序的各个部分、 针对特定服务的应用程序使用，或单独的组件。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-144">These checks can be as simple as measuring response time for the application as a whole, for individual parts of the application, for specific services that the application uses, or for separate components.</span></span>

<span data-ttu-id="ee9c5-145">检查函数，可以运行以确保它们产生有效的结果、 测量滞后时间和检查可用性，并从系统中提取信息的过程。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-145">Check functions can run processes to ensure that they produce valid results, measure latency and check availability, and extract information from the system.</span></span>

## <a name="long-running-workflow-failures"></a><span data-ttu-id="ee9c5-146">长时间运行工作流失败</span><span class="sxs-lookup"><span data-stu-id="ee9c5-146">Long-running workflow failures</span></span>

<span data-ttu-id="ee9c5-147">长时间运行的工作流通常包括多个步骤，其中每个应是独立。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-147">Long-running workflows often include multiple steps, each of which should be independent.</span></span>

<span data-ttu-id="ee9c5-148">跟踪长时间运行的进程来最大程度减少整个工作流需要回滚，或将需要执行多个补偿事务的可能性的进度。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-148">Track the progress of long-running processes to minimize the likelihood that the entire workflow will need to be rolled back or that multiple compensating transactions will need to be executed.</span></span>

>[!TIP]
> <span data-ttu-id="ee9c5-149">监视和管理长时间运行工作流的进度实现一种模式，例如[计划程序代理监督程序](../patterns/scheduler-agent-supervisor.md)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-149">Monitor and manage the progress of long-running workflows by implementing a pattern such as [Scheduler Agent Supervisor](../patterns/scheduler-agent-supervisor.md).</span></span>

## <a name="application-logs"></a><span data-ttu-id="ee9c5-150">应用程序日志</span><span class="sxs-lookup"><span data-stu-id="ee9c5-150">Application logs</span></span>

<span data-ttu-id="ee9c5-151">应用程序日志是诊断数据的重要来源。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-151">Application logs are an important source of diagnostics data.</span></span> <span data-ttu-id="ee9c5-152">若要深入了解在需要时大多数，请执行应用程序日志记录的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-152">To gain insight when you need it most, follow best practices for application logging.</span></span>

### <a name="log-data-in-the-production-environment"></a><span data-ttu-id="ee9c5-153">在生产环境中的日志数据</span><span class="sxs-lookup"><span data-stu-id="ee9c5-153">Log data in the production environment</span></span>

<span data-ttu-id="ee9c5-154">应用程序运行在生产环境中，以便有足够的信息来诊断生产状态中的问题的原因时捕获可靠的遥测数据。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-154">Capture robust telemetry data while the application is running in the production environment, so you have sufficient information to diagnose the cause of issues in the production state.</span></span>

### <a name="log-events-at-service-boundaries"></a><span data-ttu-id="ee9c5-155">在服务边界处的日志事件</span><span class="sxs-lookup"><span data-stu-id="ee9c5-155">Log events at service boundaries</span></span>

<span data-ttu-id="ee9c5-156">包含跨服务边界流动的关联 ID。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-156">Include a correlation ID that flows across service boundaries.</span></span> <span data-ttu-id="ee9c5-157">如果某个事务流经多个服务，其中的一个失败的关联 ID 可帮助您跟踪请求跨应用程序和查明事务失败的原因。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-157">If a transaction flows through multiple services and one of them fails, the correlation ID helps you track requests across your application and pinpoints why the transaction failed.</span></span>

### <a name="use-semantic-structured-logging"></a><span data-ttu-id="ee9c5-158">使用语义 （结构化） 的日志记录</span><span class="sxs-lookup"><span data-stu-id="ee9c5-158">Use semantic (structured) logging</span></span>

<span data-ttu-id="ee9c5-159">使用结构化日志很方便地自动执行的消耗和分析日志数据，这是在云规模尤其重要。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-159">With structured logs, it's easier to automate the consumption and analysis of the log data, which is especially important at cloud scale.</span></span> <span data-ttu-id="ee9c5-160">通常情况下，我们建议将 Azure 资源指标和诊断数据存储在 Log Analytics 工作区中，而不存储帐户中。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-160">Generally, we recommend storing Azure resources metrics and diagnostics data in a Log Analytics workspace rather than in a storage account.</span></span> <span data-ttu-id="ee9c5-161">这样一来，可以使用 Kusto 查询以获取所需快速和结构化格式的数据。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-161">This way, you can use Kusto queries to obtain the data you want quickly and in a structured format.</span></span> <span data-ttu-id="ee9c5-162">此外可以使用 Azure Monitor Api 和 Azure Log Analytics Api。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-162">You can also use Azure Monitor APIs and Azure Log Analytics APIs.</span></span>

### <a name="use-asynchronous-logging"></a><span data-ttu-id="ee9c5-163">使用异步日志记录</span><span class="sxs-lookup"><span data-stu-id="ee9c5-163">Use asynchronous logging</span></span>

<span data-ttu-id="ee9c5-164">同步日志记录操作有时会阻止应用程序代码，导致请求一样写入日志的备份。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-164">Synchronous logging operations sometimes block your application code, causing requests to back up as logs are written.</span></span> <span data-ttu-id="ee9c5-165">使用异步日志记录以在应用程序日志记录期间保持可用性。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-165">Use asynchronous logging to preserve availability during application logging.</span></span>

### <a name="separate-application-logging-from-auditing"></a><span data-ttu-id="ee9c5-166">从审核的单独的应用程序日志记录</span><span class="sxs-lookup"><span data-stu-id="ee9c5-166">Separate application logging from auditing</span></span>

<span data-ttu-id="ee9c5-167">审核记录通常维护符合性或法规要求，并且必须完成。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-167">Audit records are commonly maintained for compliance or regulatory requirements and must be complete.</span></span> <span data-ttu-id="ee9c5-168">若要避免删除的事务，独立于诊断日志的审核日志的保留。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-168">To avoid dropped transactions, maintain audit logs separately from diagnostic logs.</span></span>

## <a name="remote-call-statistics"></a><span data-ttu-id="ee9c5-169">远程调用统计信息</span><span class="sxs-lookup"><span data-stu-id="ee9c5-169">Remote call statistics</span></span>

<span data-ttu-id="ee9c5-170">跟踪和报告远程调用实时统计信息，并可以方便地查看此信息，使运营团队获得应用程序的运行状况的即时视图。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-170">Track and report remote call statistics in real time and provide an easy way to review this information, so the operations team has an instantaneous view into the health of your application.</span></span> <span data-ttu-id="ee9c5-171">汇总远程调用指标，例如延迟、 吞吐量和 99 和 95 百分位中的错误。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-171">Summarize remote call metrics, such as latency, throughput, and errors in the 99 and 95 percentiles.</span></span>

## <a name="transient-exceptions-and-retries"></a><span data-ttu-id="ee9c5-172">暂时性异常和重试次数</span><span class="sxs-lookup"><span data-stu-id="ee9c5-172">Transient exceptions and retries</span></span>

<span data-ttu-id="ee9c5-173">跟踪一段时间来发现问题或应用程序的重试逻辑中的故障数暂时性异常和重试。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-173">Track the number of transient exceptions and retries over time to uncover issues or failures in your application's retry logic.</span></span> <span data-ttu-id="ee9c5-174">随着时间的推移增加异常的趋势可能表示该服务有问题，可能会失败。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-174">A trend of increasing exceptions over time may indicate that the service is having an issue and may fail.</span></span> <span data-ttu-id="ee9c5-175">有关详细信息，请参阅[重试服务特定指南](../best-practices/retry-service-specific.md)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-175">For more information, see [Retry service specific guidance](../best-practices/retry-service-specific.md).</span></span>

## <a name="early-warning-system"></a><span data-ttu-id="ee9c5-176">早期警告系统</span><span class="sxs-lookup"><span data-stu-id="ee9c5-176">Early warning system</span></span>

<span data-ttu-id="ee9c5-177">监视警告符号可能需要主动干预的应用程序。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-177">Monitor your application for warning signs that might require proactive intervention.</span></span> <span data-ttu-id="ee9c5-178">评估应用程序和其依赖项的总体运行状况的工具帮助您快速识别时系统或其组件突然变得不可用。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-178">Tools that assess the overall health of the application and its dependencies help you to recognize quickly when a system or its components suddenly become unavailable.</span></span> <span data-ttu-id="ee9c5-179">使用它们来实现了早期系统警告。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-179">Use them to implement an early warning system.</span></span>

1. <span data-ttu-id="ee9c5-180">标识应用程序的运行状况，例如暂时性异常和远程调用延迟的关键性能指标。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-180">Identify the key performance indicators of your application's health, such as transient exceptions and remote call latency.</span></span>
1. <span data-ttu-id="ee9c5-181">在它们变得严重并且需要恢复响应之前先行确定问题的级别上设置阈值。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-181">Set thresholds at levels that identify issues before they become critical and require a recovery response.</span></span>
1. <span data-ttu-id="ee9c5-182">达到阈值时，将警报发送到操作员。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-182">Send an alert to operations when the threshold value is reached.</span></span>

<span data-ttu-id="ee9c5-183">请考虑[Microsoft System Center 2016](https://www.microsoft.com/cloud-platform/system-center)或第三方工具，提供监视功能。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-183">Consider [Microsoft System Center 2016](https://www.microsoft.com/cloud-platform/system-center) or third-party tools to provide monitoring capabilities.</span></span> <span data-ttu-id="ee9c5-184">大多数监视解决方案跟踪关键的性能计数器和服务可用性。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-184">Most monitoring solutions track key performance counters and service availability.</span></span> <span data-ttu-id="ee9c5-185">[Azure 资源运行状况](/azure/service-health/resource-health-checks-resource-types)提供一些内置的运行状况状态检查，可以帮助诊断 Azure 服务的限制。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-185">[Azure resource health](/azure/service-health/resource-health-checks-resource-types) provides some built-in health status checks, which can help diagnose throttling of Azure services.</span></span>

## <a name="subscription-and-service-limitations"></a><span data-ttu-id="ee9c5-186">订阅和服务限制</span><span class="sxs-lookup"><span data-stu-id="ee9c5-186">Subscription and service limitations</span></span>

<span data-ttu-id="ee9c5-187">Azure 订阅在特定的资源类型，例如资源组、 内核数和存储帐户的数目受到限制。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-187">Azure subscriptions have limits on certain resource types, such as number of resource groups, cores, and storage accounts.</span></span> <span data-ttu-id="ee9c5-188">若要确保你的应用程序不会遇到 Azure 订阅限制，请创建轮询接近其限制和配额的服务的警报。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-188">To ensure that your application doesn't run up against Azure subscription limits, create alerts that poll for services nearing their limits and quotas.</span></span>

<span data-ttu-id="ee9c5-189">解决警报具有以下订阅限制。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-189">Address the following subscription limits with alerts.</span></span>

### <a name="individual-services"></a><span data-ttu-id="ee9c5-190">单个服务</span><span class="sxs-lookup"><span data-stu-id="ee9c5-190">Individual services</span></span>

<span data-ttu-id="ee9c5-191">各项 Azure 服务存在消耗量限制对存储、 吞吐量、 连接、 请求数 / 秒和其他指标。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-191">Individual Azure services have consumption limits on storage, throughput, number of connections, requests per second, and other metrics.</span></span> <span data-ttu-id="ee9c5-192">如果它尝试使用的资源超过这些限制，这样会导致服务限制，并可能停机，你的应用程序将失败。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-192">Your application will fail if it attempts to use resources beyond these limits, resulting in service throttling and possible downtime.</span></span>

<span data-ttu-id="ee9c5-193">根据特定服务和应用程序的需求，您可以经常使其保持在这些限制通过纵向扩展 （例如选择另一个定价层） 或横向扩展 （添加新的实例）。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-193">Depending on the specific service and your application requirements, you can often stay under these limits by scaling up (choosing another pricing tier, for example) or scaling out (adding new instances).</span></span>

### <a name="azure-storage-scalability-and-performance-targets"></a><span data-ttu-id="ee9c5-194">Azure 存储可伸缩性和性能目标</span><span class="sxs-lookup"><span data-stu-id="ee9c5-194">Azure storage scalability and performance targets</span></span>

<span data-ttu-id="ee9c5-195">Azure 允许每个订阅的存储帐户的最大数量。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-195">Azure allows a maximum number of storage accounts per subscription.</span></span> <span data-ttu-id="ee9c5-196">如果你的应用程序需要更多存储帐户不是当前在你的订阅中可用，请使用其他存储帐户创建新的订阅。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-196">If your application requires more storage accounts than are currently available in your subscription, create a new subscription with additional storage accounts.</span></span> <span data-ttu-id="ee9c5-197">有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](/azure/azure-subscription-service-limits/#storage-limits)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-197">For more information, see [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits/#storage-limits).</span></span>

<span data-ttu-id="ee9c5-198">如果你超出 Azure 存储可伸缩性和性能目标，你的应用程序会受到存储限制。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-198">If you exceed Azure storage scalability and performance targets, your application will experience storage throttling.</span></span> <span data-ttu-id="ee9c5-199">有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标](/azure/storage/storage-scalability-targets/)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-199">For more information, see [Azure Storage scalability and performance targets](/azure/storage/storage-scalability-targets/).</span></span>

### <a name="scalability-targets-for-virtual-machine-disks"></a><span data-ttu-id="ee9c5-200">虚拟机磁盘的可伸缩性目标</span><span class="sxs-lookup"><span data-stu-id="ee9c5-200">Scalability targets for virtual machine disks</span></span>

<span data-ttu-id="ee9c5-201">Azure 基础结构即服务 (IaaS) VM 支持附加多个数据磁盘，具体取决于多种因素，包括 VM 大小和存储帐户的类型。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-201">An Azure infrastructure as a service (IaaS) VM supports attaching a number of data disks, depending on several factors, including the VM size and the type of storage account.</span></span> <span data-ttu-id="ee9c5-202">如果应用程序超过虚拟机磁盘的可伸缩性目标，请预配更多的存储帐户并在其中创建虚拟机磁盘。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-202">If your application exceeds the scalability targets for virtual machine disks, provision additional storage accounts and create the virtual machine disks there.</span></span> <span data-ttu-id="ee9c5-203">有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-203">For more information, see [Azure Storage scalability and performance targets](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks).</span></span>

### <a name="vm-size"></a><span data-ttu-id="ee9c5-204">VM 大小</span><span class="sxs-lookup"><span data-stu-id="ee9c5-204">VM size</span></span>

<span data-ttu-id="ee9c5-205">如果实际 CPU、 内存、 磁盘和 Vm 的 I/O 接近的 VM 大小的限制，你的应用程序可能会遇到容量问题。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-205">If the actual CPU, memory, disk, and I/O of your VMs approach the limits of the VM size, your application may experience capacity issues.</span></span> <span data-ttu-id="ee9c5-206">若要更正问题，请增加 VM 大小。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-206">To correct the issues, increase the VM size.</span></span> <span data-ttu-id="ee9c5-207">中介绍了 VM 大小[在 Azure 中的虚拟机的大小](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-207">VM sizes are described in [Sizes for virtual machines in Azure](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).</span></span>

<span data-ttu-id="ee9c5-208">如果你的工作负荷量随着时间的推移，请考虑使用虚拟机规模集自动缩放 VM 实例数。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-208">If your workload fluctuates over time, consider using virtual machine scale sets to automatically scale the number of VM instances.</span></span> <span data-ttu-id="ee9c5-209">否则，您需要手动增加或减少 Vm 的数目。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-209">Otherwise, you need to manually increase or decrease the number of VMs.</span></span> <span data-ttu-id="ee9c5-210">有关详细信息，请参阅[虚拟机规模集概述](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-210">For more information, see the [virtual machine scale sets overview](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/).</span></span>

### <a name="azure-sql-database"></a><span data-ttu-id="ee9c5-211">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="ee9c5-211">Azure SQL Database</span></span>

<span data-ttu-id="ee9c5-212">如果你的 Azure SQL 数据库层不足够用来处理应用程序的数据库事务单位 (DTU) 要求，你的数据的使用将受到限制。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-212">If your Azure SQL Database tier isn't adequate to handle your application's Database Transaction Unit (DTU) requirements, your data use will be throttled.</span></span> <span data-ttu-id="ee9c5-213">有关选择正确的服务计划的详细信息，请参阅[购买模型的 Azure SQL 数据库](/azure/sql-database/sql-database-service-tiers/)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-213">For more information on selecting the correct service plan, see [Azure SQL Database purchasing models](/azure/sql-database/sql-database-service-tiers/).</span></span>

## <a name="third-party-services"></a><span data-ttu-id="ee9c5-214">第三方服务</span><span class="sxs-lookup"><span data-stu-id="ee9c5-214">Third-party services</span></span>

<span data-ttu-id="ee9c5-215">如果你的应用程序在第三方服务上具有依赖项，确定如何对这些服务可能会失败和效果故障将具有对应用程序。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-215">If your application has dependencies on third-party services, identify how these services can fail and what effect failures will have on your application.</span></span>

<span data-ttu-id="ee9c5-216">监视和诊断，可能不包括第三方服务。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-216">A third-party service might not include monitoring and diagnostics.</span></span> <span data-ttu-id="ee9c5-217">登录到这些服务的调用，并将它们与应用程序的运行状况和使用的唯一标识符的诊断日志记录相关联。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-217">Log calls to these services and correlate them with your application's health and diagnostic logging using a unique identifier.</span></span> <span data-ttu-id="ee9c5-218">有关监视和诊断的成熟做法的详细信息，请参阅[监视和诊断指南](../best-practices/monitoring.md)。</span><span class="sxs-lookup"><span data-stu-id="ee9c5-218">For more information on proven practices for monitoring and diagnostics, see [Monitoring and diagnostics guidance](../best-practices/monitoring.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ee9c5-219">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ee9c5-219">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ee9c5-220">规划灾难恢复</span><span class="sxs-lookup"><span data-stu-id="ee9c5-220">Plan for disaster recovery</span></span>](./disaster-recovery.md)
