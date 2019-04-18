---
title: 测试 Azure 应用程序的复原能力和可用性
description: 在 Azure 中测试应用程序的可靠性的方法
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: cf33a859540810279b1cb85be5a6fb2ebe4500dc
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646492"
---
# <a name="testing-azure-applications-for-resiliency-and-availability"></a><span data-ttu-id="3986c-103">测试 Azure 应用程序的复原能力和可用性</span><span class="sxs-lookup"><span data-stu-id="3986c-103">Testing Azure applications for resiliency and availability</span></span>

<span data-ttu-id="3986c-104">若要测试复原能力，您应该验证端到端工作负荷出现间歇性故障条件下的执行方式。</span><span class="sxs-lookup"><span data-stu-id="3986c-104">To test resiliency, you should verify how the end-to-end workload performs under intermittent failure conditions.</span></span>

<span data-ttu-id="3986c-105">在使用这两个合成和真实用户数据的生产环境中运行测试。</span><span class="sxs-lookup"><span data-stu-id="3986c-105">Run tests in production using both synthetic and real user data.</span></span> <span data-ttu-id="3986c-106">测试和生产环境很少完全相同，所以务必要验证你的应用程序在生产中使用[蓝绿](https://martinfowler.com/bliki/BlueGreenDeployment.html)或[canary 部署](https://martinfowler.com/bliki/CanaryRelease.html)。</span><span class="sxs-lookup"><span data-stu-id="3986c-106">Test and production are rarely identical, so it's important to validate your application in production using a [blue-green](https://martinfowler.com/bliki/BlueGreenDeployment.html) or [canary deployment](https://martinfowler.com/bliki/CanaryRelease.html).</span></span> <span data-ttu-id="3986c-107">这种方式，以便你可以确保它将正常按预期方式在完全部署后，您要测试实际条件下的应用程序。</span><span class="sxs-lookup"><span data-stu-id="3986c-107">This way, you're testing the application under real conditions, so you can be sure that it will function as expected when fully deployed.</span></span>

<span data-ttu-id="3986c-108">作为测试计划的一部分，包括：</span><span class="sxs-lookup"><span data-stu-id="3986c-108">As part of your test plan, include:</span></span>

- <span data-ttu-id="3986c-109">自动部署前测试</span><span class="sxs-lookup"><span data-stu-id="3986c-109">Automated predeployment testing</span></span>
- <span data-ttu-id="3986c-110">错误注入测试</span><span class="sxs-lookup"><span data-stu-id="3986c-110">Fault injection testing</span></span>
- <span data-ttu-id="3986c-111">峰值负载测试</span><span class="sxs-lookup"><span data-stu-id="3986c-111">Peak load testing</span></span>
- <span data-ttu-id="3986c-112">灾难恢复测试</span><span class="sxs-lookup"><span data-stu-id="3986c-112">Disaster recovery testing</span></span>
- <span data-ttu-id="3986c-113">第三方服务测试</span><span class="sxs-lookup"><span data-stu-id="3986c-113">Third-party service testing</span></span>

<span data-ttu-id="3986c-114">测试是一个迭代过程。</span><span class="sxs-lookup"><span data-stu-id="3986c-114">Testing is an iterative process.</span></span> <span data-ttu-id="3986c-115">需要测试应用程序、测量结果、分析并解决出现的任何故障，并重复该过程。</span><span class="sxs-lookup"><span data-stu-id="3986c-115">Test the application, measure the outcome, analyze and address any failures that result, and repeat the process.</span></span>

## <a name="perform-fault-injection-testing"></a><span data-ttu-id="3986c-116">执行故障注入测试</span><span class="sxs-lookup"><span data-stu-id="3986c-116">Perform fault injection testing</span></span>

<span data-ttu-id="3986c-117">对于错误注入测试，检查系统的复原能力在发生故障，期间通过触发实际故障或模拟故障。</span><span class="sxs-lookup"><span data-stu-id="3986c-117">For fault injection testing, check the resiliency of the system during failures, either by triggering actual failures or by simulating them.</span></span> <span data-ttu-id="3986c-118">下面是一些策略来引入故障：</span><span class="sxs-lookup"><span data-stu-id="3986c-118">Here are some strategies to induce failures:</span></span>

- <span data-ttu-id="3986c-119">关闭虚拟机 (VM) 实例。</span><span class="sxs-lookup"><span data-stu-id="3986c-119">Shut down virtual machine (VM) instances.</span></span>
- <span data-ttu-id="3986c-120">使进程崩溃。</span><span class="sxs-lookup"><span data-stu-id="3986c-120">Crash processes.</span></span>
- <span data-ttu-id="3986c-121">证书过期。</span><span class="sxs-lookup"><span data-stu-id="3986c-121">Expire certificates.</span></span>
- <span data-ttu-id="3986c-122">更改访问密钥。</span><span class="sxs-lookup"><span data-stu-id="3986c-122">Change access keys.</span></span>
- <span data-ttu-id="3986c-123">关闭域控制器上的 DNS 服务。</span><span class="sxs-lookup"><span data-stu-id="3986c-123">Shut down the DNS service on domain controllers.</span></span>
- <span data-ttu-id="3986c-124">限制可用的系统资源，例如 RAM 或线程数。</span><span class="sxs-lookup"><span data-stu-id="3986c-124">Limit available system resources, such as RAM or number of threads.</span></span>
- <span data-ttu-id="3986c-125">卸载磁盘。</span><span class="sxs-lookup"><span data-stu-id="3986c-125">Unmount disks.</span></span>
- <span data-ttu-id="3986c-126">重新部署 VM。</span><span class="sxs-lookup"><span data-stu-id="3986c-126">Redeploy a VM.</span></span>

<span data-ttu-id="3986c-127">测试计划应包含在设计阶段，常见的故障案例除了标识可能的故障点：</span><span class="sxs-lookup"><span data-stu-id="3986c-127">Your test plan should incorporate possible failure points identified during the design phase, in addition to common failure scenarios:</span></span>

- <span data-ttu-id="3986c-128">尽可能地生产的环境中测试你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="3986c-128">Test your application in an environment as close to production as possible.</span></span>
- <span data-ttu-id="3986c-129">在组合中测试失败。</span><span class="sxs-lookup"><span data-stu-id="3986c-129">Test failures in combination.</span></span>
- <span data-ttu-id="3986c-130">测量恢复时间，并确保满足您的业务需求。</span><span class="sxs-lookup"><span data-stu-id="3986c-130">Measure the recovery times, and be sure that your business requirements are met.</span></span>
- <span data-ttu-id="3986c-131">验证故障不级联和隔离的方式来处理的。</span><span class="sxs-lookup"><span data-stu-id="3986c-131">Verify that failures don't cascade and are handled in an isolated way.</span></span>

<span data-ttu-id="3986c-132">有关故障方案的详细信息，请参阅[Azure 应用程序的故障和灾难恢复](./disaster-recovery.md)。</span><span class="sxs-lookup"><span data-stu-id="3986c-132">For more information about failure scenarios, see [Failure and disaster recovery for Azure applications](./disaster-recovery.md).</span></span>

## <a name="test-under-peak-loads"></a><span data-ttu-id="3986c-133">在峰值负载下测试</span><span class="sxs-lookup"><span data-stu-id="3986c-133">Test under peak loads</span></span>

<span data-ttu-id="3986c-134">负载测试的标识的负载，例如后端数据库瘫痪，才会发生故障或服务限制至关重要。</span><span class="sxs-lookup"><span data-stu-id="3986c-134">Load testing is crucial for identifying failures that only happen under load, such as the back-end database being overwhelmed or service throttling.</span></span> <span data-ttu-id="3986c-135">峰值负载和峰值负载，使用生产数据或生产数据尽可能接近的合成数据预期的增加的测试。</span><span class="sxs-lookup"><span data-stu-id="3986c-135">Test for peak load and anticipated increase in peak load, using production data or synthetic data that is as close to production data as possible.</span></span> <span data-ttu-id="3986c-136">你的目标是了解应用程序在实际情况下的行为方式。</span><span class="sxs-lookup"><span data-stu-id="3986c-136">Your goal is to see how the application behaves under real-world conditions.</span></span>

## <a name="conduct-disaster-recovery-drills"></a><span data-ttu-id="3986c-137">执行灾难恢复演练</span><span class="sxs-lookup"><span data-stu-id="3986c-137">Conduct disaster recovery drills</span></span>

<span data-ttu-id="3986c-138">开始良好的灾难恢复计划，并测试定期以确保它能够正常工作。</span><span class="sxs-lookup"><span data-stu-id="3986c-138">Start with a good disaster recovery plan, and test it periodically to make sure it works.</span></span>

<span data-ttu-id="3986c-139">对于 Azure 虚拟机，可以使用[Azure Site Recovery](/azure/site-recovery/azure-to-azure-quickstart/)复制并执行[灾难恢复演练](/azure/site-recovery/azure-to-azure-tutorial-dr-drill/)而不会影响生产应用程序或正在进行的复制。</span><span class="sxs-lookup"><span data-stu-id="3986c-139">For Azure Virtual Machines, you can use [Azure Site Recovery](/azure/site-recovery/azure-to-azure-quickstart/) to replicate and perform [disaster recovery drills](/azure/site-recovery/azure-to-azure-tutorial-dr-drill/) without impacting production applications or ongoing replication.</span></span>

### <a name="failover-and-failback-testing"></a><span data-ttu-id="3986c-140">故障转移和故障回复测试</span><span class="sxs-lookup"><span data-stu-id="3986c-140">Failover and failback testing</span></span>

<span data-ttu-id="3986c-141">测试故障转移和故障回复以验证，应用程序的依赖服务恢复以同步方式在灾难恢复过程。</span><span class="sxs-lookup"><span data-stu-id="3986c-141">Test failover and failback to verify that your application's dependent services come back up in a synchronized manner during disaster recovery.</span></span> <span data-ttu-id="3986c-142">对系统和操作的更改可能会影响故障转移和故障回复的功能，但可能检测到影响，直到主系统发生故障或过载。</span><span class="sxs-lookup"><span data-stu-id="3986c-142">Changes to systems and operations may affect failover and failback functions, but the impact may not be detected until the main system fails or becomes overloaded.</span></span> <span data-ttu-id="3986c-143">测试故障转移功能*之前*需要它们来弥补实时问题。</span><span class="sxs-lookup"><span data-stu-id="3986c-143">Test failover capabilities *before* they are required to compensate for a live problem.</span></span> <span data-ttu-id="3986c-144">此外请确保依赖服务故障转移和故障回复中正确的顺序。</span><span class="sxs-lookup"><span data-stu-id="3986c-144">Also be sure that dependent services fail over and fail back in the correct order.</span></span>

<span data-ttu-id="3986c-145">如果使用的[Azure Site Recovery](/azure/site-recovery/)若要将 Vm 复制，灾难恢复演练定期运行通过执行测试故障转移，验证复制策略。</span><span class="sxs-lookup"><span data-stu-id="3986c-145">If you are using [Azure Site Recovery](/azure/site-recovery/) to replicate VMs, run disaster recovery drills periodically by doing test failovers to validate your replication strategy.</span></span> <span data-ttu-id="3986c-146">测试故障转移不会影响正在进行的 VM 复制或生产环境。</span><span class="sxs-lookup"><span data-stu-id="3986c-146">A test failover does not affect the ongoing VM replication or your production environment.</span></span> <span data-ttu-id="3986c-147">有关详细信息，请参阅[运行到 Azure 的灾难恢复演练](/azure/site-recovery/site-recovery-test-failover-to-azure)。</span><span class="sxs-lookup"><span data-stu-id="3986c-147">For more information, see [Run a disaster recovery drill to Azure](/azure/site-recovery/site-recovery-test-failover-to-azure).</span></span>

### <a name="simulation-testing"></a><span data-ttu-id="3986c-148">模拟测试</span><span class="sxs-lookup"><span data-stu-id="3986c-148">Simulation testing</span></span>

<span data-ttu-id="3986c-149">模拟测试涉及创建小型的现实情况。</span><span class="sxs-lookup"><span data-stu-id="3986c-149">Simulation testing involves creating small, real-life situations.</span></span> <span data-ttu-id="3986c-150">模拟在恢复计划中演示的解决方案的有效性，并突出显示任何未充分解决的问题。</span><span class="sxs-lookup"><span data-stu-id="3986c-150">Simulations demonstrate the effectiveness of the solutions in the recovery plan and highlight any issues that weren't adequately addressed.</span></span>

<span data-ttu-id="3986c-151">在执行模拟测试时，请遵循最佳实践：</span><span class="sxs-lookup"><span data-stu-id="3986c-151">As you perform simulation testing, follow best practices:</span></span>

- <span data-ttu-id="3986c-152">应用程序执行模拟不会中断实际业务，但让人感觉像真实情况的方式。</span><span class="sxs-lookup"><span data-stu-id="3986c-152">Conduct simulations in a manner that doesn't disrupt actual business but feels like a real situation.</span></span>
- <span data-ttu-id="3986c-153">请确保模拟的方案是完全可控。</span><span class="sxs-lookup"><span data-stu-id="3986c-153">Make sure that simulated scenarios are completely controllable.</span></span> <span data-ttu-id="3986c-154">如果恢复计划似乎将失败，则可以还原到正常还原这种情况，而不会导致损坏。</span><span class="sxs-lookup"><span data-stu-id="3986c-154">If the recovery plan seems to be failing, you can restore the situation back to normal without causing damage.</span></span>
- <span data-ttu-id="3986c-155">通知有关何时以及如何将执行模拟练习的管理。</span><span class="sxs-lookup"><span data-stu-id="3986c-155">Inform management about when and how the simulation exercises will be conducted.</span></span> <span data-ttu-id="3986c-156">时间范围和在模拟期间受影响的资源，你的计划应详细说明。</span><span class="sxs-lookup"><span data-stu-id="3986c-156">Your plan should detail the time frame and the resources affected during the simulation.</span></span>

## <a name="test-health-probes-for-load-balancers-and-traffic-managers"></a><span data-ttu-id="3986c-157">测试负载均衡器和流量管理器的运行状况探测</span><span class="sxs-lookup"><span data-stu-id="3986c-157">Test health probes for load balancers and traffic managers</span></span>

<span data-ttu-id="3986c-158">配置和测试的负载均衡器和流量管理器的运行状况探测。</span><span class="sxs-lookup"><span data-stu-id="3986c-158">Configure and test health probes for your load balancers and traffic managers.</span></span> <span data-ttu-id="3986c-159">请确保运行状况终结点检查系统的关键部分，并做出相应的响应。</span><span class="sxs-lookup"><span data-stu-id="3986c-159">Ensure that your health endpoint checks the critical parts of the system and responds appropriately.</span></span>

- <span data-ttu-id="3986c-160">有关[Azure 流量管理器](/azure/traffic-manager/traffic-manager-overview/)，运行状况探测确定是否将故障转移到另一个区域。</span><span class="sxs-lookup"><span data-stu-id="3986c-160">For [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview/), the health probe determines whether to fail over to another region.</span></span> <span data-ttu-id="3986c-161">运行状况终结点应检查在同一区域内部署所有关键依赖项。</span><span class="sxs-lookup"><span data-stu-id="3986c-161">Your health endpoint should check any critical dependencies that are deployed within the same region.</span></span>
- <span data-ttu-id="3986c-162">有关[Azure 负载均衡器](/azure/load-balancer/load-balancer-overview/)，运行状况探测确定是否从轮转中删除 VM。</span><span class="sxs-lookup"><span data-stu-id="3986c-162">For [Azure Load Balancer](/azure/load-balancer/load-balancer-overview/), the health probe determines whether to remove a VM from rotation.</span></span> <span data-ttu-id="3986c-163">运行状况终结点应报告 VM 的运行状况。</span><span class="sxs-lookup"><span data-stu-id="3986c-163">The health endpoint should report the health of the VM.</span></span> <span data-ttu-id="3986c-164">不要包含其他层或外部服务。</span><span class="sxs-lookup"><span data-stu-id="3986c-164">Don't include other tiers or external services.</span></span> <span data-ttu-id="3986c-165">否则，在 VM 外部发生的故障会导致负载均衡器从轮转项目中删除该 VM。</span><span class="sxs-lookup"><span data-stu-id="3986c-165">Otherwise, a failure that occurs outside the VM will cause the load balancer to remove the VM from rotation.</span></span>

<span data-ttu-id="3986c-166">有关在应用程序中实施运行状况监视的指导，请参阅[运行状况终结点监视模式](../patterns/health-endpoint-monitoring.md)。</span><span class="sxs-lookup"><span data-stu-id="3986c-166">For guidance on implementing health monitoring in your application, see [Health Endpoint Monitoring pattern](../patterns/health-endpoint-monitoring.md).</span></span>

## <a name="test-monitoring-systems"></a><span data-ttu-id="3986c-167">测试监视系统</span><span class="sxs-lookup"><span data-stu-id="3986c-167">Test monitoring systems</span></span>

<span data-ttu-id="3986c-168">包括测试计划中监视系统。</span><span class="sxs-lookup"><span data-stu-id="3986c-168">Include monitoring systems in your test plan.</span></span> <span data-ttu-id="3986c-169">自动的故障转移和故障回复系统依赖于监视和检测的正确运行。</span><span class="sxs-lookup"><span data-stu-id="3986c-169">Automated failover and failback systems depend on the correct functioning of monitoring and instrumentation.</span></span> <span data-ttu-id="3986c-170">若要可视化系统运行状况和运算符警报的仪表板也依赖于具有准确地监视和检测。</span><span class="sxs-lookup"><span data-stu-id="3986c-170">Dashboards to visualize system health and operator alerts also depend on having accurate monitoring and instrumentation.</span></span> <span data-ttu-id="3986c-171">如果这些元素发生故障、遗漏重要信息或报告错误的数据，操作人员可能不知道系统不正常或即将发生故障。</span><span class="sxs-lookup"><span data-stu-id="3986c-171">If these elements fail, miss critical information, or report inaccurate data, an operator might not realize that the system is unhealthy or failing.</span></span>

## <a name="include-third-party-services-in-test-scenarios"></a><span data-ttu-id="3986c-172">在测试方案中包括第三方服务</span><span class="sxs-lookup"><span data-stu-id="3986c-172">Include third-party services in test scenarios</span></span>

<span data-ttu-id="3986c-173">如果你的应用程序在第三方服务上具有依赖项，确定位置和如何这些服务可能会失败并有何影响这些故障将对你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="3986c-173">If your application has dependencies on third-party services, identify where and how these services can fail and what effect those failures will have on your application.</span></span> <span data-ttu-id="3986c-174">请记住服务级别协议 (SLA 的第三方服务和效果这可能会对灾难恢复计划)。</span><span class="sxs-lookup"><span data-stu-id="3986c-174">Keep in mind the service-level agreement (SLA) for the third-party service and the effect it might have on your disaster recovery plan.</span></span>

<span data-ttu-id="3986c-175">第三方服务可能会提供监视和诊断功能，因此它是重要要记录其调用并将其关联应用程序的运行状况和诊断日志记录使用的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="3986c-175">A third-party service might not provide monitoring and diagnostics capabilities, so it's important to log your invocations of them and to correlate them with your application's health and diagnostic logging using a unique identifier.</span></span> <span data-ttu-id="3986c-176">有关监视和诊断的成熟做法的详细信息，请参阅[监视和诊断指南](../best-practices/monitoring.md)。</span><span class="sxs-lookup"><span data-stu-id="3986c-176">For more information on proven practices for monitoring and diagnostics, see [Monitoring and diagnostics guidance](../best-practices/monitoring.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="3986c-177">后续步骤</span><span class="sxs-lookup"><span data-stu-id="3986c-177">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3986c-178">部署的弹性和可用性</span><span class="sxs-lookup"><span data-stu-id="3986c-178">Deploy for resiliency and availability</span></span>](./deploy.md)
