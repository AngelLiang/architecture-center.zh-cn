---
title: 大型企业 – 资源一致性演变
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企业 – 资源一致性演变
author: BrianBlanchard
ms.openlocfilehash: 9fc6ca015310c02338463d3c8aa53ddfc74699df
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900535"
---
# <a name="large-enterprise-resource-consistency-evolution"></a><span data-ttu-id="73cf9-103">大型企业：资源一致性演变</span><span class="sxs-lookup"><span data-stu-id="73cf9-103">Large enterprise: Resource Consistency evolution</span></span>

<span data-ttu-id="73cf9-104">本文通过为治理 MVP 增加资源一致性控件来支持任务关键型应用程序，从而对叙述进行了改进。</span><span class="sxs-lookup"><span data-stu-id="73cf9-104">This article evolves the narrative by adding Resource Consistency controls to the governance MVP to support mission-critical applications.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="73cf9-105">说明的演变</span><span class="sxs-lookup"><span data-stu-id="73cf9-105">Evolution of the narrative</span></span>

<span data-ttu-id="73cf9-106">云采用团队已满足移动受保护数据的所有要求。</span><span class="sxs-lookup"><span data-stu-id="73cf9-106">The cloud adoption teams have met all requirements to move protected data.</span></span> <span data-ttu-id="73cf9-107">随着这些应用程序的出现，对业务的 SLA 承诺和对 IT 运营支持的需求也随之出现。</span><span class="sxs-lookup"><span data-stu-id="73cf9-107">With those applications come SLA commitments to the business and need for support from IT Operations.</span></span> <span data-ttu-id="73cf9-108">在迁移两个数据中心的团队背后，多个应用程序开发和 BI 团队准备开始将新的解决方案推向生产环境。</span><span class="sxs-lookup"><span data-stu-id="73cf9-108">Right behind the team migrating the two datacenters, multiple app dev and BI teams are ready to begin launching new solutions into production.</span></span> <span data-ttu-id="73cf9-109">IT 运营是云运营概念中的新生事物，需要通过一种方式快速整合现有运营流程。</span><span class="sxs-lookup"><span data-stu-id="73cf9-109">IT Operations is new to the thought of cloud operations and needs a way to quickly integrate existing operational processes.</span></span>

### <a name="evolution-of-current-state"></a><span data-ttu-id="73cf9-110">当前状态的演变</span><span class="sxs-lookup"><span data-stu-id="73cf9-110">Evolution of current state</span></span>

- <span data-ttu-id="73cf9-111">IT 正在积极地将受保护数据的生产工作负载转移到 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="73cf9-111">IT is actively moving production workloads with protected data into Azure.</span></span> <span data-ttu-id="73cf9-112">许多低优先级工作负载正在为生产流量提供服务。</span><span class="sxs-lookup"><span data-stu-id="73cf9-112">A number of low priority workloads are serving production traffic.</span></span> <span data-ttu-id="73cf9-113">一旦 IT 运营部门准备好支持工作负载，就可以切换更多内容。</span><span class="sxs-lookup"><span data-stu-id="73cf9-113">More can be cut over, as soon as IT Operations signs off on readiness to support the workloads.</span></span>
- <span data-ttu-id="73cf9-114">应用程序开发团队已准备好支持生产流量。</span><span class="sxs-lookup"><span data-stu-id="73cf9-114">The application development teams are ready for production traffic.</span></span>
- <span data-ttu-id="73cf9-115">BI 团队已准备好将预测和见解整合到为三个业务部门支持运营的系统中。</span><span class="sxs-lookup"><span data-stu-id="73cf9-115">The BI team is ready to integrate predictions and insights into the systems that run operations for the three business units.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="73cf9-116">未来状态的演变</span><span class="sxs-lookup"><span data-stu-id="73cf9-116">Evolution of the future state</span></span>

- <span data-ttu-id="73cf9-117">IT 运营是云运营概念中的新生事物，需要通过一种方式快速整合现有运营流程。</span><span class="sxs-lookup"><span data-stu-id="73cf9-117">IT operations is new to the thought of cloud operations and needs a way to quickly integrate existing operational processes.</span></span>

<span data-ttu-id="73cf9-118">当前状态和未来状态的变化带来需要新策略声明的新风险。</span><span class="sxs-lookup"><span data-stu-id="73cf9-118">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="73cf9-119">有形风险的演变</span><span class="sxs-lookup"><span data-stu-id="73cf9-119">Evolution of tangible risks</span></span>

<span data-ttu-id="73cf9-120">业务中断：任何新的平台都存在着固有的风险，可导致任务关键型业务流程中断。</span><span class="sxs-lookup"><span data-stu-id="73cf9-120">**Business Interruption**: There is an inherent risk of any new platform causing interruptions to mission-critical business processes.</span></span> <span data-ttu-id="73cf9-121">IT 运营团队和执行各种云采用的团队对云运营相对缺乏经验。</span><span class="sxs-lookup"><span data-stu-id="73cf9-121">The IT Operations team and the teams executing on various cloud adoptions are relatively inexperienced with cloud operations.</span></span> <span data-ttu-id="73cf9-122">这就增加了中断的风险，必须加以缓解和治理。</span><span class="sxs-lookup"><span data-stu-id="73cf9-122">This increases the risk of interruption and must be mitigated and governed.</span></span>

<span data-ttu-id="73cf9-123">该业务风险可以延伸为几种技术风险：</span><span class="sxs-lookup"><span data-stu-id="73cf9-123">This business risk can be expanded into several technical risks:</span></span>

- <span data-ttu-id="73cf9-124">不一致的运营流程可能会导致无法快速检测或修复的中断。</span><span class="sxs-lookup"><span data-stu-id="73cf9-124">Misaligned operational processes might lead to outages that can’t be detected or remediated quickly.</span></span>
- <span data-ttu-id="73cf9-125">外部入侵或拒绝服务攻击可能会导致业务中断</span><span class="sxs-lookup"><span data-stu-id="73cf9-125">External intrusion or denial of service attacks might cause a business interruption</span></span>
- <span data-ttu-id="73cf9-126">可能无法正确发现任务关键型资产，并因而可能无法正常运行。</span><span class="sxs-lookup"><span data-stu-id="73cf9-126">Mission-critical assets might not be properly discovered and therefore not properly operated.</span></span>
- <span data-ttu-id="73cf9-127">现有的运营管理流程可能不支持未发现或标注错误的资产。</span><span class="sxs-lookup"><span data-stu-id="73cf9-127">Undiscovered or mislabeled assets might not be supported by existing operational management processes.</span></span>
- <span data-ttu-id="73cf9-128">已部署资产的配置可能无法满足性能预期。</span><span class="sxs-lookup"><span data-stu-id="73cf9-128">Configuration of deployed assets might not meet performance expectations.</span></span>
- <span data-ttu-id="73cf9-129">可能无法正确记录和集中管理日志记录，从而无法修复性能问题。</span><span class="sxs-lookup"><span data-stu-id="73cf9-129">Logging might not be properly recorded and centralized to allow for remediation of performance issues.</span></span>
- <span data-ttu-id="73cf9-130">恢复策略可能会失败或花费的时间超过预期。</span><span class="sxs-lookup"><span data-stu-id="73cf9-130">Recovery policies may fail or take longer than expected.</span></span>
- <span data-ttu-id="73cf9-131">不一致的部署流程可能会导致安全漏洞，从而导致数据泄露或中断。</span><span class="sxs-lookup"><span data-stu-id="73cf9-131">Inconsistent deployment processes might result in security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="73cf9-132">配置偏差或缺少修补程序可能会导致意外安全漏洞，从而导致数据泄露或中断。</span><span class="sxs-lookup"><span data-stu-id="73cf9-132">Configuration drift or missed patches might result in unintended security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="73cf9-133">配置可能不会强制执行已定义的 SLA 要求或已提交的恢复要求。</span><span class="sxs-lookup"><span data-stu-id="73cf9-133">Configuration might not enforce the requirements of defined SLAs or committed recovery requirements.</span></span>
- <span data-ttu-id="73cf9-134">部署的操作系统或应用程序可能无法满足 OS 和应用程序强化要求。</span><span class="sxs-lookup"><span data-stu-id="73cf9-134">Deployed operating systems or applications might not meet OS and application hardening requirements.</span></span>
- <span data-ttu-id="73cf9-135">由于多个团队在云中工作，因此会有不一致性的风险。</span><span class="sxs-lookup"><span data-stu-id="73cf9-135">There is a risk of inconsistency due to multiple teams working in the cloud.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="73cf9-136">策略声明的演变</span><span class="sxs-lookup"><span data-stu-id="73cf9-136">Evolution of the policy statements</span></span>

<span data-ttu-id="73cf9-137">以下策略的变化将有助于缓解新的风险和指南如何实现。</span><span class="sxs-lookup"><span data-stu-id="73cf9-137">The following changes to policy will help mitigate the new risks and guide implementation.</span></span> <span data-ttu-id="73cf9-138">清单内容看起来很多，但要采用这些策略并没有想象的那么困难。</span><span class="sxs-lookup"><span data-stu-id="73cf9-138">The list looks long, but the adoption of these policies may be easier than it would appear.</span></span>

1. <span data-ttu-id="73cf9-139">所有已部署的资产必须按照重要程度和数据分类来划分类别。</span><span class="sxs-lookup"><span data-stu-id="73cf9-139">All deployed assets must be categorized by criticality and data classification.</span></span> <span data-ttu-id="73cf9-140">在部署到云之前，云治理团队和应用程序所有者将对分类进行审查。</span><span class="sxs-lookup"><span data-stu-id="73cf9-140">Classifications are to be reviewed by the Cloud Governance team and the application owner before deployment to the cloud.</span></span>
2. <span data-ttu-id="73cf9-141">包含任务关键型应用程序的子网必须受到能够检测入侵和响应攻击的防火墙解决方案的保护。</span><span class="sxs-lookup"><span data-stu-id="73cf9-141">Subnets containing mission-critical applications must be protected by a firewall solution capable of detecting intrusions and responding to attacks.</span></span>
3. <span data-ttu-id="73cf9-142">治理工具必须审核和强制执行安全基线团队定义的网络配置要求。</span><span class="sxs-lookup"><span data-stu-id="73cf9-142">Governance tooling must audit and enforce network configuration requirements defined by the Security Baseline team.</span></span>
4. <span data-ttu-id="73cf9-143">治理工具必须确认将与任务关键型应用程序或受保护数据相关的所有资产均已纳入资源消耗和优化监视范围。</span><span class="sxs-lookup"><span data-stu-id="73cf9-143">Governance tooling must validate that all assets related to mission-critical applications or protected data are included in monitoring for resource depletion and optimization.</span></span>
5. <span data-ttu-id="73cf9-144">治理工具必须确认为所有任务关键型应用程序或受保护数据收集适当级别的日志记录数据。</span><span class="sxs-lookup"><span data-stu-id="73cf9-144">Governance tooling must validate that the appropriate level of logging data is being collected for all mission-critical applications or protected data.</span></span>
6. <span data-ttu-id="73cf9-145">治理流程必须确认任务关键型应用程序和受保护数据正确实现了备份、恢复和 SLA 遵从性。</span><span class="sxs-lookup"><span data-stu-id="73cf9-145">Governance process must validate that backup, recovery, and SLA adherence are properly implemented for mission-critical applications and protected data.</span></span>
7. <span data-ttu-id="73cf9-146">治理工具必须限定虚拟机只能部署已批准的映像。</span><span class="sxs-lookup"><span data-stu-id="73cf9-146">Governance tooling must limit virtual machine deployment to approved images only.</span></span>
8. <span data-ttu-id="73cf9-147">治理工具必须强制要求禁止对支持任务关键型应用程序的所有已部署资产自动更新。</span><span class="sxs-lookup"><span data-stu-id="73cf9-147">Governance tooling must enforce that automatic updates are **prevented** on all deployed assets that support mission-critical applications.</span></span> <span data-ttu-id="73cf9-148">必须同运维管理团队合作评审违反规定的情况，并按照运营策略予以纠正。</span><span class="sxs-lookup"><span data-stu-id="73cf9-148">Violations must be reviewed with operational management teams and remediated in accordance with operations policies.</span></span> <span data-ttu-id="73cf9-149">不自动更新的资产必须包含在归 IT 运维的流程中。</span><span class="sxs-lookup"><span data-stu-id="73cf9-149">Assets that are not automatically updated must be included in processes owned by IT operations.</span></span>
9. <span data-ttu-id="73cf9-150">治理工具必须验证与成本、关键性、SLA、应用程序和数据分类相关的标记。</span><span class="sxs-lookup"><span data-stu-id="73cf9-150">Governance tooling must validate tagging related to cost, criticality, SLA, application, and data classification.</span></span> <span data-ttu-id="73cf9-151">所有值都必须与云治理团队管理的预定义值保持一致。</span><span class="sxs-lookup"><span data-stu-id="73cf9-151">All values must align to predefined values managed by the Cloud Governance team.</span></span>
10. <span data-ttu-id="73cf9-152">治理流程必须包括部署时的审核和周期性审核，以确保所有资产的一致性。</span><span class="sxs-lookup"><span data-stu-id="73cf9-152">Governance processes must include audits at the point of deployment and at regular cycles to ensure consistency across all assets.</span></span>
11. <span data-ttu-id="73cf9-153">安全团队应定期检查可能影响云部署的趋势和攻击漏洞，以更新云中使用的安全基准工具。</span><span class="sxs-lookup"><span data-stu-id="73cf9-153">Trends and exploits that could affect cloud deployments should be reviewed regularly by the security team to provide updates to Security Baseline tooling used in the cloud.</span></span>
12. <span data-ttu-id="73cf9-154">在发布到生产环境之前，必须将所有任务关键型应用程序和受保护数据添加到指定的运营监控解决方案中。</span><span class="sxs-lookup"><span data-stu-id="73cf9-154">Prior to release into production, all mission-critical applications and protected data must be added to the designated operational monitoring solution.</span></span> <span data-ttu-id="73cf9-155">选定的 IT 运营工具无法发现的资产不能发布用于生产。</span><span class="sxs-lookup"><span data-stu-id="73cf9-155">Assets that cannot be discovered by the chosen IT operations tooling cannot be released for production use.</span></span> <span data-ttu-id="73cf9-156">必须对相关部署流程进行必要的更改才能使资产可被发现，以确保在将来的部署中可以发现资产。</span><span class="sxs-lookup"><span data-stu-id="73cf9-156">Any changes required to make the assets discoverable must be made to the relevant deployment processes to ensure assets will be discoverable in future deployments.</span></span>
13. <span data-ttu-id="73cf9-157">发现后，运营管理团队将验证资产规模，从而验证资产是否符合性能要求。</span><span class="sxs-lookup"><span data-stu-id="73cf9-157">Upon discovery, asset sizing is to be validated by operational management teams to validate that the asset meets performance requirements.</span></span>
14. <span data-ttu-id="73cf9-158">部署工具必须得到云治理团队的批准，以确保对部署的资产进行持续管理。</span><span class="sxs-lookup"><span data-stu-id="73cf9-158">Deployment tooling must be approved by the Cloud Governance team to ensure ongoing governance of deployed assets.</span></span>
15. <span data-ttu-id="73cf9-159">必须在云治理团队可访问的中央存储库中维护部署脚本，以便进行定期检查和审核。</span><span class="sxs-lookup"><span data-stu-id="73cf9-159">Deployment scripts must be maintained in central repository accessible by the Cloud Governance team for periodic review and auditing.</span></span>
16. <span data-ttu-id="73cf9-160">治理评审流程必须验证已部署的资产是否已根据 SLA 和恢复需求进行了正确配置。</span><span class="sxs-lookup"><span data-stu-id="73cf9-160">Governance review processes must validate that deployed assets are properly configured in alignment with SLA and recovery requirements.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="73cf9-161">最佳做法的演变</span><span class="sxs-lookup"><span data-stu-id="73cf9-161">Evolution of the best practices</span></span>

<span data-ttu-id="73cf9-162">本文的这一部分将改进治理 MVP 设计，以包括新的 Azure 策略和 Azure 成本管理的实现。</span><span class="sxs-lookup"><span data-stu-id="73cf9-162">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="73cf9-163">这两项设计变更将共同实现新的企业策略声明。</span><span class="sxs-lookup"><span data-stu-id="73cf9-163">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

<span data-ttu-id="73cf9-164">根据这个虚构的示例，假定已经发生了受保护的数据演变。</span><span class="sxs-lookup"><span data-stu-id="73cf9-164">Following the experience of this fictional example, it is assumed that the Protected Data evolution has already happened.</span></span> <span data-ttu-id="73cf9-165">基于此最佳做法，以下内容将增加运营监控要求，为任务关键型应用程序准备订阅。</span><span class="sxs-lookup"><span data-stu-id="73cf9-165">Building on that best practice, the following will add operational monitoring requirements, readying a subscription for mission-critical applications.</span></span>

<span data-ttu-id="73cf9-166">**企业 IT 订阅**：将以下内容添加到作为中心的企业 IT 订阅中。</span><span class="sxs-lookup"><span data-stu-id="73cf9-166">**Corporate IT Subscription**: Add the following to the Corporate IT subscription, which acts as a hub.</span></span>

1. <span data-ttu-id="73cf9-167">作为外部依赖项，云运营团队需要定义运营监控工具、业务连续性/灾难恢复 (BCDR) 工具和自动修复工具。</span><span class="sxs-lookup"><span data-stu-id="73cf9-167">As an external dependency, the Cloud Operations team will need to define operational monitoring tooling, Business Continuity/Disaster Recovery (BCDR) tooling and automated remediation tooling.</span></span> <span data-ttu-id="73cf9-168">然后，云治理团队可以支持必要的发现流程。</span><span class="sxs-lookup"><span data-stu-id="73cf9-168">The Cloud Governance team can then support necessary discovery processes.</span></span>
    1. <span data-ttu-id="73cf9-169">在此用例中，云运营团队选择了 Azure Monitor 作为监视任务关键型应用程序的主要工具。</span><span class="sxs-lookup"><span data-stu-id="73cf9-169">In this use case, the Cloud Operations team chose Azure Monitor as the primary tool for monitoring mission-critical applications.</span></span>
    2. <span data-ttu-id="73cf9-170">团队还选择了 Azure Site Recovery 作为主要的 BCDR 工具。</span><span class="sxs-lookup"><span data-stu-id="73cf9-170">The team also chose Azure Site Recovery as the primary BCDR tooling.</span></span>
2. <span data-ttu-id="73cf9-171">Azure Site Recovery 实现</span><span class="sxs-lookup"><span data-stu-id="73cf9-171">Azure Site Recovery implementation</span></span>
    1. <span data-ttu-id="73cf9-172">为备份和恢复过程定义和部署 Azure 保管库</span><span class="sxs-lookup"><span data-stu-id="73cf9-172">Define and deploy Azure Vault for backup and recovery processes</span></span>
    2. <span data-ttu-id="73cf9-173">创建 Azure 资源管理模板，以便在每个订阅中创建保管库</span><span class="sxs-lookup"><span data-stu-id="73cf9-173">Create an Azure Resource Management template for creation of a vault in each subscription</span></span>
3. <span data-ttu-id="73cf9-174">Azure Monitor 实现</span><span class="sxs-lookup"><span data-stu-id="73cf9-174">Azure Monitor implementation</span></span>
    1. <span data-ttu-id="73cf9-175">确定任务关键型订阅后，可使用 PowerShell 创建日志分析工作区。</span><span class="sxs-lookup"><span data-stu-id="73cf9-175">Once a mission-critical subscription is identified, a log analytics workspace can be created using PowerShell.</span></span> <span data-ttu-id="73cf9-176">这是一个预先部署过程。</span><span class="sxs-lookup"><span data-stu-id="73cf9-176">This is a pre-deployment process.</span></span>

<span data-ttu-id="73cf9-177">单个云采用订阅：以下内容将确保监控解决方案可以发现每个订阅，并准备好加入 BCDR 做法。</span><span class="sxs-lookup"><span data-stu-id="73cf9-177">**Individual cloud adoption subscription**: The following will ensure that each subscription is discoverable by the monitoring solution and ready to be included in BCDR practices.</span></span>

1. <span data-ttu-id="73cf9-178">任务关键型节点的 Azure Policy</span><span class="sxs-lookup"><span data-stu-id="73cf9-178">Azure Policy for mission-critical nodes</span></span>
    1. <span data-ttu-id="73cf9-179">仅审核并强制使用标准角色。</span><span class="sxs-lookup"><span data-stu-id="73cf9-179">Audit and enforce use of standard roles only.</span></span>
    2. <span data-ttu-id="73cf9-180">审核和强制应用程序加密所有存储帐户。</span><span class="sxs-lookup"><span data-stu-id="73cf9-180">Audit and enforce application of encryption for all storage accounts.</span></span>
    3. <span data-ttu-id="73cf9-181">审核并强制每个网络接口使用已批准的网络子网和 VNet。</span><span class="sxs-lookup"><span data-stu-id="73cf9-181">Audit and enforce use of approved network subnet and VNet per network interface.</span></span>
    4. <span data-ttu-id="73cf9-182">审核并强制实施用户定义路由表的限制。</span><span class="sxs-lookup"><span data-stu-id="73cf9-182">Audit and enforce the limitation of user-defined routing tables.</span></span>
    5. <span data-ttu-id="73cf9-183">审核并强制部署适用于 Windows 和 Linux 虚拟机的 Log Analytics 代理。</span><span class="sxs-lookup"><span data-stu-id="73cf9-183">Audit and enforce the deployment of Log Analytics agents for Windows and Linux virtual machines.</span></span>
2. <span data-ttu-id="73cf9-184">Azure 蓝图</span><span class="sxs-lookup"><span data-stu-id="73cf9-184">Azure blueprint</span></span>
    1. <span data-ttu-id="73cf9-185">创建名为 `mission-critical-workloads-and-protected-data` 的蓝图。</span><span class="sxs-lookup"><span data-stu-id="73cf9-185">Create a blueprint named `mission-critical-workloads-and-protected-data`.</span></span> <span data-ttu-id="73cf9-186">除受保护的数据蓝图外，此蓝图还将应用资产。</span><span class="sxs-lookup"><span data-stu-id="73cf9-186">This blueprint will apply assets in addition to the protected data blueprint.</span></span>
    2. <span data-ttu-id="73cf9-187">向蓝图添加新的 Azure 策略。</span><span class="sxs-lookup"><span data-stu-id="73cf9-187">Add the new Azure policies to the blueprint.</span></span>
    3. <span data-ttu-id="73cf9-188">将蓝图应用于应能托管任务关键型应用程序的任何订阅。</span><span class="sxs-lookup"><span data-stu-id="73cf9-188">Apply the blueprint to any subscription that is expected to host a mission-critical application.</span></span>

## <a name="conclusion"></a><span data-ttu-id="73cf9-189">结束语</span><span class="sxs-lookup"><span data-stu-id="73cf9-189">Conclusion</span></span>

<span data-ttu-id="73cf9-190">将这些流程和更改添加到治理 MVP，有助于缓解与资源治理相关联的许多风险。</span><span class="sxs-lookup"><span data-stu-id="73cf9-190">Adding these processes and changes to the governance MVP helps mitigate many of the risks associated with resource governance.</span></span> <span data-ttu-id="73cf9-191">它们共同增加了实现云感知运营必需的恢复、大小调整和监视控件。</span><span class="sxs-lookup"><span data-stu-id="73cf9-191">Together, they add the recovery, sizing, and monitoring controls necessary to empower cloud-aware operations.</span></span>

## <a name="next-steps"></a><span data-ttu-id="73cf9-192">后续步骤</span><span class="sxs-lookup"><span data-stu-id="73cf9-192">Next steps</span></span>

<span data-ttu-id="73cf9-193">随着云采用的不断演进和业务附加值的提升，风险和云治理需求也在不断发展。</span><span class="sxs-lookup"><span data-stu-id="73cf9-193">As cloud adoption continues to evolve and deliver additional business value, the risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="73cf9-194">对于这一过程中的虚构公司来说，下一个触发器是部署到云上的资产超过 1,000 项，或者每月的开销超过 10,000 美元。</span><span class="sxs-lookup"><span data-stu-id="73cf9-194">For the fictional company in this journey, the next trigger is when the scale of deployment exceeds 1,000 assets to the cloud or monthly spending exceeds $10,000 USD per month.</span></span> <span data-ttu-id="73cf9-195">此时，云治理团队可增加“成本管理”控件。</span><span class="sxs-lookup"><span data-stu-id="73cf9-195">At this point, the Cloud Governance team adds Cost Management controls.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="73cf9-196">成本管理演变</span><span class="sxs-lookup"><span data-stu-id="73cf9-196">Cost Management evolution</span></span>](./cost-management-evolution.md)