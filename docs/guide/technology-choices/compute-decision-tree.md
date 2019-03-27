---
title: Azure 计算服务的决策树
titleSuffix: Azure Application Architecture Guide
description: 用于选择计算服务的流程图。
author: MikeWasson
ms.date: 11/03/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: e3df1cbdd049e8c40597a85eca29899d8d0d1bc3
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245588"
---
# <a name="decision-tree-for-azure-compute-services"></a><span data-ttu-id="f913e-103">Azure 计算服务的决策树</span><span class="sxs-lookup"><span data-stu-id="f913e-103">Decision tree for Azure compute services</span></span>

<span data-ttu-id="f913e-104">Azure 提供多种方式来托管应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="f913e-104">Azure offers a number of ways to host your application code.</span></span> <span data-ttu-id="f913e-105">术语“计算”指的是计算资源（应用程序在这些资源上运行）的承载模型。</span><span class="sxs-lookup"><span data-stu-id="f913e-105">The term *compute* refers to the hosting model for the computing resources that your application runs on.</span></span> <span data-ttu-id="f913e-106">以下流程图将会帮助你选择应用程序的计算服务。</span><span class="sxs-lookup"><span data-stu-id="f913e-106">The following flowchart will help you to choose a compute service for your application.</span></span> <span data-ttu-id="f913e-107">该流程图将引导你创建一组关键决策条件用于访问建议。</span><span class="sxs-lookup"><span data-stu-id="f913e-107">The flowchart guides you through a set of key decision criteria to reach a recommendation.</span></span>

<span data-ttu-id="f913e-108">**请将此流程图视为起点。**</span><span class="sxs-lookup"><span data-stu-id="f913e-108">**Treat this flowchart as a starting point.**</span></span> <span data-ttu-id="f913e-109">每个应用程序有独特的要求，因此请将该建议作为起点。</span><span class="sxs-lookup"><span data-stu-id="f913e-109">Every application has unique requirements, so use the recommendation as a starting point.</span></span> <span data-ttu-id="f913e-110">然后执行更详细的评估，例如查看：</span><span class="sxs-lookup"><span data-stu-id="f913e-110">Then perform a more detailed evaluation, looking at aspects such as:</span></span>

- <span data-ttu-id="f913e-111">特征集</span><span class="sxs-lookup"><span data-stu-id="f913e-111">Feature set</span></span>
- [<span data-ttu-id="f913e-112">服务限制</span><span class="sxs-lookup"><span data-stu-id="f913e-112">Service limits</span></span>](/azure/azure-subscription-service-limits)
- [<span data-ttu-id="f913e-113">成本</span><span class="sxs-lookup"><span data-stu-id="f913e-113">Cost</span></span>](https://azure.microsoft.com/pricing/)
- [<span data-ttu-id="f913e-114">SLA</span><span class="sxs-lookup"><span data-stu-id="f913e-114">SLA</span></span>](https://azure.microsoft.com/support/legal/sla/)
- [<span data-ttu-id="f913e-115">区域可用性</span><span class="sxs-lookup"><span data-stu-id="f913e-115">Regional availability</span></span>](https://azure.microsoft.com/global-infrastructure/services/)
- <span data-ttu-id="f913e-116">开发人员生态系统和团队技能</span><span class="sxs-lookup"><span data-stu-id="f913e-116">Developer ecosystem and team skills</span></span>
- [<span data-ttu-id="f913e-117">计算比较表</span><span class="sxs-lookup"><span data-stu-id="f913e-117">Compute comparison tables</span></span>](./compute-comparison.md)

<span data-ttu-id="f913e-118">如果应用程序包括多个工作负荷，请单独评估每个工作负荷。</span><span class="sxs-lookup"><span data-stu-id="f913e-118">If your application consists of multiple workloads, evaluate each workload separately.</span></span> <span data-ttu-id="f913e-119">完整的解决方案可能会合并两个或更多个计算服务。</span><span class="sxs-lookup"><span data-stu-id="f913e-119">A complete solution may incorporate two or more compute services.</span></span>

<span data-ttu-id="f913e-120">若要详细了解用于在 Azure 中托管容器的选项，请参阅 [Azure 容器](https://azure.microsoft.com/overview/containers/)。</span><span class="sxs-lookup"><span data-stu-id="f913e-120">For more information about your options for hosting containers in Azure, see [Azure Containers](https://azure.microsoft.com/overview/containers/).</span></span>

## <a name="flowchart"></a><span data-ttu-id="f913e-121">流程图</span><span class="sxs-lookup"><span data-stu-id="f913e-121">Flowchart</span></span>

![Azure 计算服务的决策树](../images/compute-decision-tree.svg)

## <a name="definitions"></a><span data-ttu-id="f913e-123">定义</span><span class="sxs-lookup"><span data-stu-id="f913e-123">Definitions</span></span>

- <span data-ttu-id="f913e-124">“直接迁移”是一种将工作负荷迁移到云的策略，不重新设计应用程序，也不进行代码更改。</span><span class="sxs-lookup"><span data-stu-id="f913e-124">**Lift and shift** is a strategy for migrating a workload to the cloud without redesigning the application or making code changes.</span></span> <span data-ttu-id="f913e-125">也称“重新托管”。</span><span class="sxs-lookup"><span data-stu-id="f913e-125">Also called *rehosting*.</span></span> <span data-ttu-id="f913e-126">有关详细信息，请参阅 [Azure 迁移中心](https://azure.microsoft.com/migration/)。</span><span class="sxs-lookup"><span data-stu-id="f913e-126">For more information, see [Azure migration center](https://azure.microsoft.com/migration/).</span></span>

- <span data-ttu-id="f913e-127">“云优化”是一种云迁移策略，需重构应用程序，以便充分利用云原生特性和功能。</span><span class="sxs-lookup"><span data-stu-id="f913e-127">**Cloud optimized** is a strategy for migrating to the cloud by refactoring an application to take advantage of cloud-native features and capabilities.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f913e-128">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f913e-128">Next steps</span></span>

<span data-ttu-id="f913e-129">若要考虑其他条件，请参阅[选择 Azure 计算服务的条件](./compute-comparison.md)。</span><span class="sxs-lookup"><span data-stu-id="f913e-129">For additional criteria to consider, see [Criteria for choosing an Azure compute service](./compute-comparison.md).</span></span>
