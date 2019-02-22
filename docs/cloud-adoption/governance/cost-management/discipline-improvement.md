---
title: CAF：成本管理规则改进
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 成本管理规则改进
author: BrianBlanchard
ms.openlocfilehash: 34975d195a95b1a85ada96efe8c76a6138385ec1
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900666"
---
# <a name="cost-management-discipline-improvement"></a><span data-ttu-id="dbe1e-103">成本管理规则改进</span><span class="sxs-lookup"><span data-stu-id="dbe1e-103">Cost Management discipline improvement</span></span>

<span data-ttu-id="dbe1e-104">成本管理规则尝试解决与托管基于云的工作负荷时所产生的费用相关的核心业务风险。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-104">The Cost Management discipline attempts to address core business risks related to expenses incurred when hosting cloud-based workloads.</span></span> <span data-ttu-id="dbe1e-105">在云治理的五个规则中，成本管理涉及控制云资源的成本和使用，目标是创建和维护计划的成本周期。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-105">Within the five disciplines of Cloud Governance, Cost Management is involved in controlling cost and usage of cloud resources with the goal of creating and maintaining a planned cost cycle.</span></span>

<span data-ttu-id="dbe1e-106">本文概述了贵公司为制定成本管理规则并使其成熟而执行的潜在任务。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-106">This article outlines potential tasks your company perform to develop and mature your Cost Management discipline.</span></span> <span data-ttu-id="dbe1e-107">在实现云解决方案中，这些任务可分为计划、构建、采用和操作几个阶段，然后在允许开发[云治理增量方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)的基础上进行迭代。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-107">These tasks can be broken down into planning, building, adopting, and operating phases of implementing a cloud solution, which are then iterated on allowing the development of an [incremental approach to cloud governance](../journeys/overview.md#an-incremental-approach-to-cloud-governance).</span></span>

![采用的四个阶段](../../_images/adoption-phases.png)

<span data-ttu-id="dbe1e-109">*图 1.云治理增量方法的采用阶段。*</span><span class="sxs-lookup"><span data-stu-id="dbe1e-109">*Figure 1. Adoption phases of the incremental approach to cloud governance.*</span></span>

<span data-ttu-id="dbe1e-110">没有单个文档可以说明所有业务的要求。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-110">No single document can account for the requirements of all businesses.</span></span> <span data-ttu-id="dbe1e-111">因此，本文针对治理成熟过程的每个阶段只概述了建议的最小和可能的示例活动。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-111">As such, this article outlines suggested minimum and potential example activities for each phase of the governance maturation process.</span></span> <span data-ttu-id="dbe1e-112">这些活动的最初目标是帮助生成[策略 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，并为增量策略演化建立框架。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-112">The initial objective of these activities is to help you build a [Policy MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance) and establish a framework for incremental policy evolution.</span></span> <span data-ttu-id="dbe1e-113">云治理团队将需要决定在这些活动需要投入多少成本才能提高成本管理治理能力。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-113">Your Cloud Governance team will need to decide how much to invest in these activities to improve your Cost Management governance capabilities.</span></span>

> [!CAUTION]
> <span data-ttu-id="dbe1e-114">本文列出的最小活动或可能的活动都不针对特定的企业策略或第三方符合性要求。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-114">Neither the minimum or potential activities outlined in this article are aligned to specific corporate policies or third party compliance requirements.</span></span> <span data-ttu-id="dbe1e-115">本指南旨在帮助促进对话，从而使两个需求与云治理模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-115">This guidance is designed to help facilitate the conversations that will lead to alignment of both requirements with a cloud governance model.</span></span>

## <a name="planning-and-readiness"></a><span data-ttu-id="dbe1e-116">规划和准备情况</span><span class="sxs-lookup"><span data-stu-id="dbe1e-116">Planning and readiness</span></span>

<span data-ttu-id="dbe1e-117">这个治理成熟阶段弥合了业务结果与可操作策略之间的分歧。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-117">This phase of governance maturity bridges the divide between business outcomes and actionable strategies.</span></span> <span data-ttu-id="dbe1e-118">在此过程中，领导团队定义具体指标，将这些指标对应到数字资产，并开始规划整个迁移工作。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-118">During this process, the leadership team defines specific metrics, maps those metrics to the digital estate, and begins planning the overall migration effort.</span></span>

<span data-ttu-id="dbe1e-119">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="dbe1e-119">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="dbe1e-120">请评估你的[成本管理工具链](toolchain.md)选项。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-120">Evaluate your [Cost Management toolchain](toolchain.md) options.</span></span>
* <span data-ttu-id="dbe1e-121">制定草拟的“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-121">Develop a draft Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="dbe1e-122">培训受“体系结构指南”制定影响的人员和团队，并使其参与进来。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-122">Educate and involve the people and teams affected by the development of Architecture Guidelines.</span></span>

<span data-ttu-id="dbe1e-123">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="dbe1e-123">**Potential activities:**</span></span>

* <span data-ttu-id="dbe1e-124">确保支持云策略业务理由的预算决策。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-124">Ensure budgetary decisions that support the business justification for your cloud strategy.</span></span>
* <span data-ttu-id="dbe1e-125">验证用于报告资金成功分配情况的学习指标。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-125">Validate learning metrics that you use to report on the successful allocation of funding.</span></span>
* <span data-ttu-id="dbe1e-126">了解影响云成本应如何计算的所需云计算模型。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-126">Understand the desired cloud accounting model that affects how cloud costs should be accounted for.</span></span>
* <span data-ttu-id="dbe1e-127">熟悉数字资产计划并验证准确的成本预期。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-127">Become familiar with the digital estate plan and validate accurate costing expectations.</span></span>
* <span data-ttu-id="dbe1e-128">评估购买选项，以确定是“即用即付”更好，还是通过购买企业协议预先承诺更好。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-128">Evaluate buying options to determine if it's better to "pay as you go" or to make a precommitment by purchasing an Enterprise Agreement.</span></span>
* <span data-ttu-id="dbe1e-129">使业务目标与计划预算保持一致，并根据需要调整预算计划。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-129">Align business goals with planned budgets and adjust budgetary plans as necessary.</span></span>
* <span data-ttu-id="dbe1e-130">制定目标和预算报告机制，以便在每个成本周期结束时通知技术和业务利益干系人。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-130">Develop a goals and budget reporting mechanism to notify technical and business stakeholders at the end of each cost cycle.</span></span>

## <a name="build-and-pre-deployment"></a><span data-ttu-id="dbe1e-131">生成和预部署</span><span class="sxs-lookup"><span data-stu-id="dbe1e-131">Build and pre-deployment</span></span>

<span data-ttu-id="dbe1e-132">成功迁移环境需要许多技术和非技术先决条件。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-132">A number of technical and nontechnical prerequisites are required to successfully migrate an environment.</span></span> <span data-ttu-id="dbe1e-133">此过程侧重于推进迁移的决策、准备情况和核心基础结构。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-133">This process focuses on the decisions, readiness, and core infrastructure that proceeds a migration.</span></span>

<span data-ttu-id="dbe1e-134">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="dbe1e-134">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="dbe1e-135">在预部署阶段推出，以实现[成本管理工具链](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-135">Implement your [Cost Management toolchain](toolchain.md) by rolling out in a pre-deployment phase.</span></span>
* <span data-ttu-id="dbe1e-136">更新“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-136">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="dbe1e-137">制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-137">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>
* <span data-ttu-id="dbe1e-138">确定购买需求是否符合预算和目标。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-138">Determine if your purchase requirements align with your budgets and goals.</span></span>

<span data-ttu-id="dbe1e-139">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="dbe1e-139">**Potential activities:**</span></span>

* <span data-ttu-id="dbe1e-140">使预算计划与定义核心所有权模型的[订阅策略](../../decision-guides/subscriptions/overview.md)保持一致。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-140">Align your budgetary plans with the [Subscription Strategy](../../decision-guides/subscriptions/overview.md) that defines your core ownership model.</span></span>
* <span data-ttu-id="dbe1e-141">利用[资源一致性策略](../../decision-guides/resource-consistency/overview.md)，最终强制执行体系结构和成本指南。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-141">Leverage the [Resource Consistency Strategy](../../decision-guides/resource-consistency/overview.md) to enforce architecture and cost guidelines over time.</span></span>
* <span data-ttu-id="dbe1e-142">确定是否存在影响采用和迁移计划的任何成本异常。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-142">Determine if there are any cost anomalies that affect your adoption and migration plans.</span></span>

## <a name="adopt-and-migrate"></a><span data-ttu-id="dbe1e-143">采用和迁移</span><span class="sxs-lookup"><span data-stu-id="dbe1e-143">Adopt and migrate</span></span>

<span data-ttu-id="dbe1e-144">迁移是一个渐进过程，侧重于现有数字资产中应用程序或工作负载的移动、测试和采用。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-144">Migration is an incremental process that focuses on the movement, testing, and adoption of applications or workloads in an existing digital estate.</span></span>

<span data-ttu-id="dbe1e-145">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="dbe1e-145">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="dbe1e-146">将[成本管理工具链](toolchain.md)从预部署迁移到生产环境。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-146">Migrate your [Cost Management toolchain](toolchain.md) from pre-deployment to production.</span></span>
* <span data-ttu-id="dbe1e-147">更新“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-147">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="dbe1e-148">制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-148">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>

<span data-ttu-id="dbe1e-149">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="dbe1e-149">**Potential activities:**</span></span>

* <span data-ttu-id="dbe1e-150">实现云计帐模型。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-150">Implement your Cloud Accounting Model.</span></span>
* <span data-ttu-id="dbe1e-151">确保预算反映每次发布期间的实际支出，并根据需要进行调整。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-151">Ensure that your budgets reflect your actual spending during each release and adjust as necessary.</span></span>
* <span data-ttu-id="dbe1e-152">监视预算计划的变化，并在需要进行额外签核时与利益干系人进行验证。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-152">Monitor changes in budgetary plans and validate with stakeholders if additional sign-offs are needed.</span></span>
* <span data-ttu-id="dbe1e-153">更新“体系结构指南”文档的更改以反映实际成本。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-153">Update changes to the Architecture Guidelines document to reflect actual costs.</span></span>

## <a name="operate-and-post-implementation"></a><span data-ttu-id="dbe1e-154">运营和实现后</span><span class="sxs-lookup"><span data-stu-id="dbe1e-154">Operate and post-implementation</span></span>

<span data-ttu-id="dbe1e-155">转换完成后，治理和操作必须依存于应用程序或工作负载的自然生命周期。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-155">Once the transformation is complete, governance and operations must live on for the natural lifecycle of an application or workload.</span></span> <span data-ttu-id="dbe1e-156">治理成熟度的这一阶段侧重于在实现解决方案并且转换周期开始稳定之后通常会出现的活动。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-156">This phase of governance maturity focuses on the activities that commonly come after the solution is implemented and the transformation cycle begins to stabilize.</span></span>

<span data-ttu-id="dbe1e-157">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="dbe1e-157">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="dbe1e-158">根据组织成本管理需求的变化，自定义[成本管理工具链](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-158">Customize your [Cost Management toolchain](toolchain.md) based on changes in your organization’s cost management needs.</span></span>
* <span data-ttu-id="dbe1e-159">考虑自动化任何通知和报告以反映实际支出。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-159">Consider automating any notifications and reports to reflect actual spending.</span></span>
* <span data-ttu-id="dbe1e-160">优化“体系结构指南”以指导未来的采用流程。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-160">Refine Architecture Guidelines to guide future adoption processes.</span></span>
* <span data-ttu-id="dbe1e-161">定期对受影响的团队进行培训，以确保持续遵守“体系结构指南”。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-161">Educate affected teams on a periodic basis to ensure ongoing adherence to the Architecture Guidelines.</span></span>

<span data-ttu-id="dbe1e-162">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="dbe1e-162">**Potential activities:**</span></span>

* <span data-ttu-id="dbe1e-163">执行季度云业务审核，以传达交付给业务的价值和相关成本。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-163">Execute a quarterly cloud business review to communicate value delivered to the business and associated costs.</span></span>
* <span data-ttu-id="dbe1e-164">每季度调整计划以反映实际支出的变化。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-164">Adjust plans quarterly to reflect changes to actual spending.</span></span>
* <span data-ttu-id="dbe1e-165">确定财务与业务单位订阅的损益表相一致。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-165">Determine financial alignment to P&Ls for business unit subscriptions.</span></span>
* <span data-ttu-id="dbe1e-166">每月分析利益干系人价值和成本报告方法。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-166">Analyze stakeholder value and cost reporting methods on a monthly basis.</span></span>
* <span data-ttu-id="dbe1e-167">修复未充分利用的资产并确定它们是否值得继续使用。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-167">Remediate underused assets and determine if they're worth continuing.</span></span>
* <span data-ttu-id="dbe1e-168">检测计划与实际支出之间的错位和异常。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-168">Detect misalignments and anomalies between the plan and actual spending.</span></span>
* <span data-ttu-id="dbe1e-169">协助云采用团队和云战略团队理解并解决这些异常现象。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-169">Assist the cloud adoption teams and the Cloud Strategy team with understanding and resolving these anomalies.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dbe1e-170">后续步骤</span><span class="sxs-lookup"><span data-stu-id="dbe1e-170">Next steps</span></span>

<span data-ttu-id="dbe1e-171">现你已了解云标识治理的概念，请检查[成本管理工具链](toolchain.md)，以确定在 Azure 平台上开发成本管理治理规则时所需的 Azure 工具和功能。</span><span class="sxs-lookup"><span data-stu-id="dbe1e-171">Now that you understand the concept of cloud identity governance, examine the [Cost Management toolchain](toolchain.md) to identify Azure tools and features that you'll need when developing the Cost Management governance discipline on the Azure platform.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="dbe1e-172">Azure 的成本管理工具链</span><span class="sxs-lookup"><span data-stu-id="dbe1e-172">Cost Management toolchain for Azure</span></span>](toolchain.md)