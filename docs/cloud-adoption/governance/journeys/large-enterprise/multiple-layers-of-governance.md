---
title: CAF：大型企业 – 监管在大型企业中的多个层
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企业 – 监管在大型企业中的多个层
author: BrianBlanchard
ms.openlocfilehash: 1a90f8007077df0ecefa8ec5d8c0dd6bfca9ccc7
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641206"
---
# <a name="multiple-layers-of-governance-in-large-enterprises"></a><span data-ttu-id="d2d88-103">大型企业的多层治理</span><span class="sxs-lookup"><span data-stu-id="d2d88-103">Multiple layers of governance in large enterprises</span></span>

<span data-ttu-id="d2d88-104">当大型企业需要进行多层治理时，必须在治理 MVP 和以后的治理演变中考虑到更高级别的复杂性。</span><span class="sxs-lookup"><span data-stu-id="d2d88-104">When large enterprises require multiple layers of governance, there are greater levels of complexity that must be factored into the governance MVP and later governance evolutions.</span></span>

<span data-ttu-id="d2d88-105">此类复杂性的几个常见示例包括：</span><span class="sxs-lookup"><span data-stu-id="d2d88-105">A few common examples of such complexities include:</span></span>

- <span data-ttu-id="d2d88-106">分布式治理功能。</span><span class="sxs-lookup"><span data-stu-id="d2d88-106">Distributed governance functions.</span></span>
- <span data-ttu-id="d2d88-107">企业 IT 支持业务部门 IT 组织。</span><span class="sxs-lookup"><span data-stu-id="d2d88-107">Corporate IT supporting Business unit IT organizations.</span></span>
- <span data-ttu-id="d2d88-108">企业 IT 支持按地理位置分布的 IT 组织。</span><span class="sxs-lookup"><span data-stu-id="d2d88-108">Corporate IT supporting geographically distributed IT organizations.</span></span>

<span data-ttu-id="d2d88-109">本文探讨了应对此类复杂性的一些方法。</span><span class="sxs-lookup"><span data-stu-id="d2d88-109">This article explores some ways to navigate this type of complexity.</span></span>

## <a name="large-enterprise-governance-is-a-team-sport"></a><span data-ttu-id="d2d88-110">大型企业治理是一项团队运动</span><span class="sxs-lookup"><span data-stu-id="d2d88-110">Large enterprise governance is a team sport</span></span>

<span data-ttu-id="d2d88-111">大型成熟企业通常拥有专门的团队和员工，专注于整个治理过程中所涉及的规则。</span><span class="sxs-lookup"><span data-stu-id="d2d88-111">Large established enterprises often have teams or employees who focus on the disciplines mentioned throughout this journey.</span></span> <span data-ttu-id="d2d88-112">此过程演示了使治理成为一项团队运动的方法。</span><span class="sxs-lookup"><span data-stu-id="d2d88-112">This journey demonstrates one approach to making governance a team sport.</span></span>

<span data-ttu-id="d2d88-113">在许多大型企业，云监管五个领域可能会阻塞程序的采用。</span><span class="sxs-lookup"><span data-stu-id="d2d88-113">In many large enterprises, the Five Disciplines of Cloud Governance can be blockers to adoption.</span></span> <span data-ttu-id="d2d88-114">在整个企业内开发标识、安全性、操作、部署和配置方面的云专业技能需要时间。</span><span class="sxs-lookup"><span data-stu-id="d2d88-114">Developing cloud expertise in identity, security, operations, deployments, and configuration across an enterprise takes time.</span></span> <span data-ttu-id="d2d88-115">全面实现 IT 治理策略和 IT 安全可能会使创新速度减缓数月甚至数年。</span><span class="sxs-lookup"><span data-stu-id="d2d88-115">Holistically implementing IT governance policy and IT security can slow innovation by months or even years.</span></span> <span data-ttu-id="d2d88-116">平衡业务创新需求和保护现有资源的治理需求是很微妙的。</span><span class="sxs-lookup"><span data-stu-id="d2d88-116">Balancing the business need to innovate and the governance need to protect existing resources is delicate.</span></span>

<span data-ttu-id="d2d88-117">云的固有功能可以消除创新障碍，但也会增加风险。</span><span class="sxs-lookup"><span data-stu-id="d2d88-117">The inherent capabilities of the cloud can remove blockers to innovation but increase risks.</span></span> <span data-ttu-id="d2d88-118">在此治理过程中，我们展示了示例公司如何建立防范措施来降低风险。</span><span class="sxs-lookup"><span data-stu-id="d2d88-118">In this governance journey, we showed how the example company created guardrails to mitigate the risk.</span></span> <span data-ttu-id="d2d88-119">在其他团队生成必要的云成熟度时，云治理团队采用基于风险的方法来治理可部署的内容，而不是处理保护环境所需的每项规则。</span><span class="sxs-lookup"><span data-stu-id="d2d88-119">Rather than tackling each of the disciplines required to protect the environment, the Cloud Governance team leads a risk-based approach to govern what could be deployed, while the other teams build the necessary cloud maturities.</span></span> <span data-ttu-id="d2d88-120">最重要的是，当每个团队达到云成熟度时，治理团队将整体应用其解决方案。</span><span class="sxs-lookup"><span data-stu-id="d2d88-120">Most importantly, as each team reaches cloud maturity, governance applies their solutions holistically.</span></span> <span data-ttu-id="d2d88-121">随着每个团队逐渐成熟并添加到整个解决方案，云治理团队可以打开阶段关卡，以便大力推进更多的创新和采用。</span><span class="sxs-lookup"><span data-stu-id="d2d88-121">As each team matures and adds to the overall solution, the Cloud Governance team can open stage gates, allowing additional innovation and adoption to thrive.</span></span>

<span data-ttu-id="d2d88-122">该模型演示了云治理团队和现有企业团队（安全、IT 治理、网络、标识和其他）之间合作关系的发展。</span><span class="sxs-lookup"><span data-stu-id="d2d88-122">This model illustrates the growth of a partnership between the Cloud Governance team and existing enterprise teams (Security, IT Governance, Networking, Identity, and others).</span></span> <span data-ttu-id="d2d88-123">这一过程从治理 MVP 开始，并通过治理演变发展到整体最终状态。</span><span class="sxs-lookup"><span data-stu-id="d2d88-123">The journey starts with the governance MVP and grows to a holistic end state through governance evolutions.</span></span>

## <a name="requirements-to-supporting-such-a-team-sport"></a><span data-ttu-id="d2d88-124">支持此类团队运动的要求</span><span class="sxs-lookup"><span data-stu-id="d2d88-124">Requirements to supporting such a team sport</span></span>

<span data-ttu-id="d2d88-125">多层治理模型的第一个要求是了解治理层次结构。</span><span class="sxs-lookup"><span data-stu-id="d2d88-125">The first requirement of a multi-layer governance model is to understand of the governance hierarchy.</span></span> <span data-ttu-id="d2d88-126">回答以下问题将帮助您了解的常规管理层次结构：</span><span class="sxs-lookup"><span data-stu-id="d2d88-126">Answering the following questions will help you to understand the general governance hierarchy:</span></span>

- <span data-ttu-id="d2d88-127">如何跨业务部门分配云核算（云服务计费）？</span><span class="sxs-lookup"><span data-stu-id="d2d88-127">How is cloud accounting (billing for cloud services) allocated across business units?</span></span>
- <span data-ttu-id="d2d88-128">如何跨企业 IT 和每个业务部门分配治理职责？</span><span class="sxs-lookup"><span data-stu-id="d2d88-128">How are governance responsibilities allocated across corporate IT and each business unit?</span></span>
- <span data-ttu-id="d2d88-129">每个 IT 部门都管理哪些类型的环境？</span><span class="sxs-lookup"><span data-stu-id="d2d88-129">What types of environments do each of those units of IT manage?</span></span>

## <a name="central-governance-of-a-distributed-governance-hierarchy"></a><span data-ttu-id="d2d88-130">分布式治理层次结构的中心治理</span><span class="sxs-lookup"><span data-stu-id="d2d88-130">Central governance of a distributed governance hierarchy</span></span>

<span data-ttu-id="d2d88-131">借助管理组等工具，企业 IT 可以创建与治理层次结构匹配的层次结构。</span><span class="sxs-lookup"><span data-stu-id="d2d88-131">Tools like management groups allow corporate IT to create a hierarchy structure that matches the governance hierarchy.</span></span> <span data-ttu-id="d2d88-132">诸如 Azure 蓝图这样的工具可以将资产应用到该层次结构的不同层。</span><span class="sxs-lookup"><span data-stu-id="d2d88-132">Tools like Azure Blueprints can apply assets to different layers of that hierarchy.</span></span> <span data-ttu-id="d2d88-133">可以对 Azure 蓝图进行版本控制，将各种版本应用于管理组、订阅或资源组。</span><span class="sxs-lookup"><span data-stu-id="d2d88-133">Azure Blueprints can be versioned and various versions can be applied to management groups, subscriptions, or resource groups.</span></span> <span data-ttu-id="d2d88-134">其中的每个概念将在治理 MVP 中做出详细描述。</span><span class="sxs-lookup"><span data-stu-id="d2d88-134">Each of these concepts is described in more detail in the governance MVP.</span></span>

<span data-ttu-id="d2d88-135">其中每个工具的重要方面是能够对一个层次结构应用多个蓝图。</span><span class="sxs-lookup"><span data-stu-id="d2d88-135">The important aspect of each of these tools is the ability to apply multiple blueprints to a hierarchy.</span></span> <span data-ttu-id="d2d88-136">因此，治理可以是一个分层过程。</span><span class="sxs-lookup"><span data-stu-id="d2d88-136">This allows governance to be a layered process.</span></span> <span data-ttu-id="d2d88-137">下面是此分层治理应用程序的一个示例：</span><span class="sxs-lookup"><span data-stu-id="d2d88-137">The following is one example of this hierarchical application of governance:</span></span>

- <span data-ttu-id="d2d88-138">企业 IT：企业 IT 创建一组适用于所有云采用的标准和策略。</span><span class="sxs-lookup"><span data-stu-id="d2d88-138">Corporate IT: Corporate IT creates a set of standards and policies that apply to all cloud adoption.</span></span> <span data-ttu-id="d2d88-139">这是在“基线”蓝图中具体实现的。</span><span class="sxs-lookup"><span data-stu-id="d2d88-139">This is materialized in a "Baseline" blueprint.</span></span> <span data-ttu-id="d2d88-140">然后，企业 IT 拥有管理组层次结构，确保基线的一个版本应用于层次结构中的所有订阅。</span><span class="sxs-lookup"><span data-stu-id="d2d88-140">Corporate IT then owns the management group hierarchy, ensuring that a version of the baseline is applied to all subscriptions in the hierarchy.</span></span>
- <span data-ttu-id="d2d88-141">地区或业务部门 IT：各种 IT 团队可以通过创建自己的蓝图来应用额外的治理层。</span><span class="sxs-lookup"><span data-stu-id="d2d88-141">Regional or Business Unit IT: Various IT teams can apply an additional layer of governance by creating their own blueprint.</span></span> <span data-ttu-id="d2d88-142">这些蓝图将创建附加策略和标准。</span><span class="sxs-lookup"><span data-stu-id="d2d88-142">Those blueprints would create additive policies and standards.</span></span> <span data-ttu-id="d2d88-143">开发完成后，企业 IT 就可以将这些蓝图应用到管理组层次结构中的适用节点。</span><span class="sxs-lookup"><span data-stu-id="d2d88-143">Once developed, Corporate IT could apply those blueprints to the applicable nodes within the management group hierarchy.</span></span>
- <span data-ttu-id="d2d88-144">云采用团队：关于应用程序或工作负载的详细决策和实现可以由云采用团队在治理要求的上下文中完成。</span><span class="sxs-lookup"><span data-stu-id="d2d88-144">Cloud adoption teams: Detailed decisions and implementation about applications or workloads can be made by the cloud adoption teams, within the context of governance requirements.</span></span> <span data-ttu-id="d2d88-145">有时团队还可以请求额外的 Azure 资源一致性模板来加速采用进程。</span><span class="sxs-lookup"><span data-stu-id="d2d88-145">At times the team can also request additional Azure Resource Consistency templates to accelerate adoption efforts.</span></span>

<span data-ttu-id="d2d88-146">关于每个级别的治理实现的详细信息需要每个团队之间的协调。</span><span class="sxs-lookup"><span data-stu-id="d2d88-146">The details regarding governance implementation at each level will require coordination between each team.</span></span> <span data-ttu-id="d2d88-147">在此过程中概述的治理 MVP 和治理演变可以帮助调整这一协调。</span><span class="sxs-lookup"><span data-stu-id="d2d88-147">The governance MVP and governance evolutions outlined in this journey can aid in aligning that coordination.</span></span>
