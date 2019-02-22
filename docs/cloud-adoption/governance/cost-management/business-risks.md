---
title: CAF：成本管理动机和业务风险
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 成本管理动机和业务风险
author: BrianBlanchard
ms.openlocfilehash: b9bf31a796ea1a7530c6a668a231d74b9b765509
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900548"
---
# <a name="cost-management-motivations-and-business-risks"></a><span data-ttu-id="b1566-103">成本管理动机和业务风险</span><span class="sxs-lookup"><span data-stu-id="b1566-103">Cost Management motivations and business risks</span></span>

<span data-ttu-id="b1566-104">本文讨论客户通常在云治理策略中采用成本管理规则的原因。</span><span class="sxs-lookup"><span data-stu-id="b1566-104">This article discusses the reasons that customers typically adopt a Cost Management discipline within a cloud governance strategy.</span></span> <span data-ttu-id="b1566-105">此外，还提供了驱动策略语句的几个示例业务风险。</span><span class="sxs-lookup"><span data-stu-id="b1566-105">It also provides a few examples of business risks that drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-cost-management-relevant"></a><span data-ttu-id="b1566-106">成本管理是否相关？</span><span class="sxs-lookup"><span data-stu-id="b1566-106">Is Cost Management relevant?</span></span>

<span data-ttu-id="b1566-107">在成本治理方面，云采用形成了范例转移。</span><span class="sxs-lookup"><span data-stu-id="b1566-107">In terms of cost governance, cloud adoption creates a paradigm shift.</span></span> <span data-ttu-id="b1566-108">传统本地环境中的成本管理基于刷新周期、数据中心购置、主机续订和定期维护问题。</span><span class="sxs-lookup"><span data-stu-id="b1566-108">Management of cost in a traditional on-premises world is based on refresh cycles, datacenter acquisitions, host renewals, and recurring maintenance issues.</span></span> <span data-ttu-id="b1566-109">可以预测、规划和优化其中每个成本，以便与每年资本支出预算保持一致。</span><span class="sxs-lookup"><span data-stu-id="b1566-109">You can forecast, plan, and refine each of these costs to align with annual capital expenditure budgets.</span></span>

<span data-ttu-id="b1566-110">对于云解决方案，许多企业倾向于采用更具响应性的成本管理方法。</span><span class="sxs-lookup"><span data-stu-id="b1566-110">For cloud solutions, many businesses tend to take a more reactive approach to Cost Management.</span></span> <span data-ttu-id="b1566-111">在许多情况下，企业会预先购买或承诺使用一定数量的云服务。</span><span class="sxs-lookup"><span data-stu-id="b1566-111">In many cases, businesses will prepurchase, or commit to use, a set amount of cloud services.</span></span> <span data-ttu-id="b1566-112">此模型假设，根据企业计划对特定云供应商的支出金额来最大化折扣会形成对主动计划成本周期的认知。</span><span class="sxs-lookup"><span data-stu-id="b1566-112">This model assumes that maximizing discounts, based on how much the business plans on spending with a specific cloud vendor, creates the perception of a proactive, planned cost cycle.</span></span> <span data-ttu-id="b1566-113">但是，仅当企业还实现了成熟的成本管理规则时，该认知才会成为现实。</span><span class="sxs-lookup"><span data-stu-id="b1566-113">However, that perception will only become a reality if the business also implements mature Cost Management disciplines.</span></span>

<span data-ttu-id="b1566-114">云提供了以前在传统本地数据中心内前所未闻的自助服务功能。</span><span class="sxs-lookup"><span data-stu-id="b1566-114">The cloud offers self-service capabilities that were previously unheard of in traditional on-premises datacenters.</span></span> <span data-ttu-id="b1566-115">这些新功能使企业在采用新技术方面可以更灵活、限制更少且更开放。</span><span class="sxs-lookup"><span data-stu-id="b1566-115">These new capabilities empower businesses to be more agile, less restrictive, and more open to adopt new technologies.</span></span> <span data-ttu-id="b1566-116">但是，自助服务的缺点是最终用户可能在不知不觉中超出分配的预算。</span><span class="sxs-lookup"><span data-stu-id="b1566-116">However, the downside of self-service is that end users can unknowingly exceed allocated budgets.</span></span> <span data-ttu-id="b1566-117">相反，相同的用户可能会遇到计划发生变化，意外地不使用预测的云服务量。</span><span class="sxs-lookup"><span data-stu-id="b1566-117">Conversely, the same users can experience a change in plans and unexpectedly not use the amount of cloud services forecasted.</span></span> <span data-ttu-id="b1566-118">任一方向的可能转变都证明在治理团队中投资成本管理规则是合理的。</span><span class="sxs-lookup"><span data-stu-id="b1566-118">The potential of shift in either direction justifies investment in a Cost Management discipline within the governance team.</span></span>

## <a name="business-risk"></a><span data-ttu-id="b1566-119">业务风险</span><span class="sxs-lookup"><span data-stu-id="b1566-119">Business risk</span></span>

<span data-ttu-id="b1566-120">成本管理规则尝试解决与托管基于云的工作负荷时所产生的费用相关的核心业务风险。</span><span class="sxs-lookup"><span data-stu-id="b1566-120">The Cost Management discipline attempts to address core business risks related to expenses incurred when hosting cloud-based workloads.</span></span> <span data-ttu-id="b1566-121">与企业合作来确定这些风险，并在规划和实现云部署时监视每个风险的相关性。</span><span class="sxs-lookup"><span data-stu-id="b1566-121">Work with your business to identify these risks and monitor each of them for relevance as you plan for and implement your cloud deployments.</span></span>

<span data-ttu-id="b1566-122">组织间的风险会有所不同，不过以下这些常见的成本相关风险可以用作在云治理团队中展开讨论的起点：</span><span class="sxs-lookup"><span data-stu-id="b1566-122">Risks will differ between organization, but the following serve as common cost-related risks that you can use as a starting point for discussions within your Cloud Governance team:</span></span>

- <span data-ttu-id="b1566-123">**预算控制**。</span><span class="sxs-lookup"><span data-stu-id="b1566-123">**Budget control**.</span></span> <span data-ttu-id="b1566-124">未控制预算可能会导致对云供应商过度支出。</span><span class="sxs-lookup"><span data-stu-id="b1566-124">Not controlling budget can lead to excessive spending with a cloud vendor.</span></span>
- <span data-ttu-id="b1566-125">**利用率损失**。</span><span class="sxs-lookup"><span data-stu-id="b1566-125">**Utilization loss**.</span></span> <span data-ttu-id="b1566-126">未使用的预先购买或预先承诺会形成投资浪费。</span><span class="sxs-lookup"><span data-stu-id="b1566-126">Prepurchases or precommitments that are not used can result in wasted investments.</span></span>
- <span data-ttu-id="b1566-127">**支出异常**：任一方向上的意外激增都可能指示不正确的使用情况。</span><span class="sxs-lookup"><span data-stu-id="b1566-127">**Spending anomalies**: Unexpected spikes in either direction can be indicators of improper usage.</span></span>
- <span data-ttu-id="b1566-128">**预配过度的资产**。</span><span class="sxs-lookup"><span data-stu-id="b1566-128">**Overprovisioned assets**.</span></span> <span data-ttu-id="b1566-129">如果在配置中部署的资产超过应用程序或虚拟机 (VM) 的需求，则它们可能会增加成本并形成浪费。</span><span class="sxs-lookup"><span data-stu-id="b1566-129">When assets are deployed in a configuration that exceed the needs of an application or virtual machine (VM), they can increase costs and create waste.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b1566-130">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b1566-130">Next steps</span></span>

<span data-ttu-id="b1566-131">使用[云管理模板](./template.md)，记录可能由当前云采用计划引入的业务风险。</span><span class="sxs-lookup"><span data-stu-id="b1566-131">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="b1566-132">确立了对实际业务风险的了解后，下一步是记录业务对风险的容忍度，以及用于监视该容忍度的指示器和关键指标。</span><span class="sxs-lookup"><span data-stu-id="b1566-132">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b1566-133">了解指示器、指标和风险容忍度</span><span class="sxs-lookup"><span data-stu-id="b1566-133">Understand indicators, metrics, and risk tolerance</span></span>](./metrics-tolerance.md)
