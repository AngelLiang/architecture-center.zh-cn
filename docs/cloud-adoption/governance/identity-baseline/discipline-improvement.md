---
title: CAF：标识基线规则改进
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 标识基线规则改进
author: BrianBlanchard
ms.openlocfilehash: c96a638af549782fec22b2068c9b4943df4b943a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243248"
---
# <a name="identity-baseline-discipline-improvement"></a><span data-ttu-id="1d4b0-103">标识基线规则改进</span><span class="sxs-lookup"><span data-stu-id="1d4b0-103">Identity Baseline discipline improvement</span></span>

<span data-ttu-id="1d4b0-104">标识基线规则侧重于建立策略的方法，以确保用户标识的一致性和连续性，而不考虑托管应用程序或工作负载的云提供商。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-104">The Identity Baseline discipline focuses on ways of establishing policies that ensure consistency and continuity of user identities regardless of the cloud provider that hosts the application or workload.</span></span> <span data-ttu-id="1d4b0-105">在云治理的五个规则中，标识基线包括有关[混合标识策略](../../decision-guides/identity/overview.md)的决策、标识存储库的评估和扩展、单一登录（相同登录）的实现、对未经授权使用或恶意参与者的审核和监控。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-105">Within the Five Disciplines of Cloud Governance, Identity Baseline includes decisions regarding the [Hybrid Identity Strategy](../../decision-guides/identity/overview.md), evaluation and extension of identity repositories, implementation of single sign-on (same sign-on), auditing and monitoring for unauthorized use or malicious actors.</span></span> <span data-ttu-id="1d4b0-106">在某些情况下，还可能涉及到对多个标识提供者进行现代化、合并或集成的决策。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-106">In some cases, it may also involve decisions to modernize, consolidate, or integrate multiple identity providers.</span></span>

<span data-ttu-id="1d4b0-107">本文概述了贵公司可以参与以更好制定和完善标识基线规则的一些潜在任务。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-107">This article outlines some potential tasks your company can engage in to better develop and mature the Identity Baseline discipline.</span></span> <span data-ttu-id="1d4b0-108">在实现云解决方案中，这些任务可分为计划、构建、采用和操作几个阶段，然后在允许开发[云治理增量方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)的基础上进行迭代。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-108">These tasks can be broken down into planning, building, adopting, and operating phases of implementing a cloud solution, which are then iterated on allowing the development of an [incremental approach to cloud governance](../journeys/overview.md#an-incremental-approach-to-cloud-governance).</span></span>

![采用的四个阶段](../../_images/adoption-phases.png)

<span data-ttu-id="1d4b0-110">*图 1.云治理增量方法的采用阶段。*</span><span class="sxs-lookup"><span data-stu-id="1d4b0-110">*Figure 1. Adoption phases of the incremental approach to cloud governance.*</span></span>

<span data-ttu-id="1d4b0-111">单个文档很难涵盖所有的业务需求。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-111">It's impossible for any one document to account for the requirements of all businesses.</span></span> <span data-ttu-id="1d4b0-112">因此，本文针对治理成熟过程的每个阶段只概述了建议的最小和可能的示例活动。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-112">As such, this article outlines suggested minimum and potential example activities for each phase of the governance maturation process.</span></span> <span data-ttu-id="1d4b0-113">这些活动的初衷是帮助生成[策略 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，并为增量策略演化建立框架。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-113">The initial objective of these activities is to help you build a [Policy MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance) and establish a framework for incremental policy evolution.</span></span> <span data-ttu-id="1d4b0-114">云治理团队将需要决定在这些活动需要投入多少成本才能提高标识基线治理能力。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-114">Your Cloud Governance team will need to decide how much to invest in these activities to improve your Identity Baseline governance capabilities.</span></span>

> [!CAUTION]
> <span data-ttu-id="1d4b0-115">本文列出的最小活动或可能的活动都不针对特定的企业策略或第三方合规性要求。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-115">Neither the minimum or potential activities outlined in this article are aligned to specific corporate policies or third party compliance requirements.</span></span> <span data-ttu-id="1d4b0-116">本指南旨在帮助促进对话，使两个需求与云治理模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-116">This guidance is designed to help facilitate the conversations that will lead to alignment of both requirements with a cloud governance model.</span></span>

## <a name="planning-and-readiness"></a><span data-ttu-id="1d4b0-117">规划和准备情况</span><span class="sxs-lookup"><span data-stu-id="1d4b0-117">Planning and readiness</span></span>

<span data-ttu-id="1d4b0-118">这个治理成熟阶段弥合了业务结果与可操作策略之间的分歧。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-118">This phase of governance maturity bridges the divide between business outcomes and actionable strategies.</span></span> <span data-ttu-id="1d4b0-119">在此过程中，领导团队定义具体指标，将这些指标对应到数字资产，并开始规划整个迁移工作。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-119">During this process, the leadership team defines specific metrics, maps those metrics to the digital estate, and begins planning the overall migration effort.</span></span>

<span data-ttu-id="1d4b0-120">最小建议的活动：</span><span class="sxs-lookup"><span data-stu-id="1d4b0-120">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="1d4b0-121">评估[标识工具链](toolchain.md)选项并实现适用于组织的混合策略。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-121">Evaluate your [Identity toolchain](toolchain.md) options and implement a hybrid strategy that is appropriate to your organization.</span></span>
* <span data-ttu-id="1d4b0-122">制定草拟的“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-122">Develop a draft Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="1d4b0-123">培训受“体系结构指南”制定影响的人员和团队，并使其参与进来。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-123">Educate and involve the people and teams affected by the development of architecture guidelines.</span></span>

<span data-ttu-id="1d4b0-124">可能的活动：</span><span class="sxs-lookup"><span data-stu-id="1d4b0-124">**Potential activities:**</span></span>

* <span data-ttu-id="1d4b0-125">定义将控制云中标识和访问管理的角色和工作。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-125">Define roles and assignments that will govern identity and access management in the cloud.</span></span>
* <span data-ttu-id="1d4b0-126">定义本地组并映射到相应的基于云的角色。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-126">Define your on-premises groups and map to corresponding cloud-based roles.</span></span>
* <span data-ttu-id="1d4b0-127">清单标识提供者（包括自定义应用程序使用的数据库驱动的标识）。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-127">Inventory identity providers (including database-driven identities used by custom applications).</span></span>
* <span data-ttu-id="1d4b0-128">在存在重复的情况下，考虑采用合并或集成标识提供者的选项，以简化整体标识解决方案。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-128">Consider options for consolidation or integration of identity providers where duplication exists, to simplify the overall identity solution.</span></span>
* <span data-ttu-id="1d4b0-129">评估现有标识提供者的混合兼容性。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-129">Evaluate hybrid compatibility of existing identity providers.</span></span>
* <span data-ttu-id="1d4b0-130">对于非混合兼容的标识提供者，评估合并或替换选项。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-130">For identity providers that are not hybrid compatible, evaluate consolidation or replacement options.</span></span>

## <a name="build-and-pre-deployment"></a><span data-ttu-id="1d4b0-131">生成和预部署</span><span class="sxs-lookup"><span data-stu-id="1d4b0-131">Build and pre-deployment</span></span>

<span data-ttu-id="1d4b0-132">成功迁移环境需要许多技术和非技术性的先决条件。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-132">A number of technical and nontechnical prerequisites are required to successfully migrate an environment.</span></span> <span data-ttu-id="1d4b0-133">此过程侧重于推进迁移的决策、准备情况和核心基础结构。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-133">This process focuses on the decisions, readiness, and core infrastructure that proceeds a migration.</span></span>

<span data-ttu-id="1d4b0-134">最小建议的活动：</span><span class="sxs-lookup"><span data-stu-id="1d4b0-134">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="1d4b0-135">在实现[标识工具链](toolchain.md)之前，建议进行一个试点测试，确保它尽可能的简化用户体验。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-135">Consider a pilot test before implementing your [Identity toolchain](toolchain.md), making sure it simplifies the user experience as much as possible.</span></span>
* <span data-ttu-id="1d4b0-136">然后，将来自试点测试的反馈应用到预部署中。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-136">Apply feedback from pilot tests into the pre-deployment.</span></span> <span data-ttu-id="1d4b0-137">重复此操作，直到结果可接受。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-137">Repeat until results are acceptable.</span></span>
* <span data-ttu-id="1d4b0-138">更新“体系结构指南”文档，以包含部署和用户采用计划，并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-138">Update the Architecture Guidelines document to include deployment and user adoption plans, and distribute to key stakeholders.</span></span>
* <span data-ttu-id="1d4b0-139">考虑建立一个早期采用者计划，并向限定数量的用户推出。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-139">Consider establishing an early adopter program and rolling out to a limited number of users.</span></span>
* <span data-ttu-id="1d4b0-140">持续对受体系结构指导规则影响最大的人员和团队进行培训。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-140">Continue to educate the people and teams most affected by the architecture guidelines.</span></span>

<span data-ttu-id="1d4b0-141">可能的活动：</span><span class="sxs-lookup"><span data-stu-id="1d4b0-141">**Potential activities:**</span></span>

* <span data-ttu-id="1d4b0-142">评估逻辑和物理体系结构，并确定[混合标识策略](../../decision-guides/identity/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-142">Evaluate your logical and physical architecture and determine a [Hybrid Identity Strategy](../../decision-guides/identity/overview.md).</span></span>
* <span data-ttu-id="1d4b0-143">映射标识访问管理策略（如登录 ID 分配），并为 Azure AD 选择适当的身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-143">Map identity access management policies, such as login ID assignments, and choose the appropriate authentication method for Azure AD.</span></span>
  * <span data-ttu-id="1d4b0-144">如果是联合身份验证，则为管理帐户启用租户限制。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-144">If federated, enable tenant restrictions for administrative accounts.</span></span>
* <span data-ttu-id="1d4b0-145">集成本地和云目录。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-145">Integrate your on-premises and cloud directories.</span></span>
* <span data-ttu-id="1d4b0-146">请考虑使用以下访问模型：</span><span class="sxs-lookup"><span data-stu-id="1d4b0-146">Consider using the following access models:</span></span>
  * <span data-ttu-id="1d4b0-147">[最低访问权限](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)模型</span><span class="sxs-lookup"><span data-stu-id="1d4b0-147">[Least Privilege Access](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) model</span></span>
  * <span data-ttu-id="1d4b0-148">[特权标识基线](/azure/active-directory/privileged-identity-management/pim-configure)访问模型</span><span class="sxs-lookup"><span data-stu-id="1d4b0-148">[Privileged Identity Baseline](/azure/active-directory/privileged-identity-management/pim-configure) access model</span></span>
* <span data-ttu-id="1d4b0-149">完成所有预集成详细信息并查看[标识最佳做法](/azure/security/azure-security-identity-management-best-practices)。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-149">Finalize all pre-integration details and review [Identity Best Practices](/azure/security/azure-security-identity-management-best-practices).</span></span>
  * <span data-ttu-id="1d4b0-150">启用单一标识、单一登录 (SSO) 或无缝 SSO</span><span class="sxs-lookup"><span data-stu-id="1d4b0-150">Enable single identity, single sign-on (SSO), or seamless SSO</span></span>
  * <span data-ttu-id="1d4b0-151">为管理员配置多重身份验证 (MFA)</span><span class="sxs-lookup"><span data-stu-id="1d4b0-151">Configure multi-factor authentication (MFA) for administrators</span></span>
  * <span data-ttu-id="1d4b0-152">必要时合并或集成标识提供者</span><span class="sxs-lookup"><span data-stu-id="1d4b0-152">Consolidate or integrate identity providers, where necessary</span></span>
  * <span data-ttu-id="1d4b0-153">实现集中管理标识所需的工具</span><span class="sxs-lookup"><span data-stu-id="1d4b0-153">Implement tooling necessary to centralize management of identities</span></span>
  * <span data-ttu-id="1d4b0-154">启用实时 (JIT) 访问和角色更改警报</span><span class="sxs-lookup"><span data-stu-id="1d4b0-154">Enable just-in-time (JIT) access and role change alerting</span></span>
  * <span data-ttu-id="1d4b0-155">对分配给内置角色的关键管理活动进行风险分析</span><span class="sxs-lookup"><span data-stu-id="1d4b0-155">Conduct a risk analysis of key admin activities for assigning to built-in roles</span></span>
  * <span data-ttu-id="1d4b0-156">考虑为所有用户更新更强的身份验证</span><span class="sxs-lookup"><span data-stu-id="1d4b0-156">Consider an updated rollout of stronger authentication for all users</span></span>
  * <span data-ttu-id="1d4b0-157">为其他管理角色启用 JIT（使用有时间限制的激活）的特权标识基线 (PIM)</span><span class="sxs-lookup"><span data-stu-id="1d4b0-157">Enable Privileged Identity Baseline (PIM) for JIT (using time-limited activation) for additional administrative roles</span></span>
  * <span data-ttu-id="1d4b0-158">将用户帐户与全局管理员帐户分开（确保管理员不会无意中打开电子邮件或运行与其全局管理员帐户关联的程序）</span><span class="sxs-lookup"><span data-stu-id="1d4b0-158">Separate user accounts from Global admin accounts (to make sure that administrators do not inadvertently open emails or run programs associated with their Global admin accounts)</span></span>

## <a name="adopt-and-migrate"></a><span data-ttu-id="1d4b0-159">采用和迁移</span><span class="sxs-lookup"><span data-stu-id="1d4b0-159">Adopt and migrate</span></span>

<span data-ttu-id="1d4b0-160">迁移是一个渐进过程，侧重于现有数字资产中应用程序或工作负载的移动、测试和采用。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-160">Migration is an incremental process that focuses on the movement, testing, and adoption of applications or workloads in an existing digital estate.</span></span>

<span data-ttu-id="1d4b0-161">最小建议的活动：</span><span class="sxs-lookup"><span data-stu-id="1d4b0-161">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="1d4b0-162">将[标识工具链](toolchain.md)从开发迁移到生产环境。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-162">Migrate your [Identity toolchain](toolchain.md) from development to production.</span></span>
* <span data-ttu-id="1d4b0-163">更新“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-163">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
* <span data-ttu-id="1d4b0-164">制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-164">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>

<span data-ttu-id="1d4b0-165">可能的活动：</span><span class="sxs-lookup"><span data-stu-id="1d4b0-165">**Potential activities:**</span></span>

* <span data-ttu-id="1d4b0-166">验证在生成/预部署阶段定义的最佳做法是否正确执行。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-166">Validate that the best practices defined during the Build / Pre-deployment phases are properly executed.</span></span>
* <span data-ttu-id="1d4b0-167">验证和/或改进[混合标识策略](../../decision-guides/identity/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-167">Validate and/or refine your [Hybrid Identity Strategy](../../decision-guides/identity/overview.md).</span></span>
* <span data-ttu-id="1d4b0-168">确保每个应用程序或工作负载继续与发布前的标识策略保持一致。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-168">Ensure that each application or workload continues to align with the identity strategy before release.</span></span>
* <span data-ttu-id="1d4b0-169">验证应用程序的单一登录 (SSO) 和无缝 SSO 是否正常运行。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-169">Validate that single sign-on (SSO) and seamless SSO is working as expected for your applications.</span></span>
* <span data-ttu-id="1d4b0-170">尽可能减少或消除替代标识存储的数量。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-170">Reduce or eliminate the number of alternative identity stores, when possible.</span></span>
* <span data-ttu-id="1d4b0-171">仔细检查任何应用内或数据库内标识存储的需求。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-171">Scrutinize the need for any in-app or in-database identity stores.</span></span> <span data-ttu-id="1d4b0-172">不属于正确标识提供者（第一方或第三方）的标识可能会给应用程序和用户带来风险。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-172">Identities that fall outside of a proper identity provider (first-party or third-party) can represent risk to the application and the users.</span></span>
* <span data-ttu-id="1d4b0-173">启用对[本地联合应用程序](/azure/active-directory/active-directory-device-registration-on-premises-setup)的条件访问。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-173">Enable conditional access for [on-premises federated applications](/azure/active-directory/active-directory-device-registration-on-premises-setup).</span></span>
* <span data-ttu-id="1d4b0-174">在多个中心跨全局区域分布标识，并在区域之间同步。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-174">Distribute identity across global regions in multiple hubs with synchronization between regions.</span></span>
* <span data-ttu-id="1d4b0-175">建立中央基于角色的访问控制 (RBAC) 联合。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-175">Establish central role-based access control (RBAC) federation.</span></span>

## <a name="operate-and-post-implementation"></a><span data-ttu-id="1d4b0-176">运营和实施后</span><span class="sxs-lookup"><span data-stu-id="1d4b0-176">Operate and post-implementation</span></span>

<span data-ttu-id="1d4b0-177">转换完成后，治理和运维必须依存于应用程序或工作负载的自然生命周期。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-177">Once the transformation is complete, governance and operations must live on for the natural lifecycle of an application or workload.</span></span> <span data-ttu-id="1d4b0-178">治理成熟度这一阶段侧重于在实施解决方案并且转换周期开始稳定之后通常出现的活动。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-178">This phase of governance maturity focuses on the activities that commonly come after the solution is implemented and the transformation cycle begins to stabilize.</span></span>

<span data-ttu-id="1d4b0-179">最小建议的活动：</span><span class="sxs-lookup"><span data-stu-id="1d4b0-179">**Minimum suggested activities:**</span></span>

* <span data-ttu-id="1d4b0-180">根据组织不断变化的标识需求的变更，自定义你的[标识基线工具链](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-180">Customize your [Identity Baseline toolchain](toolchain.md) based on changes to your organization’s changing identity needs.</span></span>
* <span data-ttu-id="1d4b0-181">自动化通知和报告，提醒潜在的恶意威胁。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-181">Automate notifications and reports to alert you of potential malicious threats.</span></span>
* <span data-ttu-id="1d4b0-182">监控和报告系统使用情况和用户采用进度。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-182">Monitor and report on system usage and user adoption progress.</span></span>
* <span data-ttu-id="1d4b0-183">报告部署后的指标并分发给利益干系人。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-183">Report on post-deployment metrics and distribute to stakeholders.</span></span>
* <span data-ttu-id="1d4b0-184">优化体系结构指南以指导未来的采用流程。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-184">Refine the Architecture Guidelines to guide future adoption processes.</span></span>
* <span data-ttu-id="1d4b0-185">定期与受影响的团队进行沟通并持续培训，确保持续遵守体系结构指南。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-185">Communicate and continually educate the affected teams on a periodic basis to ensure ongoing adherence to architecture guidelines.</span></span>

<span data-ttu-id="1d4b0-186">可能的活动：</span><span class="sxs-lookup"><span data-stu-id="1d4b0-186">**Potential activities:**</span></span>

* <span data-ttu-id="1d4b0-187">对标识策略和遵循实践进行定期审核。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-187">Conduct periodic audits of identity policies and adherence practices.</span></span>
* <span data-ttu-id="1d4b0-188">定期扫描恶意参与者和数据泄露，特别是那些与标识欺诈相关的行为，如潜在管理员帐户接管。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-188">Scan for malicious actors and data breaches regularly, particularly those related to identity fraud, such as potential admin account takeovers.</span></span>
* <span data-ttu-id="1d4b0-189">配置监控和报告工具。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-189">Configure a monitoring and reporting tool.</span></span>
* <span data-ttu-id="1d4b0-190">考虑更紧密地与安全和防欺诈系统集成。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-190">Consider integrating more closely with security and fraud-prevention systems.</span></span>
* <span data-ttu-id="1d4b0-191">定期查看提升的用户或角色的访问权限。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-191">Regularly review access rights for elevated users or roles.</span></span>
  * <span data-ttu-id="1d4b0-192">确定每个有资格激活管理权限的用户。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-192">Identify every user who is eligible to activate admin privilege.</span></span>
* <span data-ttu-id="1d4b0-193">评审载入、调动和凭据更新流程。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-193">Review on-boarding, off-boarding, and credential update processes.</span></span>
* <span data-ttu-id="1d4b0-194">调查标识访问管理 (IAM) 模块之间自动化和通信级别的提升。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-194">Investigate increasing levels of automation and communication between identity access management (IAM) modules.</span></span>
* <span data-ttu-id="1d4b0-195">考虑实施开发安全操作 (DevSecOps) 方法。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-195">Consider implementing a development security operations (DevSecOps) approach.</span></span>
* <span data-ttu-id="1d4b0-196">执行影响分析，以评估成本、安全性和用户采用的结果。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-196">Carry out an impact analysis to gauge results on costs, security, and user adoption.</span></span>
* <span data-ttu-id="1d4b0-197">定期生成影响报告，以显示系统创建的指标变化，并评估[混合标识策略](../../decision-guides/identity/overview.md)的业务影响。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-197">Periodically produce an impact report that shows the changes in metrics created by the system and estimate the business impacts of the [Hybrid Identity Strategy](../../decision-guides/identity/overview.md).</span></span>
* <span data-ttu-id="1d4b0-198">建立 [Azure 安全中心](/azure/security-center/security-center-intro)推荐的集成监控。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-198">Establish integrated monitoring recommended by the [Azure Security Center](/azure/security-center/security-center-intro).</span></span>

## <a name="next-steps"></a><span data-ttu-id="1d4b0-199">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1d4b0-199">Next steps</span></span>

<span data-ttu-id="1d4b0-200">至此，你已了解云标识治理的概念，接下来，请检查[标识基线工具链](toolchain.md)，确定在 Azure 平台上开发标识基线治理规则时所需的 Azure 工具和功能。</span><span class="sxs-lookup"><span data-stu-id="1d4b0-200">Now that you understand the concept of cloud identity governance, examine the [Identity Baseline toolchain](toolchain.md) to identify Azure tools and features that you'll need when developing the Identity Baseline governance discipline on the Azure platform.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1d4b0-201">适用于 Azure 的标识基线工具链</span><span class="sxs-lookup"><span data-stu-id="1d4b0-201">Identity Baseline toolchain for Azure</span></span>](toolchain.md)