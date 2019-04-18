---
title: CAF：小型到中型企业的初始公司策略背后的管理策略
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 小型到中型企业的初始公司策略背后的管理策略
author: BrianBlanchard
ms.openlocfilehash: 2133145c9933ad450ea3cc55ecd68b8a667df783
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640016"
---
# <a name="small-to-medium-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a><span data-ttu-id="079b6-103">中小型企业：治理策略背后的初始公司策略</span><span class="sxs-lookup"><span data-stu-id="079b6-103">Small-to-medium enterprise: Initial corporate policy behind the governance strategy</span></span>

<span data-ttu-id="079b6-104">下面的公司策略定义了初始治理位置，即此过程的起点。</span><span class="sxs-lookup"><span data-stu-id="079b6-104">The following corporate policy defines an initial governance position, which is the starting point for this journey.</span></span> <span data-ttu-id="079b6-105">本文定义了早期风险、初始策略声明和强制执行策略声明的早期流程。</span><span class="sxs-lookup"><span data-stu-id="079b6-105">This article defines early-stage risks, initial policy statements, and early processes to enforce policy statements.</span></span>

> [!NOTE]
><span data-ttu-id="079b6-106">公司策略并非技术文档，但它可推动许多技术决策。</span><span class="sxs-lookup"><span data-stu-id="079b6-106">The corporate policy is not a technical document, but it drives many technical decisions.</span></span> <span data-ttu-id="079b6-107">[概述](./overview.md)中所描述的治理 MVP 最终源自此策略。</span><span class="sxs-lookup"><span data-stu-id="079b6-107">The governance MVP described in the [overview](./overview.md) ultimately derives from this policy.</span></span> <span data-ttu-id="079b6-108">组织应基于自己的目标和业务风险制定公司策略，然后实现治理 MVP。</span><span class="sxs-lookup"><span data-stu-id="079b6-108">Before implementing a governance MVP, your organization should develop a corporate policy based on your own objectives and business risks.</span></span>

## <a name="cloud-governance-team"></a><span data-ttu-id="079b6-109">云治理团队</span><span class="sxs-lookup"><span data-stu-id="079b6-109">Cloud Governance team</span></span>

<span data-ttu-id="079b6-110">在此叙述性内容中，云治理团队由两个已意识到治理需求的系统管理员组成。</span><span class="sxs-lookup"><span data-stu-id="079b6-110">In this narrative, the Cloud Governance team is comprised of two systems administrators who have recognized the need for governance.</span></span> <span data-ttu-id="079b6-111">在接下来的几个月里，他们将接手整顿公司云业务治理的工作，从而获得云管理者头衔。</span><span class="sxs-lookup"><span data-stu-id="079b6-111">Over the next several months, they will inherit the job of cleaning up the governance of the company’s cloud presence, earning them the title of Cloud Custodians.</span></span> <span data-ttu-id="079b6-112">在随后的演变中，可能会更改此头衔。</span><span class="sxs-lookup"><span data-stu-id="079b6-112">In subsequent evolutions, this title will likely change.</span></span>

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a><span data-ttu-id="079b6-113">容忍度指标</span><span class="sxs-lookup"><span data-stu-id="079b6-113">Tolerance indicators</span></span>

<span data-ttu-id="079b6-114">当前对风险的容忍度较高，而投入云治理的意愿很低。</span><span class="sxs-lookup"><span data-stu-id="079b6-114">The current tolerance for risk is high and the appetite for investing in cloud governance is low.</span></span> <span data-ttu-id="079b6-115">因此，容忍度指标可作为早期预警系统，以促使投入时间和精力。</span><span class="sxs-lookup"><span data-stu-id="079b6-115">As such, the tolerance indicators act as an early warning system to trigger more investment of time and energy.</span></span> <span data-ttu-id="079b6-116">如果观察到以下指标，则应改进治理策略。</span><span class="sxs-lookup"><span data-stu-id="079b6-116">If and when the following indicators are observed, you should evolve the governance strategy.</span></span>

- <span data-ttu-id="079b6-117">成本管理：部署到云上的资产规模超过 100 项，或者每月开销超过 1000 美元。</span><span class="sxs-lookup"><span data-stu-id="079b6-117">Cost Management: The scale of deployment exceeds 100 assets to the cloud, or monthly spending exceeds $1,000 USD per month.</span></span>
- <span data-ttu-id="079b6-118">安全基线：包含已定义的云采用计划中的受保护数据。</span><span class="sxs-lookup"><span data-stu-id="079b6-118">Security Baseline: Inclusion of protected data in defined cloud adoption plans.</span></span>
- <span data-ttu-id="079b6-119">资源一致性：包含已定义的云采用计划中的任何任务关键型应用程序。</span><span class="sxs-lookup"><span data-stu-id="079b6-119">Resource Consistency: Inclusion of any mission-critical applications in defined cloud adoption plans.</span></span>

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a><span data-ttu-id="079b6-120">后续步骤</span><span class="sxs-lookup"><span data-stu-id="079b6-120">Next steps</span></span>

<span data-ttu-id="079b6-121">此公司策略使得云治理团队对实现治理 MVP 有所准备，该策略将作为采用的基础。</span><span class="sxs-lookup"><span data-stu-id="079b6-121">This corporate policy prepares the Cloud Governance team to implement the governance MVP, which will be the foundation for adoption.</span></span> <span data-ttu-id="079b6-122">下一步是实现此 MVP。</span><span class="sxs-lookup"><span data-stu-id="079b6-122">The next step is to implement this MVP.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="079b6-123">最佳做法介绍</span><span class="sxs-lookup"><span data-stu-id="079b6-123">Best practice explained</span></span>](./best-practice-explained.md)