---
title: CAF：大型企业 - 治理策略背后的初始公司策略
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: 大型企业 - 治理策略背后的初始公司策略。
author: BrianBlanchard
ms.openlocfilehash: d3bc31f95229a82cbc2f6ac6e684a87107783c07
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900628"
---
# <a name="large-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a><span data-ttu-id="08d4c-103">大型企业：治理策略背后的初始公司策略</span><span class="sxs-lookup"><span data-stu-id="08d4c-103">Large enterprise: Initial corporate policy behind the governance strategy</span></span>

<span data-ttu-id="08d4c-104">下面的公司策略定义了初始治理位置，即此过程的起点。</span><span class="sxs-lookup"><span data-stu-id="08d4c-104">The following corporate policy defines the initial governance position, which is the starting point for this journey.</span></span> <span data-ttu-id="08d4c-105">本文定义了早期风险、初始策略声明和强制执行策略声明的早期流程。</span><span class="sxs-lookup"><span data-stu-id="08d4c-105">This article defines early-stage risks, initial policy statements, and early processes to enforce policy statements.</span></span>

> [!NOTE]
><span data-ttu-id="08d4c-106">公司策略不是技术文档，但它将推动许多技术决策。</span><span class="sxs-lookup"><span data-stu-id="08d4c-106">The corporate policy is not a technical document, but it drives many technical decisions.</span></span> <span data-ttu-id="08d4c-107">[概述](./overview.md)中所描述的治理 MVP 根本上源于此策略。</span><span class="sxs-lookup"><span data-stu-id="08d4c-107">The governance MVP described in the [overview](./overview.md) ultimately derives from this policy.</span></span> <span data-ttu-id="08d4c-108">组织应该先基于自己的目标和业务风险制定公司策略，然后再实施治理 MVP。</span><span class="sxs-lookup"><span data-stu-id="08d4c-108">Before implementing a governance MVP, your organization should develop a corporate policy based on your own objectives and business risks.</span></span>

## <a name="cloud-governance-team"></a><span data-ttu-id="08d4c-109">云治理团队</span><span class="sxs-lookup"><span data-stu-id="08d4c-109">Cloud Governance team</span></span>

<span data-ttu-id="08d4c-110">CIO 最近与 IT 治理团队开了一次会，以了解 PII 和任务关键型策略的历史记录，并评审更改这些策略的影响。</span><span class="sxs-lookup"><span data-stu-id="08d4c-110">The CIO recently held a meeting with the IT Governance team to understand the history of the PII and mission-critical policies and review the impact of changing those policies.</span></span> <span data-ttu-id="08d4c-111">她还讨论了面向 IT 和公司的云的整体潜力。</span><span class="sxs-lookup"><span data-stu-id="08d4c-111">She also discussed the overall potential of the cloud for IT and the company.</span></span>

<span data-ttu-id="08d4c-112">会后，IT 治理团队的两名成员请求获得权限，以展开云规划的研究和支持工作。</span><span class="sxs-lookup"><span data-stu-id="08d4c-112">After the meeting, two members of the IT Governance team requested permission to research and support the cloud planning efforts.</span></span> <span data-ttu-id="08d4c-113">认识到治理的必要性和限制影子 IT 的机会，IT 治理总监表示支持这一想法。</span><span class="sxs-lookup"><span data-stu-id="08d4c-113">Recognizing the need for governance and an opportunity to limit shadow IT, the Director of IT Governance supported this idea.</span></span> <span data-ttu-id="08d4c-114">云治理团队就这样诞生了。</span><span class="sxs-lookup"><span data-stu-id="08d4c-114">With that, the Cloud Governance team was born.</span></span> <span data-ttu-id="08d4c-115">在接下来的几个月里，他们将从治理的角度继承清理在云探索过程中犯下的诸多错误。</span><span class="sxs-lookup"><span data-stu-id="08d4c-115">Over the next several months, they will inherit the cleanup of many mistakes made during exploration in the cloud from a governance perspective.</span></span> <span data-ttu-id="08d4c-116">这将为他们赢得云管理员的称谓。</span><span class="sxs-lookup"><span data-stu-id="08d4c-116">This will earn them the moniker of Cloud Custodians.</span></span> <span data-ttu-id="08d4c-117">在以后的演进中，该过程将显示他们的角色是如何随时间变化的。</span><span class="sxs-lookup"><span data-stu-id="08d4c-117">In later evolutions, this journey will show how their roles change over time.</span></span>

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a><span data-ttu-id="08d4c-118">容忍度指标</span><span class="sxs-lookup"><span data-stu-id="08d4c-118">Tolerance indicators</span></span>

<span data-ttu-id="08d4c-119">当前风险容忍度较高，而投入云治理的意愿很低。</span><span class="sxs-lookup"><span data-stu-id="08d4c-119">The current risk tolerance is high and the appetite for investing in cloud governance is low.</span></span> <span data-ttu-id="08d4c-120">因此，容忍度指标可作为早期预警系统，以促使投入时间和精力。</span><span class="sxs-lookup"><span data-stu-id="08d4c-120">As such, the tolerance indicators act as an early warning system to trigger the investment of time and energy.</span></span> <span data-ttu-id="08d4c-121">如果观察到以下指标，明智的做法是改进治理策略。</span><span class="sxs-lookup"><span data-stu-id="08d4c-121">If the following indicators are observed, it would be wise to evolve the governance strategy.</span></span>

- <span data-ttu-id="08d4c-122">成本管理：部署到云上的资产规模超过 1,000 项，或者每月开销超过 10,000 美元。</span><span class="sxs-lookup"><span data-stu-id="08d4c-122">Cost Management: Scale of deployment exceeds 1,000 assets to the cloud, or monthly spending exceeds $10,000 USD per month.</span></span>
- <span data-ttu-id="08d4c-123">标识基线：包含具有传统或第三方多重身份验证 (MFA) 需求的应用程序。</span><span class="sxs-lookup"><span data-stu-id="08d4c-123">Identity Baseline: Inclusion of applications with legacy or third-party multifactor authentication (MFA) requirements.</span></span>
- <span data-ttu-id="08d4c-124">安全基线：包含已定义的云采用计划中的受保护数据。</span><span class="sxs-lookup"><span data-stu-id="08d4c-124">Security Baseline: Inclusion of protected data in defined cloud adoption plans.</span></span>
- <span data-ttu-id="08d4c-125">资源一致性：包含已定义的云采用计划中的任何任务关键型应用程序。</span><span class="sxs-lookup"><span data-stu-id="08d4c-125">Resource Consistency: Inclusion of any mission-critical applications in defined cloud adoption plans.</span></span>

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a><span data-ttu-id="08d4c-126">后续步骤</span><span class="sxs-lookup"><span data-stu-id="08d4c-126">Next steps</span></span>

<span data-ttu-id="08d4c-127">此公司策略使得云治理团队对实现治理 MVP 有所准备，这将是云采用的基础。</span><span class="sxs-lookup"><span data-stu-id="08d4c-127">This corporate policy prepares the Cloud Governance team to implement the governance MVP, which will be the foundation for adoption.</span></span> <span data-ttu-id="08d4c-128">下一步是实现此 MVP。</span><span class="sxs-lookup"><span data-stu-id="08d4c-128">The next step is to implement this MVP.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="08d4c-129">最佳做法介绍</span><span class="sxs-lookup"><span data-stu-id="08d4c-129">Best practice explained</span></span>](./best-practice-explained.md)