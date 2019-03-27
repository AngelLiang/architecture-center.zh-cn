---
title: CAF：资源一致性规则改进
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 资源一致性规则改进
author: BrianBlanchard
ms.openlocfilehash: bc81b894d46266c52291c53dba5532ab2ab6b860
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241568"
---
# <a name="resource-consistency-discipline-improvement"></a><span data-ttu-id="b54f2-103">资源一致性规则改进</span><span class="sxs-lookup"><span data-stu-id="b54f2-103">Resource Consistency discipline improvement</span></span>

<span data-ttu-id="b54f2-104">资源一致性规则侧重于建立与环境、应用程序或工作负载的运营管理相关的策略的方法。</span><span class="sxs-lookup"><span data-stu-id="b54f2-104">The Resource Consistency discipline focuses on ways of establishing policies related to the operational management of an environment, application, or workload.</span></span> <span data-ttu-id="b54f2-105">在云治理的五个规则中，资源一致性包括应用程序、工作负载和资产性能的监视。</span><span class="sxs-lookup"><span data-stu-id="b54f2-105">Within the five disciplines of Cloud Governance, Resource Consistency includes monitoring of applications, workload, and asset performance.</span></span> <span data-ttu-id="b54f2-106">资源一致性还包括满足规模需求、纠正性能服务级别协议 (SLA) 违规以及通过自动补救主动避免 SLA 违规所需的任务。</span><span class="sxs-lookup"><span data-stu-id="b54f2-106">It also includes the tasks required to meet scale demands, remediate performance Service Level Agreement (SLA) violations, and proactively avoid SLA violations through automated remediation.</span></span>

<span data-ttu-id="b54f2-107">本文概述了贵公司可以参与以更好制定和完善资源一致性规则的一些潜在任务。</span><span class="sxs-lookup"><span data-stu-id="b54f2-107">This article outlines some potential tasks your company can engage in to better develop and mature the Resource Consistency discipline.</span></span> <span data-ttu-id="b54f2-108">在实现云解决方案中，这些任务可分为计划、构建、采用和操作几个阶段，然后在允许开发[云治理增量方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)的基础上进行迭代。</span><span class="sxs-lookup"><span data-stu-id="b54f2-108">These tasks can be broken down into planning, building, adopting, and operating phases of implementing a cloud solution, which are then iterated on allowing the development of an [incremental approach to cloud governance](../journeys/overview.md#an-incremental-approach-to-cloud-governance).</span></span>

![采用的四个阶段](../../_images/adoption-phases.png)

<span data-ttu-id="b54f2-110">*图 1.云治理增量方法的采用阶段。*</span><span class="sxs-lookup"><span data-stu-id="b54f2-110">*Figure 1. Adoption phases of the incremental approach to cloud governance.*</span></span>

<span data-ttu-id="b54f2-111">单个文档很难涵盖所有的业务需求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-111">It's impossible for any one document to account for the requirements of all businesses.</span></span> <span data-ttu-id="b54f2-112">因此，本文针对治理成熟过程的每个阶段只概述了建议的最小和可能的示例活动。</span><span class="sxs-lookup"><span data-stu-id="b54f2-112">As such, this article outlines suggested minimum and potential example activities for each phase of the governance maturation process.</span></span> <span data-ttu-id="b54f2-113">这些活动的最初目标是帮助生成[策略 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，并为增量策略演化建立框架。</span><span class="sxs-lookup"><span data-stu-id="b54f2-113">The initial objective of these activities is to help you build a [Policy MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance) and establish a framework for incremental policy evolution.</span></span> <span data-ttu-id="b54f2-114">云治理团队将需要决定在这些活动需要投入多少成本才能提高资源一致性治理能力。</span><span class="sxs-lookup"><span data-stu-id="b54f2-114">Your Cloud Governance team will need to decide how much to invest in these activities to improve your Resource Consistency governance capabilities.</span></span>

> [!CAUTION]
> <span data-ttu-id="b54f2-115">本文列出的最小活动或可能的活动都不针对特定的企业策略或第三方符合性要求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-115">Neither the minimum or potential activities outlined in this article are aligned to specific corporate policies or third party compliance requirements.</span></span> <span data-ttu-id="b54f2-116">本指南旨在帮助促进对话，使两个需求与云治理模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="b54f2-116">This guidance is designed to help facilitate the conversations that will lead to alignment of both requirements with a cloud governance model.</span></span>

## <a name="planning-and-readiness"></a><span data-ttu-id="b54f2-117">规划和准备情况</span><span class="sxs-lookup"><span data-stu-id="b54f2-117">Planning and readiness</span></span>

<span data-ttu-id="b54f2-118">这个治理成熟阶段弥合了业务结果与可操作策略之间的分歧。</span><span class="sxs-lookup"><span data-stu-id="b54f2-118">This phase of governance maturity bridges the divide between business outcomes and actionable strategies.</span></span> <span data-ttu-id="b54f2-119">在此过程中，领导团队定义具体指标，将这些指标对应到数字资产，并开始规划整个迁移工作。</span><span class="sxs-lookup"><span data-stu-id="b54f2-119">During this process, the leadership team defines specific metrics, maps those metrics to the digital estate, and begins planning the overall migration effort.</span></span>

<span data-ttu-id="b54f2-120">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="b54f2-120">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="b54f2-121">评估[资源一致性工具链](toolchain.md)选项。</span><span class="sxs-lookup"><span data-stu-id="b54f2-121">Evaluate your [Resource Consistency toolchain](toolchain.md) options.</span></span>
* <span data-ttu-id="b54f2-122">了解云策略的授权要求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-122">Understand the licensing requirements for your cloud strategy.</span></span>
* <span data-ttu-id="b54f2-123">制定草拟的“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="b54f2-123">Develop a draft Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="b54f2-124">熟悉以组形式用于为解决方案部署、管理和监视所有资源的资源管理器。</span><span class="sxs-lookup"><span data-stu-id="b54f2-124">Become familiar with the resource manager you use to deploy, manage, and monitor all the resources for your solution as a group.</span></span>
* <span data-ttu-id="b54f2-125">培训受“体系结构指南”制定影响的人员和团队，并使其参与进来。</span><span class="sxs-lookup"><span data-stu-id="b54f2-125">Educate and involve the people and teams affected by the development of architecture guidelines.</span></span>
* <span data-ttu-id="b54f2-126">向迁移积压工作 (backlog) 添加优先资源部署任务。</span><span class="sxs-lookup"><span data-stu-id="b54f2-126">Add prioritized resource deployment tasks to your migration backlog.</span></span>

<span data-ttu-id="b54f2-127">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="b54f2-127">**Potential activities:**</span></span>

* <span data-ttu-id="b54f2-128">与业务利益干系人和/或云策略团队合作，整体了解业务单元和组织中所期望的云会计方法和成本会计做法。</span><span class="sxs-lookup"><span data-stu-id="b54f2-128">Work with the business stakeholders and/or your cloud strategy team to understand the desired cloud accounting approach and cost accounting practices within your business units and organization as a whole.</span></span>
* <span data-ttu-id="b54f2-129">定义[监视和策略实施](compliance-processes.md)要求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-129">Define your [monitoring and policy enforcement](compliance-processes.md) requirements.</span></span>
* <span data-ttu-id="b54f2-130">检查停机的业务价值和成本，以定义补救策略和 SLA 需求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-130">Examine the business value and cost of outage to define remediation policy and SLA requirements.</span></span>
* <span data-ttu-id="b54f2-131">确定是否为资源部署[简单工作负载](./governance-simple-workload.md)或[多团队](./governance-multiple-teams.md)治理策略。</span><span class="sxs-lookup"><span data-stu-id="b54f2-131">Determine whether you'll deploy a [simple workload](./governance-simple-workload.md) or [multi-team](./governance-multiple-teams.md) governance strategy for your resources.</span></span>
* <span data-ttu-id="b54f2-132">确定已规划工作负载的可伸缩性需求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-132">Determine scalability requirements for your planned workloads.</span></span>

## <a name="build-and-pre-deployment"></a><span data-ttu-id="b54f2-133">生成和预部署</span><span class="sxs-lookup"><span data-stu-id="b54f2-133">Build and pre-deployment</span></span>

<span data-ttu-id="b54f2-134">成功迁移环境需要许多技术和非技术先决条件。</span><span class="sxs-lookup"><span data-stu-id="b54f2-134">A number of technical and nontechnical prerequisites are required to successful migrate an environment.</span></span> <span data-ttu-id="b54f2-135">此过程侧重于推进迁移的决策、准备情况和核心基础结构。</span><span class="sxs-lookup"><span data-stu-id="b54f2-135">This process focuses on the decisions, readiness, and core infrastructure that proceeds a migration.</span></span>

<span data-ttu-id="b54f2-136">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="b54f2-136">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="b54f2-137">在预部署阶段推出，以实现[资源一致性工具链](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="b54f2-137">Implement your [Resource Consistency toolchain](toolchain.md) by rolling out in a pre-deployment phase.</span></span>
* <span data-ttu-id="b54f2-138">更新“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="b54f2-138">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="b54f2-139">在优先迁移积压工作 (backlog) 上实现资源部署任务。</span><span class="sxs-lookup"><span data-stu-id="b54f2-139">Implement resource deployment tasks on your prioritized migration backlog.</span></span>
* <span data-ttu-id="b54f2-140">制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用。</span><span class="sxs-lookup"><span data-stu-id="b54f2-140">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>

<span data-ttu-id="b54f2-141">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="b54f2-141">**Potential activities:**</span></span>

* <span data-ttu-id="b54f2-142">决定[订阅设计策略](../../decision-guides/subscriptions/overview.md)，选择最适合组织和工作负载需求的订阅模式。</span><span class="sxs-lookup"><span data-stu-id="b54f2-142">Decide on a [subscription design strategy](../../decision-guides/subscriptions/overview.md), choosing the subscription patterns that best fit your organization and workload needs.</span></span>
* <span data-ttu-id="b54f2-143">使用[资源一致性](../../decision-guides/resource-consistency/overview.md)策略，最终强制执行体系结构指南。</span><span class="sxs-lookup"><span data-stu-id="b54f2-143">Use a [resource consistency](../../decision-guides/resource-consistency/overview.md) strategy to enforce architecture guidelines over time.</span></span>
* <span data-ttu-id="b54f2-144">实现资源的[资源命名和标记标准](../../decision-guides/resource-tagging/overview.md)以便匹配组织需求和会计需求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-144">Implement [resource naming, and tagging standards](../../decision-guides/resource-tagging/overview.md) for your resources to match your organizational and accounting requirements.</span></span>
* <span data-ttu-id="b54f2-145">若要创建主动式时间点治理，请在部署资源和资源组时，使用部署模板和自动化来强制执行公共配置和一致的分组结构。</span><span class="sxs-lookup"><span data-stu-id="b54f2-145">To create proactive point-in-time governance, use deployment templates and automation to enforce common configurations and a consistent grouping structure when deploying resources and resource groups.</span></span>
* <span data-ttu-id="b54f2-146">建立最低特权的权限模型，其中默认用户没有任何权限。</span><span class="sxs-lookup"><span data-stu-id="b54f2-146">Establish a least privilege permissions model, where users have no permissions by default.</span></span>
* <span data-ttu-id="b54f2-147">确定组织中每个工作负载和帐户的所有者，以及需要访问以维护或修改这些资源的人员。</span><span class="sxs-lookup"><span data-stu-id="b54f2-147">Determine who in your organization owns each workload and account, and who will need to access to maintain or modify these resources.</span></span> <span data-ttu-id="b54f2-148">定义与这些需求相匹配的云角色和职责，并将这些角色用作访问控制的基础。</span><span class="sxs-lookup"><span data-stu-id="b54f2-148">Define cloud roles and responsibilities that match these needs and use these roles as the basis for access control.</span></span>
* <span data-ttu-id="b54f2-149">定义资源之间的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b54f2-149">Define dependencies between resources.</span></span>
* <span data-ttu-id="b54f2-150">实现自动资源缩放，以便匹配计划阶段中定义的需求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-150">Implement automated resource scaling to match requirements defined in the Plan stage.</span></span>
* <span data-ttu-id="b54f2-151">执行访问性能以衡量所接收服务的质量。</span><span class="sxs-lookup"><span data-stu-id="b54f2-151">Conduct access performance to measure the quality of services received.</span></span>
* <span data-ttu-id="b54f2-152">考虑使用配置设置和资源创建规则来部署管理 SLA 强制执行的[策略](/azure/governance/policy/overview)。</span><span class="sxs-lookup"><span data-stu-id="b54f2-152">Consider deploying [policy](/azure/governance/policy/overview) to manage SLA enforcement using configuration settings and resource creation rules.</span></span>

## <a name="adopt-and-migrate"></a><span data-ttu-id="b54f2-153">采用和迁移</span><span class="sxs-lookup"><span data-stu-id="b54f2-153">Adopt and migrate</span></span>

<span data-ttu-id="b54f2-154">迁移是一个渐进过程，侧重于现有数字资产中应用程序或工作负载的移动、测试和采用。</span><span class="sxs-lookup"><span data-stu-id="b54f2-154">Migration is an incremental process that focuses on the movement, testing, and adoption of applications or workloads in an existing digital estate.</span></span>

<span data-ttu-id="b54f2-155">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="b54f2-155">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="b54f2-156">将[资源一致性工具链](toolchain.md)从预部署迁移到生产环境。</span><span class="sxs-lookup"><span data-stu-id="b54f2-156">Migrate your [Resource Consistency toolchain](toolchain.md) from pre-deployment to production.</span></span>
* <span data-ttu-id="b54f2-157">更新“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="b54f2-157">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="b54f2-158">制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用。</span><span class="sxs-lookup"><span data-stu-id="b54f2-158">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>
* <span data-ttu-id="b54f2-159">迁移任何现有自动补救脚本或工具，以支持定义的 SLA 需求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-159">Migrate any existing automated remediation scripts or tools to support defined SLA requirements.</span></span>

<span data-ttu-id="b54f2-160">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="b54f2-160">**Potential activities:**</span></span>

* <span data-ttu-id="b54f2-161">完成并测试监视和报告数据。</span><span class="sxs-lookup"><span data-stu-id="b54f2-161">Complete and test monitoring and reporting data.</span></span> <span data-ttu-id="b54f2-162">使用所选的本地、云网关或混合解决方案。</span><span class="sxs-lookup"><span data-stu-id="b54f2-162">with your chosen on-premises, cloud gateway, or hybrid solution.</span></span>
* <span data-ttu-id="b54f2-163">确定是否需要更改资源的 SLA 或管理策略。</span><span class="sxs-lookup"><span data-stu-id="b54f2-163">Determine if changes need to be made to SLA or management policy for resources.</span></span>
* <span data-ttu-id="b54f2-164">实现查询功能以高效查找云资产上的资源，从而改进操作任务。</span><span class="sxs-lookup"><span data-stu-id="b54f2-164">Improve operations tasks by implementing query capabilities to efficiently find resource across your cloud estate.</span></span>
* <span data-ttu-id="b54f2-165">保持资源与不断变化的业务需求和治理需求一致。</span><span class="sxs-lookup"><span data-stu-id="b54f2-165">Align resources to changing business needs and governance requirements.</span></span>
* <span data-ttu-id="b54f2-166">确保虚拟机、虚拟网络和存储帐户反映每个版本的实际资源访问需求，并根据需要进行调整。</span><span class="sxs-lookup"><span data-stu-id="b54f2-166">Ensure that your virtual machines, virtual networks, and storage accounts reflect actual resource access needs during each release, and adjust as necessary.</span></span>
* <span data-ttu-id="b54f2-167">验证资源的自动缩放是否满足访问需求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-167">Verify automated scaling of resources meets access requirements.</span></span>
* <span data-ttu-id="b54f2-168">查看用户对资源、资源组和 Azure 订阅的访问，并根据需要调整访问控制。</span><span class="sxs-lookup"><span data-stu-id="b54f2-168">Review user access to resources, resource groups, and Azure subscriptions, and adjust access controls as necessary.</span></span>
* <span data-ttu-id="b54f2-169">监视资源访问计划的变化，并在需要进行额外签核时与利益干系人进行验证。</span><span class="sxs-lookup"><span data-stu-id="b54f2-169">Monitor changes in resource access plans and validate with stakeholders if additional sign-offs are needed.</span></span>
* <span data-ttu-id="b54f2-170">更新“体系结构指南”文档的更改以反映实际成本。</span><span class="sxs-lookup"><span data-stu-id="b54f2-170">Update changes to the Architecture Guidelines document to reflect actual costs.</span></span>
* <span data-ttu-id="b54f2-171">确定组织是否需要与业务单位的损益表保持更为清晰的财务一致性。</span><span class="sxs-lookup"><span data-stu-id="b54f2-171">Determine whether your organization requires clearer financial alignment to P&Ls for business units.</span></span>
* <span data-ttu-id="b54f2-172">对于全球性组织，实现 SLA 符合性或主权要求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-172">For global organizations, implement your SLA compliance or sovereignty requirements.</span></span>
* <span data-ttu-id="b54f2-173">对于云聚合，将网关解决方案部署到云提供商。</span><span class="sxs-lookup"><span data-stu-id="b54f2-173">For cloud aggregation, deploy a gateway solution to a cloud provider.</span></span>
* <span data-ttu-id="b54f2-174">对于不支持混合或网关选项的工具，将监视与操作监视工具紧密耦合。</span><span class="sxs-lookup"><span data-stu-id="b54f2-174">For tools that don't allow for hybrid or gateway options, tightly couple monitoring with an operational monitoring tool.</span></span>

## <a name="operate-and-post-implementation"></a><span data-ttu-id="b54f2-175">运营和实现后</span><span class="sxs-lookup"><span data-stu-id="b54f2-175">Operate and post-implementation</span></span>

<span data-ttu-id="b54f2-176">转换完成后，治理和运维必须依存于应用程序或工作负载的自然生命周期。</span><span class="sxs-lookup"><span data-stu-id="b54f2-176">Once the transformation is complete, governance and operations must live on for the natural lifecycle of an application or workload.</span></span> <span data-ttu-id="b54f2-177">治理成熟度这一阶段侧重于在实施解决方案并且转换周期开始稳定之后通常出现的活动。</span><span class="sxs-lookup"><span data-stu-id="b54f2-177">This phase of governance maturity focuses on the activities that commonly come after the solution is implemented and the transformation cycle begins to stabilize.</span></span>

<span data-ttu-id="b54f2-178">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="b54f2-178">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="b54f2-179">基于组织不断变化的“成本管理”需求更新，自定义[资源一致性工具链](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="b54f2-179">Customize your [Resource Consistency toolchain](toolchain.md) based on updates to your organization’s changing Cost Management needs.</span></span>
* <span data-ttu-id="b54f2-180">考虑自动化任何通知和报表以反映实际资源使用情况。</span><span class="sxs-lookup"><span data-stu-id="b54f2-180">Consider automating any notifications and reports to reflect actual resource usage.</span></span>
* <span data-ttu-id="b54f2-181">优化“体系结构指南”以指导未来的采用流程。</span><span class="sxs-lookup"><span data-stu-id="b54f2-181">Refine Architecture Guidelines to guide future adoption processes.</span></span>
* <span data-ttu-id="b54f2-182">定期培训受影响的团队，以确保持续遵守体系结构指南。</span><span class="sxs-lookup"><span data-stu-id="b54f2-182">Educate affected teams periodically to ensure ongoing adherence to the architecture guidelines.</span></span>

<span data-ttu-id="b54f2-183">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="b54f2-183">**Potential activities:**</span></span>

* <span data-ttu-id="b54f2-184">每季度调整计划以反映实际资源使用情况的变化。</span><span class="sxs-lookup"><span data-stu-id="b54f2-184">Adjust plans quarterly to reflect changes to actual resources.</span></span>
* <span data-ttu-id="b54f2-185">在未来的部署过程中，自动应用和强制执行治理需求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-185">Automatically apply and enforce governance requirements during future deployments.</span></span>
* <span data-ttu-id="b54f2-186">评估未充分利用的资源并确定其是否值得继续使用。</span><span class="sxs-lookup"><span data-stu-id="b54f2-186">Evaluate underused resources and determine if they're worth continuing.</span></span>
* <span data-ttu-id="b54f2-187">检测计划与实际资源使用情况之间的错位和异常。</span><span class="sxs-lookup"><span data-stu-id="b54f2-187">Detect misalignments and anomalies between planned and actual resource usage.</span></span>
* <span data-ttu-id="b54f2-188">协助云采用团队和云策略团队理解并解决这些异常。</span><span class="sxs-lookup"><span data-stu-id="b54f2-188">Assist the cloud adoption teams and the Cloud Strategy team in understanding and resolving these anomalies.</span></span>
* <span data-ttu-id="b54f2-189">确定是否需要更改帐单和 SLA 的资源一致性。</span><span class="sxs-lookup"><span data-stu-id="b54f2-189">Determine if changes need to be made to Resource Consistency for billing and SLAs.</span></span>
* <span data-ttu-id="b54f2-190">评估日志记录和监视工具，确定是否需要调整本地、云网关或混合解决方案。</span><span class="sxs-lookup"><span data-stu-id="b54f2-190">Evaluate logging and monitoring tools to determine whether your on-premises, cloud gateway, or hybrid solution needs adjusting.</span></span>
* <span data-ttu-id="b54f2-191">对于业务单元和地理分布式组，确定组织是否应考虑使用额外云管理功能（例如 [Azure 管理组](/azure/governance/management-groups/)）来更好地应用集中式策略并满足 SLA 需求。</span><span class="sxs-lookup"><span data-stu-id="b54f2-191">For business units and geographically distributed groups, determine if your organization should consider using additional cloud management features (for example [Azure management groups](/azure/governance/management-groups/)) to better apply centralized policy and meet SLA requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b54f2-192">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b54f2-192">Next steps</span></span>

<span data-ttu-id="b54f2-193">了解云资源治理的概念后，接下来请详细了解在准备学习如何设计[单个工作负载](governance-simple-workload.md)或[多个团队](governance-multiple-teams.md)的治理模型之前，在 Azure 中[如何管理资源访问](azure-resource-access.md)。</span><span class="sxs-lookup"><span data-stu-id="b54f2-193">Now that you understand the concept of cloud resource governance, move on to learn more about [how resource access is managed](azure-resource-access.md) in Azure in preparation for learning how to design a governance model for a [simple workload](governance-simple-workload.md) or for [multiple teams](governance-multiple-teams.md).</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="b54f2-194">[了解 Azure 中的资源访问](azure-resource-access.md)
> [了解 Azure 中的 SLA](https://azure.microsoft.com/support/legal/sla/)
> [了解日志记录、报告和监视](../../decision-guides/log-and-report/overview.md)</span><span class="sxs-lookup"><span data-stu-id="b54f2-194">[Learn about resource access in Azure](azure-resource-access.md)
[Learn about SLAs for Azure](https://azure.microsoft.com/support/legal/sla/)
[Learn about logging, reporting, and monitoring](../../decision-guides/log-and-report/overview.md)</span></span>
