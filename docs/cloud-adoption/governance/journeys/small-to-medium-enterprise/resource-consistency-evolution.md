---
title: 'CAF：中小型企业 - 资源一致性演变 '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 阐释：中小型企业 - 资源一致性演变
author: BrianBlanchard
ms.openlocfilehash: 402bb3ff4182123cdc8825522f965f7cf8637980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900559"
---
# <a name="small-to-medium-enterprise-resource-consistency-evolution"></a><span data-ttu-id="509ac-103">中小型企业：资源一致性演变</span><span class="sxs-lookup"><span data-stu-id="509ac-103">Small-to-medium enterprise: Resource Consistency evolution</span></span>

<span data-ttu-id="509ac-104">本文通过添加资源一致性控件来支持任务关键型应用，从而对叙述进行了改进。</span><span class="sxs-lookup"><span data-stu-id="509ac-104">This article evolves the narrative by adding Resource Consistency controls to support mission-critical apps.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="509ac-105">叙述性内容的演变</span><span class="sxs-lookup"><span data-stu-id="509ac-105">Evolution of the narrative</span></span>

<span data-ttu-id="509ac-106">新的客户体验、新的预测工具和迁移的基础结构不断改进。</span><span class="sxs-lookup"><span data-stu-id="509ac-106">New customer experiences, new prediction tools, and migrated infrastructure continue to progress.</span></span> <span data-ttu-id="509ac-107">现在，企业已准备好开始在生产容量中使用这些资产。</span><span class="sxs-lookup"><span data-stu-id="509ac-107">The business is now ready to begin using those assets in a production capacity.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="509ac-108">当前状态的演变</span><span class="sxs-lookup"><span data-stu-id="509ac-108">Evolution of the current state</span></span>

<span data-ttu-id="509ac-109">在此叙述性内容的上一阶段，应用程序开发和 BI 团队几乎准备好将客户和财务数据集成到生产工作负载中。</span><span class="sxs-lookup"><span data-stu-id="509ac-109">In the previous phase of this narrative, the application development and BI teams were nearly ready to integrate customer and financial data into production workloads.</span></span> <span data-ttu-id="509ac-110">IT 团队正在停用灾难恢复数据中心。</span><span class="sxs-lookup"><span data-stu-id="509ac-110">The IT team was in the process of retiring the DR datacenter.</span></span>

<span data-ttu-id="509ac-111">自此，一些事态已改变，将会影响到治理：</span><span class="sxs-lookup"><span data-stu-id="509ac-111">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="509ac-112">IT 已提前停用 100% 的灾难恢复数据中心。</span><span class="sxs-lookup"><span data-stu-id="509ac-112">IT has retired 100% of the DR datacenter, ahead of schedule.</span></span> <span data-ttu-id="509ac-113">在此过程中，生产数据中心中的许多资产被识别为云迁移候选者。</span><span class="sxs-lookup"><span data-stu-id="509ac-113">In the process, a number of assets in the Production datacenter were identified as cloud migration candidates.</span></span>
- <span data-ttu-id="509ac-114">应用程序开发团队已对生产流量做好准备。</span><span class="sxs-lookup"><span data-stu-id="509ac-114">The application development teams are now ready for production traffic.</span></span>
- <span data-ttu-id="509ac-115">BI 团队已做好将预测和见解反馈到生产数据中心的操作系统的准备。</span><span class="sxs-lookup"><span data-stu-id="509ac-115">The BI team is ready to feed predictions and insights back into operation systems in the Production datacenter.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="509ac-116">未来状态的演变</span><span class="sxs-lookup"><span data-stu-id="509ac-116">Evolution of the future state</span></span>

<span data-ttu-id="509ac-117">在生产业务流程中使用 Azure 部署之前，云操作必须成熟。</span><span class="sxs-lookup"><span data-stu-id="509ac-117">Before using Azure deployments in production business processes, cloud operations must mature.</span></span> <span data-ttu-id="509ac-118">与此同时，还需要进行额外的治理演变，以确保资产能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="509ac-118">In conjunction, an additional governance evolution is required to ensure assets can be operated properly.</span></span>

<span data-ttu-id="509ac-119">当前和未来状态的变化揭示了新的风险，需要新的策略声明。</span><span class="sxs-lookup"><span data-stu-id="509ac-119">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="509ac-120">有形风险的演变</span><span class="sxs-lookup"><span data-stu-id="509ac-120">Evolution of tangible risks</span></span>

<span data-ttu-id="509ac-121">**业务中断**：任何新平台都存在固有的风险，导致任务关键型业务流程中断。</span><span class="sxs-lookup"><span data-stu-id="509ac-121">**Business Interruption**: There is an inherent risk of any new platform causing interruptions to mission-critical business processes.</span></span> <span data-ttu-id="509ac-122">IT 运营团队和执行各种云采用的团队对云操作相对缺乏经验。</span><span class="sxs-lookup"><span data-stu-id="509ac-122">The IT Operations team and the teams executing on various cloud adoptions are relatively inexperienced with cloud operations.</span></span> <span data-ttu-id="509ac-123">这就增加了中断的风险，必须加以缓解和治理。</span><span class="sxs-lookup"><span data-stu-id="509ac-123">This increases the risk of interruption and must be mitigated and governed.</span></span>

<span data-ttu-id="509ac-124">该业务风险可以扩展为许多技术风险：</span><span class="sxs-lookup"><span data-stu-id="509ac-124">This business risk can be expanded into a number of technical risks:</span></span>

- <span data-ttu-id="509ac-125">外部入侵或拒绝服务攻击可能会导致业务中断</span><span class="sxs-lookup"><span data-stu-id="509ac-125">External intrusion or denial of service attacks might cause a business interruption</span></span>
- <span data-ttu-id="509ac-126">可能无法正确发现任务关键型资产，并因此可能无法正常运行</span><span class="sxs-lookup"><span data-stu-id="509ac-126">Mission-critical assets may not be properly discovered, and therefore might not be properly operated</span></span>
- <span data-ttu-id="509ac-127">现有的运营管理流程可能不支持未发现或标注错误的资产。</span><span class="sxs-lookup"><span data-stu-id="509ac-127">Undiscovered or mislabeled assets might not be supported by existing operational management processes.</span></span>
- <span data-ttu-id="509ac-128">已部署资产的配置可能无法满足性能预期</span><span class="sxs-lookup"><span data-stu-id="509ac-128">The configuration of deployed assets may not meet performance expectations</span></span>
- <span data-ttu-id="509ac-129">可能无法正确记录和集中管理日志记录，从而无法修复性能问题。</span><span class="sxs-lookup"><span data-stu-id="509ac-129">Logging might not be properly recorded and centralized to allow for remediation of performance issues.</span></span>
- <span data-ttu-id="509ac-130">恢复策略可能会失败或花费的时间超过预期。</span><span class="sxs-lookup"><span data-stu-id="509ac-130">Recovery policies may fail or take longer than expected.</span></span>
- <span data-ttu-id="509ac-131">不一致的部署流程可能会导致安全漏洞，从而导致数据泄露或中断。</span><span class="sxs-lookup"><span data-stu-id="509ac-131">Inconsistent deployment processes might result in security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="509ac-132">配置偏差或缺少修补程序可能会导致意外安全漏洞，从而导致数据泄露或中断。</span><span class="sxs-lookup"><span data-stu-id="509ac-132">Configuration drift or missed patches might result in unintended security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="509ac-133">配置可能不会强制执行已定义的 SLA 要求或已提交的恢复要求。</span><span class="sxs-lookup"><span data-stu-id="509ac-133">Configuration might not enforce the requirements of defined SLAs or committed recovery requirements.</span></span>
- <span data-ttu-id="509ac-134">部署的操作系统或应用程序可能无法满足强化要求</span><span class="sxs-lookup"><span data-stu-id="509ac-134">Deployed operating systems or applications might fail to meet hardening requirements</span></span>
- <span data-ttu-id="509ac-135">由于有如此多的团队在云中工作，因此存在不一致性的风险。</span><span class="sxs-lookup"><span data-stu-id="509ac-135">With so many teams working in the cloud, there is a risk of inconsistency.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="509ac-136">策略语句的演变</span><span class="sxs-lookup"><span data-stu-id="509ac-136">Evolution of the policy statements</span></span>

<span data-ttu-id="509ac-137">以下策略的更改将有助于缓解新的风险，同时有助于实施指南。</span><span class="sxs-lookup"><span data-stu-id="509ac-137">The following changes to policy will help mitigate the new risks and guide implementation.</span></span> <span data-ttu-id="509ac-138">此清单内容看起来很多，但要采用这些策略并没有想象的那么困难。</span><span class="sxs-lookup"><span data-stu-id="509ac-138">The list looks long, but adopting these policies may be easier than it appears.</span></span>

1. <span data-ttu-id="509ac-139">所有已部署的资产必须按照重要程度和数据分类进行分类。</span><span class="sxs-lookup"><span data-stu-id="509ac-139">All deployed assets must be categorized by criticality and data classification.</span></span> <span data-ttu-id="509ac-140">在部署到云之前，云治理团队和应用程序所有者将对分类进行审查</span><span class="sxs-lookup"><span data-stu-id="509ac-140">Classifications are to be reviewed by the Cloud Governance team and the application owner before deployment to the cloud</span></span>
2. <span data-ttu-id="509ac-141">包含任务关键型应用程序的子网必须受到能够检测入侵和响应攻击的防火墙解决方案的保护。</span><span class="sxs-lookup"><span data-stu-id="509ac-141">Subnets containing mission-critical applications must be protected by a firewall solution capable of detecting intrusions and responding to attacks.</span></span>
3. <span data-ttu-id="509ac-142">管理工具必须审核和强制执行安全管理团队定义的网络配置要求</span><span class="sxs-lookup"><span data-stu-id="509ac-142">Governance tooling must audit and enforce network configuration requirements defined by the Security Management team</span></span>
4. <span data-ttu-id="509ac-143">治理工具必须确认将与任务关键型应用或受保护数据相关的所有资产均已纳入资源消耗和优化监视范围。</span><span class="sxs-lookup"><span data-stu-id="509ac-143">Governance tooling must validate that all assets related to mission-critical apps or protected data are included in monitoring for resource depletion and optimization.</span></span>
5. <span data-ttu-id="509ac-144">治理工具必须确认为所有任务关键型应用程序或受保护数据收集适当级别的日志记录数据。</span><span class="sxs-lookup"><span data-stu-id="509ac-144">Governance tooling must validate that the appropriate level of logging data is being collected for all mission-critical applications or protected data.</span></span>
6. <span data-ttu-id="509ac-145">治理流程必须确认任务关键型应用程序和受保护数据正确实现了备份、恢复和 SLA 遵从性。</span><span class="sxs-lookup"><span data-stu-id="509ac-145">Governance process must validate that backup, recovery, and SLA adherence are properly implemented for mission-critical applications and protected data.</span></span>
7. <span data-ttu-id="509ac-146">治理工具必须仅将虚拟机部署限制为已批准的映像。</span><span class="sxs-lookup"><span data-stu-id="509ac-146">Governance tooling must limit virtual machine deployments to approved images only.</span></span>
8. <span data-ttu-id="509ac-147">治理工具必须强制要求在所有支持任务关键型应用程序的已部署资产上禁止自动更新。</span><span class="sxs-lookup"><span data-stu-id="509ac-147">Governance tooling must enforce that automatic updates are prevented on all deployed assets that support mission-critical applications.</span></span> <span data-ttu-id="509ac-148">必须与运营管理团队协同审查违规行为，并根据运营策略进行补救。</span><span class="sxs-lookup"><span data-stu-id="509ac-148">Violations must be reviewed with operational management teams and remediated in accordance with operations policies.</span></span> <span data-ttu-id="509ac-149">不自动更新的资产必须包含在 IT 运营部门的流程中。</span><span class="sxs-lookup"><span data-stu-id="509ac-149">Assets that are not automatically updated must be included in processes owned by IT Operations.</span></span>
9. <span data-ttu-id="509ac-150">治理工具必须验证与成本、关键性、SLA、应用程序和数据分类相关的标记。</span><span class="sxs-lookup"><span data-stu-id="509ac-150">Governance tooling must validate tagging related to cost, criticality, SLA, application, and data classification.</span></span> <span data-ttu-id="509ac-151">所有值都必须与治理团队管理的预定义值保持一致。</span><span class="sxs-lookup"><span data-stu-id="509ac-151">All values must align to predefined values managed by the governance team.</span></span>
10. <span data-ttu-id="509ac-152">治理过程必须包括部署时的审核和周期性审核，以确保所有资产的一致性。</span><span class="sxs-lookup"><span data-stu-id="509ac-152">Governance processes must include audits at the point of deployment and at regular cycles to ensure consistency across all assets.</span></span>
11. <span data-ttu-id="509ac-153">安全团队应定期检查可能影响云部署的趋势和攻击，以更新云中使用的安全管理工具。</span><span class="sxs-lookup"><span data-stu-id="509ac-153">Trends and exploits that could affect cloud deployments should be reviewed regularly by the Security team to provide updates to security management tooling used in the cloud.</span></span>
12. <span data-ttu-id="509ac-154">在发布到生产环境之前，必须将所有任务关键型应用和受保护数据添加到指定的运营监控解决方案中。</span><span class="sxs-lookup"><span data-stu-id="509ac-154">Before release into production, all mission-critical apps and protected data must be added to the designated operational monitoring solution.</span></span> <span data-ttu-id="509ac-155">如果资产无法被选定的 IT 运营工具发现，则无法发布供生产使用。</span><span class="sxs-lookup"><span data-stu-id="509ac-155">Assets that cannot be discovered by the chosen IT operations tooling, cannot be released for production use.</span></span> <span data-ttu-id="509ac-156">必须对相关部署流程进行任何必要的更改才能使资产可被发现，以确保在将来的部署中可以发现资产。</span><span class="sxs-lookup"><span data-stu-id="509ac-156">Any changes required to make the assets discoverable must be made to the relevant deployment processes to ensure assets will be discoverable in future deployments.</span></span>
13. <span data-ttu-id="509ac-157">一旦发现，运营管理团队将调整资产规模，以确保资产满足性能要求</span><span class="sxs-lookup"><span data-stu-id="509ac-157">Upon discovery, operational management teams will size assets, to ensure that assets meet performance requirements</span></span>
14. <span data-ttu-id="509ac-158">部署工具必须得到云治理团队的批准，以确保对部署的资产进行持续管理。</span><span class="sxs-lookup"><span data-stu-id="509ac-158">Deployment tooling must be approved by the Cloud Governance team to ensure ongoing governance of deployed assets.</span></span>
15. <span data-ttu-id="509ac-159">必须在云治理团队可访问的中央存储库中对部署脚本进行维护，以便进行定期检查和审核。</span><span class="sxs-lookup"><span data-stu-id="509ac-159">Deployment scripts must be maintained in a central repository accessible by the Cloud Governance team for periodic review and auditing.</span></span>
16. <span data-ttu-id="509ac-160">治理评审流程必须验证已部署的资产是否已根据 SLA 和恢复需求进行了正确配置。</span><span class="sxs-lookup"><span data-stu-id="509ac-160">Governance review processes must validate that deployed assets are properly configured in alignment with SLA and recovery requirements.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="509ac-161">最佳做法的演变</span><span class="sxs-lookup"><span data-stu-id="509ac-161">Evolution of the best practices</span></span>

<span data-ttu-id="509ac-162">本文的这一部分将改进治理 MVP 设计，以包括新的 Azure 策略和 Azure 成本管理的实现。</span><span class="sxs-lookup"><span data-stu-id="509ac-162">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="509ac-163">这两项设计变更将共同实现新的公司策略语句。</span><span class="sxs-lookup"><span data-stu-id="509ac-163">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="509ac-164">云运营团队将定义运营监视工具和自动修复工具。</span><span class="sxs-lookup"><span data-stu-id="509ac-164">The Cloud Operations team will define operational monitoring tooling and automated remediation tooling.</span></span> <span data-ttu-id="509ac-165">云治理团队将支持这些发现进程。</span><span class="sxs-lookup"><span data-stu-id="509ac-165">The Cloud Governance team will support those discovery processes.</span></span> <span data-ttu-id="509ac-166">在此用例中，云运营团队选择 Azure Monitor 作为监视任务关键型应用程序的主要工具。</span><span class="sxs-lookup"><span data-stu-id="509ac-166">In this use case, the Cloud Operations team chose Azure Monitor as the primary tool for monitoring mission-critical applications.</span></span>
2. <span data-ttu-id="509ac-167">在 Azure DevOps 中创建一个存储库来存储和版本化所有相关的资源管理器模板和脚本化的配置。</span><span class="sxs-lookup"><span data-stu-id="509ac-167">Create a repository in Azure DevOps to store and version all relevant Resource Manager templates and scripted configurations.</span></span>
3. <span data-ttu-id="509ac-168">Azure 保管库实现：</span><span class="sxs-lookup"><span data-stu-id="509ac-168">Azure Vault implementation:</span></span>
    1. <span data-ttu-id="509ac-169">为备份和恢复过程定义和部署 Azure 保管库。</span><span class="sxs-lookup"><span data-stu-id="509ac-169">Define and deploy Azure Vault for backup and recovery processes.</span></span>
    2. <span data-ttu-id="509ac-170">创建资源管理器模板，以便在每个订阅中创建保管库。</span><span class="sxs-lookup"><span data-stu-id="509ac-170">Create a Resource Manager template for creation of a vault in each subscription.</span></span>
4. <span data-ttu-id="509ac-171">更新所有订阅的 Azure 策略：</span><span class="sxs-lookup"><span data-stu-id="509ac-171">Update Azure Policy for all subscriptions:</span></span>
    1. <span data-ttu-id="509ac-172">审核并强制执行所有订阅的重要程度和数据分类，以识别任何具有任务关键型资产的订阅。</span><span class="sxs-lookup"><span data-stu-id="509ac-172">Audit and enforce criticality and data classification across all subscriptions to identify any subscriptions with mission-critical assets.</span></span>
    2. <span data-ttu-id="509ac-173">仅审核和强制使用已批准的映像。</span><span class="sxs-lookup"><span data-stu-id="509ac-173">Audit and enforce the use of approved images only.</span></span>
5. <span data-ttu-id="509ac-174">Azure Monitor 实现：</span><span class="sxs-lookup"><span data-stu-id="509ac-174">Azure Monitor implementation:</span></span>
    1. <span data-ttu-id="509ac-175">确定任务关键型订阅后，使用 PowerShell 创建 Azure Monitor 工作区。</span><span class="sxs-lookup"><span data-stu-id="509ac-175">Once a mission-critical subscription is identified, create an Azure Monitor workspace using PowerShell.</span></span> <span data-ttu-id="509ac-176">这是一个预先部署过程。</span><span class="sxs-lookup"><span data-stu-id="509ac-176">This is a pre-deployment process.</span></span>
    2. <span data-ttu-id="509ac-177">在部署测试期间，云运营团队部署必要的代理和测试发现。</span><span class="sxs-lookup"><span data-stu-id="509ac-177">During deployment testing, the Cloud Operations team deploys the necessary agents and tests discovery.</span></span>
6. <span data-ttu-id="509ac-178">为包含任务关键型应用程序的所有订阅更新 Azure 策略。</span><span class="sxs-lookup"><span data-stu-id="509ac-178">Update Azure Policy for all subscriptions that contain mission-critical applications.</span></span>
    1. <span data-ttu-id="509ac-179">审核并强制将 NSG 应用于所有 NIC 和子网。</span><span class="sxs-lookup"><span data-stu-id="509ac-179">Audit and enforce the application of an NSG to all NICs and subnets.</span></span> <span data-ttu-id="509ac-180">网络和 IT 安全团队定义 NSG。</span><span class="sxs-lookup"><span data-stu-id="509ac-180">Networking and IT Security define the NSG.</span></span>
    2. <span data-ttu-id="509ac-181">审核并强制为每个网络接口使用已批准的网络子网和 VNet。</span><span class="sxs-lookup"><span data-stu-id="509ac-181">Audit and enforce the use of approved network subnets and VNets for each network interface.</span></span>
    3. <span data-ttu-id="509ac-182">审核并强制实施用户定义路由表的限制。</span><span class="sxs-lookup"><span data-stu-id="509ac-182">Audit and enforce the limitation of user-defined routing tables.</span></span>
    4. <span data-ttu-id="509ac-183">审核并强制部署所有虚拟机的 Azure Monitor 代理。</span><span class="sxs-lookup"><span data-stu-id="509ac-183">Audit and enforce deployment of Azure Monitor agents for all virtual machines.</span></span>
    5. <span data-ttu-id="509ac-184">审核并强制 Azure 保管库存在于订阅中。</span><span class="sxs-lookup"><span data-stu-id="509ac-184">Audit and enforce that Azure Vault exists in the subscription.</span></span>
7. <span data-ttu-id="509ac-185">防火墙配置：</span><span class="sxs-lookup"><span data-stu-id="509ac-185">Firewall configuration:</span></span>
    1. <span data-ttu-id="509ac-186">确定符合安全要求的 Azure 防火墙配置。</span><span class="sxs-lookup"><span data-stu-id="509ac-186">Identify a configuration of Azure Firewall that meets security requirements.</span></span> <span data-ttu-id="509ac-187">或者，确定与 Azure 兼容的第三方设备。</span><span class="sxs-lookup"><span data-stu-id="509ac-187">Alternatively, identify a third-party appliance that is compatible with Azure.</span></span>
    2. <span data-ttu-id="509ac-188">创建资源管理器模板来部署具有所需配置的防火墙。</span><span class="sxs-lookup"><span data-stu-id="509ac-188">Create a Resource Manager template to deploy the firewall with required configurations.</span></span>
8. <span data-ttu-id="509ac-189">Azure 蓝图：</span><span class="sxs-lookup"><span data-stu-id="509ac-189">Azure blueprint:</span></span>
    1. <span data-ttu-id="509ac-190">创建名为 `protected-data` 的新 Azure 蓝图。</span><span class="sxs-lookup"><span data-stu-id="509ac-190">Create a new Azure blueprint named `protected-data`.</span></span>
    2. <span data-ttu-id="509ac-191">将防火墙和 Azure 保管库模板添加到该蓝图。</span><span class="sxs-lookup"><span data-stu-id="509ac-191">Add the firewall and Azure Vault templates to the blueprint.</span></span>
    3. <span data-ttu-id="509ac-192">为受保护的数据订阅添加新策略。</span><span class="sxs-lookup"><span data-stu-id="509ac-192">Add the new policies for protected data subscriptions.</span></span>
    4. <span data-ttu-id="509ac-193">将蓝图发布到任何旨在托管任务关键型应用程序的管理组。</span><span class="sxs-lookup"><span data-stu-id="509ac-193">Publish the blueprint to any management group intended to host mission-critical applications.</span></span>
    5. <span data-ttu-id="509ac-194">将新蓝图应用于每个受影响的订阅以及现有蓝图。</span><span class="sxs-lookup"><span data-stu-id="509ac-194">Apply the new blueprint to each affected subscription as well as existing blueprints.</span></span>

## <a name="conclusion"></a><span data-ttu-id="509ac-195">结束语</span><span class="sxs-lookup"><span data-stu-id="509ac-195">Conclusion</span></span>

<span data-ttu-id="509ac-196">这些额外的流程和治理 MVP 的变化有助于缓解与资源治理相关的许多风险。</span><span class="sxs-lookup"><span data-stu-id="509ac-196">These additional processes and changes to the governance MVP help mitigate many of the risks associated with resource governance.</span></span> <span data-ttu-id="509ac-197">它们共同增加了恢复、大小调整和监视控件，从而实现了云感知操作。</span><span class="sxs-lookup"><span data-stu-id="509ac-197">Together they add recovery, sizing, and monitoring controls that empower cloud-aware operations.</span></span>

## <a name="next-steps"></a><span data-ttu-id="509ac-198">后续步骤</span><span class="sxs-lookup"><span data-stu-id="509ac-198">Next steps</span></span>

<span data-ttu-id="509ac-199">随着云采用的不断演进和业务附加值的提升，风险和云治理需求也在不断发展。</span><span class="sxs-lookup"><span data-stu-id="509ac-199">As cloud adoption continues to evolve and deliver additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="509ac-200">对于这一过程中的虚拟公司来说，下一个目标是部署到云上的资产超过 100 项，或者每月开销超过 1000 美元。</span><span class="sxs-lookup"><span data-stu-id="509ac-200">For the fictional company in this journey, the next trigger is when the scale of deployment exceeds 100 assets to the cloud or monthly spending exceeds $1,000 per month.</span></span> <span data-ttu-id="509ac-201">此时，云治理团队添加了“成本管理”控件。</span><span class="sxs-lookup"><span data-stu-id="509ac-201">At this point, the Cloud Governance team adds Cost Management controls.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="509ac-202">成本管理演变</span><span class="sxs-lookup"><span data-stu-id="509ac-202">Cost Management evolution</span></span>](./cost-management-evolution.md)