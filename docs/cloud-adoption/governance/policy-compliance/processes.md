---
title: CAF：监视和强制执行策略遵循
description: 如何确保符合既定的策略？
author: BrianBlanchard
ms.date: 1/4/2019
ms.openlocfilehash: 9066f33c8baa183476a9632e82d6eb960d03752c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900470"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-processes-can-help-ensure-policy-adherence"></a><span data-ttu-id="17bc3-103">哪些流程可帮助确保策略遵循？</span><span class="sxs-lookup"><span data-stu-id="17bc3-103">What processes can help ensure policy adherence?</span></span>

<!---
I've defined policies, I've provided an architecture guide. Now how do I monitor adherence to policy? If there is a violation, how do I enforce the policy?
--->

<span data-ttu-id="17bc3-104">在建立云策略声明并起草设计指南之后，你需要创建一个策略，确保云部署符合策略要求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-104">After establishing your cloud policy statements and drafting a design guide, you'll need to create a strategy for ensuring your cloud deployment stays in compliance with your policy requirements.</span></span> <span data-ttu-id="17bc3-105">此策略需包含云治理团队的持续审查和沟通流程，为何时需要对策略违反行为采取行动建立标准，以及定义检测违规和触发补救措施的自动监控和符合性系统的要求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-105">This strategy will need to encompass your Cloud Governance team's ongoing review and communication processes, establish criteria for when policy violations require action, and defining the requirements for automated monitoring and compliance systems that will detect violations and trigger remediation actions.</span></span>

<span data-ttu-id="17bc3-106">有关策略遵循流程如何适应云治理计划的示例，请参阅[可操作的治理过程](../journeys/overview.md)中的公司策略部分。</span><span class="sxs-lookup"><span data-stu-id="17bc3-106">See the corporate policy sections of the [Actionable Governance Journeys](../journeys/overview.md) for examples of how policy adherence process fit into a cloud governance plan.</span></span>

## <a name="prioritize-policy-adherence-processes"></a><span data-ttu-id="17bc3-107">设置策略遵循流程的优先级</span><span class="sxs-lookup"><span data-stu-id="17bc3-107">Prioritize policy adherence processes</span></span>

<span data-ttu-id="17bc3-108">开发流程需要多少投资来支持策略目标？</span><span class="sxs-lookup"><span data-stu-id="17bc3-108">How much investment in developing processes is required to support your policy goals?</span></span> <span data-ttu-id="17bc3-109">根据云部署的规模和成熟度，建立支持符合性的流程所需的工作和与此工作相关的成本可能千差万别。</span><span class="sxs-lookup"><span data-stu-id="17bc3-109">Depending on the size and maturity of your cloud deployment, the effort required to establish processes that support compliance, and the costs associated with this effort, can vary widely.</span></span>

<span data-ttu-id="17bc3-110">对于只包括开发和测试资源的小型部署来说，策略要求可能很简单，并且只需要很少的专用资源来处理。</span><span class="sxs-lookup"><span data-stu-id="17bc3-110">For small deployments consisting of development and test resources, policy requirements may be simple and require few dedicated resources to address.</span></span> <span data-ttu-id="17bc3-111">另一方面，对于一个成熟的任务关键型云部署，其具有高优先级安全性和性能需求，这就可能需要一组人员、全面的内部流程和自定义监控工具来支持策略目标。</span><span class="sxs-lookup"><span data-stu-id="17bc3-111">On the other hand, a mature mission-critical cloud deployment with high-priority security and performance needs may require a team of staff, extensive internal processes, and custom monitoring tooling to support your policy goals.</span></span>

<span data-ttu-id="17bc3-112">作为定义策略遵循规划的第一步，请评估下面讨论的流程如何支持策略要求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-112">As a first step in defining your policy adherence strategy, evaluate how the processes discussed below can support your policy requirements.</span></span> <span data-ttu-id="17bc3-113">确定在这些流程中值得投入多少工作量，然后使用这些信息来建立切合实际的预算和人员配置计划，以满足这些需求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-113">Determine how much effort is worth investing in these processes, and then use this information to establish realistic budget and staffing plans to meet these needs.</span></span>

## <a name="establish-cloud-governance-team-processes"></a><span data-ttu-id="17bc3-114">建立云治理团队流程</span><span class="sxs-lookup"><span data-stu-id="17bc3-114">Establish Cloud Governance team processes</span></span>

<span data-ttu-id="17bc3-115">在定义策略符合性修正的触发器之前，需要建立团队将使用的整个流程，以及如何在 IT 人员和云治理团队之间共享和升级信息。</span><span class="sxs-lookup"><span data-stu-id="17bc3-115">Before defining triggers for policy compliance remediation, you need establish the overall processes that your team will use and how information will be shared and escalated between IT staff and the Cloud Governance team.</span></span>

### <a name="assign-cloud-governance-team-members"></a><span data-ttu-id="17bc3-116">分配云治理团队成员</span><span class="sxs-lookup"><span data-stu-id="17bc3-116">Assign Cloud Governance team members</span></span>

<span data-ttu-id="17bc3-117">谁将提供关于策略符合性的持续指导，并处理在部署和运营云资产时出现的与策略相关的问题？</span><span class="sxs-lookup"><span data-stu-id="17bc3-117">Who will provide ongoing guidance on policy compliance and handle policy-related issues that emerge when deploying and operating your cloud assets?</span></span> <span data-ttu-id="17bc3-118">云治理团队的规模和人员构成将取决于策略需求的复杂性，以及与策略符合性相关的预算和人员优先级。</span><span class="sxs-lookup"><span data-stu-id="17bc3-118">The size and composition of your Cloud Governance team will depend on the complexity of your policy requirements, and the budgeting and staffing priorities you've attached to policy compliance.</span></span>

<span data-ttu-id="17bc3-119">请选择在你所定义的策略声明所涉及领域具有专业知识的团队成员。</span><span class="sxs-lookup"><span data-stu-id="17bc3-119">Choose team members that have expertise in the areas covered by your defined policy statements.</span></span> <span data-ttu-id="17bc3-120">对于初始测试部署，这可以仅限于少数几个负责建立治理基础的系统管理员。</span><span class="sxs-lookup"><span data-stu-id="17bc3-120">For initial test deployments this can be limited to a few system administrators responsible for establishing the basics of governance.</span></span> <span data-ttu-id="17bc3-121">随着部署的成熟和策略复杂性的增加，以及与更广泛的公司策略需求更密切地整合，云治理团队需要随之变化，以支持日益复杂的策略需求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-121">As your deployments mature and your policies become more complex and more integrated with your wider corporate policy requirements, your Cloud Governance team will need to change to support increasingly complicated policy requirements.</span></span>

<span data-ttu-id="17bc3-122">随着治理流程的成熟，定期检查云指导团队的成员资格，以确保能够正确处理最新的策略需求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-122">As your governance processes mature, review the cloud guidance team's membership regularly to ensure that you can properly address the latest policy requirements.</span></span> <span data-ttu-id="17bc3-123">确定 IT 人员中具有特定治理领域的相关经验或对该领域感兴趣的成员，并根据需要将他们永久或临时纳入团队。</span><span class="sxs-lookup"><span data-stu-id="17bc3-123">Identify members of your IT staff with relevant experience or interest in specific areas of governance and include them in your teams on a permanent or ad-hoc basis as-needed.</span></span>

### <a name="reviews-and-policy-iteration"></a><span data-ttu-id="17bc3-124">审查和策略迭代</span><span class="sxs-lookup"><span data-stu-id="17bc3-124">Reviews and policy iteration</span></span>

<span data-ttu-id="17bc3-125">部署其他资源时，云治理团队需要确保新的工作负载或资产符合策略需求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-125">As additional resources are deployed, the Cloud Governance team will need to ensure that new workloads or assets comply with policy requirements.</span></span> <span data-ttu-id="17bc3-126">计划与负责部署任何新资源的团队会面，讨论与设计指南的一致性。</span><span class="sxs-lookup"><span data-stu-id="17bc3-126">Plan to meet with the teams responsible for deploying any new resources to discuss alignment with your design guides.</span></span>

<span data-ttu-id="17bc3-127">随着整体部署的扩大，定期评估新的潜在风险，并根据需要更新策略声明和设计指南。</span><span class="sxs-lookup"><span data-stu-id="17bc3-127">As your overall deployment grows, evaluate new potential risks regularly and update policy statements and design guides as needed.</span></span> <span data-ttu-id="17bc3-128">定期安排五个治理规则的评审周期，确保策略最新且满足其需求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-128">Schedule regular review cycles each of the five governance disciplines to ensure policy is up-to-date and being met.</span></span>

### <a name="education"></a><span data-ttu-id="17bc3-129">教育</span><span class="sxs-lookup"><span data-stu-id="17bc3-129">Education</span></span>

<span data-ttu-id="17bc3-130">策略符合性要求 IT 人员和开发者理解影响其职责范围的策略需求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-130">Policy compliance requires IT staff and developers to understand the policy requirements that affect their areas of responsibility.</span></span> <span data-ttu-id="17bc3-131">计划投入资源来记录决策和需求，并就支持策略需求的设计指南对所有相关团队进行培训。</span><span class="sxs-lookup"><span data-stu-id="17bc3-131">Plan to devote resources to document decisions and requirements, and educate all relevant teams on the design guides that support your policy requirements.</span></span>

<span data-ttu-id="17bc3-132">随着策略的变化，定期更新文档和培训材料，并确保培训工作传达最新要求及指导给相关 IT 人员。</span><span class="sxs-lookup"><span data-stu-id="17bc3-132">As policy changes, regularly update documentation and training materials, and ensure education efforts communicate updated requirements and guidance to relevant IT staff.</span></span>  

### <a name="establish-escalation-paths"></a><span data-ttu-id="17bc3-133">建立升级路径</span><span class="sxs-lookup"><span data-stu-id="17bc3-133">Establish escalation paths</span></span>

<span data-ttu-id="17bc3-134">如果资源不合规，谁将获得通知？</span><span class="sxs-lookup"><span data-stu-id="17bc3-134">If a resource goes out of compliance, who gets notified?</span></span> <span data-ttu-id="17bc3-135">如果 IT 人员检测到一个策略符合性问题，他们应联系谁？</span><span class="sxs-lookup"><span data-stu-id="17bc3-135">If IT staff detect a policy compliance issue, who do they contact?</span></span> <span data-ttu-id="17bc3-136">确保对云治理团队的升级流程进行明确定义。</span><span class="sxs-lookup"><span data-stu-id="17bc3-136">Make sure the escalation process to the Cloud Governance team is clearly defined.</span></span> <span data-ttu-id="17bc3-137">确保这些沟通渠道保持更新，以反映员工和组织的变化。</span><span class="sxs-lookup"><span data-stu-id="17bc3-137">Ensure these communication channels are kept updated to reflect staff and organization changes.</span></span>

## <a name="violation-triggers-and-actions"></a><span data-ttu-id="17bc3-138">违规触发器和操作</span><span class="sxs-lookup"><span data-stu-id="17bc3-138">Violation triggers and actions</span></span>

<span data-ttu-id="17bc3-139">在定义云治理团队及其流程之后，需要明确定义哪些符合性违规行为将会触发操作，以及对应的操作应是什么。</span><span class="sxs-lookup"><span data-stu-id="17bc3-139">After defining your Cloud Governance team and its processes, you need to explicitly define what qualifies as compliance violations that will triggers actions, and what those actions should be.</span></span>

### <a name="define-triggers"></a><span data-ttu-id="17bc3-140">定义触发器</span><span class="sxs-lookup"><span data-stu-id="17bc3-140">Define triggers</span></span>

<span data-ttu-id="17bc3-141">对于每个策略声明，查看各项要求，确定哪些行为将导致违反策略。</span><span class="sxs-lookup"><span data-stu-id="17bc3-141">For each of your policy statements, review requirements to determine what constitutes a policy violation.</span></span> <span data-ttu-id="17bc3-142">使用策略定义流程中已经建立的信息生成触发器。</span><span class="sxs-lookup"><span data-stu-id="17bc3-142">Generate your triggers using the information you've already established as part of the policy definition process.</span></span>

* <span data-ttu-id="17bc3-143">风险容忍度 - 根据在[风险容忍度分析](risk-tolerance.md)中建立的度量值和风险指标创建违规触发器。</span><span class="sxs-lookup"><span data-stu-id="17bc3-143">Risk tolerance - Create violation triggers based on the metrics and risk indicators you established as part of your [risk tolerance analysis](risk-tolerance.md).</span></span>
* <span data-ttu-id="17bc3-144">已定义的策略需求 - 策略声明可能提供服务级别协议 (SLA)、业务连续性和灾难恢复 (BRCD)，或者应用作符合性触发器基础的性能要求。</span><span class="sxs-lookup"><span data-stu-id="17bc3-144">Defined policy requirements - Policy statements may provide Service Level Agreement (SLA), Business continuity and disaster recovery (BRCD), or performance requirements that should be used as the basis for compliance triggers.</span></span>

### <a name="define-actions"></a><span data-ttu-id="17bc3-145">定义操作</span><span class="sxs-lookup"><span data-stu-id="17bc3-145">Define actions</span></span>

<span data-ttu-id="17bc3-146">每个违规触发器都应具有相应的操作。</span><span class="sxs-lookup"><span data-stu-id="17bc3-146">Each violation trigger should have a corresponding action.</span></span> <span data-ttu-id="17bc3-147">触发的操作应始终在出现违规情况时通知适当的 IT 人员或云治理团队成员。</span><span class="sxs-lookup"><span data-stu-id="17bc3-147">Triggered actions should always notify an appropriate IT staff or Cloud Governance team member when a violation occurs.</span></span> <span data-ttu-id="17bc3-148">根据此通知，可能会对符合性问题进行手动审查，或根据检测到的违反行为的类型和严重程度启动预先建立的修正流程。</span><span class="sxs-lookup"><span data-stu-id="17bc3-148">This notification can lead to a manual review of the compliance issue or kickoff a pre-established remediation process depending on the type and severity of the detected violation.</span></span>

<span data-ttu-id="17bc3-149">违规触发器和操作的一些示例：</span><span class="sxs-lookup"><span data-stu-id="17bc3-149">Some examples of violation triggers and actions:</span></span>

| <span data-ttu-id="17bc3-150">云治理规则</span><span class="sxs-lookup"><span data-stu-id="17bc3-150">Cloud governance discipline</span></span> | <span data-ttu-id="17bc3-151">示例触发器</span><span class="sxs-lookup"><span data-stu-id="17bc3-151">Sample trigger</span></span> | <span data-ttu-id="17bc3-152">示例操作</span><span class="sxs-lookup"><span data-stu-id="17bc3-152">Sample action</span></span> |
|-----------------------------|----------------|---------------|
| <span data-ttu-id="17bc3-153">成本管理</span><span class="sxs-lookup"><span data-stu-id="17bc3-153">Cost Management</span></span> | <span data-ttu-id="17bc3-154">每月云支出比预期高出 20% 以上。</span><span class="sxs-lookup"><span data-stu-id="17bc3-154">Monthly cloud spending is more than 20% higher than expected.</span></span> | <span data-ttu-id="17bc3-155">通知计费部门负责人，他们将开始审查资源使用情况。</span><span class="sxs-lookup"><span data-stu-id="17bc3-155">Notify billing unit leader who will begin a review of resource usage.</span></span> |
| <span data-ttu-id="17bc3-156">安全基线</span><span class="sxs-lookup"><span data-stu-id="17bc3-156">Security Baseline</span></span> | <span data-ttu-id="17bc3-157">检测到可疑的用户登录活动。</span><span class="sxs-lookup"><span data-stu-id="17bc3-157">Detect suspicious user login activity.</span></span> | <span data-ttu-id="17bc3-158">通知 IT 安全团队，并禁用可疑的用户帐户。</span><span class="sxs-lookup"><span data-stu-id="17bc3-158">Notify IT security team and disable suspect user account.</span></span> |
| <span data-ttu-id="17bc3-159">资源一致性</span><span class="sxs-lookup"><span data-stu-id="17bc3-159">Resource Consistency</span></span> | <span data-ttu-id="17bc3-160">工作负载的 CPU 使用率大于 90%。</span><span class="sxs-lookup"><span data-stu-id="17bc3-160">CPU utilization for workload is greater than 90%.</span></span> | <span data-ttu-id="17bc3-161">通知 IT 运维团队并横向扩展要处理负载的其他资源。</span><span class="sxs-lookup"><span data-stu-id="17bc3-161">Notify the IT Operations team and scale out additional resources to handle load.</span></span> |

## <a name="monitoring-and-compliance-automation"></a><span data-ttu-id="17bc3-162">监视和符合性自动化</span><span class="sxs-lookup"><span data-stu-id="17bc3-162">Monitoring and compliance automation</span></span>

<span data-ttu-id="17bc3-163">在定义符合性违规触发器和操作之后，可以开始计划如何最大程度地使用云平台的日志记录和报告工具，以及其他功能来帮助自动化监视和策略符合性策略。</span><span class="sxs-lookup"><span data-stu-id="17bc3-163">After you've defined your compliance violation triggers and actions, you can start planning how best to use the logging and reporting tools and other features of the cloud platform to help automate your monitoring and policy compliance strategy.</span></span>

<span data-ttu-id="17bc3-164">请参阅 CAF [日志记录和报告决策指南](../../decision-guides/log-and-report/overview.md)主题，获得关于为部署选择最佳监视模式的指导。</span><span class="sxs-lookup"><span data-stu-id="17bc3-164">Consult the CAF [logging and reporting decision guide](../../decision-guides/log-and-report/overview.md) topic for guidance on choosing the best monitoring pattern for your deployment.</span></span>
