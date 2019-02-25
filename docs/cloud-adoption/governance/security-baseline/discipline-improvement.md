---
title: CAF：安全基线规则改进
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 安全基线规则改进
author: BrianBlanchard
ms.openlocfilehash: 28a971f56c9f8ada1d184bdc1cb3dbb9a17c3507
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900601"
---
# <a name="security-baseline-discipline-improvement"></a><span data-ttu-id="9d235-103">安全基线规则改进</span><span class="sxs-lookup"><span data-stu-id="9d235-103">Security Baseline discipline improvement</span></span>

<span data-ttu-id="9d235-104">安全基线规则重点围绕创建策略的方法，以保护网络、资产及将位于云提供商解决方案的数据（此内容最为重要）。</span><span class="sxs-lookup"><span data-stu-id="9d235-104">The Security Baseline discipline focuses on ways of establishing policies that protect the network, assets, and most importantly the data that will reside on a Cloud Provider's solution.</span></span> <span data-ttu-id="9d235-105">在云治理的五个规则中，安全基线包括数字资产和数据的加密。</span><span class="sxs-lookup"><span data-stu-id="9d235-105">Within the five disciplines of Cloud Governance, Security Baseline includes classification of the digital estate and data.</span></span> <span data-ttu-id="9d235-106">此外，它还包括对风险、业务容错以及数据、资产和网络安全性的相关缓解策略的说明。</span><span class="sxs-lookup"><span data-stu-id="9d235-106">It also includes documentation of risks, business tolerance, and mitigation strategies associated with the security of the data, assets, and network.</span></span> <span data-ttu-id="9d235-107">从专业角度而言，它还涉及[加密](../../decision-guides/encryption/overview.md)决策、[网络要求](../../decision-guides/software-defined-network/overview.md)、[混合标识策略](../../decision-guides/identity/overview.md)和用于制定云安全基线策略的[流程](compliance-processes.md)。</span><span class="sxs-lookup"><span data-stu-id="9d235-107">From a technical perspective, this also includes involvement in decisions regarding [encryption](../../decision-guides/encryption/overview.md), [network requirements](../../decision-guides/software-defined-network/overview.md), [hybrid identity strategies](../../decision-guides/identity/overview.md), and the [processes](compliance-processes.md) used to develop cloud Security Baseline policies.</span></span>

<span data-ttu-id="9d235-108">本文概述了贵公司可以参与以更好制定和完善安全基线原则的一些潜在任务。</span><span class="sxs-lookup"><span data-stu-id="9d235-108">This article outlines some potential tasks your company can engage in to better develop and mature the Security Baseline discipline.</span></span> <span data-ttu-id="9d235-109">在实现云解决方案中，这些任务可分为计划、构建、采用和操作几个阶段，然后在允许开发[云治理增量方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)的基础上进行迭代。</span><span class="sxs-lookup"><span data-stu-id="9d235-109">These tasks can be broken down into planning, building, adopting, and operating phases of implementing a cloud solution, which are then iterated on allowing the development of an [incremental approach to cloud governance](../journeys/overview.md#an-incremental-approach-to-cloud-governance).</span></span>

![采用的四个阶段](../../_images/adoption-phases.png)

<span data-ttu-id="9d235-111">*图 1.云治理增量方法的采用阶段。*</span><span class="sxs-lookup"><span data-stu-id="9d235-111">*Figure 1. Adoption phases of the incremental approach to cloud governance.*</span></span>

<span data-ttu-id="9d235-112">任何一个文档都不可能考虑到所有业务的需求。</span><span class="sxs-lookup"><span data-stu-id="9d235-112">It's impossible for any one document to account for the requirements of all businesses.</span></span> <span data-ttu-id="9d235-113">因此，本文针对治理成熟过程的每个阶段只概述了建议的最小和可能的示例活动。</span><span class="sxs-lookup"><span data-stu-id="9d235-113">As such, this article outlines suggested minimum and potential example activities for each phase of the governance maturation process.</span></span> <span data-ttu-id="9d235-114">这些活动的最初目标是帮助生成[策略 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，并为增量策略演化建立框架。</span><span class="sxs-lookup"><span data-stu-id="9d235-114">The initial objective of these activities is to help you build a [Policy MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance) and establish a framework for incremental policy evolution.</span></span> <span data-ttu-id="9d235-115">云治理团队将需要决定在这些活动需要投入多少成本才能提高安全基线治理能力。</span><span class="sxs-lookup"><span data-stu-id="9d235-115">Your Cloud Governance team will need to decide how much to invest in these activities to improve your Security Baseline governance capabilities.</span></span>

> [!CAUTION]
> <span data-ttu-id="9d235-116">本文列出的最小活动或可能的活动都不针对特定的企业策略或第三方符合性要求。</span><span class="sxs-lookup"><span data-stu-id="9d235-116">Neither the minimum or potential activities outlined in this article are aligned to specific corporate policies or third party compliance requirements.</span></span> <span data-ttu-id="9d235-117">本指南旨在帮助促进对话，从而使两个需求与云治理模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="9d235-117">This guidance is designed to help facilitate the conversations that will lead to alignment of both requirements with a cloud governance model.</span></span>

## <a name="planning-and-readiness"></a><span data-ttu-id="9d235-118">规划和准备情况</span><span class="sxs-lookup"><span data-stu-id="9d235-118">Planning and readiness</span></span>

<span data-ttu-id="9d235-119">这个治理成熟阶段弥合了业务结果与可操作策略之间的分歧。</span><span class="sxs-lookup"><span data-stu-id="9d235-119">This phase of governance maturity bridges the divide between business outcomes and actionable strategies.</span></span> <span data-ttu-id="9d235-120">在此过程中，领导团队定义具体指标，将这些指标对应到数字资产，并开始规划整个迁移工作。</span><span class="sxs-lookup"><span data-stu-id="9d235-120">During this process, the leadership team defines specific metrics, maps those metrics to the digital estate, and begins planning the overall migration effort.</span></span>

<span data-ttu-id="9d235-121">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="9d235-121">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="9d235-122">评估[安全基线工具链](toolchain.md)选项。</span><span class="sxs-lookup"><span data-stu-id="9d235-122">Evaluate your [Security Baseline toolchain](toolchain.md) options.</span></span>
- <span data-ttu-id="9d235-123">制定草拟的“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="9d235-123">Develop a draft Architecture Guidelines document and distribute to key stakeholders.</span></span>
- <span data-ttu-id="9d235-124">培训受“体系结构指南”制定影响的人员和团队，并使其参与进来。</span><span class="sxs-lookup"><span data-stu-id="9d235-124">Educate and involve the people and teams affected by the development of architecture guidelines.</span></span>
- <span data-ttu-id="9d235-125">向迁移积压工作 (backlog) 添加优先安全性任务。</span><span class="sxs-lookup"><span data-stu-id="9d235-125">Add prioritized security tasks to your migration backlog.</span></span>

<span data-ttu-id="9d235-126">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="9d235-126">**Potential activities:**</span></span>

- <span data-ttu-id="9d235-127">定义数据分类架构。</span><span class="sxs-lookup"><span data-stu-id="9d235-127">Define a data classification schema.</span></span>
- <span data-ttu-id="9d235-128">执行数字资产规划流程，以盘点促进业务流程和支持操作的当前 IT 资产。</span><span class="sxs-lookup"><span data-stu-id="9d235-128">Conduct a digital estate planning process to inventory the current IT assets powering your business processes and supporting operations.</span></span>
- <span data-ttu-id="9d235-129">执行[策略评审](../../governance/policy-compliance/what-is-a-cloud-policy-review.md)，开始实现现有公司 IT 安全策略的现代化，并定义处理已知风险的 MVP 策略。</span><span class="sxs-lookup"><span data-stu-id="9d235-129">Conduct a [policy review](../../governance/policy-compliance/what-is-a-cloud-policy-review.md) to begin the process of modernizing existing corporate IT security policies, and define MVP policies addressing known risks.</span></span>
- <span data-ttu-id="9d235-130">查看云平台的安全指南。</span><span class="sxs-lookup"><span data-stu-id="9d235-130">Review your cloud platform's security guidelines.</span></span> <span data-ttu-id="9d235-131">对于 Azure 而言，这些可以在 [Microsoft Service Trust 平台](https://www.microsoft.com/trustcenter/stp/default.aspx)中查找到。</span><span class="sxs-lookup"><span data-stu-id="9d235-131">For Azure these can be found in the [Microsoft Service Trust Platform](https://www.microsoft.com/trustcenter/stp/default.aspx).</span></span>
- <span data-ttu-id="9d235-132">确定安全基线策略是否包括[安全开发生命周期](https://www.microsoft.com/securityengineering/sdl/)。</span><span class="sxs-lookup"><span data-stu-id="9d235-132">Determine whether your Security Baseline policy includes a [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/).</span></span>
- <span data-ttu-id="9d235-133">根据接下来的一到三个版本，评估网络、数据和与资产相关的业务风险，并评估组织对这些风险的容忍度。</span><span class="sxs-lookup"><span data-stu-id="9d235-133">Evaluate network, data, and asset-related business risks based on the next one to three releases, and gauge your organization's tolerance for those risks.</span></span>
- <span data-ttu-id="9d235-134">查看 Microsoft 的 [网络安全的最新趋势](https://www.microsoft.com/security/operations/security-intelligence-report) 报表，获取当前安全环境的概述。</span><span class="sxs-lookup"><span data-stu-id="9d235-134">Review Microsoft's [top trends in cybersecurity](https://www.microsoft.com/security/operations/security-intelligence-report) report to get an overview of the current security landscape.</span></span>
- <span data-ttu-id="9d235-135">考虑在组织中开发 [Security DevOps](https://www.microsoft.com/en-us/securityengineering/devsecops)（安全 DevOps）角色。</span><span class="sxs-lookup"><span data-stu-id="9d235-135">Consider developing a [Security DevOps](https://www.microsoft.com/en-us/securityengineering/devsecops) role in your organization.</span></span>

<!-- "en-us" location is required for the URL above. -->

## <a name="build-and-pre-deployment"></a><span data-ttu-id="9d235-136">生成和预部署</span><span class="sxs-lookup"><span data-stu-id="9d235-136">Build and pre-deployment</span></span>

<span data-ttu-id="9d235-137">成功迁移环境需要许多技术和非技术先决条件。</span><span class="sxs-lookup"><span data-stu-id="9d235-137">A number of technical and nontechnical prerequisites are required to successful migrate an environment.</span></span> <span data-ttu-id="9d235-138">此过程侧重于推进迁移的决策、准备情况和核心基础结构。</span><span class="sxs-lookup"><span data-stu-id="9d235-138">This process focuses on the decisions, readiness, and core infrastructure that proceeds a migration.</span></span>

<span data-ttu-id="9d235-139">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="9d235-139">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="9d235-140">在预先部署阶段推出，以实现[安全基线工具链](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="9d235-140">Implement your [Security Baseline toolchain](toolchain.md) by rolling out in a pre-deployment phase.</span></span>
- <span data-ttu-id="9d235-141">更新“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="9d235-141">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
- <span data-ttu-id="9d235-142">在优先迁移积压工作 (backlog) 上实现安全性任务。</span><span class="sxs-lookup"><span data-stu-id="9d235-142">Implement security tasks on your prioritized migration backlog.</span></span>
- <span data-ttu-id="9d235-143">制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用。</span><span class="sxs-lookup"><span data-stu-id="9d235-143">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption.</span></span>

<span data-ttu-id="9d235-144">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="9d235-144">**Potential activities:**</span></span>

- <span data-ttu-id="9d235-145">确定组织对云托管数据的[加密](../../decision-guides/encryption/overview.md)策略。</span><span class="sxs-lookup"><span data-stu-id="9d235-145">Determine your organization's [encryption](../../decision-guides/encryption/overview.md) strategy for cloud-hosted data.</span></span>
- <span data-ttu-id="9d235-146">评估云部署的[标识](../../decision-guides/identity/overview.md)策略。</span><span class="sxs-lookup"><span data-stu-id="9d235-146">Evaluate your cloud deployment's [identity](../../decision-guides/identity/overview.md) strategy.</span></span> <span data-ttu-id="9d235-147">确定基于云的标识解决方案与本地标识提供者如何共存或集成。</span><span class="sxs-lookup"><span data-stu-id="9d235-147">Determine how your cloud-based identity solution will coexist or integrate with on-premises identity providers.</span></span>
- <span data-ttu-id="9d235-148">为[软件定义网络 (SDN)](../../decision-guides/software-defined-network/overview.md) 设计确定网络边界策略，以确保安全的虚拟网络功能。</span><span class="sxs-lookup"><span data-stu-id="9d235-148">Determine network boundary policies for your [Software Defined Networking (SDN)](../../decision-guides/software-defined-network/overview.md) design to ensure secure virtualized networking capabilities.</span></span>
- <span data-ttu-id="9d235-149">评估组织的[最小特权访问](/azure/active-directory/users-groups-roles/roles-delegate-by-task)策略，并使用基于任务的角色提供对特定资源的访问。</span><span class="sxs-lookup"><span data-stu-id="9d235-149">Evaluate your organization's [least privilege access](/azure/active-directory/users-groups-roles/roles-delegate-by-task) policies, and use task-based roles to provide access to specific resources.</span></span>
- <span data-ttu-id="9d235-150">为所有云服务和虚拟机应用安全性和监视机制。</span><span class="sxs-lookup"><span data-stu-id="9d235-150">Apply security and monitoring mechanisms to for all cloud services and virtual machines.</span></span>
- <span data-ttu-id="9d235-151">尽可能自动执行[安全策略](../../decision-guides/policy-enforcement/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="9d235-151">Automate [security policies](../../decision-guides/policy-enforcement/overview.md) where possible.</span></span>
- <span data-ttu-id="9d235-152">查看安全基线策略，并确定是否需要根据最佳做法指导（如[安全开发生命周期](https://www.microsoft.com/securityengineering/sdl/)中所概述的指导）修改计划。</span><span class="sxs-lookup"><span data-stu-id="9d235-152">Review your Security Baseline policy and determine if you need to modify your plans according to best practices guidance such as those outlined in the [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/).</span></span>

## <a name="adopt-and-migrate"></a><span data-ttu-id="9d235-153">采用和迁移</span><span class="sxs-lookup"><span data-stu-id="9d235-153">Adopt and migrate</span></span>

<span data-ttu-id="9d235-154">迁移是一个渐进过程，侧重于现有数字资产中应用程序或工作负载的移动、测试和采用。</span><span class="sxs-lookup"><span data-stu-id="9d235-154">Migration is an incremental process that focuses on the movement, testing, and adoption of applications or workloads in an existing digital estate.</span></span>

<span data-ttu-id="9d235-155">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="9d235-155">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="9d235-156">将[安全基线工具链](toolchain.md)从预部署迁移到生产环境。</span><span class="sxs-lookup"><span data-stu-id="9d235-156">Migrate your [Security Baseline toolchain](toolchain.md) from pre-deployment to production.</span></span>
- <span data-ttu-id="9d235-157">更新“体系结构指南”文档并分发给关键的利益干系人。</span><span class="sxs-lookup"><span data-stu-id="9d235-157">Update the Architecture Guidelines document and distribute to key stakeholders.</span></span>
- <span data-ttu-id="9d235-158">制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用</span><span class="sxs-lookup"><span data-stu-id="9d235-158">Develop educational materials and documentation, awareness communications, incentives, and other programs to help drive user adoption</span></span>

<span data-ttu-id="9d235-159">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="9d235-159">**Potential activities:**</span></span>

- <span data-ttu-id="9d235-160">查看最新安全基线和威胁信息，以识别任何新的业务风险。</span><span class="sxs-lookup"><span data-stu-id="9d235-160">Review the latest Security Baseline and threat information to identify any new business risks.</span></span>
- <span data-ttu-id="9d235-161">评估组织对处理可能出现的新安全风险的容忍度。</span><span class="sxs-lookup"><span data-stu-id="9d235-161">Gauge your organization's tolerance to handle new security risks that may arise.</span></span>
- <span data-ttu-id="9d235-162">识别策略偏离，并强制纠正。</span><span class="sxs-lookup"><span data-stu-id="9d235-162">Identify deviations from policy, and enforce corrections.</span></span>
- <span data-ttu-id="9d235-163">调整安全性和访问控制自动化，以确保最大限度的策略符合性。</span><span class="sxs-lookup"><span data-stu-id="9d235-163">Adjust security and access control automation to ensure maximum policy compliance.</span></span>  
- <span data-ttu-id="9d235-164">验证在生成/预部署阶段定义的最佳做法是否正确执行。</span><span class="sxs-lookup"><span data-stu-id="9d235-164">Validate that the best practices defined during the Build / Pre-deployment phases are properly executed.</span></span>
- <span data-ttu-id="9d235-165">查看最小特权访问策略并调整访问控制，最大限度地提高安全性。</span><span class="sxs-lookup"><span data-stu-id="9d235-165">Review your least privilege access policies and adjust access controls to maximize security.</span></span>
- <span data-ttu-id="9d235-166">根据工作负载测试安全基线工具链，识别和解决任何漏洞。</span><span class="sxs-lookup"><span data-stu-id="9d235-166">Test your Security Baseline toolchain against your workloads to identify and resolve any vulnerabilities.</span></span>

## <a name="operate-and-post-implementation"></a><span data-ttu-id="9d235-167">运营和实现后</span><span class="sxs-lookup"><span data-stu-id="9d235-167">Operate and post-implementation</span></span>

<span data-ttu-id="9d235-168">转换完成后，治理和操作必须依存于应用程序或工作负载的自然生命周期。</span><span class="sxs-lookup"><span data-stu-id="9d235-168">Once the transformation is complete, governance and operations must live on for the natural lifecycle of an application or workload.</span></span> <span data-ttu-id="9d235-169">治理成熟度的这一阶段侧重于在实现解决方案并且转换周期开始稳定之后通常会出现的活动。</span><span class="sxs-lookup"><span data-stu-id="9d235-169">This phase of governance maturity focuses on the activities that commonly come after the solution is implemented and the transformation cycle begins to stabilize.</span></span>

<span data-ttu-id="9d235-170">**最小建议的活动：**</span><span class="sxs-lookup"><span data-stu-id="9d235-170">**Minimum suggested activities:**</span></span>

- <span data-ttu-id="9d235-171">验证和/或优化[安全基准工具链](toolchain.md)。</span><span class="sxs-lookup"><span data-stu-id="9d235-171">Validate and/or refine your [Security Baseline toolchain](toolchain.md).</span></span>
- <span data-ttu-id="9d235-172">自定义通知和报表，提醒潜在的安全问题。</span><span class="sxs-lookup"><span data-stu-id="9d235-172">Customize notifications and reports to alert you of potential security issues.</span></span>
- <span data-ttu-id="9d235-173">优化体系结构指南以指导未来的采用流程。</span><span class="sxs-lookup"><span data-stu-id="9d235-173">Refine the Architecture Guidelines to guide future adoption processes.</span></span>
- <span data-ttu-id="9d235-174">定期与受影响的团队进行沟通并进行培训，以确保持续遵守体系结构指南。</span><span class="sxs-lookup"><span data-stu-id="9d235-174">Communicate and educate the affected teams periodically to ensure ongoing adherence to architecture guidelines.</span></span>

<span data-ttu-id="9d235-175">**可能的活动：**</span><span class="sxs-lookup"><span data-stu-id="9d235-175">**Potential activities:**</span></span>

- <span data-ttu-id="9d235-176">找出工作负载的模式和行为，并配置监视和报告工具，以识别任何异常活动、访问或资源使用情况并进行通知。</span><span class="sxs-lookup"><span data-stu-id="9d235-176">Discover patterns and behavior for your workloads and configure your monitoring and reporting tools to identify and notify you of any abnormal activity, access, or resource usage.</span></span>
- <span data-ttu-id="9d235-177">持续更新监视和报告策略，以检测最新漏洞、渗透和攻击。</span><span class="sxs-lookup"><span data-stu-id="9d235-177">Continuously update your monitoring and reporting policies to detect the latest vulnerabilities, exploits, and attacks.</span></span>
- <span data-ttu-id="9d235-178">部署程序，以便快速阻止未经授权的访问并禁用可能已受攻击者损害的资源。</span><span class="sxs-lookup"><span data-stu-id="9d235-178">Have procedures in place to quickly stop unauthorized access and disable resources that may have been compromised by an attacker.</span></span>
- <span data-ttu-id="9d235-179">定期查看最新安全最佳做法，并尽可能将建议应用于安全策略、自动化和监视功能。</span><span class="sxs-lookup"><span data-stu-id="9d235-179">Regularly review the latest security best practices and apply recommendations to your security policy, automation, and monitoring capabilities where possible.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9d235-180">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9d235-180">Next steps</span></span>

<span data-ttu-id="9d235-181">了解云安全治理的概念后，接下来请详细了解针对 Azure [Microsoft 提供的安全性和最佳做法指导](azure-security-guidance.md)。</span><span class="sxs-lookup"><span data-stu-id="9d235-181">Now that you understand the concept of cloud security governance, move on to learn more about [what security and best practices guidance Microsoft provides](azure-security-guidance.md) for Azure.</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="9d235-182">[了解 Azure 的安全指南](azure-security-guidance.md)
> [Azure 安全性简介](/azure/security/azure-security)
> [了解日志记录、报告和监视](../../decision-guides/log-and-report/overview.md)</span><span class="sxs-lookup"><span data-stu-id="9d235-182">[Learn about security guidance for Azure](azure-security-guidance.md)
[Introduction to Azure Security](/azure/security/azure-security)
[Learn about logging, reporting, and monitoring](../../decision-guides/log-and-report/overview.md)</span></span>
