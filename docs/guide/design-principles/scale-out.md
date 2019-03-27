---
title: 扩大式设计
titleSuffix: Azure Application Architecture Guide
description: 云应用程序应设计为适合水平缩放。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 32ea1e7dc732c819783ad2fc06dbcffd75685d23
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243078"
---
# <a name="design-to-scale-out"></a><span data-ttu-id="df7df-103">扩大式设计</span><span class="sxs-lookup"><span data-stu-id="df7df-103">Design to scale out</span></span>

## <a name="design-your-application-so-that-it-can-scale-horizontally"></a><span data-ttu-id="df7df-104">设计应用程序，使其可进行水平缩放</span><span class="sxs-lookup"><span data-stu-id="df7df-104">Design your application so that it can scale horizontally</span></span>

<span data-ttu-id="df7df-105">云的主要优点是可以弹性缩放 &mdash; 能够根据需要使用容量，在负载增加时扩大，在不需要额外容量时缩小。</span><span class="sxs-lookup"><span data-stu-id="df7df-105">A primary advantage of the cloud is elastic scaling &mdash; the ability to use as much capacity as you need, scaling out as load increases, and scaling in when the extra capacity is not needed.</span></span> <span data-ttu-id="df7df-106">设计应用程序，使其能够扩大，根据需要添加或删除新实例。</span><span class="sxs-lookup"><span data-stu-id="df7df-106">Design your application so that it can scale horizontally, adding or removing new instances as demand requires.</span></span>

## <a name="recommendations"></a><span data-ttu-id="df7df-107">建议</span><span class="sxs-lookup"><span data-stu-id="df7df-107">Recommendations</span></span>

<span data-ttu-id="df7df-108">**避免实例粘性**。</span><span class="sxs-lookup"><span data-stu-id="df7df-108">**Avoid instance stickiness**.</span></span> <span data-ttu-id="df7df-109">在来自相同客户端的请求始终路由至同一台服务器时，才会出现粘性或会话相关性。</span><span class="sxs-lookup"><span data-stu-id="df7df-109">Stickiness, or *session affinity*, is when requests from the same client are always routed to the same server.</span></span> <span data-ttu-id="df7df-110">粘性限制了应用程序扩大的能力。例如，来自高容量用户的流量不会跨实例分布。</span><span class="sxs-lookup"><span data-stu-id="df7df-110">Stickiness limits the application's ability to scale out. For example, traffic from a high-volume user will not be distributed across instances.</span></span> <span data-ttu-id="df7df-111">粘性的成因包括在内存中存储会话状态以及使用特定于计算的密钥加密。</span><span class="sxs-lookup"><span data-stu-id="df7df-111">Causes of stickiness include storing session state in memory, and using machine-specific keys for encryption.</span></span> <span data-ttu-id="df7df-112">请确保任何实例都可处理任何请求。</span><span class="sxs-lookup"><span data-stu-id="df7df-112">Make sure that any instance can handle any request.</span></span>

<span data-ttu-id="df7df-113">**识别瓶颈**。</span><span class="sxs-lookup"><span data-stu-id="df7df-113">**Identify bottlenecks**.</span></span> <span data-ttu-id="df7df-114">扩大并不是解决每个性能问题的万能方式。</span><span class="sxs-lookup"><span data-stu-id="df7df-114">Scaling out isn't a magic fix for every performance issue.</span></span> <span data-ttu-id="df7df-115">例如，如果后端数据库是瓶颈，添加再多 Web 服务器也于事无补。</span><span class="sxs-lookup"><span data-stu-id="df7df-115">For example, if your backend database is the bottleneck, it won't help to add more web servers.</span></span> <span data-ttu-id="df7df-116">首先识别并解析系统中的瓶颈，然后针对该问题引发更多实例。</span><span class="sxs-lookup"><span data-stu-id="df7df-116">Identify and resolve the bottlenecks in the system first, before throwing more instances at the problem.</span></span> <span data-ttu-id="df7df-117">系统中有状态的部分最有可能引起瓶颈问题。</span><span class="sxs-lookup"><span data-stu-id="df7df-117">Stateful parts of the system are the most likely cause of bottlenecks.</span></span>

<span data-ttu-id="df7df-118">**通过可伸缩性要求分解工作负荷**</span><span class="sxs-lookup"><span data-stu-id="df7df-118">**Decompose workloads by scalability requirements.**</span></span>  <span data-ttu-id="df7df-119">应用程序通常由多个工作负荷组成，它们的缩放要求各不相同。</span><span class="sxs-lookup"><span data-stu-id="df7df-119">Applications often consist of multiple workloads, with different requirements for scaling.</span></span> <span data-ttu-id="df7df-120">例如，应用程序可能有面向公众的网站和单独的管理网站。</span><span class="sxs-lookup"><span data-stu-id="df7df-120">For example, an application might have a public-facing site and a separate administration site.</span></span> <span data-ttu-id="df7df-121">公众网站可能会遇到流量突然激增的情况，而管理网站的负荷更小，且更具可预测性。</span><span class="sxs-lookup"><span data-stu-id="df7df-121">The public site may experience sudden surges in traffic, while the administration site has a smaller, more predictable load.</span></span>

<span data-ttu-id="df7df-122">**卸载资源密集型任务**</span><span class="sxs-lookup"><span data-stu-id="df7df-122">**Offload resource-intensive tasks.**</span></span> <span data-ttu-id="df7df-123">需要大量的 CPU 或 I/O 资源的任务应尽可能移动到[后台作业][background-jobs]以减少处理用户请求的前端上的负载。</span><span class="sxs-lookup"><span data-stu-id="df7df-123">Tasks that require a lot of CPU or I/O resources should be moved to [background jobs][background-jobs] when possible, to minimize the load on the front end that is handling user requests.</span></span>

<span data-ttu-id="df7df-124">**使用内置自动缩放功能**。</span><span class="sxs-lookup"><span data-stu-id="df7df-124">**Use built-in autoscaling features**.</span></span> <span data-ttu-id="df7df-125">许多 Azure 计算服务有内置的自动缩放支持。</span><span class="sxs-lookup"><span data-stu-id="df7df-125">Many Azure compute services have built-in support for autoscaling.</span></span> <span data-ttu-id="df7df-126">如果应用程序具有可预测的常规工作负荷，则可按计划扩大。</span><span class="sxs-lookup"><span data-stu-id="df7df-126">If the application has a predictable, regular workload, scale out on a schedule.</span></span> <span data-ttu-id="df7df-127">例如，在营业时间期间扩大。</span><span class="sxs-lookup"><span data-stu-id="df7df-127">For example, scale out during business hours.</span></span> <span data-ttu-id="df7df-128">否则，如果工作负荷不可预测，则可使用 CPU 或请求队列长度等性能指标来触发自动缩放。</span><span class="sxs-lookup"><span data-stu-id="df7df-128">Otherwise, if the workload is not predictable, use performance metrics such as CPU or request queue length to trigger autoscaling.</span></span> <span data-ttu-id="df7df-129">有关自动缩放最佳做法的信息，请参阅[自动缩放][autoscaling]。</span><span class="sxs-lookup"><span data-stu-id="df7df-129">For autoscaling best practices, see [Autoscaling][autoscaling].</span></span>

<span data-ttu-id="df7df-130">**请为关键工作负荷考虑自动缩放**。</span><span class="sxs-lookup"><span data-stu-id="df7df-130">**Consider aggressive autoscaling for critical workloads**.</span></span> <span data-ttu-id="df7df-131">对于关键工作负荷，你希望供应一直超过需求。</span><span class="sxs-lookup"><span data-stu-id="df7df-131">For critical workloads, you want to keep ahead of demand.</span></span> <span data-ttu-id="df7df-132">在负荷加重的情况下，最好快速增加新实例以处理额外的流量，然后再逐渐缩减。</span><span class="sxs-lookup"><span data-stu-id="df7df-132">It's better to add new instances quickly under heavy load to handle the additional traffic, and then gradually scale back.</span></span>

<span data-ttu-id="df7df-133">**缩小式设计**。</span><span class="sxs-lookup"><span data-stu-id="df7df-133">**Design for scale in**.</span></span>  <span data-ttu-id="df7df-134">请记住，使用弹性缩放时，应用程序会有缩小时期，此时实例会被移除。</span><span class="sxs-lookup"><span data-stu-id="df7df-134">Remember that with elastic scale, the application will have periods of scale in, when instances get removed.</span></span> <span data-ttu-id="df7df-135">应用程序必须妥善处理正在移除的实例。</span><span class="sxs-lookup"><span data-stu-id="df7df-135">The application must gracefully handle instances being removed.</span></span> <span data-ttu-id="df7df-136">以下是一些处理缩小功能的方法：</span><span class="sxs-lookup"><span data-stu-id="df7df-136">Here are some ways to handle scalein:</span></span>

- <span data-ttu-id="df7df-137">侦听关闭事件（可用时），然后全部关闭。</span><span class="sxs-lookup"><span data-stu-id="df7df-137">Listen for shutdown events (when available) and shut down cleanly.</span></span>
- <span data-ttu-id="df7df-138">服务的客户端/使用者应支持暂时性故障处理和重试。</span><span class="sxs-lookup"><span data-stu-id="df7df-138">Clients/consumers of a service should support transient fault handling and retry.</span></span>
- <span data-ttu-id="df7df-139">对于运行时间长的任务，请考虑使用检查点，或者[管道和筛选器][pipes-filters-pattern]模式分解工作。</span><span class="sxs-lookup"><span data-stu-id="df7df-139">For long-running tasks, consider breaking up the work, using checkpoints or the [Pipes and Filters][pipes-filters-pattern] pattern.</span></span>
- <span data-ttu-id="df7df-140">如果实例是在处理过程中移除的，请将工作项放在队列中，以便另一个实例可以接收工作。</span><span class="sxs-lookup"><span data-stu-id="df7df-140">Put work items on a queue so that another instance can pick up the work, if an instance is removed in the middle of processing.</span></span>

<!-- links -->

[autoscaling]: ../../best-practices/auto-scaling.md
[background-jobs]: ../../best-practices/background-jobs.md
[pipes-filters-pattern]: ../../patterns/pipes-and-filters.md
