---
title: CAF：成本管理指标、指示器和风险容忍度
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 与云治理相关的成本管理说明
author: BrianBlanchard
ms.openlocfilehash: 76e6b1b32dd862322f6cafd9aa63c6c4f79f4f5d
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241728"
---
# <a name="cost-management-metrics-indicators-and-risk-tolerance"></a><span data-ttu-id="3ec48-103">成本管理指标、指示器和风险容忍度</span><span class="sxs-lookup"><span data-stu-id="3ec48-103">Cost Management metrics, indicators, and risk tolerance</span></span>

<span data-ttu-id="3ec48-104">本文旨在量化业务风险容忍度，因为它与成本管理相关。</span><span class="sxs-lookup"><span data-stu-id="3ec48-104">This article is intended to help you quantify business risk tolerance as it relates to Cost Management.</span></span> <span data-ttu-id="3ec48-105">定义指标和指示器有助于，创建在完善成本管理规范方面进行投资的业务案例。</span><span class="sxs-lookup"><span data-stu-id="3ec48-105">Defining metrics and indicators helps you create a business case for making an investment in the maturity of the Cost Management discipline.</span></span>

## <a name="metrics"></a><span data-ttu-id="3ec48-106">度量值</span><span class="sxs-lookup"><span data-stu-id="3ec48-106">Metrics</span></span>

<span data-ttu-id="3ec48-107">成本管理通常侧重于与成本相关的指标。</span><span class="sxs-lookup"><span data-stu-id="3ec48-107">Cost Management generally focuses on metrics related to costs.</span></span> <span data-ttu-id="3ec48-108">在风险分析过程中，建议收集与当前和计划用于基于云的工作负载的支出相关的数据，以确定所面临的风险大小，以及在成本治理方面的投资对云采用策略有多重要。</span><span class="sxs-lookup"><span data-stu-id="3ec48-108">As part of your risk analysis, you'll want to gather data related to your current and planned spending on cloud-based workloads to determine how much risk you face, and how important investment in cost governance is to your cloud adoption strategy.</span></span>

<span data-ttu-id="3ec48-109">以下是应收集的一些实用指标示例，因为它们有助于在安全基线规范中评估风险容忍度：</span><span class="sxs-lookup"><span data-stu-id="3ec48-109">The following are examples of useful metrics that you should gather to help evaluate risk tolerance within the Security Baseline discipline:</span></span>

- <span data-ttu-id="3ec48-110">年度支出：云提供商提供的服务的年度总成本</span><span class="sxs-lookup"><span data-stu-id="3ec48-110">Annual spending: The total annual cost for services provided by a cloud provider</span></span>
- <span data-ttu-id="3ec48-111">月度支出：云提供商提供的服务的月度总成本</span><span class="sxs-lookup"><span data-stu-id="3ec48-111">Monthly spending: The total monthly cost for services provided by a cloud provider</span></span>
- <span data-ttu-id="3ec48-112">预测支出与实际支出的比率：比较预测支出与实际支出（月度或年度）的比率</span><span class="sxs-lookup"><span data-stu-id="3ec48-112">Forecasted versus actual ratio: The ratio comparing forecasted and actual spending (monthly or annual)</span></span>
- <span data-ttu-id="3ec48-113">采用速度 (MOM) 比率：云成本逐月增量所占的百分比</span><span class="sxs-lookup"><span data-stu-id="3ec48-113">Pace of adoption (MOM) ratio: The percentage of the delta in cloud costs from month to month</span></span>
- <span data-ttu-id="3ec48-114">累计成本：从当月月初开始累计的每日支出总额</span><span class="sxs-lookup"><span data-stu-id="3ec48-114">Accumulated cost: Total accrued daily spending, starting from the beginning of the month</span></span>
- <span data-ttu-id="3ec48-115">支出趋势：对照预算的支出趋势</span><span class="sxs-lookup"><span data-stu-id="3ec48-115">Spending trends: Spending trend against the budget</span></span>

## <a name="risk-tolerance-indicators"></a><span data-ttu-id="3ec48-116">风险容忍度指示器</span><span class="sxs-lookup"><span data-stu-id="3ec48-116">Risk tolerance indicators</span></span>

<span data-ttu-id="3ec48-117">在早期部署（如开发/测试或试验首次工作负载）期间，成本管理的风险可能相对较低。</span><span class="sxs-lookup"><span data-stu-id="3ec48-117">During early deployments, such as Dev/Test or experimental first workloads, Cost Management is likely to be of relatively low risk.</span></span> <span data-ttu-id="3ec48-118">随着部署的资产越来越多，风险会越来越高，而企业的风险容忍度可能会下降。</span><span class="sxs-lookup"><span data-stu-id="3ec48-118">As more assets are deployed, the risk grows and the business' tolerance for risk is likely to decline.</span></span> <span data-ttu-id="3ec48-119">此外，随着越来越多的云采用团队能够将资产配置或部署到云中，也会导致风险越来越高，风险容忍度越来越低。</span><span class="sxs-lookup"><span data-stu-id="3ec48-119">Additionally, as more cloud adoption teams are given the ability to configure or deploy assets to the cloud, the risk grows and tolerance decreases.</span></span> <span data-ttu-id="3ec48-120">反过来说，形成成本管理规范会让处于云采用阶段的人员部署更多创新型新技术。</span><span class="sxs-lookup"><span data-stu-id="3ec48-120">Conversely, growing a Cost Management discipline will take people from the cloud adoption phase to deploy more innovative new technologies.</span></span>

<span data-ttu-id="3ec48-121">在云采用的早期阶段，你将与企业一起确定风险容忍度基线。</span><span class="sxs-lookup"><span data-stu-id="3ec48-121">In the early stages of cloud adoption, you will work with your business to determine a risk tolerance baseline.</span></span> <span data-ttu-id="3ec48-122">确定基线后，便需要确定触发在成本管理规范方面投资的条件。</span><span class="sxs-lookup"><span data-stu-id="3ec48-122">Once you have a baseline, you will need to determine the criteria that would trigger an investment in the Cost Management discipline.</span></span> <span data-ttu-id="3ec48-123">这些条件可能因组织而异。</span><span class="sxs-lookup"><span data-stu-id="3ec48-123">These criteria will likely be different for every organization.</span></span>

<span data-ttu-id="3ec48-124">确定[业务风险](./business-risks.md)后，便要与企业一起确定基准，用来确定可能会增加这些风险的触发器。</span><span class="sxs-lookup"><span data-stu-id="3ec48-124">Once you have identified [business risks](./business-risks.md), you will work with your business to identify benchmarks that you can use to identify triggers that could potentially increase those risks.</span></span> <span data-ttu-id="3ec48-125">下面的一些示例展示了如何比较指标（如上所述）与风险基线容忍度，以判断企业是否需要进一步在成本管理方面投资。</span><span class="sxs-lookup"><span data-stu-id="3ec48-125">The following are a few examples of how metrics, such as those mentioned above, can be compared against your risk baseline tolerance to indicate your business's need to further invest in Cost Management.</span></span>

- <span data-ttu-id="3ec48-126">承诺驱动（最常见）：某公司承诺今年用于云供应商的支出为 X,000,000 美元。</span><span class="sxs-lookup"><span data-stu-id="3ec48-126">Commitment-driven (most common): A company that is committed to spending $X,000,000 this year on a cloud vendor.</span></span> <span data-ttu-id="3ec48-127">他们需要采用成本管理规范，以确保企业超过支出目标的增幅少于 20%，且至少支出承诺金额的 90%。</span><span class="sxs-lookup"><span data-stu-id="3ec48-127">They need a Cost Management discipline to ensure that the business doesn't exceed its spending targets by more than 20%, and that they will use at least 90% of that commitment.</span></span>
- <span data-ttu-id="3ec48-128">百分比触发器：某公司的云支出对于自己的生产系统而言很稳定。</span><span class="sxs-lookup"><span data-stu-id="3ec48-128">Percentage trigger: A company with cloud spending that is stable for their production systems.</span></span> <span data-ttu-id="3ec48-129">如果变化超过 X%，在成本管理规范方面投资将是明智之举。</span><span class="sxs-lookup"><span data-stu-id="3ec48-129">If that changes by more that X%, then a Cost Management discipline will be a wise investment.</span></span>
- <span data-ttu-id="3ec48-130">过度预配触发器：某公司认为自己部署的解决方案存在预配过度的现象。</span><span class="sxs-lookup"><span data-stu-id="3ec48-130">Overprovisioned trigger: A company who believes their deployed solutions are overprovisioned.</span></span> <span data-ttu-id="3ec48-131">在能够证明预配和资产利用率的调整适当之前，他们优先要在成本管理方面投资。</span><span class="sxs-lookup"><span data-stu-id="3ec48-131">Cost Management is a priority investment until they can demonstrate proper alignment of provisioning and asset utilization.</span></span>
- <span data-ttu-id="3ec48-132">月度支出触发器：某公司每月支出超过 x,000 美元就会被视为成本相当高。</span><span class="sxs-lookup"><span data-stu-id="3ec48-132">Monthly spending trigger: A company that spends over $x,000 per month is considered a sizable cost.</span></span> <span data-ttu-id="3ec48-133">如果给定月份的支出超过此金额，他们就需要在成本管理方面投资。</span><span class="sxs-lookup"><span data-stu-id="3ec48-133">If spending exceeds that amount in a given month, they will need to invest in Cost Management.</span></span>
- <span data-ttu-id="3ec48-134">年度支出触发器：某公司的 IT 研发预算允许每年在云试验上支出 X,000 美元。</span><span class="sxs-lookup"><span data-stu-id="3ec48-134">Annual spending trigger: A company with an IT R&D budget that allows for spending $X,000 per year on cloud experimentation.</span></span> <span data-ttu-id="3ec48-135">他们可能会在云中运行生产工作负载，但它们仍被视为试验解决方案，前提是未超过此预算金额。</span><span class="sxs-lookup"><span data-stu-id="3ec48-135">They may run production workloads in the cloud, but they will still be considered experimental solutions if the budget doesn't exceed that amount.</span></span> <span data-ttu-id="3ec48-136">一旦超过预算，他们就需要像对待生产投资一样对待预算，并密切管理支出。</span><span class="sxs-lookup"><span data-stu-id="3ec48-136">Once it goes over, they will need to treat the budget like a production investment and manage spending closely.</span></span>
- <span data-ttu-id="3ec48-137">运营支出不利（不常见）：某公司的运营支出非常不利，需要先制定成本管理控制措施，再部署开发/测试工作负载。</span><span class="sxs-lookup"><span data-stu-id="3ec48-137">OpEx adverse (uncommon): As a company, they are very OpEx adverse and will need Cost Management controls in place before deploying a dev/test workload.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3ec48-138">后续步骤</span><span class="sxs-lookup"><span data-stu-id="3ec48-138">Next steps</span></span>

<span data-ttu-id="3ec48-139">使用[云管理模板](./template.md)，记录与当前云采用计划保持一致的指标和容忍度指示器。</span><span class="sxs-lookup"><span data-stu-id="3ec48-139">Using the [Cloud Management template](./template.md), document metrics and tolerance indicators that align to the current cloud adoption plan.</span></span>

<span data-ttu-id="3ec48-140">在风险和容忍度基础上，建立治理和沟通成本管理策略遵循情况的流程。</span><span class="sxs-lookup"><span data-stu-id="3ec48-140">Building on risks and tolerance, establish a process for governing and communicating Cost Management policy adherence.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3ec48-141">建立策略合规性流程</span><span class="sxs-lookup"><span data-stu-id="3ec48-141">Establish policy compliance processes</span></span>](compliance-processes.md)
