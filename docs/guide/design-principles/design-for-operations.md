---
title: 运营设计
description: 合理设计应用程序，使运营团队获得所需的工具。
author: MikeWasson
ms.date: 08/30/2018
ms.openlocfilehash: a73479a7661c042d05db61907d1f993fc04ac11d
ms.sourcegitcommit: ae8a1de6f4af7a89a66a8339879843d945201f85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "43326232"
---
# <a name="design-for-operations"></a><span data-ttu-id="d2bca-103">运营设计</span><span class="sxs-lookup"><span data-stu-id="d2bca-103">Design for operations</span></span>

## <a name="design-an-application-so-that-the-operations-team-has-the-tools-they-need"></a><span data-ttu-id="d2bca-104">合理设计应用程序，使运营团队获得所需的工具。</span><span class="sxs-lookup"><span data-stu-id="d2bca-104">Design an application so that the operations team has the tools they need</span></span>

<span data-ttu-id="d2bca-105">云已经显著改变了运营团队的角色。</span><span class="sxs-lookup"><span data-stu-id="d2bca-105">The cloud has dramatically changed the role of the operations team.</span></span> <span data-ttu-id="d2bca-106">他们不再负责管理托管应用程序的硬件和基础结构。</span><span class="sxs-lookup"><span data-stu-id="d2bca-106">They are no longer responsible for managing the hardware and infrastructure that hosts the application.</span></span>  <span data-ttu-id="d2bca-107">即便如此，运营仍是成功运行云应用程序的关键环节。</span><span class="sxs-lookup"><span data-stu-id="d2bca-107">That said, operations is still a critical part of running a successful cloud application.</span></span> <span data-ttu-id="d2bca-108">运营团队的一些重要功能包括：</span><span class="sxs-lookup"><span data-stu-id="d2bca-108">Some of the important functions of the operations team include:</span></span>

- <span data-ttu-id="d2bca-109">部署</span><span class="sxs-lookup"><span data-stu-id="d2bca-109">Deployment</span></span>
- <span data-ttu-id="d2bca-110">监视</span><span class="sxs-lookup"><span data-stu-id="d2bca-110">Monitoring</span></span>
- <span data-ttu-id="d2bca-111">升级</span><span class="sxs-lookup"><span data-stu-id="d2bca-111">Escalation</span></span>
- <span data-ttu-id="d2bca-112">事件响应</span><span class="sxs-lookup"><span data-stu-id="d2bca-112">Incident response</span></span>
- <span data-ttu-id="d2bca-113">安全审核</span><span class="sxs-lookup"><span data-stu-id="d2bca-113">Security auditing</span></span>

<span data-ttu-id="d2bca-114">可靠的记录和跟踪对于云应用程序非常重要。</span><span class="sxs-lookup"><span data-stu-id="d2bca-114">Robust logging and tracing are particularly important in cloud applications.</span></span> <span data-ttu-id="d2bca-115">邀请运营团队参与设计和规划，确保应用程序向他们提供了成功所需的数据和见解。</span><span class="sxs-lookup"><span data-stu-id="d2bca-115">Involve the operations team in design and planning, to ensure the application gives them the data and insight thay need to be successful.</span></span>  <!-- to do: Link to DevOps checklist -->

## <a name="recommendations"></a><span data-ttu-id="d2bca-116">建议</span><span class="sxs-lookup"><span data-stu-id="d2bca-116">Recommendations</span></span>

<span data-ttu-id="d2bca-117">**确保可以观测到所有内容**。</span><span class="sxs-lookup"><span data-stu-id="d2bca-117">**Make all things observable**.</span></span> <span data-ttu-id="d2bca-118">部署和运行解决方案后，记录和跟踪的结果将是对系统的主要见解。</span><span class="sxs-lookup"><span data-stu-id="d2bca-118">Once a solution is deployed and running, logs and traces are your primary insight into the system.</span></span> <span data-ttu-id="d2bca-119">“跟踪”就是记录系统中的路径，有助于找出瓶颈、性能问题和故障点。</span><span class="sxs-lookup"><span data-stu-id="d2bca-119">*Tracing* records a path through the system, and is useful to pinpoint bottlenecks, performance issues, and failure points.</span></span> <span data-ttu-id="d2bca-120">“记录”就是捕获单个事件，例如应用程序状态更改、错误和异常。</span><span class="sxs-lookup"><span data-stu-id="d2bca-120">*Logging* captures individual events such as application state changes, errors, and exceptions.</span></span> <span data-ttu-id="d2bca-121">请在生产时记录，否则将在最需要它的时候缺乏见解。</span><span class="sxs-lookup"><span data-stu-id="d2bca-121">Log in production, or else you lose insight at the very times when you need it the most.</span></span>

<span data-ttu-id="d2bca-122">**用于监视的手段**。</span><span class="sxs-lookup"><span data-stu-id="d2bca-122">**Instrument for monitoring**.</span></span> <span data-ttu-id="d2bca-123">通过监视可了解应用程序在可用性、性能和系统运行状况方面的表现是否良好。</span><span class="sxs-lookup"><span data-stu-id="d2bca-123">Monitoring gives insight into how well (or poorly) an application is performing, in terms of availability, performance, and system health.</span></span> <span data-ttu-id="d2bca-124">例如，监视可指示是否符合 SLA。</span><span class="sxs-lookup"><span data-stu-id="d2bca-124">For example, monitoring tells you whether you are meeting your SLA.</span></span> <span data-ttu-id="d2bca-125">在系统的常规运行期间都会进行监视。</span><span class="sxs-lookup"><span data-stu-id="d2bca-125">Monitoring happens during the normal operation of the system.</span></span> <span data-ttu-id="d2bca-126">应尽可能实时监视，以便操作人员可以迅速对问题作出反应。</span><span class="sxs-lookup"><span data-stu-id="d2bca-126">It should be as close to real-time as possible, so that the operations staff can react to issues quickly.</span></span> <span data-ttu-id="d2bca-127">理想情况下，监视可在导致严重故障之前，帮助避免问题的出现。</span><span class="sxs-lookup"><span data-stu-id="d2bca-127">Ideally, monitoring can help avert problems before they lead to a critical failure.</span></span> <span data-ttu-id="d2bca-128">有关详细信息，请参阅[监视和诊断][monitoring]。</span><span class="sxs-lookup"><span data-stu-id="d2bca-128">For more information, see [Monitoring and diagnostics][monitoring].</span></span>

<span data-ttu-id="d2bca-129">**用于根本原因分析的手段**。</span><span class="sxs-lookup"><span data-stu-id="d2bca-129">**Instrument for root cause analysis**.</span></span> <span data-ttu-id="d2bca-130">根本原因分析是查找故障的根本原因的过程。</span><span class="sxs-lookup"><span data-stu-id="d2bca-130">Root cause analysis is the process of finding the underlying cause of failures.</span></span> <span data-ttu-id="d2bca-131">它发生在故障出现后。</span><span class="sxs-lookup"><span data-stu-id="d2bca-131">It occurs after a failure has already happened.</span></span> 

<span data-ttu-id="d2bca-132">**使用分布式跟踪**。</span><span class="sxs-lookup"><span data-stu-id="d2bca-132">**Use distributed tracing**.</span></span> <span data-ttu-id="d2bca-133">使用专为并发、异步和云规模设计的分布式跟踪系统。</span><span class="sxs-lookup"><span data-stu-id="d2bca-133">Use a distributed tracing system that is designed for concurrency, asynchrony, and cloud scale.</span></span> <span data-ttu-id="d2bca-134">跟踪应包括跨服务边界的关联 ID。</span><span class="sxs-lookup"><span data-stu-id="d2bca-134">Traces should include a correlation ID that flows across service boundaries.</span></span> <span data-ttu-id="d2bca-135">单个操作可能涉及对多个应用程序服务的调用。</span><span class="sxs-lookup"><span data-stu-id="d2bca-135">A single operation may involve calls to multiple application services.</span></span> <span data-ttu-id="d2bca-136">如果操作失败，关联 ID 可帮助找出失败的原因。</span><span class="sxs-lookup"><span data-stu-id="d2bca-136">If an operation fails, the correlation ID helps to pinpoint the cause of the failure.</span></span> 

<span data-ttu-id="d2bca-137">**标准化日志和指标**。</span><span class="sxs-lookup"><span data-stu-id="d2bca-137">**Standardize logs and metrics**.</span></span> <span data-ttu-id="d2bca-138">运营团队需要在解决方案中聚合来自各种服务的日志。</span><span class="sxs-lookup"><span data-stu-id="d2bca-138">The operations team will need to aggregate logs from across the various services in your solution.</span></span> <span data-ttu-id="d2bca-139">如果每种服务使用各自的日志格式，将很难或不可能从中获取有用的信息。</span><span class="sxs-lookup"><span data-stu-id="d2bca-139">If every service uses its own logging format, it becomes difficult or impossible to get useful information from them.</span></span> <span data-ttu-id="d2bca-140">定义包括关联 ID、事件名称、发送者 IP 地址等字段的常见架构。</span><span class="sxs-lookup"><span data-stu-id="d2bca-140">Define a common schema that includes fields such as correlation ID, event name, IP address of the sender, and so forth.</span></span> <span data-ttu-id="d2bca-141">单个服务可以派生继承基础架构并包含附加字段的自定义架构。</span><span class="sxs-lookup"><span data-stu-id="d2bca-141">Individual services can derive custom schemas that inherit the base schema, and contain additional fields.</span></span>

<span data-ttu-id="d2bca-142">**自动化管理任务**，包括预配、部署和监视。</span><span class="sxs-lookup"><span data-stu-id="d2bca-142">**Automate management tasks**, including provisioning, deployment, and monitoring.</span></span> <span data-ttu-id="d2bca-143">自动化任务具有可重复性并且可以减少人为错误。</span><span class="sxs-lookup"><span data-stu-id="d2bca-143">Automating a task makes it repeatable and less prone to human errors.</span></span> 

<span data-ttu-id="d2bca-144">**将配置视为代码**。</span><span class="sxs-lookup"><span data-stu-id="d2bca-144">**Treat configuration as code**.</span></span> <span data-ttu-id="d2bca-145">通过将配置文件签入版本控制系统，可以对更改进行跟踪和版本控制，并在需要时回滚。</span><span class="sxs-lookup"><span data-stu-id="d2bca-145">Check configuration files into a version control system, so that you can track and version your changes, and roll back if needed.</span></span> 


<!-- links -->

[monitoring]: ../../best-practices/monitoring.md


