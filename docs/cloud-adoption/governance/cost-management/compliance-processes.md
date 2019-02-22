---
title: CAF：成本管理策略符合性过程
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 成本管理策略符合性过程
author: BrianBlanchard
ms.openlocfilehash: 5fd007546589020f376107382c54c391cc5d05ad
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900471"
---
# <a name="cost-management-policy-compliance-processes"></a><span data-ttu-id="80bbc-103">成本管理策略符合性过程</span><span class="sxs-lookup"><span data-stu-id="80bbc-103">Cost Management policy compliance processes</span></span>

<span data-ttu-id="80bbc-104">本文介绍一种方法，用于创建支持[成本管理](./overview.md)治理规则的流程。</span><span class="sxs-lookup"><span data-stu-id="80bbc-104">This article discusses an approach to creating processes that support a [Cost Management](./overview.md) governance discipline.</span></span> <span data-ttu-id="80bbc-105">云成本的有效治理方法从重复执行的手动流程开始，旨在支持策略符合性。</span><span class="sxs-lookup"><span data-stu-id="80bbc-105">Effective governance of cloud costs starts with recurring manual processes designed to support policy compliance.</span></span> <span data-ttu-id="80bbc-106">这需要云治理团队和业务利益干系人的定期参与，以查看和更新策略，并确保策略符合性。</span><span class="sxs-lookup"><span data-stu-id="80bbc-106">This requires regular involvement of the Cloud Governance team and interested business stakeholders to review and update policy and ensure policy compliance.</span></span> <span data-ttu-id="80bbc-107">此外，还可以使用工具自动处理或补充许多进行中的监视和强制流程，以减少治理开销并允许对策略偏差的实现更快响应。</span><span class="sxs-lookup"><span data-stu-id="80bbc-107">In addition, many ongoing monitoring and enforcement processes can be automated or supplemented with tooling to reduce the overhead of governance and allow for faster response to policy deviation.</span></span>

## <a name="planning-review-and-reporting-processes"></a><span data-ttu-id="80bbc-108">计划、评审和报告流程</span><span class="sxs-lookup"><span data-stu-id="80bbc-108">Planning, review, and reporting processes</span></span>

<span data-ttu-id="80bbc-109">云中的最佳成本管理工具与它们所支持的流程和策略一样优秀。</span><span class="sxs-lookup"><span data-stu-id="80bbc-109">The best Cost Management tools in the cloud are only as good as the processes and policies that they support.</span></span> <span data-ttu-id="80bbc-110">以下是成本管理规则中通常涉及的一组示例流程。</span><span class="sxs-lookup"><span data-stu-id="80bbc-110">The following is a set of example processes commonly involved in the Cost Management discipline.</span></span> <span data-ttu-id="80bbc-111">当计划流程时，请将这些示例作为起始点，通过这些流程，可根据业务变更和业务团队的反馈（以成本治理指南为准）继续更新成本策略。</span><span class="sxs-lookup"><span data-stu-id="80bbc-111">Use these examples as a starting point when planning the processes that will allow you to continue to update cost policy based on business change and feedback from the business teams subject to cost governance guidance.</span></span>

<span data-ttu-id="80bbc-112">**初始风险评估和计划**：作为初始采用成本管理规则的一部分，请确定与云成本相关的核心业务风险和容差。</span><span class="sxs-lookup"><span data-stu-id="80bbc-112">**Initial risk assessment and planning**: As part of your initial adoption of the Cost Management discipline, identify your core business risks and tolerances related to cloud costs.</span></span> <span data-ttu-id="80bbc-113">利用此信息与业务团队成员讨论预算和成本相关风险，并制定一组基准策略以降低这些风险，从而建立初始治理策略。</span><span class="sxs-lookup"><span data-stu-id="80bbc-113">Use this information to discuss budget and cost-related risks with members of your business teams and develop a baseline set of policies for mitigating these risks to establish your initial governance strategy.</span></span>

<span data-ttu-id="80bbc-114">**部署计划：** 在部署任何资产前，请根据预期的云分配建立预测预算。</span><span class="sxs-lookup"><span data-stu-id="80bbc-114">**Deployment planning:** Prior to deployment of any asset, establish a forecasted budget based on expected cloud allocation.</span></span> <span data-ttu-id="80bbc-115">请确保部署的所有权和记帐信息都记录在案。</span><span class="sxs-lookup"><span data-stu-id="80bbc-115">Ensure that ownership and accounting information for the deployment is documented.</span></span>  

<span data-ttu-id="80bbc-116">**年度计划：** 每年，对所有已部署和要部署的资产进行汇总分析。</span><span class="sxs-lookup"><span data-stu-id="80bbc-116">**Annual planning:** On an annual basis, perform a roll-up analysis on all deployed and to-be-deployed assets.</span></span> <span data-ttu-id="80bbc-117">使业务部门、各部门、团队和其他相应部分的预算保持一致，以实现自助采用。</span><span class="sxs-lookup"><span data-stu-id="80bbc-117">Align budgets by business units, departments, teams, and other appropriate divisions to empower self-service adoption.</span></span> <span data-ttu-id="80bbc-118">请确保每个账单单位负责人都了解预算以及跟踪支出的方法。</span><span class="sxs-lookup"><span data-stu-id="80bbc-118">Ensure that the leader of each billing unit is aware of the budget and how to track spending.</span></span>

<span data-ttu-id="80bbc-119">此时，应该做出预先承诺或预先购买，以最大程度地获得折扣。</span><span class="sxs-lookup"><span data-stu-id="80bbc-119">This is the time to make a precommitment or prepurchase to maximize discounting.</span></span> <span data-ttu-id="80bbc-120">最好将年度预算与云供应商的财年保持一致，以将年末折扣方案进一步转换为利润。</span><span class="sxs-lookup"><span data-stu-id="80bbc-120">It is wise to align annual budgeting with the cloud vendor's fiscal year to further capitalize on year-end discount options.</span></span>

<span data-ttu-id="80bbc-121">**季度计划：** 每个季度与每个账单单位负责人一起审核预算，调整预测和实际支出。</span><span class="sxs-lookup"><span data-stu-id="80bbc-121">**Quarterly planning:** On a quarterly basis, review budgets with each billing unit leader to align forecast and actual spending.</span></span> <span data-ttu-id="80bbc-122">如果计划出现变更或为意外支出模式，请调整预算并对其重新分配。</span><span class="sxs-lookup"><span data-stu-id="80bbc-122">If there are changes to the plan or unexpected spending patterns, align and reallocate the budget.</span></span>

<span data-ttu-id="80bbc-123">这种季度性计划流程也是一个很好的时机，用于评估云治理团队的当前成员身份，了解与当前或将来业务计划相关的知识缺口。</span><span class="sxs-lookup"><span data-stu-id="80bbc-123">This quarterly planning process is also a good time to evaluate the current membership of your Cloud Governance team for knowledge gaps related to current or future business plans.</span></span> <span data-ttu-id="80bbc-124">邀请相关人员和工作负荷所有者作为临时顾问或团队的常任成员参与审核和计划。</span><span class="sxs-lookup"><span data-stu-id="80bbc-124">Invite relevant staff and workload owners to participate in reviews and planning as either temporary advisors or permanent members of your team.</span></span>

<span data-ttu-id="80bbc-125">**教育和培训**：每两个月提供一次培训课程，确保业务和 IT 人员了解最新的成本管理策略要求。</span><span class="sxs-lookup"><span data-stu-id="80bbc-125">**Education and Training**: On a bi-monthly basis, offer training sessions to make sure business and IT staff are up-to-date on the latest Cost Management policy requirements.</span></span> <span data-ttu-id="80bbc-126">作为此流程的一部分，请审核并更新所有文档、指南或其他培训资产，确保其与最新的公司策略声明保持同步。</span><span class="sxs-lookup"><span data-stu-id="80bbc-126">As part of this process review and update any documentation, guidance, or other training assets to ensure they are in sync with the latest corporate policy statements.</span></span>

<span data-ttu-id="80bbc-127">**月度报告：** 每月根据预测报告实际支出。</span><span class="sxs-lookup"><span data-stu-id="80bbc-127">**Monthly reporting:** On a monthly basis, report actual spending against forecast.</span></span> <span data-ttu-id="80bbc-128">通知账单负责人任何意外偏差。</span><span class="sxs-lookup"><span data-stu-id="80bbc-128">Notify billing leaders of any unexpected deviations.</span></span>

<span data-ttu-id="80bbc-129">这些基本流程有助于使支出保持一致，并建立成本管理规则的基础。</span><span class="sxs-lookup"><span data-stu-id="80bbc-129">These basic processes will help align spending and establish a foundation for the Cost Management discipline.</span></span>

## <a name="ongoing-monitoring-processes"></a><span data-ttu-id="80bbc-130">持续的监视流程</span><span class="sxs-lookup"><span data-stu-id="80bbc-130">Ongoing monitoring processes</span></span>

<span data-ttu-id="80bbc-131">功能的成本管理治理策略取决于对过去、当前和计划内的将来云相关支出的可见性。</span><span class="sxs-lookup"><span data-stu-id="80bbc-131">A successful Cost Management governance strategy depends on visibility into the past, current, and planned future cloud-related spending.</span></span> <span data-ttu-id="80bbc-132">如果无法分析现有成本的相关指标和数据，则无法识别风险中的变化或检测风险容差违规的情况。</span><span class="sxs-lookup"><span data-stu-id="80bbc-132">Without the ability to analyze the relevant metrics and data of your existing costs, you cannot identify changes in your risks or detect violations of your risk tolerances.</span></span> <span data-ttu-id="80bbc-133">上文所述的持续治理流程要求质量数据，以确保可以修改策略，从而更好地针对不断变化的业务要求和云使用情况保护基础结构。</span><span class="sxs-lookup"><span data-stu-id="80bbc-133">The ongoing governance processes discussed above require quality data to ensure policy can be modified to better protect your infrastructure against changing business requirements and cloud usage.</span></span>

<span data-ttu-id="80bbc-134">确保你的 IT 团队已实现自动化系统，用于监视云支出以及预期成本中出现的计划外偏差。</span><span class="sxs-lookup"><span data-stu-id="80bbc-134">Ensure that your IT teams have implemented automated systems for monitoring your cloud spending and usage for unplanned deviations from expected costs.</span></span> <span data-ttu-id="80bbc-135">建立报告和警报系统，以确保潜在策略违规的提示检测和风险缓解。</span><span class="sxs-lookup"><span data-stu-id="80bbc-135">Establish reporting and alerting systems to ensure prompt detection and mitigation of potential policy violations.</span></span>

## <a name="compliance-violation-triggers-and-enforcement-actions"></a><span data-ttu-id="80bbc-136">符合性违规触发器和强制执行操作</span><span class="sxs-lookup"><span data-stu-id="80bbc-136">Compliance violation triggers and enforcement actions</span></span>

<span data-ttu-id="80bbc-137">检测到违规时，应采取强制措施重新调整策略。</span><span class="sxs-lookup"><span data-stu-id="80bbc-137">When violations are detected, you should take enforcement actions to realign with policy.</span></span> <span data-ttu-id="80bbc-138">可以使用[适用于 Azure 的成本管理工具链](toolchain.md)中概述的工具自动执行大多数违规触发器。</span><span class="sxs-lookup"><span data-stu-id="80bbc-138">You can automate most violation triggers using the tools outlined in the [Cost Management toolchain for Azure](toolchain.md).</span></span>

<span data-ttu-id="80bbc-139">下面是触发器的示例：</span><span class="sxs-lookup"><span data-stu-id="80bbc-139">The following are examples of triggers:</span></span>

* <span data-ttu-id="80bbc-140">每月预算偏差：与账单单位负责人讨论每月支出的预测实际比率超出 20% 的任何偏差。</span><span class="sxs-lookup"><span data-stu-id="80bbc-140">Monthly budget deviations: Discuss any deviations in monthly spending that exceed 20% forecast-versus-actual ratio with the billing unit leader.</span></span> <span data-ttu-id="80bbc-141">在预测中记录解决方法和变化。</span><span class="sxs-lookup"><span data-stu-id="80bbc-141">Record resolutions and changes in forecast.</span></span>
* <span data-ttu-id="80bbc-142">采用步伐：在订阅级别超过 20% 的任何偏差都触发账单单位负责人评审。</span><span class="sxs-lookup"><span data-stu-id="80bbc-142">Pace of adoption: Any deviation at a subscription level exceeding 20% will trigger a review with billing unit leader.</span></span> <span data-ttu-id="80bbc-143">在预测中记录解决方法和变化。</span><span class="sxs-lookup"><span data-stu-id="80bbc-143">Record resolutions and changes in forecast.</span></span>

## <a name="next-steps"></a><span data-ttu-id="80bbc-144">后续步骤</span><span class="sxs-lookup"><span data-stu-id="80bbc-144">Next steps</span></span>

<span data-ttu-id="80bbc-145">使用[云管理模板](./template.md)，记录与当前云采用计划保持一致的流程和触发器。</span><span class="sxs-lookup"><span data-stu-id="80bbc-145">Using the [Cloud Management template](./template.md), document the processes and triggers that align to the current cloud adoption plan.</span></span>

<span data-ttu-id="80bbc-146">有关根据采用计划执行云管理策略的指南，请参阅有关成本管理规则改进的文章。</span><span class="sxs-lookup"><span data-stu-id="80bbc-146">For guidance on executing cloud management policies in alignment with adoption plans, see the article on Cost Management discipline improvement.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="80bbc-147">成本管理规则改进</span><span class="sxs-lookup"><span data-stu-id="80bbc-147">Cost Management discipline improvement</span></span>](./discipline-improvement.md)
