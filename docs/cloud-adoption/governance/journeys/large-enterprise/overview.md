---
title: CAF：大型企业治理之旅
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企业治理之旅
author: BrianBlanchard
ms.openlocfilehash: 689b600524c3be6c505ade8c5aaa447d772c6e35
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900472"
---
# <a name="caf-large-enterprise-governance-journey"></a><span data-ttu-id="396d3-103">CAF：大型企业治理之旅</span><span class="sxs-lookup"><span data-stu-id="396d3-103">CAF: Large enterprise governance journey</span></span>

## <a name="best-practice-overview"></a><span data-ttu-id="396d3-104">最佳做法概述</span><span class="sxs-lookup"><span data-stu-id="396d3-104">Best practice overview</span></span>

<span data-ttu-id="396d3-105">此治理过程遵循虚构公司在治理成熟的各个阶段的经验。</span><span class="sxs-lookup"><span data-stu-id="396d3-105">This governance journey follows the experiences of a fictional company through various stages of governance maturity.</span></span> <span data-ttu-id="396d3-106">它基于实际客户过程。</span><span class="sxs-lookup"><span data-stu-id="396d3-106">It is based on real customer journeys.</span></span> <span data-ttu-id="396d3-107">建议的最佳做法基于虚构公司的约束和需求。</span><span class="sxs-lookup"><span data-stu-id="396d3-107">The suggested best practices are based on the constraints and needs of the fictional company.</span></span>

<span data-ttu-id="396d3-108">作为快速起点，本概述根据最佳做法定义了治理的最低可行产品 (MVP)。</span><span class="sxs-lookup"><span data-stu-id="396d3-108">As a quick starting point, this overview defines a minimum viable product (MVP) for governance based on best practices.</span></span> <span data-ttu-id="396d3-109">本概述还提供了一些治理演变的链接。随着新的业务或技术风险的出现，这些演变将添加更多的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="396d3-109">It also provides links to some governance evolutions that add further best practices as new business or technical risks emerge.</span></span>

> [!WARNING]
> <span data-ttu-id="396d3-110">此 MVP 是基于一组假设的基线起始点。</span><span class="sxs-lookup"><span data-stu-id="396d3-110">This MVP is a baseline starting point, based on a set of assumptions.</span></span> <span data-ttu-id="396d3-111">即使是这一组最低限度的最佳做法，也是基于唯一的业务风险和风险承受能力驱动的企业策略。</span><span class="sxs-lookup"><span data-stu-id="396d3-111">Even this minimal set of best practices is based on corporate policies driven by unique business risks and risk tolerances.</span></span> <span data-ttu-id="396d3-112">若要查看这些假设是否适用，请阅读本文后面的[长文](./narrative.md)。</span><span class="sxs-lookup"><span data-stu-id="396d3-112">To see if these assumptions apply to you, read the [longer narrative](./narrative.md) that follows this article.</span></span>

### <a name="governance-best-practice"></a><span data-ttu-id="396d3-113">治理最佳做法</span><span class="sxs-lookup"><span data-stu-id="396d3-113">Governance best practice</span></span>

<span data-ttu-id="396d3-114">此最佳做法可作为组织可用于快速一致地在多个 Azure 订阅中添加治理护栏的基础。</span><span class="sxs-lookup"><span data-stu-id="396d3-114">This best practice serves as a foundation that an organization can use to quickly and consistently add governance guardrails across multiple Azure subscriptions.</span></span>

### <a name="resource-organization"></a><span data-ttu-id="396d3-115">资源组织</span><span class="sxs-lookup"><span data-stu-id="396d3-115">Resource organization</span></span>

<span data-ttu-id="396d3-116">下图显示用于组织资源的治理 MVP 层次结构。</span><span class="sxs-lookup"><span data-stu-id="396d3-116">The following diagram shows the governance MVP hierarchy for organizing resources.</span></span>

![资源组织图](../../../_images/governance/resource-organization.png)

<span data-ttu-id="396d3-118">应将每个应用程序部署在管理组、订阅和资源组层次结构的适当区域中。</span><span class="sxs-lookup"><span data-stu-id="396d3-118">Every application should be deployed in the proper area of the management group, subscription, and resource group hierarchy.</span></span> <span data-ttu-id="396d3-119">在部署规划期间，云治理团队将在层次结构中创建必要的节点，为云采用团队提供支持。</span><span class="sxs-lookup"><span data-stu-id="396d3-119">During deployment planning, the Cloud Governance team will create the necessary nodes in the hierarchy to empower the cloud adoption teams.</span></span>

1. <span data-ttu-id="396d3-120">每个业务单位的管理组的详细层次结构反映了地理位置，然后是环境类型（生产、非生产）。</span><span class="sxs-lookup"><span data-stu-id="396d3-120">A management group for each business unit with a detailed hierarchy that reflects geography then environment type (Production, Non-Production).</span></span>
2. <span data-ttu-id="396d3-121">订阅业务单位、地理位置、环境和“应用程序分类”的每个唯一组合。</span><span class="sxs-lookup"><span data-stu-id="396d3-121">A subscription for each unique combination of business unit, geography, environment, and "Application Categorization."</span></span>
3. <span data-ttu-id="396d3-122">每个应用程序的单独资源组。</span><span class="sxs-lookup"><span data-stu-id="396d3-122">A separate resource group for each application.</span></span>
4. <span data-ttu-id="396d3-123">应在此分组层次结构的每个级别应用一致的命名法。</span><span class="sxs-lookup"><span data-stu-id="396d3-123">Consistent nomenclature should be applied at each level of this grouping hierarchy.</span></span>

![大型企业资源组织图](../../../_images/governance/large-enterprise-resource-organization.png)

<span data-ttu-id="396d3-125">这些模式提供了增长空间，而不会导致不必要的层次结构复杂化。</span><span class="sxs-lookup"><span data-stu-id="396d3-125">These patterns provide room for growth without complicating the hierarchy unnecessarily.</span></span>

[!INCLUDE [governance-of-resources](../../../../../includes/cloud-adoption/governance/governance-of-resources.md)]

## <a name="governance-evolutions"></a><span data-ttu-id="396d3-126">治理演变</span><span class="sxs-lookup"><span data-stu-id="396d3-126">Governance evolutions</span></span>

<span data-ttu-id="396d3-127">部署此 MVP 后，其他的治理层可以快速整合到环境中。</span><span class="sxs-lookup"><span data-stu-id="396d3-127">Once this MVP has been deployed, additional layers of governance can be quickly incorporated into the environment.</span></span> <span data-ttu-id="396d3-128">以下是一些发展 MVP 以满足特定业务需求的方法：</span><span class="sxs-lookup"><span data-stu-id="396d3-128">Here are some ways to evolve the MVP to meet specific business needs:</span></span>

- [<span data-ttu-id="396d3-129">适用于受保护数据的安全基线</span><span class="sxs-lookup"><span data-stu-id="396d3-129">Security Baseline for protected data</span></span>](./security-baseline-evolution.md)
- [<span data-ttu-id="396d3-130">关键任务应用程序的资源配置</span><span class="sxs-lookup"><span data-stu-id="396d3-130">Resource configurations for mission-critical applications</span></span>](./resource-consistency-evolution.md)
- [<span data-ttu-id="396d3-131">成本管理控制</span><span class="sxs-lookup"><span data-stu-id="396d3-131">Controls for Cost Management</span></span>](./cost-management-evolution.md)
- [<span data-ttu-id="396d3-132">多云演变控制</span><span class="sxs-lookup"><span data-stu-id="396d3-132">Controls for multi-cloud evolution</span></span>](./multi-cloud-evolution.md)

<!-- markdownlint-disable MD026 -->

## <a name="what-does-this-best-practice-do"></a><span data-ttu-id="396d3-133">此最佳做法有什么作用？</span><span class="sxs-lookup"><span data-stu-id="396d3-133">What does this best practice do?</span></span>

<span data-ttu-id="396d3-134">在 MVP 中，建立了[部署加速](../../deployment-acceleration/overview.md)规则的做法和工具，以快速应用公司策略。</span><span class="sxs-lookup"><span data-stu-id="396d3-134">In the MVP, practices and tools from the [Deployment Acceleration](../../deployment-acceleration/overview.md) discipline are established to quickly apply corporate policy.</span></span> <span data-ttu-id="396d3-135">具体而言，MVP 使用 Azure 蓝图、Azure Policy 和 Azure 管理组来应用一些基本的公司策略（例如，此虚构公司的说明中定义的策略）。</span><span class="sxs-lookup"><span data-stu-id="396d3-135">In particular, the MVP uses Azure Blueprints, Azure Policy, and Azure management groups to apply a few basic corporate policies, as defined in the narrative for this fictional company.</span></span> <span data-ttu-id="396d3-136">使用 Azure 资源管理器模板和 Azure 策略应用这些公司策略，为标识和安全性建立非常小的基线。</span><span class="sxs-lookup"><span data-stu-id="396d3-136">Those corporate policies are applied using Azure Resource Manager templates and Azure policies to establish a very small baseline for identity and security.</span></span>

![增量治理 MVP 的示例](../../../_images/governance/governance-mvp.png)

## <a name="evolving-the-best-practice"></a><span data-ttu-id="396d3-138">不断演变的最佳做法</span><span class="sxs-lookup"><span data-stu-id="396d3-138">Evolving the best practice</span></span>

<span data-ttu-id="396d3-139">随着时间的推移，这种治理 MVP 将用于改进治理做法。</span><span class="sxs-lookup"><span data-stu-id="396d3-139">Over time, this governance MVP will be used to evolve the governance practices.</span></span> <span data-ttu-id="396d3-140">随着采用的发展，业务风险也会增加。</span><span class="sxs-lookup"><span data-stu-id="396d3-140">As adoption advances, business risk grows.</span></span> <span data-ttu-id="396d3-141">CAF 治理模型中的各种规则将不断改进，以缓解这些风险。</span><span class="sxs-lookup"><span data-stu-id="396d3-141">Various disciplines within the CAF governance model will evolve to mitigate those risks.</span></span> <span data-ttu-id="396d3-142">本系列的后续文章讨论了影响虚构公司的公司策略的演变。</span><span class="sxs-lookup"><span data-stu-id="396d3-142">Later articles in this series discuss the evolution of corporate policy affecting the fictional company.</span></span> <span data-ttu-id="396d3-143">这些演变的发生跨三项规则：</span><span class="sxs-lookup"><span data-stu-id="396d3-143">These evolutions happen across three disciplines:</span></span>

- <span data-ttu-id="396d3-144">标识基线，作为迁移依赖项在叙述中的演变</span><span class="sxs-lookup"><span data-stu-id="396d3-144">Identity Baseline, as migration dependencies evolve in the narrative</span></span>
- <span data-ttu-id="396d3-145">成本管理，作为采用规模。</span><span class="sxs-lookup"><span data-stu-id="396d3-145">Cost Management, as adoption scales.</span></span>
- <span data-ttu-id="396d3-146">安全基线，作为受保护的数据部署。</span><span class="sxs-lookup"><span data-stu-id="396d3-146">Security Baseline, as protected data is deployed.</span></span>
- <span data-ttu-id="396d3-147">资源一致性，作为 IT 操作开始支持任务关键型工作负荷。</span><span class="sxs-lookup"><span data-stu-id="396d3-147">Resource Consistency, as IT Operations begins supporting mission-critical workloads.</span></span>

![增量治理 MVP 的示例](../../../_images/governance/governance-evolution-large.png)

## <a name="next-steps"></a><span data-ttu-id="396d3-149">后续步骤</span><span class="sxs-lookup"><span data-stu-id="396d3-149">Next steps</span></span>

<span data-ttu-id="396d3-150">现在你熟悉了治理 MVP 并了解了要遵循的治理演变，请阅读其他上下文的支持说明。</span><span class="sxs-lookup"><span data-stu-id="396d3-150">Now that you’re familiar with the governance MVP and have an idea of the governance evolutions to follow, read the supporting narrative for additional context.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="396d3-151">阅读支持说明</span><span class="sxs-lookup"><span data-stu-id="396d3-151">Read the supporting narrative</span></span>](./narrative.md)
