---
title: 定义可复原的 Azure 应用程序的要求
description: 收集 Azure 应用程序的可用性和可恢复性要求的概述
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: 2af2ce4200c285abfda1395862d21efc3c17e577
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646491"
---
# <a name="developing-requirements-for-resilient-azure-applications"></a><span data-ttu-id="18a44-103">开发可复原的 Azure 应用程序的要求</span><span class="sxs-lookup"><span data-stu-id="18a44-103">Developing requirements for resilient Azure applications</span></span>

<span data-ttu-id="18a44-104">构建*复原*（从故障中恢复） 和*可用性*（而无需大量的停机时间正常运行） 到你的应用开始与收集要求。</span><span class="sxs-lookup"><span data-stu-id="18a44-104">Building *resiliency* (recovering from failures) and *availability* (running in a healthy state without significant downtime) into your apps begins with gathering requirements.</span></span> <span data-ttu-id="18a44-105">例如，长的停机时间是可接受？</span><span class="sxs-lookup"><span data-stu-id="18a44-105">For example, how much downtime is acceptable?</span></span> <span data-ttu-id="18a44-106">潜在的停机时间价格业务是多少？</span><span class="sxs-lookup"><span data-stu-id="18a44-106">How much does potential downtime cost your business?</span></span> <span data-ttu-id="18a44-107">客户的可用性要求是什么？</span><span class="sxs-lookup"><span data-stu-id="18a44-107">What are your customer's availability requirements?</span></span> <span data-ttu-id="18a44-108">多少投入使你的应用程序具有高可用性？</span><span class="sxs-lookup"><span data-stu-id="18a44-108">How much do you invest in making your application highly available?</span></span> <span data-ttu-id="18a44-109">什么是风险和成本？</span><span class="sxs-lookup"><span data-stu-id="18a44-109">What is the risk versus the cost?</span></span>

## <a name="identify-distinct-workloads"></a><span data-ttu-id="18a44-110">标识不同的工作负载</span><span class="sxs-lookup"><span data-stu-id="18a44-110">Identify distinct workloads</span></span>

<span data-ttu-id="18a44-111">云解决方案通常包括多个应用程序的*工作负荷*。</span><span class="sxs-lookup"><span data-stu-id="18a44-111">Cloud solutions typically consist of multiple application *workloads*.</span></span> <span data-ttu-id="18a44-112">工作负荷是不同的功能或逻辑上独立于其他任务根据业务逻辑和数据存储需求的任务。</span><span class="sxs-lookup"><span data-stu-id="18a44-112">A workload is a distinct capability or task that is logically separated from other tasks in terms of business logic and data storage requirements.</span></span> <span data-ttu-id="18a44-113">例如，电子商务应用可能具有以下工作负荷：</span><span class="sxs-lookup"><span data-stu-id="18a44-113">For example, an e-commerce app might have the following workloads:</span></span>

- <span data-ttu-id="18a44-114">浏览和搜索产品目录。</span><span class="sxs-lookup"><span data-stu-id="18a44-114">Browse and search a product catalog.</span></span>
- <span data-ttu-id="18a44-115">创建和跟踪订单。</span><span class="sxs-lookup"><span data-stu-id="18a44-115">Create and track orders.</span></span>
- <span data-ttu-id="18a44-116">查看推荐商品。</span><span class="sxs-lookup"><span data-stu-id="18a44-116">View recommendations.</span></span>

<span data-ttu-id="18a44-117">每个工作负荷具有不同的可用性、 可伸缩性、 数据一致性和灾难恢复要求。</span><span class="sxs-lookup"><span data-stu-id="18a44-117">Each workload has different requirements for availability, scalability, data consistency, and disaster recovery.</span></span> <span data-ttu-id="18a44-118">通过平衡成本与每个工作负载的风险使您的业务决策。</span><span class="sxs-lookup"><span data-stu-id="18a44-118">Make your business decisions by balancing cost versus risk for each workload.</span></span>

<span data-ttu-id="18a44-119">此外将工作负荷分解服务级别目标。</span><span class="sxs-lookup"><span data-stu-id="18a44-119">Also decompose workloads by service-level objective.</span></span> <span data-ttu-id="18a44-120">如果服务包含的关键并不太重要的工作负荷，以不同的方式对其进行管理，并指定服务功能和要满足其可用性要求所需的实例数。</span><span class="sxs-lookup"><span data-stu-id="18a44-120">If a service is composed of critical and less-critical workloads, manage them differently and specify the service features and number of instances needed to meet their availability requirements.</span></span>

## <a name="plan-for-usage-patterns"></a><span data-ttu-id="18a44-121">使用模式的计划</span><span class="sxs-lookup"><span data-stu-id="18a44-121">Plan for usage patterns</span></span>

<span data-ttu-id="18a44-122">在关键和非关键时段标识要求之间的差异。</span><span class="sxs-lookup"><span data-stu-id="18a44-122">Identify differences in requirements during critical and non-critical periods.</span></span> <span data-ttu-id="18a44-123">系统是否必须在某些关键时段保持可用？</span><span class="sxs-lookup"><span data-stu-id="18a44-123">Are there certain critical periods when the system must be available?</span></span> <span data-ttu-id="18a44-124">例如，税务申报应用程序不能在申报截止时间期间失败和视频流服务不应在实时事件滞后。</span><span class="sxs-lookup"><span data-stu-id="18a44-124">For example, a tax-filing application can't fail during a filing deadline and a video streaming service shouldn't lag during a live event.</span></span> <span data-ttu-id="18a44-125">在这些情况下，权衡成本与风险。</span><span class="sxs-lookup"><span data-stu-id="18a44-125">In these situations, weigh the cost against the risk.</span></span>

- <span data-ttu-id="18a44-126">若要确保正常运行时间并满足服务级别协议 (Sla)，在关键时段，计划冗余跨多个区域以防出现故障时，即使它的成本的详细信息。</span><span class="sxs-lookup"><span data-stu-id="18a44-126">To ensure uptime and meet service-level agreements (SLAs) in critical periods, plan redundancy across several regions in case one fails, even if it costs more.</span></span>
- <span data-ttu-id="18a44-127">相反，在非关键时段，最小化成本在单个区域中运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="18a44-127">Conversely, during non-critical periods, run your application in a single region to minimize costs.</span></span>
- <span data-ttu-id="18a44-128">在某些情况下，可以通过使用具有基于使用量的计费的新式无服务器技术降低其他费用。</span><span class="sxs-lookup"><span data-stu-id="18a44-128">In some cases, you can mitigate additional expenses by using modern serverless techniques that have consumption-based billing.</span></span>

## <a name="establish-availability-and-recovery-metrics"></a><span data-ttu-id="18a44-129">建立可用性和恢复的度量值</span><span class="sxs-lookup"><span data-stu-id="18a44-129">Establish availability and recovery metrics</span></span>

<span data-ttu-id="18a44-130">创建两个集的度量值要求过程的一部分的基线号。</span><span class="sxs-lookup"><span data-stu-id="18a44-130">Create baseline numbers for two sets of metrics as part of the requirements process.</span></span> <span data-ttu-id="18a44-131">第一组可帮助你确定将冗余添加到云服务的位置，并为客户提供的服务级别协议。</span><span class="sxs-lookup"><span data-stu-id="18a44-131">The first set helps you determine where to add redundancy to cloud services and which SLAs to provide to customers.</span></span> <span data-ttu-id="18a44-132">第二个设置可帮助规划灾难恢复。</span><span class="sxs-lookup"><span data-stu-id="18a44-132">The second set helps you plan your disaster recovery.</span></span>

### <a name="availability-metrics"></a><span data-ttu-id="18a44-133">可用性指标</span><span class="sxs-lookup"><span data-stu-id="18a44-133">Availability metrics</span></span>

<span data-ttu-id="18a44-134">这些度量值用于计划冗余和确定客户的 Sla。</span><span class="sxs-lookup"><span data-stu-id="18a44-134">Use these measures to plan for redundancy and determine customer SLAs.</span></span>

- <span data-ttu-id="18a44-135">**平均恢复 (MTTR) 时间**是平均值所需的时间发生故障后还原一个组件。</span><span class="sxs-lookup"><span data-stu-id="18a44-135">**Mean time to recover (MTTR)** is the average time it takes to restore a component after a failure.</span></span>
- <span data-ttu-id="18a44-136">**平均无故障 (MTBF) 时间**是如何完成长时间组件可以合理期望到最后一个之间中断。</span><span class="sxs-lookup"><span data-stu-id="18a44-136">**Mean time between failures (MTBF)** is the how long a component can reasonably expect to last between outages.</span></span>

### <a name="recovery-metrics"></a><span data-ttu-id="18a44-137">恢复指标</span><span class="sxs-lookup"><span data-stu-id="18a44-137">Recovery metrics</span></span>

<span data-ttu-id="18a44-138">获得这些值的执行风险评估，并确保了解成本和停机时间和数据丢失的风险。</span><span class="sxs-lookup"><span data-stu-id="18a44-138">Derive these values by conducting a risk assessment, and make sure you understand the cost and risk of downtime and data loss.</span></span> <span data-ttu-id="18a44-139">这是系统的非功能性要求，应符合业务要求。</span><span class="sxs-lookup"><span data-stu-id="18a44-139">These are nonfunctional requirements of a system and should be dictated by business requirements.</span></span>

- <span data-ttu-id="18a44-140">**恢复时间目标 (RTO)** 是应用程序是否在事件发生后不可用的最长可接受时间。</span><span class="sxs-lookup"><span data-stu-id="18a44-140">**Recovery time objective (RTO)** is the maximum acceptable time an application is unavailable after an incident.</span></span>
- <span data-ttu-id="18a44-141">**恢复点目标 (RPO)** 是在发生灾难期间是可接受的数据丢失的最大持续时间。</span><span class="sxs-lookup"><span data-stu-id="18a44-141">**Recovery point objective (RPO)** is the maximum duration of data loss that's acceptable during a disaster.</span></span>

<span data-ttu-id="18a44-142">如果 MTTR 值*任何*高度可用的安装程序中的关键组件超过 RTO 的系统，在系统中的失败可能会导致不可接受的业务中断。</span><span class="sxs-lookup"><span data-stu-id="18a44-142">If the MTTR value of *any* critical component in a highly available setup exceeds the system RTO, a failure in the system might cause an unacceptable business disruption.</span></span> <span data-ttu-id="18a44-143">也就是说，不能还原系统中定义的 RTO。</span><span class="sxs-lookup"><span data-stu-id="18a44-143">That is, you can't restore the system within the defined RTO.</span></span>

## <a name="determine-workload-availability-targets"></a><span data-ttu-id="18a44-144">确定工作负荷的可用性目标</span><span class="sxs-lookup"><span data-stu-id="18a44-144">Determine workload availability targets</span></span>

<span data-ttu-id="18a44-145">在解决方案中定义自己的目标 Sla 为每个工作负荷，以便可以确定体系结构是否满足业务要求。</span><span class="sxs-lookup"><span data-stu-id="18a44-145">Define your own target SLAs for each workload in your solution so you can determine whether the architecture meets the business requirements.</span></span>

### <a name="consider-cost-and-complexity"></a><span data-ttu-id="18a44-146">请考虑成本和复杂性</span><span class="sxs-lookup"><span data-stu-id="18a44-146">Consider cost and complexity</span></span>

<span data-ttu-id="18a44-147">其他条件相同，更高的可用性是更好。</span><span class="sxs-lookup"><span data-stu-id="18a44-147">Everything else being equal, higher availability is better.</span></span> <span data-ttu-id="18a44-148">但是，如果追求更多九，增加的成本和复杂性。</span><span class="sxs-lookup"><span data-stu-id="18a44-148">But as you strive for more nines, the cost and complexity grow.</span></span> <span data-ttu-id="18a44-149">99.99%的运行时间相当于大约五分钟内的每月总停机时间。</span><span class="sxs-lookup"><span data-stu-id="18a44-149">An uptime of 99.99% translates to about five minutes of total downtime per month.</span></span> <span data-ttu-id="18a44-150">是否值得提高复杂性和成本来实现五个九？</span><span class="sxs-lookup"><span data-stu-id="18a44-150">Is it worth the additional complexity and cost to reach five nines?</span></span> <span data-ttu-id="18a44-151">答案取决于业务要求。</span><span class="sxs-lookup"><span data-stu-id="18a44-151">The answer depends on the business requirements.</span></span>

<span data-ttu-id="18a44-152">下面是定义 SLA 时需要考虑的其他一些因素：</span><span class="sxs-lookup"><span data-stu-id="18a44-152">Here are some other considerations when defining an SLA:</span></span>

- <span data-ttu-id="18a44-153">若要实现 4 个 9 （99.99%)，不能依赖于人工干预来从故障中恢复。</span><span class="sxs-lookup"><span data-stu-id="18a44-153">To achieve four nines (99.99%), you can't rely on manual intervention to recover from failures.</span></span> <span data-ttu-id="18a44-154">应用程序必须能够自我诊断和自我修复。</span><span class="sxs-lookup"><span data-stu-id="18a44-154">The application must be self-diagnosing and self-healing.</span></span>
- <span data-ttu-id="18a44-155">超过 4 个 9，却很难以足够快的速度检测服务中断，以便满足 SLA。</span><span class="sxs-lookup"><span data-stu-id="18a44-155">Beyond four nines, it's challenging to detect outages quickly enough to meet the SLA.</span></span>
- <span data-ttu-id="18a44-156">请考虑衡量 SLA 时所依据的时限。</span><span class="sxs-lookup"><span data-stu-id="18a44-156">Think about the time window that your SLA is measured against.</span></span> <span data-ttu-id="18a44-157">该时限越小，容限就越严格。</span><span class="sxs-lookup"><span data-stu-id="18a44-157">The smaller the window, the tighter the tolerances.</span></span> <span data-ttu-id="18a44-158">它没有意义定义根据每小时或每日运行时间 SLA。</span><span class="sxs-lookup"><span data-stu-id="18a44-158">It doesn't make sense to define your SLA in terms of hourly or daily uptime.</span></span>
- <span data-ttu-id="18a44-159">考虑 MTBF 和 MTTR 度量。</span><span class="sxs-lookup"><span data-stu-id="18a44-159">Consider the MTBF and MTTR measurements.</span></span> <span data-ttu-id="18a44-160">更高版本 SLA，频率越低服务可以向下去服务必须恢复更快。</span><span class="sxs-lookup"><span data-stu-id="18a44-160">The higher your SLA, the less frequently the service can go down and the quicker the service must recover.</span></span>
- <span data-ttu-id="18a44-161">获取来自客户的应用程序中，每个部分的可用性目标的协议，并记下此信息。</span><span class="sxs-lookup"><span data-stu-id="18a44-161">Get agreement from your customers for the availability targets of each piece of your application, and document it.</span></span> <span data-ttu-id="18a44-162">否则，您的设计可能不符合客户期望。</span><span class="sxs-lookup"><span data-stu-id="18a44-162">Otherwise, your design may not meet the customers' expectations.</span></span>

### <a name="identify-dependencies"></a><span data-ttu-id="18a44-163">标识依赖项</span><span class="sxs-lookup"><span data-stu-id="18a44-163">Identify dependencies</span></span>

<span data-ttu-id="18a44-164">执行依赖项映射练习来标识内部和外部依赖关系。</span><span class="sxs-lookup"><span data-stu-id="18a44-164">Perform dependency-mapping exercises to identify internal and external dependencies.</span></span> <span data-ttu-id="18a44-165">示例包括与安全或标识，如 Active Directory 或第三方服务，例如的支付提供商相关的依赖项，或消息传送服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="18a44-165">Examples include dependencies relating to security or identity, such as Active Directory, or third-party services such as a payment provider or e-mail messaging service.</span></span>

<span data-ttu-id="18a44-166">应特别注意到可能是单点故障或导致瓶颈的外部依赖项。</span><span class="sxs-lookup"><span data-stu-id="18a44-166">Pay particular attention to external dependencies that might be a single point of failure or cause bottlenecks.</span></span> <span data-ttu-id="18a44-167">如果工作负荷需要 99.99%运行时间，但依赖于具有 99.9%的 SLA 的服务，该服务不能是系统的单一故障点。</span><span class="sxs-lookup"><span data-stu-id="18a44-167">If a workload requires 99.99% uptime but depends on a service with a 99.9% SLA, that service can't be a single point of failure in the system.</span></span> <span data-ttu-id="18a44-168">一种补救措施是建立回退路径以防服务发生故障时。</span><span class="sxs-lookup"><span data-stu-id="18a44-168">One remedy is to have a fallback path in case the service fails.</span></span> <span data-ttu-id="18a44-169">或者，采取其他措施来从该服务中的失败中恢复。</span><span class="sxs-lookup"><span data-stu-id="18a44-169">Alternatively, take other measures to recover from a failure in that service.</span></span>

<span data-ttu-id="18a44-170">下表显示了各个 SLA 级别的潜在累积停机时间。</span><span class="sxs-lookup"><span data-stu-id="18a44-170">The following table shows the potential cumulative downtime for various SLA levels.</span></span>

| <span data-ttu-id="18a44-171">**SLA**</span><span class="sxs-lookup"><span data-stu-id="18a44-171">**SLA**</span></span> | <span data-ttu-id="18a44-172">**每周故障时间**</span><span class="sxs-lookup"><span data-stu-id="18a44-172">**Downtime per week**</span></span> | <span data-ttu-id="18a44-173">**每月停机时间**</span><span class="sxs-lookup"><span data-stu-id="18a44-173">**Downtime per month**</span></span> | <span data-ttu-id="18a44-174">**每年的停机时间**</span><span class="sxs-lookup"><span data-stu-id="18a44-174">**Downtime per year**</span></span> |
|---------|-----------------------|------------------------|-----------------------|
| <span data-ttu-id="18a44-175">99%</span><span class="sxs-lookup"><span data-stu-id="18a44-175">99%</span></span>     | <span data-ttu-id="18a44-176">1.68 小时</span><span class="sxs-lookup"><span data-stu-id="18a44-176">1.68 hours</span></span>            | <span data-ttu-id="18a44-177">7.2 小时</span><span class="sxs-lookup"><span data-stu-id="18a44-177">7.2 hours</span></span>              | <span data-ttu-id="18a44-178">3.65 天</span><span class="sxs-lookup"><span data-stu-id="18a44-178">3.65 days</span></span>             |
| <span data-ttu-id="18a44-179">99.9%</span><span class="sxs-lookup"><span data-stu-id="18a44-179">99.9%</span></span>   | <span data-ttu-id="18a44-180">10.1 分钟</span><span class="sxs-lookup"><span data-stu-id="18a44-180">10.1 minutes</span></span>          | <span data-ttu-id="18a44-181">43.2 分钟</span><span class="sxs-lookup"><span data-stu-id="18a44-181">43.2 minutes</span></span>           | <span data-ttu-id="18a44-182">8.76 小时</span><span class="sxs-lookup"><span data-stu-id="18a44-182">8.76 hours</span></span>            |
| <span data-ttu-id="18a44-183">99.95%</span><span class="sxs-lookup"><span data-stu-id="18a44-183">99.95%</span></span>  | <span data-ttu-id="18a44-184">5 分钟</span><span class="sxs-lookup"><span data-stu-id="18a44-184">5 minutes</span></span>             | <span data-ttu-id="18a44-185">21.6 分钟</span><span class="sxs-lookup"><span data-stu-id="18a44-185">21.6 minutes</span></span>           | <span data-ttu-id="18a44-186">4.38 小时</span><span class="sxs-lookup"><span data-stu-id="18a44-186">4.38 hours</span></span>            |
| <span data-ttu-id="18a44-187">99.99%</span><span class="sxs-lookup"><span data-stu-id="18a44-187">99.99%</span></span>  | <span data-ttu-id="18a44-188">1.01 分钟</span><span class="sxs-lookup"><span data-stu-id="18a44-188">1.01 minutes</span></span>          | <span data-ttu-id="18a44-189">4.32 分钟</span><span class="sxs-lookup"><span data-stu-id="18a44-189">4.32 minutes</span></span>           | <span data-ttu-id="18a44-190">52.56 分钟</span><span class="sxs-lookup"><span data-stu-id="18a44-190">52.56 minutes</span></span>         |
| <span data-ttu-id="18a44-191">99.999%</span><span class="sxs-lookup"><span data-stu-id="18a44-191">99.999%</span></span> | <span data-ttu-id="18a44-192">6 秒</span><span class="sxs-lookup"><span data-stu-id="18a44-192">6 seconds</span></span>             | <span data-ttu-id="18a44-193">25.9 秒</span><span class="sxs-lookup"><span data-stu-id="18a44-193">25.9 seconds</span></span>           | <span data-ttu-id="18a44-194">5.26 分钟</span><span class="sxs-lookup"><span data-stu-id="18a44-194">5.26 minutes</span></span>          |

## <a name="understand-service-level-agreements"></a><span data-ttu-id="18a44-195">了解服务级别协议</span><span class="sxs-lookup"><span data-stu-id="18a44-195">Understand service-level agreements</span></span>

<span data-ttu-id="18a44-196">在 Azure 中，[服务级别协议](https://azure.microsoft.com/en-us/support/legal/sla/)描述 Microsoft 的关于运行时间和连接承诺。</span><span class="sxs-lookup"><span data-stu-id="18a44-196">In Azure, the [Service Level Agreement](https://azure.microsoft.com/en-us/support/legal/sla/) describes Microsoft's commitments for uptime and connectivity.</span></span> <span data-ttu-id="18a44-197">如果针对特定服务的 SLA 为 99.9%，你应期望服务能够在 99.9%的时间可用。</span><span class="sxs-lookup"><span data-stu-id="18a44-197">If the SLA for a particular service is 99.9%, you should expect the service to be available 99.9% of the time.</span></span> <span data-ttu-id="18a44-198">不同的服务具有不同 Sla。</span><span class="sxs-lookup"><span data-stu-id="18a44-198">Different services have different SLAs.</span></span>

<span data-ttu-id="18a44-199">Azure SLA 还包括用于获取的服务信用，如果不满足 SLA，以及特定的定义的预配*可用性*为每个服务。</span><span class="sxs-lookup"><span data-stu-id="18a44-199">The Azure SLA also includes provisions for obtaining a service credit if the SLA is not met, along with specific definitions of *availability* for each service.</span></span> <span data-ttu-id="18a44-200">SLA 的此项规定充当强制策略。</span><span class="sxs-lookup"><span data-stu-id="18a44-200">That aspect of the SLA acts as an enforcement policy.</span></span>

### <a name="composite-slas"></a><span data-ttu-id="18a44-201">复合 SLA</span><span class="sxs-lookup"><span data-stu-id="18a44-201">Composite SLAs</span></span>

<span data-ttu-id="18a44-202">*复合 Sla*涉及多个服务支持的应用程序，每个都有不同的可用性级别。</span><span class="sxs-lookup"><span data-stu-id="18a44-202">*Composite SLAs* involve multiple services supporting an application, each with differing levels of availability.</span></span> <span data-ttu-id="18a44-203">例如，考虑将写入到 Azure SQL 数据库的应用服务 web 应用。</span><span class="sxs-lookup"><span data-stu-id="18a44-203">For example, consider an App Service web app that writes to Azure SQL Database.</span></span> <span data-ttu-id="18a44-204">在撰写本文时，这些 Azure 服务的 SLA 如下：</span><span class="sxs-lookup"><span data-stu-id="18a44-204">At the time of this writing, these Azure services have the following SLAs:</span></span>

- <span data-ttu-id="18a44-205">应用服务 web 应用 = 99.95%</span><span class="sxs-lookup"><span data-stu-id="18a44-205">App Service web apps = 99.95%</span></span>
- <span data-ttu-id="18a44-206">SQL 数据库 = 99.99%</span><span class="sxs-lookup"><span data-stu-id="18a44-206">SQL Database = 99.99%</span></span>

<span data-ttu-id="18a44-207">此应用程序的预期最大停机时间是多少？</span><span class="sxs-lookup"><span data-stu-id="18a44-207">What is the maximum downtime you would expect for this application?</span></span> <span data-ttu-id="18a44-208">如果任一服务发生故障，整个应用程序也会发生故障。</span><span class="sxs-lookup"><span data-stu-id="18a44-208">If either service fails, the whole application fails.</span></span> <span data-ttu-id="18a44-209">每个服务发生故障的概率是独立的因此为此应用程序的复合 SLA 为 99.95%× 99.99%= 99.94%。</span><span class="sxs-lookup"><span data-stu-id="18a44-209">The probability of each service failing is independent, so the composite SLA for this application is 99.95% × 99.99% = 99.94%.</span></span> <span data-ttu-id="18a44-210">这是比单个 Sla 更低的不让人意外，因为依赖于多个服务的应用程序具有更多的潜在故障点。</span><span class="sxs-lookup"><span data-stu-id="18a44-210">That's lower than the individual SLAs, which isn't surprising because an application that relies on multiple services has more potential failure points.</span></span>

<span data-ttu-id="18a44-211">可以通过创建独立的回退路径来提高复合 SLA。</span><span class="sxs-lookup"><span data-stu-id="18a44-211">You can improve the composite SLA by creating independent fallback paths.</span></span> <span data-ttu-id="18a44-212">例如，如果 SQL 数据库不可用，将事务放入队列供以后处理。</span><span class="sxs-lookup"><span data-stu-id="18a44-212">For example, if SQL Database is unavailable, put transactions into a queue to be processed later.</span></span>

![复合 SLA](_images/composite-sla.png)

<span data-ttu-id="18a44-214">如果采用这种设计，即使应用程序无法连接到数据库，它也能保持可用性。</span><span class="sxs-lookup"><span data-stu-id="18a44-214">With this design, the application is still available even if it can't connect to the database.</span></span> <span data-ttu-id="18a44-215">但是，如果数据库和队列同时发生故障，则应用程序也会发生故障。</span><span class="sxs-lookup"><span data-stu-id="18a44-215">However, it fails if the database and the queue both fail at the same time.</span></span> <span data-ttu-id="18a44-216">预期同时发生故障百分比是时间的 0.0001 × 0.001，因此此组合路径的复合 SLA 是时间的：</span><span class="sxs-lookup"><span data-stu-id="18a44-216">The expected percentage of time for a simultaneous failure is 0.0001 × 0.001, so the composite SLA for this combined path is:</span></span>

- <span data-ttu-id="18a44-217">数据库*或*队列 = 1.0 − (0.0001 × 0.001) = 99.99999%</span><span class="sxs-lookup"><span data-stu-id="18a44-217">Database *or* queue = 1.0 − (0.0001 × 0.001) = 99.99999%</span></span>

<span data-ttu-id="18a44-218">总复合 SLA 是：</span><span class="sxs-lookup"><span data-stu-id="18a44-218">The total composite SLA is:</span></span>

- <span data-ttu-id="18a44-219">Web 应用*并*(数据库*或*队列) = 99.95%× 99.99999%= \~99.95%</span><span class="sxs-lookup"><span data-stu-id="18a44-219">Web app *and* (database *or* queue) = 99.95% × 99.99999% = \~99.95%</span></span>

<span data-ttu-id="18a44-220">有几个这种方法的缺点。</span><span class="sxs-lookup"><span data-stu-id="18a44-220">There are tradeoffs to this approach.</span></span> <span data-ttu-id="18a44-221">应用程序逻辑更复杂，因此需要支付队列，，您需要考虑数据一致性问题。</span><span class="sxs-lookup"><span data-stu-id="18a44-221">The application logic is more complex, you are paying for the queue, and you need to consider data consistency issues.</span></span>

### <a name="slas-for-multiregion-deployments"></a><span data-ttu-id="18a44-222">对于多区域部署的服务级别协议</span><span class="sxs-lookup"><span data-stu-id="18a44-222">SLAs for multiregion deployments</span></span>

<span data-ttu-id="18a44-223">*对于多区域部署的 Sla*涉及部署多个区域中的应用程序和使用 Azure 流量管理器在一个区域中的应用程序出现故障时故障转移的高可用性技术。</span><span class="sxs-lookup"><span data-stu-id="18a44-223">*SLAs for multiregion deployments* involve a high-availability technique to deploy the application in more than one region and use Azure Traffic Manager to fail over if the application fails in one region.</span></span>

<span data-ttu-id="18a44-224">多区域部署的复合 SLA 的计算方式如下：</span><span class="sxs-lookup"><span data-stu-id="18a44-224">The composite SLA for a multiregion deployment is calculated as follows:</span></span>

- <span data-ttu-id="18a44-225">*N*是在一个区域中部署的应用程序的复合 SLA。</span><span class="sxs-lookup"><span data-stu-id="18a44-225">*N* is the composite SLA for the application deployed in one region.</span></span>
- <span data-ttu-id="18a44-226">*R*是部署应用程序的区域数量。</span><span class="sxs-lookup"><span data-stu-id="18a44-226">*R* is the number of regions where the application is deployed.</span></span>

<span data-ttu-id="18a44-227">应用程序失败，同时在所有区域中的预期的可能性是 ((1 − N) \^ R)。</span><span class="sxs-lookup"><span data-stu-id="18a44-227">The expected chance that the application fails in all regions at the same time is ((1 − N) \^ R).</span></span> <span data-ttu-id="18a44-228">例如，如果单区域 SLA 为 99.95%:</span><span class="sxs-lookup"><span data-stu-id="18a44-228">For example, if the single-region SLA is 99.95%:</span></span>

- <span data-ttu-id="18a44-229">两个区域的组合的 SLA = (1 − （1 − 0.9995） \^ 2) = 99.999975%</span><span class="sxs-lookup"><span data-stu-id="18a44-229">The combined SLA for two regions = (1 − (1 − 0.9995) \^ 2) = 99.999975%</span></span>
- <span data-ttu-id="18a44-230">四个区域的组合的 SLA = (1 − （1 − 0.9995） \^ 4) = 99.999999%</span><span class="sxs-lookup"><span data-stu-id="18a44-230">The combined SLA for four regions =  (1 − (1 − 0.9995) \^ 4)  = 99.999999%</span></span>

<span data-ttu-id="18a44-231">[流量管理器的 SLA](https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/)也是一个因素。</span><span class="sxs-lookup"><span data-stu-id="18a44-231">The [SLA for Traffic Manager](https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/) is also a factor.</span></span> <span data-ttu-id="18a44-232">故障转移不是在主动-被动配置中，这可能会导致在故障转移期间的停机时间瞬间完成的。</span><span class="sxs-lookup"><span data-stu-id="18a44-232">Failing over is not instantaneous in active-passive configurations, which can result in downtime during a failover.</span></span> <span data-ttu-id="18a44-233">请参阅[流量管理器终结点监视和故障转移](/azure/traffic-manager/traffic-manager-monitoring)。</span><span class="sxs-lookup"><span data-stu-id="18a44-233">See [Traffic Manager endpoint monitoring and failover](/azure/traffic-manager/traffic-manager-monitoring).</span></span>

## <a name="next-steps"></a><span data-ttu-id="18a44-234">后续步骤</span><span class="sxs-lookup"><span data-stu-id="18a44-234">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="18a44-235">架构师的弹性和可用性</span><span class="sxs-lookup"><span data-stu-id="18a44-235">Architect for resiliency and availability</span></span>](./architect.md)
