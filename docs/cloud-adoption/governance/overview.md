---
title: CAF：Azure Microsoft 自助餐厅中的监管
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Azure Microsoft 自助餐厅中的监管
author: BrianBlanchard
ms.openlocfilehash: ce407de0daa590e767382346692c80e0a113bb3c
ms.sourcegitcommit: 0a8a60d782facc294f7f78ec0e9033e3ee16bf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068831"
---
# <a name="governance-in-the-microsoft-caf-for-azure"></a><span data-ttu-id="4d99b-103">Azure Microsoft 自助餐厅中的监管</span><span class="sxs-lookup"><span data-stu-id="4d99b-103">Governance in the Microsoft CAF for Azure</span></span>

<span data-ttu-id="4d99b-104">在支持业务的技术方面，云开创了全新的模式。</span><span class="sxs-lookup"><span data-stu-id="4d99b-104">The cloud creates new paradigms regarding the technologies that support the business.</span></span> <span data-ttu-id="4d99b-105">这些新模式也会导致这些技术在采用、管理和治理方式上发生变化。</span><span class="sxs-lookup"><span data-stu-id="4d99b-105">These new paradigms also cause shifts in how those technologies are adopted, managed, and governed.</span></span> <span data-ttu-id="4d99b-106">当整个数据中心可以通过一个无人值守进程执行的一行代码来销毁和重新创建时，我们必须反思传统方法。</span><span class="sxs-lookup"><span data-stu-id="4d99b-106">When entire datacenters can be destroyed and recreated with one line of code executed from an unattended process, we have to rethink traditional approaches.</span></span> <span data-ttu-id="4d99b-107">在治理方面也是如此。</span><span class="sxs-lookup"><span data-stu-id="4d99b-107">This is equally true when it comes to governance.</span></span>

<span data-ttu-id="4d99b-108">对于具有治理本地 IT 环境现有策略的组织，云治理应补充这些策略。</span><span class="sxs-lookup"><span data-stu-id="4d99b-108">For organizations with existing policies governing on-premises IT environments, cloud governance should complement those policies.</span></span> <span data-ttu-id="4d99b-109">但是，本地和云之间的企业策略整合水平将随云治理成熟度和云中的数字资产而发生变化。</span><span class="sxs-lookup"><span data-stu-id="4d99b-109">However, the level of corporate policy integration between on-premises and the cloud will vary depending on cloud governance maturity and digital estate in the cloud.</span></span> <span data-ttu-id="4d99b-110">随着云资产的逐渐发展，云治理流程和策略也将随之发展。</span><span class="sxs-lookup"><span data-stu-id="4d99b-110">As the cloud estate evolves over time, so will cloud governance processes and policies.</span></span>

<span data-ttu-id="4d99b-111">本节中的 CAF 指导用于两个目的：</span><span class="sxs-lookup"><span data-stu-id="4d99b-111">The guidance in this section of the CAF is designed for two purposes:</span></span>

* <span data-ttu-id="4d99b-112">提供可操作的客户过程，反映客户经常遇到的常见体验。</span><span class="sxs-lookup"><span data-stu-id="4d99b-112">Provide actionable customer journeys that represent common experiences that customers often encounter.</span></span> <span data-ttu-id="4d99b-113">其中每一种都包含业务风险、企业风险缓解策略，以及实现技术解决方案的设计指南。</span><span class="sxs-lookup"><span data-stu-id="4d99b-113">Each of these encapsulate business risks, corporate policies for risk mitigation, and design guidance to implement technical solutions.</span></span> <span data-ttu-id="4d99b-114">设计指南必须特定于 Azure。</span><span class="sxs-lookup"><span data-stu-id="4d99b-114">By necessity, the design guidance is specific to Azure.</span></span> <span data-ttu-id="4d99b-115">这些过程中的所有其他指南可以作为云不可知或多云方法的一部分应用。</span><span class="sxs-lookup"><span data-stu-id="4d99b-115">All other guidance in these journeys could be applied as part of a cloud-agnostic or multi-cloud approach.</span></span>
* <span data-ttu-id="4d99b-116">通过有关企业策略、流程和工具开发的详细指南，帮助读者创建可满足各种业务需求的个性化治理解决方案，包括多个公共云的治理。</span><span class="sxs-lookup"><span data-stu-id="4d99b-116">Help readers create personalized governance solutions that can meet a variety of business needs, including the governance of multiple public clouds, through detailed guidance on the development of corporate policies, processes, and tooling.</span></span>

<span data-ttu-id="4d99b-117">此内容适用于云治理团队。</span><span class="sxs-lookup"><span data-stu-id="4d99b-117">This content is intended for the Cloud Governance team.</span></span> <span data-ttu-id="4d99b-118">同时也适用于需要在云治理方面建立坚实基础的云架构师。</span><span class="sxs-lookup"><span data-stu-id="4d99b-118">It is also relevant to cloud architects that need to develop a strong foundation in cloud governance.</span></span>

## <a name="audience"></a><span data-ttu-id="4d99b-119">目标受众</span><span class="sxs-lookup"><span data-stu-id="4d99b-119">Audience</span></span>

<span data-ttu-id="4d99b-120">CAF 中的内容会影响业务、技术和企业文化。</span><span class="sxs-lookup"><span data-stu-id="4d99b-120">The content in the CAF affects the business, technology, and culture of enterprises.</span></span> <span data-ttu-id="4d99b-121">CAF 的这一部分主要面向 IT 安全、IT 治理、财务、业务线主管、网络、标识和云采用团队进行互动。</span><span class="sxs-lookup"><span data-stu-id="4d99b-121">This section of the CAF will interact heavily with IT Security, IT Governance, Finance, line-of-business leaders, Networking, Identity, and cloud adoption teams.</span></span> <span data-ttu-id="4d99b-122">这些角色存在各种共同依赖关系，需要云架构师借助本指南采取便利的方法。</span><span class="sxs-lookup"><span data-stu-id="4d99b-122">There are various co-dependencies on these personas which will require a facilitative approach by the Cloud Architects using this guidance.</span></span> <span data-ttu-id="4d99b-123">对这些团队的协助可能是一次性的工作，但在某些情况下，它将导致与这些其他角色的反复互动。</span><span class="sxs-lookup"><span data-stu-id="4d99b-123">Facilitation with these teams may be a one-time effort, but in some cases, it will result in recurring interactions with these other personas.</span></span>

<span data-ttu-id="4d99b-124">云架构师可作为思想领导者和参与者将这些受众组合在一起。</span><span class="sxs-lookup"><span data-stu-id="4d99b-124">The Cloud Architect serves as the thought leader and facilitator to bring these audiences together.</span></span> <span data-ttu-id="4d99b-125">本系列指南的内容旨在帮助云架构师促进与正确的受众进行正确的对话，从而推动必要的决策。</span><span class="sxs-lookup"><span data-stu-id="4d99b-125">The content in this collection of guides is designed to help the Cloud Architect facilitate the right conversation, with the right audience, to drive necessary decisions.</span></span> <span data-ttu-id="4d99b-126">由云推动的业务转型取决于云架构师角色，能够帮助指导整个业务和 IT 的决策。</span><span class="sxs-lookup"><span data-stu-id="4d99b-126">Business transformation that is empowered by the cloud is dependent upon the Cloud Architect role to help guide decisions throughout the business and IT.</span></span>

<span data-ttu-id="4d99b-127">本部分中云架构师的专用性：CAF 的每个部分都代表不同的云架构师的专门角色或变体。</span><span class="sxs-lookup"><span data-stu-id="4d99b-127">**Cloud Architect specialization in this section:** Each section of the CAF represents a different specialization or variant of the Cloud Architect role.</span></span> <span data-ttu-id="4d99b-128">此部分中的自助餐厅专为云架构师热衷于缓解或降低技术的风险。</span><span class="sxs-lookup"><span data-stu-id="4d99b-128">This section of the CAF is designed for cloud architects with a passion for mitigating or reducing technical risks.</span></span> <span data-ttu-id="4d99b-129">许多云服务提供商将这些专家称为“云托管者”，我们更愿意称之为“云监管者”，或者统称为“云治理团队”。</span><span class="sxs-lookup"><span data-stu-id="4d99b-129">Many cloud providers refer to these specialists as cloud custodians, we prefer cloud guardians or, collectively, the Cloud Governance team.</span></span> <span data-ttu-id="4d99b-130">在每个可操作的客户过程中，文章对云治理团队的构成和角色随时间变化的方式均有介绍。</span><span class="sxs-lookup"><span data-stu-id="4d99b-130">In each actionable customer journey, the articles show how the composition and role of the Cloud Governance team may change over time.</span></span>

## <a name="using-this-guide"></a><span data-ttu-id="4d99b-131">使用本指南</span><span class="sxs-lookup"><span data-stu-id="4d99b-131">Using this guide</span></span>

<span data-ttu-id="4d99b-132">对于希望从头至尾遵循本指南的读者，此内容将有助于与云实现并行开发强大的云治理策略。</span><span class="sxs-lookup"><span data-stu-id="4d99b-132">For readers who wish to follow this guide from beginning to end, this content will aid in developing a robust cloud governance strategy in parallel to cloud implementation.</span></span> <span data-ttu-id="4d99b-133">本指南将引导读者了解这种策略的理论和实现。</span><span class="sxs-lookup"><span data-stu-id="4d99b-133">The guidance walks the reader through the theory and implementation of such a strategy.</span></span>

<span data-ttu-id="4d99b-134">有关从理论上讲并快速访问 Azure 实现速成教程，入门[概述的可操作的监管旅程](./journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="4d99b-134">For a crash course on the theory and quick access to Azure implementation, get started with the [Overview of actionable governance journeys](./journeys/overview.md).</span></span> <span data-ttu-id="4d99b-135">通过此指南，读者可以从小规模治理开始，并在云采用工作的同时按需治理。</span><span class="sxs-lookup"><span data-stu-id="4d99b-135">Through this guidance, the reader can start small and evolve their governance needs in parallel with cloud adoption efforts.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4d99b-136">后续步骤</span><span class="sxs-lookup"><span data-stu-id="4d99b-136">Next steps</span></span>

<span data-ttu-id="4d99b-137">查看可操作的监管旅程。</span><span class="sxs-lookup"><span data-stu-id="4d99b-137">Review the actionable governance journeys.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="4d99b-138">可操作的监管旅程</span><span class="sxs-lookup"><span data-stu-id="4d99b-138">Actionable Governance Journeys</span></span>](./journeys/overview.md)
