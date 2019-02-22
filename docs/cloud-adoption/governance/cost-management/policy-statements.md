---
title: CAF：成本管理示例策略语句
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 1/4/2019
description: 成本管理示例策略语句
author: BrianBlanchard
ms.openlocfilehash: 1c8b94ae5285fa26cdf9760892beaced2487af8b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900640"
---
# <a name="cost-management-sample-policy-statements"></a><span data-ttu-id="ef14b-103">成本管理示例策略语句</span><span class="sxs-lookup"><span data-stu-id="ef14b-103">Cost Management sample policy statements</span></span>

<span data-ttu-id="ef14b-104">各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。</span><span class="sxs-lookup"><span data-stu-id="ef14b-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="ef14b-105">这些语句应提供简要的风险摘要，以及应对它们的计划。</span><span class="sxs-lookup"><span data-stu-id="ef14b-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="ef14b-106">每个语句定义应包括以下这些信息：</span><span class="sxs-lookup"><span data-stu-id="ef14b-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="ef14b-107">业务风险 - 此策略将解决的风险的摘要。</span><span class="sxs-lookup"><span data-stu-id="ef14b-107">Business risk - A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="ef14b-108">策略语句 - 策略要求的清楚摘要说明。</span><span class="sxs-lookup"><span data-stu-id="ef14b-108">Policy statement - A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="ef14b-109">设计选项 - IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指导。</span><span class="sxs-lookup"><span data-stu-id="ef14b-109">Design options - Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="ef14b-110">以下示例策略语句解决一些常见的成本相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的实际策略语句时进行参考。</span><span class="sxs-lookup"><span data-stu-id="ef14b-110">The following sample policy statements address a number of common cost-related business risks, and are provided as examples for you to reference when drafting actual policy statements addressing your own organization's needs.</span></span> <span data-ttu-id="ef14b-111">这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何单个确定的风险。</span><span class="sxs-lookup"><span data-stu-id="ef14b-111">These examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any single identified risk.</span></span> <span data-ttu-id="ef14b-112">与业务和 IT 团队密切合作来确定适合于特定成本相关风险的最佳策略解决方案。</span><span class="sxs-lookup"><span data-stu-id="ef14b-112">Work closely with business and IT teams to identify the best policy solutions for your particular cost-related risks.</span></span>  

## <a name="future-proofing"></a><span data-ttu-id="ef14b-113">面向将来</span><span class="sxs-lookup"><span data-stu-id="ef14b-113">Future-proofing</span></span>

<span data-ttu-id="ef14b-114">**业务风险**。</span><span class="sxs-lookup"><span data-stu-id="ef14b-114">**Business risk**.</span></span> <span data-ttu-id="ef14b-115">不保证治理团队对成本管理规则的投资的当前条件。</span><span class="sxs-lookup"><span data-stu-id="ef14b-115">Current criteria that don't warrant an investment in a Cost Management discipline from the governance team.</span></span> <span data-ttu-id="ef14b-116">但是，预期会在将来进行这类投资。</span><span class="sxs-lookup"><span data-stu-id="ef14b-116">However, you anticipate such an investment in the future.</span></span>

<span data-ttu-id="ef14b-117">**策略语句**。</span><span class="sxs-lookup"><span data-stu-id="ef14b-117">**Policy statement**.</span></span> <span data-ttu-id="ef14b-118">应将部署到云的所有资产与记帐单位、应用程序/工作负荷相关联，并满足命名标准。</span><span class="sxs-lookup"><span data-stu-id="ef14b-118">You should associate all assets deployed to the cloud with a billing unit, application/workload, and meet naming standards.</span></span> <span data-ttu-id="ef14b-119">此策略将确保未来的成本管理工作是有效的。</span><span class="sxs-lookup"><span data-stu-id="ef14b-119">This policy will ensure that future Cost Management efforts will be effective.</span></span>

<span data-ttu-id="ef14b-120">**设计选项**。</span><span class="sxs-lookup"><span data-stu-id="ef14b-120">**Design options**.</span></span> <span data-ttu-id="ef14b-121">有关建立面向未来的基础的信息，请参阅[可操作的设计指南](../journeys/overview.md)（包含在 CAF 指导中）中与创建治理 MVP 相关的讨论。</span><span class="sxs-lookup"><span data-stu-id="ef14b-121">For information on establishing a future-proof foundation, see the discussions related to creating a governance MVP in the [actionable design guides](../journeys/overview.md) included as part of the CAF guidance.</span></span>

## <a name="budget-overruns"></a><span data-ttu-id="ef14b-122">预算溢出</span><span class="sxs-lookup"><span data-stu-id="ef14b-122">Budget overruns</span></span>

<span data-ttu-id="ef14b-123">**业务风险：** 自助服务部署形成超支风险。</span><span class="sxs-lookup"><span data-stu-id="ef14b-123">**Business risk:** Self-service deployment creates a risk of overspending.</span></span>

<span data-ttu-id="ef14b-124">**策略语句：** 必须将任何云部署分配给具有已批准的预算和预算限制机制的记帐单位。</span><span class="sxs-lookup"><span data-stu-id="ef14b-124">**Policy statement:** Any cloud deployment must be allocated to a billing unit with approved budget and a mechanism for budgetary limits.</span></span>

<span data-ttu-id="ef14b-125">**设计选项：** 在 Azure 中，可以使用 [Azure 成本管理](/azure/cost-management/manage-budgets)控制预算</span><span class="sxs-lookup"><span data-stu-id="ef14b-125">**Design options:** In Azure, budget can be controlled with [Azure Cost Management](/azure/cost-management/manage-budgets)</span></span>

## <a name="underutilization"></a><span data-ttu-id="ef14b-126">使用不足</span><span class="sxs-lookup"><span data-stu-id="ef14b-126">Underutilization</span></span>

<span data-ttu-id="ef14b-127">**业务风险：** 公司对云服务进行了预付，或购买了花费特定金额的包年套餐。</span><span class="sxs-lookup"><span data-stu-id="ef14b-127">**Business risk:** The company has prepaid for cloud services or has made an annual commitment to spend a specific amount.</span></span> <span data-ttu-id="ef14b-128">这样便存在未使用商定金额的风险，从而导致投资损失。</span><span class="sxs-lookup"><span data-stu-id="ef14b-128">There is a risk that the agreed upon amount won't be used, resulting in a lost investment.</span></span>

<span data-ttu-id="ef14b-129">**策略语句：** 分配了云预算的每个记帐单位会每年设置预算，每季度调整预算，以及每月分配时间以便评审计划支出与实际支出。</span><span class="sxs-lookup"><span data-stu-id="ef14b-129">**Policy statement:** Each billing unit with an allocated cloud budget will meet annually to set budgets, quarterly to adjust budgets, and monthly to allocate time for reviewing planned versus actual spending.</span></span> <span data-ttu-id="ef14b-130">每月与记帐单位主管讨论大于 20% 的任何偏差。</span><span class="sxs-lookup"><span data-stu-id="ef14b-130">Discuss any deviations greater than 20% with the billing unit leader monthly.</span></span> <span data-ttu-id="ef14b-131">为进行跟踪，将所有资产都分配给记帐单位。</span><span class="sxs-lookup"><span data-stu-id="ef14b-131">For tracking purposes, assign all assets to a billing unit.</span></span>

<span data-ttu-id="ef14b-132">**设计选项：**</span><span class="sxs-lookup"><span data-stu-id="ef14b-132">**Design options:**</span></span>

- <span data-ttu-id="ef14b-133">在 Azure 中，计划支出与实际支出可以通过 [Azure 成本管理](/azure/cost-management/quick-acm-cost-analysis)进行管理</span><span class="sxs-lookup"><span data-stu-id="ef14b-133">In Azure, planned versus actual spending can be managed via [Azure Cost Management](/azure/cost-management/quick-acm-cost-analysis)</span></span>
- <span data-ttu-id="ef14b-134">有多个选项用于按记帐单位对资源进行分组。</span><span class="sxs-lookup"><span data-stu-id="ef14b-134">There are several options for grouping resources by billing unit.</span></span> <span data-ttu-id="ef14b-135">在 Azure 中，应与治理团队协作选择[资源一致性模型](../../decision-guides/resource-consistency/overview.md)并应用于所有资产。</span><span class="sxs-lookup"><span data-stu-id="ef14b-135">In Azure, a [resource consistency model](../../decision-guides/resource-consistency/overview.md) should be chosen in conjunction with the governance team and applied to all assets.</span></span>

## <a name="overprovisioned-assets"></a><span data-ttu-id="ef14b-136">预配过度的资产</span><span class="sxs-lookup"><span data-stu-id="ef14b-136">Overprovisioned assets</span></span>

<span data-ttu-id="ef14b-137">**业务风险：** 在传统本地数据中心内，常见做法是在部署资产时针对遥远未来的增长进行额外容量规划。</span><span class="sxs-lookup"><span data-stu-id="ef14b-137">**Business risk:** In traditional on-premises datacenters, it is common practice to deploy assets with extra capacity planning for growth in the distant future.</span></span> <span data-ttu-id="ef14b-138">云的缩放速度可以比传统设备更快。</span><span class="sxs-lookup"><span data-stu-id="ef14b-138">The cloud can scale more quickly than traditional equipment.</span></span> <span data-ttu-id="ef14b-139">云中的资产还基于技术容量进行定价。</span><span class="sxs-lookup"><span data-stu-id="ef14b-139">Assets in the cloud are also priced based on the technical capacity.</span></span> <span data-ttu-id="ef14b-140">存在旧式本地做法人为地扩大云支出的风险。</span><span class="sxs-lookup"><span data-stu-id="ef14b-140">There is a risk of the old on-premises practice artificially inflating cloud spending.</span></span>

<span data-ttu-id="ef14b-141">**策略语句：** 任何部署到云的资产都必须在可以监视利用率并报告超过 50% 利用率的任何容量的程序中进行注册。</span><span class="sxs-lookup"><span data-stu-id="ef14b-141">**Policy statement:** Any asset deployed to the cloud must be enrolled in a program that can monitor utilization and report any capacity in excess of 50% of utilization.</span></span> <span data-ttu-id="ef14b-142">任何部署到云的资产都必须以逻辑方式进行分组或标记，使治理团队成员可以在预配过度资产的任何优化方面与工作负荷所有者进行接洽。</span><span class="sxs-lookup"><span data-stu-id="ef14b-142">Any asset deployed to the cloud must be grouped or tagged in a logical manner, so governance team members can engage the workload owner regarding any optimization of overprovisioned assets.</span></span>

<span data-ttu-id="ef14b-143">**设计选项：**</span><span class="sxs-lookup"><span data-stu-id="ef14b-143">**Design options:**</span></span>

- <span data-ttu-id="ef14b-144">在 Azure 中，[Azure 顾问](/azure/advisor/advisor-cost-recommendations)可以提供优化建议。</span><span class="sxs-lookup"><span data-stu-id="ef14b-144">In Azure, [Azure Advisor](/azure/advisor/advisor-cost-recommendations) can provide optimization recommendations.</span></span>
- <span data-ttu-id="ef14b-145">有多个选项用于按记帐单位对资源进行分组。</span><span class="sxs-lookup"><span data-stu-id="ef14b-145">There are several options for grouping resources by billing unit.</span></span> <span data-ttu-id="ef14b-146">在 Azure 中，应与治理团队协作选择[资源一致性模型](../../decision-guides/resource-consistency/overview.md)并应用于所有资产。</span><span class="sxs-lookup"><span data-stu-id="ef14b-146">In Azure, a [resource consistency model](../../decision-guides/resource-consistency/overview.md) should be chosen in conjunction with the governance team and applied to all assets.</span></span>

## <a name="overoptimization"></a><span data-ttu-id="ef14b-147">过度优化</span><span class="sxs-lookup"><span data-stu-id="ef14b-147">Overoptimization</span></span>

<span data-ttu-id="ef14b-148">**业务风险：** 实际上，有效的成本管理可能会形成新风险。</span><span class="sxs-lookup"><span data-stu-id="ef14b-148">**Business risk:** Effective cost management can actually create new risks.</span></span> <span data-ttu-id="ef14b-149">支出的优化与系统性能成反比。</span><span class="sxs-lookup"><span data-stu-id="ef14b-149">Optimization of spending is inverse to system performance.</span></span> <span data-ttu-id="ef14b-150">降低成本时，存在过度紧缩支出并形成糟糕的用户体验的风险。</span><span class="sxs-lookup"><span data-stu-id="ef14b-150">When reducing costs, there is a risk of overtightening spending and producing poor user experiences.</span></span>

<span data-ttu-id="ef14b-151">**策略语句：** 必须通过分组或标记来标识直接影响客户体验的任何资产。</span><span class="sxs-lookup"><span data-stu-id="ef14b-151">**Policy statement:** Any asset that directly affects customer experiences must be identified through grouping or tagging.</span></span> <span data-ttu-id="ef14b-152">优化影响客户体验的任何资产之前，云治理团队必须基于不少于 90 天的利用率趋势来调整优化。</span><span class="sxs-lookup"><span data-stu-id="ef14b-152">Before optimizing any asset that affects customer experience, the Cloud Governance team must adjust optimization based on no fewer than 90 days of utilization trends.</span></span> <span data-ttu-id="ef14b-153">优化资产时，记录任何季节性或事件驱动的突发情况。</span><span class="sxs-lookup"><span data-stu-id="ef14b-153">Document any seasonal or event driven bursts considered when optimizing assets.</span></span>

<span data-ttu-id="ef14b-154">**设计选项：**</span><span class="sxs-lookup"><span data-stu-id="ef14b-154">**Design options:**</span></span>

- <span data-ttu-id="ef14b-155">在 Azure 中，[Azure Monitor 的见解功能](/azure/azure-monitor/insights/vminsights-performance)可以帮助分析系统利用率。</span><span class="sxs-lookup"><span data-stu-id="ef14b-155">In Azure, [Azure Monitor's insights features](/azure/azure-monitor/insights/vminsights-performance) can help with analysis of system utilization.</span></span>
- <span data-ttu-id="ef14b-156">有多个选项用于基于角色对资源进行分组和标记。</span><span class="sxs-lookup"><span data-stu-id="ef14b-156">There are several options for grouping and tagging resources based on roles.</span></span> <span data-ttu-id="ef14b-157">在 Azure 中，应与治理团队协作选择[资源一致性模型](../../decision-guides/resource-consistency/overview.md)并将此模型应用于所有资产。</span><span class="sxs-lookup"><span data-stu-id="ef14b-157">In Azure, you should choose a [resource consistency model](../../decision-guides/resource-consistency/overview.md) in conjunction with the governance team and apply this to all assets.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ef14b-158">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ef14b-158">Next steps</span></span>

<span data-ttu-id="ef14b-159">使用本文中所述的示例作为起点，来制定解决特定业务风险并与云采用计划保持一致的策略。</span><span class="sxs-lookup"><span data-stu-id="ef14b-159">Use the samples mentioned in this article as a starting point to develop policies that address specific business risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="ef14b-160">若要开始制定与成本管理相关的自己的自定义策略语句，请下载[成本管理模板](template.md)。</span><span class="sxs-lookup"><span data-stu-id="ef14b-160">To begin developing your own custom policy statements related to Cost Management, download the [Cost Management template](template.md).</span></span>

<span data-ttu-id="ef14b-161">若要加速此规则的采用，请选择与你的环境最密切符合的[可操作的治理过程](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="ef14b-161">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="ef14b-162">随后修改设计以整合特定公司策略决策。</span><span class="sxs-lookup"><span data-stu-id="ef14b-162">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ef14b-163">可操作的治理过程</span><span class="sxs-lookup"><span data-stu-id="ef14b-163">Actionable Governance Journeys</span></span>](../journeys/overview.md)
