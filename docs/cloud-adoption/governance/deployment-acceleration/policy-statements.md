---
title: CAF：部署加速示例策略语句
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 部署加速示例策略语句
author: alexbuckgit
ms.openlocfilehash: 4f7d59e6653c29db03f966d1c7105524b72586fc
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900582"
---
# <a name="deployment-acceleration-sample-policy-statements"></a><span data-ttu-id="3bc7a-103">部署加速示例策略语句</span><span class="sxs-lookup"><span data-stu-id="3bc7a-103">Deployment Acceleration sample policy statements</span></span>

<span data-ttu-id="3bc7a-104">各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="3bc7a-105">这些语句应提供简要的风险摘要，以及应对它们的计划。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="3bc7a-106">每个语句定义应包括以下这些信息：</span><span class="sxs-lookup"><span data-stu-id="3bc7a-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="3bc7a-107">**技术风险。**</span><span class="sxs-lookup"><span data-stu-id="3bc7a-107">**Technical risk.**</span></span> <span data-ttu-id="3bc7a-108">此策略将解决的风险的摘要。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-108">A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="3bc7a-109">**策略语句。**</span><span class="sxs-lookup"><span data-stu-id="3bc7a-109">**Policy statement.**</span></span> <span data-ttu-id="3bc7a-110">策略要求的清楚摘要说明。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-110">A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="3bc7a-111">**设计选项。**</span><span class="sxs-lookup"><span data-stu-id="3bc7a-111">**Design options.**</span></span> <span data-ttu-id="3bc7a-112">IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指导。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-112">Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="3bc7a-113">以下示例策略语句解决一些常见的配置相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的策略语句时进行参考。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-113">The following sample policy statements address a number of common configuration-related business risks, and are provided as examples for you to reference when drafting policy statements to address your own organization's needs.</span></span> <span data-ttu-id="3bc7a-114">请注意，这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何特定的风险。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-114">Note that these examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any particular risk.</span></span> <span data-ttu-id="3bc7a-115">与业务和 IT 团队密切合作来确定适合于独特风险集的最佳策略解决方案。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-115">Work closely with business and IT teams to identify the best policy solutions for your unique set of risks.</span></span>

## <a name="reliance-on-manual-deployment-or-configuration-of-systems"></a><span data-ttu-id="3bc7a-116">依靠手动部署或系统配置</span><span class="sxs-lookup"><span data-stu-id="3bc7a-116">Reliance on manual deployment or configuration of systems</span></span>

<span data-ttu-id="3bc7a-117">**技术风险**：部署或配置过程中依赖于人工干预将增加人为错误的可能性，并降低系统部署和配置的可重复性和可预测性。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-117">**Technical risk**: Relying on human intervention during deployment or configuration increases the likelihood of human error and reduces the repeatability and predictability of system deployments and configuration.</span></span> <span data-ttu-id="3bc7a-118">它通常也会导致系统资源部署较慢。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-118">It also typically leads to slower deployment of system resources.</span></span>

<span data-ttu-id="3bc7a-119">**策略语句**：如果可能，所有部署到云的资产都应使用模板或自动化脚本进行部署。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-119">**Policy statement**: All assets deployed to the cloud should be deployed using templates or automation scripts whenever possible.</span></span>

<span data-ttu-id="3bc7a-120">**潜在的设计选项**：[Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)提供了将资源部署到 Azure 的基础结构即代码方法。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-120">**Potential design options**: [Azure Resource Manager templates](/azure/azure-resource-manager/resource-group-overview#template-deployment) provides an infrastructure-as-code approach to deploying your resources to Azure.</span></span> <span data-ttu-id="3bc7a-121">[Azure 构建基块](https://github.com/mspnp/template-building-blocks/wiki)提供命令行工具和资源管理器模板集，旨在简化 Azure 资源的部署。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-121">The [Azure Building Blocks](https://github.com/mspnp/template-building-blocks/wiki) provide a command-line tool and set of Resource Manager templates designed to simplify deployment of Azure resources.</span></span>

## <a name="lack-of-visibility-into-system-issues"></a><span data-ttu-id="3bc7a-122">缺少系统问题的可见性</span><span class="sxs-lookup"><span data-stu-id="3bc7a-122">Lack of visibility into system issues</span></span>

<span data-ttu-id="3bc7a-123">**技术风险**：对业务系统监视和诊断不足将阻止操作人员在系统故障发生前确定和修正问题，并能明显增加适当解决故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-123">**Technical risk**: Insufficient monitoring and diagnostics for business systems prevent operations personnel from identifying and remediating issues before a system outage occurs, and can significantly increase the time needed to properly resolve an outage.</span></span>

<span data-ttu-id="3bc7a-124">**策略语句**：将实施以下策略：</span><span class="sxs-lookup"><span data-stu-id="3bc7a-124">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="3bc7a-125">将为所有生产系统标识关键指标和诊断度量值，并且将对这些系统应用监视和诊断工具，由操作人员定期监视。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-125">Key metrics and diagnostics measures will be identified for all production systems and components, and monitoring and diagnostic tools will be applied to these systems and monitored regularly by operations personnel.</span></span>
- <span data-ttu-id="3bc7a-126">运营将考虑在非生产环境（例如暂存和质量保证）中使用监视和诊断工具，以在生产环境中发生系统问题时进行识别。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-126">Operations will consider using monitoring and diagnostic tools in non-production environments such as Staging and QA to identify system issues before they occur in the production environment.</span></span>

<span data-ttu-id="3bc7a-127">**潜在的设计选项**：[Azure Monitor](/azure/azure-monitor/)，其中还包含 Log Analytics 和 Application Insight，可提供用于收集和分析遥测数据的工具，以帮助了解应用程序性能如何并主动确定影响它们的问题，以及它们所依赖的资源。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-127">**Potential design options**: [Azure Monitor](/azure/azure-monitor/), which also includes Log Analytics and Application Insights, provides tools for collecting and analyzing telemetry to help you understand how your applications are performing and proactively identify issues affecting them and the resources they depend on.</span></span>

## <a name="configuration-security-reviews"></a><span data-ttu-id="3bc7a-128">配置安全评审</span><span class="sxs-lookup"><span data-stu-id="3bc7a-128">Configuration security reviews</span></span>

<span data-ttu-id="3bc7a-129">**技术风险**：随着时间推移，新的安全威胁或忧虑会增大对受保护资源未经授权访问的风险。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-129">**Technical risk**: Over time, new security threats or concerns can increase the risks of unauthorized access to secure resources.</span></span>

<span data-ttu-id="3bc7a-130">**策略语句**：云治理过程必须包括配置管理团队的季度审核，以标识云资产配置应该阻止的恶意执行组件或使用模式。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-130">**Policy statement**: Cloud Governance processes must include quarterly review with configuration management teams to identify malicious actors or usage patterns that should be prevented by cloud asset configuration.</span></span>

<span data-ttu-id="3bc7a-131">**潜在的设计选项**：确立季度安全评审会议，包括负责配置云应用程序和资源的治理团队成员和 IT 人员。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-131">**Potential design options**: Establish a quarterly security review meeting that includes both governance team members and IT staff responsible for configuration cloud applications and resources.</span></span> <span data-ttu-id="3bc7a-132">评审现有安全数据和指标以发现当前部署加速策略和工具中的缺口，并更新策略以缓解任何新风险。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-132">Review existing security data and metrics to establish gaps in current Deployment Acceleration policy and tooling, and update policy to mitigate any new risks.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3bc7a-133">后续步骤</span><span class="sxs-lookup"><span data-stu-id="3bc7a-133">Next steps</span></span>

<span data-ttu-id="3bc7a-134">使用本文中所述的示例作为起点，来制定解决特定业务风险并与云采用计划保持一致的策略。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-134">Use the samples mentioned in this article as a starting point to develop policies that address specific business risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="3bc7a-135">若要开始制定与标识管理相关的自己的自定义策略语句，请下载[标识基线模板](template.md)。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-135">To begin developing your own custom policy statements related to identity management, download the [Identity Baseline template](template.md).</span></span>

<span data-ttu-id="3bc7a-136">若要加速此规则的采用，请选择与你的环境最密切符合的[可操作的治理过程](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-136">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="3bc7a-137">随后修改设计以整合特定公司策略决策。</span><span class="sxs-lookup"><span data-stu-id="3bc7a-137">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3bc7a-138">可操作的治理过程</span><span class="sxs-lookup"><span data-stu-id="3bc7a-138">Actionable Governance Journeys</span></span>](../journeys/overview.md)