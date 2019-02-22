---
title: CAF：资源一致性示例策略语句
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 资源一致性示例策略语句
author: BrianBlanchard
ms.openlocfilehash: 9217ae73b0edf5b40bedac1cdc961b7a34da87d1
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900502"
---
# <a name="resource-consistency-sample-policy-statements"></a><span data-ttu-id="c5ea3-103">资源一致性示例策略语句</span><span class="sxs-lookup"><span data-stu-id="c5ea3-103">Resource Consistency sample policy statements</span></span>

<span data-ttu-id="c5ea3-104">各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="c5ea3-105">这些语句应提供简要的风险摘要，以及应对它们的计划。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="c5ea3-106">每个语句定义应包括以下这些信息：</span><span class="sxs-lookup"><span data-stu-id="c5ea3-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="c5ea3-107">技术风险 - 此策略将解决的风险的摘要。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-107">Technical risk - A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="c5ea3-108">策略语句 - 策略要求的清楚摘要说明。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-108">Policy statement - A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="c5ea3-109">设计选项 - IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指南。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-109">Design options - Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="c5ea3-110">以下示例策略语句解决一些常见的资源一致性相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的策略语句时进行参考。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-110">The following sample policy statements address a number of common Resource Consistency-related business risks, and are provided as examples for you to reference when drafting policy statements to address your own organization's needs.</span></span> <span data-ttu-id="c5ea3-111">请注意，这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何特定的风险。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-111">Note that these examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any particular risk.</span></span> <span data-ttu-id="c5ea3-112">与业务和 IT 团队密切合作来确定适合于独特风险集的最佳策略解决方案。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-112">Work closely with business and IT teams to identify the best policy solutions for your unique set of risks.</span></span>

## <a name="tagging"></a><span data-ttu-id="c5ea3-113">标记</span><span class="sxs-lookup"><span data-stu-id="c5ea3-113">Tagging</span></span>

<span data-ttu-id="c5ea3-114">**技术风险**：如果没有与已部署资源相关联的适当元数据标记，IT 运营就无法根据所需的 SLA、业务运营的重要性或运营成本来优先支持或优化资源。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-114">**Technical risk**: Without proper metadata tagging associated with deployed resources, IT Operations cannot prioritize support or optimization of resources based on required SLA, importance to business operations, or operational cost.</span></span> <span data-ttu-id="c5ea3-115">这可能导致 IT 资源的错误分配，并可能延误事件的解决。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-115">This can result in mis-allocation of IT resources and potential delays in incident resolution.</span></span>

<span data-ttu-id="c5ea3-116">**策略语句**：将实施以下策略：</span><span class="sxs-lookup"><span data-stu-id="c5ea3-116">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="c5ea3-117">部署的资产应使用以下值进行标记：成本、关键性、SLA 和环境。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-117">Deployed assets should be tagged with the following values: cost, criticality, SLA, and environment.</span></span>
- <span data-ttu-id="c5ea3-118">治理工具必须验证与成本、关键性、SLA、应用程序和环境相关的标记。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-118">Governance tooling must validate tagging related to cost, criticality, SLA, application, and environment.</span></span> <span data-ttu-id="c5ea3-119">所有值都必须与治理团队管理的预定义值保持一致。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-119">All values must align to predefined values managed by the governance team.</span></span>

<span data-ttu-id="c5ea3-120">**潜在的设计选项**：在 Azure 中，大多数资源类型都支持[标准名称-值元数据标记](/azure/azure-resource-manager/resource-group-using-tags)。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-120">**Potential design options**: In Azure, [standard name-value metadata tags](/azure/azure-resource-manager/resource-group-using-tags) are supported on most resource types.</span></span> <span data-ttu-id="c5ea3-121">[Azure Policy](/azure/governance/policy/overview) 用于在资源创建过程中强制执行特定标记。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-121">[Azure Policy](/azure/governance/policy/overview) is used to enforce specific tags as part of resource creation.</span></span>

## <a name="ungoverned-subscriptions"></a><span data-ttu-id="c5ea3-122">不受控制的订阅</span><span class="sxs-lookup"><span data-stu-id="c5ea3-122">Ungoverned subscriptions</span></span>

<span data-ttu-id="c5ea3-123">**技术风险**：任意创建订阅和管理组可能会导致云资产中存在独立的部分，这些部分不受治理策略的约束。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-123">**Technical risk**: Arbitrary creation of subscriptions and management groups can lead to isolated sections of your cloud estate that are not properly subject to your governance policies.</span></span>

<span data-ttu-id="c5ea3-124">**策略语句**：为任何任务关键型应用程序或受保护数据创建新的订阅或管理组将需要云治理团队的审核。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-124">**Policy statement**: Creation of new subscriptions or management groups for any mission-critical applications or protected data will require a review from the Cloud Governance team.</span></span> <span data-ttu-id="c5ea3-125">批准的变更将被集成到适当的蓝图分配中。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-125">Approved changes will be integrated into a proper blueprint assignment.</span></span>

<span data-ttu-id="c5ea3-126">**潜在的设计选项**：将对组织 [Azure 管理组](/azure/governance/management-groups/)的管理访问权限锁定为仅批准的治理团队成员，这些成员将控制订阅创建和访问控制流程。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-126">**Potential design options**: Lock down administrative access to your organizations [Azure management groups](/azure/governance/management-groups/) to only approved governance team members who will control the subscription creation and access control process.</span></span>

## <a name="manage-updates-to-virtual-machines"></a><span data-ttu-id="c5ea3-127">管理虚拟机更新</span><span class="sxs-lookup"><span data-stu-id="c5ea3-127">Manage updates to virtual machines</span></span>

<span data-ttu-id="c5ea3-128">**技术风险**：没有使用最新更新和软件修补程序保持处于最新状态的虚拟机 (VM) 容易受到安全问题或性能问题的影响，这可能会导致服务中断。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-128">**Technical risk**: Virtual machines (VMs) that are not up-to-date with the latest updates and software patches are vulnerable to security or performance issues, which can result in service disruptions.</span></span>

<span data-ttu-id="c5ea3-129">**策略语句**：治理工具必须强制执行在所有部署的 VM 上启用的自动更新。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-129">**Policy statement**: Governance tooling must enforce that automatic updates are enabled on all deployed VMs.</span></span> <span data-ttu-id="c5ea3-130">必须与运营管理团队协同审查违规行为，并根据运营策略进行补救。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-130">Violations must be reviewed with operational management teams and remediated in accordance with operations policies.</span></span> <span data-ttu-id="c5ea3-131">不会自动更新的资产必须包含在归 IT 运营的流程中。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-131">Assets that are not automatically updated must be included in processes owned by IT Operations.</span></span>

<span data-ttu-id="c5ea3-132">**潜在的设计选项**：对于 Azure 托管的 VM，可以使用 [Azure 自动化中的更新管理解决方案](/azure/automation/automation-update-management)提供一致的更新管理。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-132">**Potential design options**: For Azure hosted VMs, you can provide consistent update management using the [Update Management solution in Azure Automation](/azure/automation/automation-update-management).</span></span>

## <a name="deployment-compliance"></a><span data-ttu-id="c5ea3-133">部署符合性</span><span class="sxs-lookup"><span data-stu-id="c5ea3-133">Deployment compliance</span></span>

<span data-ttu-id="c5ea3-134">**技术风险**：如果部署脚本和自动化工具没有经过云治理团队的全面审查，则可能导致违反策略的资源部署。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-134">**Technical risk**: Deployment scripts and automation tooling that is not fully vetted by the Cloud Governance team can result in resource deployments that violate policy.</span></span>

<span data-ttu-id="c5ea3-135">**策略语句**：将实施以下策略：</span><span class="sxs-lookup"><span data-stu-id="c5ea3-135">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="c5ea3-136">部署工具必须得到云治理团队的批准，以确保对部署的资产进行持续管理。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-136">Deployment tooling must be approved by the Cloud Governance team to ensure ongoing governance of deployed assets.</span></span>
- <span data-ttu-id="c5ea3-137">必须在云治理团队可访问的中央存储库中维护部署脚本，以便进行定期检查和审核。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-137">Deployment scripts must be maintained in central repository accessible by the Cloud Governance team for periodic review and auditing.</span></span>

<span data-ttu-id="c5ea3-138">**潜在的设计选项**：持续使用 [Azure 蓝图](/azure/governance/blueprints/)来管理自动化部署可一致部署符合组织治理标准和策略的 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-138">**Potential design options**: Consistent use of [Azure Blueprints](/azure/governance/blueprints/) to manage automated deployments allows consistent deployments of Azure resources that adhere to your organization's governance standards and policies.</span></span>

## <a name="monitoring"></a><span data-ttu-id="c5ea3-139">监视</span><span class="sxs-lookup"><span data-stu-id="c5ea3-139">Monitoring</span></span>

<span data-ttu-id="c5ea3-140">**技术风险**：不正确地实现或不一致地检测监视会阻止检测工作负载运行状况问题或其他违反策略符合性的情况。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-140">**Technical risk**: Improperly implemented or inconsistently instrumented monitoring can prevent the detection of workload health issues or other policy compliance violations.</span></span>

<span data-ttu-id="c5ea3-141">**策略语句**：将实施以下策略：</span><span class="sxs-lookup"><span data-stu-id="c5ea3-141">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="c5ea3-142">治理工具必须确认将与任务关键型应用程序或受保护数据相关的所有资产均已纳入资源消耗和优化监视范围。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-142">Governance tooling must validate that all assets related to mission-critical applications or protected data are included in monitoring for resource depletion and optimization.</span></span>
- <span data-ttu-id="c5ea3-143">治理工具必须确认为所有任务关键型应用程序或受保护数据收集适当级别的日志记录数据。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-143">Governance tooling must validate that the appropriate level of logging data is being collected for all mission-critical applications or protected data.</span></span>

<span data-ttu-id="c5ea3-144">**潜在的设计选项**：[Azure Monitor](/azure/azure-monitor/overview) 是 Azure 平台中的默认监视服务，在部署资源时，可以通过使用 [Azure 蓝图](/azure/governance/blueprints/)强制执行一致的监视。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-144">**Potential design options**: [Azure Monitor](/azure/azure-monitor/overview) is the default monitoring service in the Azure platform, and consistent monitoring can be enforced through the use of [Azure Blueprints](/azure/governance/blueprints/) when deploying resources.</span></span>

## <a name="disaster-recovery"></a><span data-ttu-id="c5ea3-145">灾难恢复</span><span class="sxs-lookup"><span data-stu-id="c5ea3-145">Disaster recovery</span></span>

<span data-ttu-id="c5ea3-146">**技术风险**：资源故障、删除或损坏可能导致任务关键型应用程序或服务中断和敏感数据丢失。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-146">**Technical risk**: Resource failure, deletions, or corruption can result in disruption of mission-critical applications or services and the loss of sensitive data.</span></span>

<span data-ttu-id="c5ea3-147">**策略语句**：所有任务关键型应用程序和受保护的数据必须实现备份和恢复解决方案，以最大限度地减少中断或系统故障对业务的影响。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-147">**Policy statement**: All mission-critical applications and protected data must have backup and recovery solutions implemented to minimize business impact of outages or system failures.</span></span>

<span data-ttu-id="c5ea3-148">**潜在的设计选项**：[Azure Site Recovery] 提供了备份、恢复和复制功能，目的是在业务连续性和灾难恢复 (BCDR) 场景中最大限度地减少停机时间。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-148">**Potential design options**: The [Azure Site Recovery] service provides backup, recovery, and replication capabilities intended to minimize outage duration in business continuity and disaster recovery (BCDR) scenarios.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c5ea3-149">后续步骤</span><span class="sxs-lookup"><span data-stu-id="c5ea3-149">Next steps</span></span>

<span data-ttu-id="c5ea3-150">使用本文所述的示例作为起始点，制定策略，用于解决符合云采用计划的特定业务风险。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-150">Use the samples mentioned in this article as a starting point to develop policies that address specific business risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="c5ea3-151">要开始制定与资源一致性相关的自定义策略语句，请下载[资源一致性模板](template.md)。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-151">To begin developing your own custom policy statements related to Resource Consistency, download the [Resource Consistency template](template.md).</span></span>

<span data-ttu-id="c5ea3-152">若要加速此规则的采用，请选择与环境最密切符合的[可操作的治理过程](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-152">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="c5ea3-153">随后修改设计以整合特定公司策略决策。</span><span class="sxs-lookup"><span data-stu-id="c5ea3-153">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c5ea3-154">可操作的治理过程</span><span class="sxs-lookup"><span data-stu-id="c5ea3-154">Actionable Governance Journeys</span></span>](../journeys/overview.md)
