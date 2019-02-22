---
title: CAF：安全基线动机和业务风险
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 1/8/2019
description: 安全基线动机和业务风险
author: BrianBlanchard
ms.openlocfilehash: d57b79b37aa257d07df0168a7fee53ec8ecd1526
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900440"
---
# <a name="security-baseline-motivations-and-business-risks"></a><span data-ttu-id="93070-103">安全基线动机和业务风险</span><span class="sxs-lookup"><span data-stu-id="93070-103">Security Baseline motivations and business risks</span></span>

<span data-ttu-id="93070-104">本文讨论客户通常在云治理策略中采用安全基线规则的原因。</span><span class="sxs-lookup"><span data-stu-id="93070-104">This article discusses the reasons that customers typically adopt a Security Baseline discipline within a cloud governance strategy.</span></span> <span data-ttu-id="93070-105">此外，还提供了可能驱动策略语句的几个潜在业务风险示例。</span><span class="sxs-lookup"><span data-stu-id="93070-105">It also provides a few examples of potential business risks that can drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-a-security-baseline-relevant"></a><span data-ttu-id="93070-106">与安全基线相关吗？</span><span class="sxs-lookup"><span data-stu-id="93070-106">Is a Security Baseline relevant?</span></span>

<span data-ttu-id="93070-107">安全性是所有 IT 组织的关键考虑事项。</span><span class="sxs-lookup"><span data-stu-id="93070-107">Security is a key concern for any IT organization.</span></span> <span data-ttu-id="93070-108">云部署面临许多与传统本地数据中心内托管的工作负荷相同的安全风险。</span><span class="sxs-lookup"><span data-stu-id="93070-108">Cloud deployments face many of the same security risks as workloads hosted in traditional on-premises datacenters.</span></span> <span data-ttu-id="93070-109">但是，公共云平台的性质（即缺乏对存储和运行工作负荷的物理硬件的直接所有权）意味着云安全需要自己的策略和流程。</span><span class="sxs-lookup"><span data-stu-id="93070-109">However, the nature of public cloud platforms, with a lack of direct ownership of the physical hardware storing and running your workloads, means cloud security requires its own policy and processes.</span></span>

<span data-ttu-id="93070-110">将云安全治理与传统安全策略区分开来的主要区别之一在于是否可以轻松选择要创建的资源，如果在部署之前未考虑安全性，则可能会增加漏洞。</span><span class="sxs-lookup"><span data-stu-id="93070-110">One of the primary things that set cloud security governance apart from traditional security policy is the ease with which resources can be created, potentially adding vulnerabilities if security isn't considered before deployment.</span></span> <span data-ttu-id="93070-111">[软件定义网络 (SDN)](../../decision-guides/software-defined-network/overview.md) 等技术为快速更改基于云的网络拓扑而提供的灵活性也可能以不可预见的方式轻松修改整个网络攻击面。</span><span class="sxs-lookup"><span data-stu-id="93070-111">The flexibility that technologies like [software defined networking (SDN)](../../decision-guides/software-defined-network/overview.md) provide for rapidly changing your cloud-based network topology can also easily modify your overall network attack surface in unforeseen ways.</span></span> <span data-ttu-id="93070-112">云平台还提供能够以在本地环境中无法实现的方式提高安全性能的工具和功能。</span><span class="sxs-lookup"><span data-stu-id="93070-112">Cloud platforms also provide tools and features that can improve your security capabilities in ways not always possible in on-premises environments.</span></span>

<span data-ttu-id="93070-113">投入安全策略和流程的金额将在很大程度上取决于云部署的性质。</span><span class="sxs-lookup"><span data-stu-id="93070-113">The amount you invest into security policy and processes will depend a great deal on the nature of your cloud deployment.</span></span> <span data-ttu-id="93070-114">初始测试部署可能只需要最基本的安全策略，而任务关键工作负荷则需要解决复杂而广泛的安全需求问题。</span><span class="sxs-lookup"><span data-stu-id="93070-114">Initial test deployments may only need the most basic of security policies in place, while a mission-critical workload will entail addressing complex and extensive security needs.</span></span> <span data-ttu-id="93070-115">所有部署都需要在某种程度上使用该规则。</span><span class="sxs-lookup"><span data-stu-id="93070-115">All deployments will need to engage with the discipline at some level.</span></span>

<span data-ttu-id="93070-116">安全基线规则涵盖可用于保护云部署免受安全风险影响的企业策略和手动流程。</span><span class="sxs-lookup"><span data-stu-id="93070-116">The Security Baseline discipline covers the corporate policies and manual processes that you can put in place to protect your cloud deployment against security risks.</span></span>

> [!NOTE]
><span data-ttu-id="93070-117">虽然在安全基线的上下文中理解[标识基线](../identity-baseline/overview.md)及其如何与访问控制相关很重要，但[云治理的五项规则](../overview.md)将[标识基线](../identity-baseline/overview.md)称为独立于安全基线的独立规则。</span><span class="sxs-lookup"><span data-stu-id="93070-117">While it is important to understand [Identity Baseline](../identity-baseline/overview.md) in the context of Security Baseline and how that relates to Access Control, the [Five Disciplines of Cloud Governance](../overview.md) calls out [Identity Baseline](../identity-baseline/overview.md) as its own discipline, separate from Security Baseline.</span></span>

## <a name="business-risk"></a><span data-ttu-id="93070-118">业务风险</span><span class="sxs-lookup"><span data-stu-id="93070-118">Business risk</span></span>

<span data-ttu-id="93070-119">安全基线规则尝试解决安全相关的核心业务风险问题。</span><span class="sxs-lookup"><span data-stu-id="93070-119">The Security Baseline discipline attempts to address core security-related business risks.</span></span> <span data-ttu-id="93070-120">与企业合作来确定这些风险，并在规划和实现云部署时监视每个风险的相关性。</span><span class="sxs-lookup"><span data-stu-id="93070-120">Work with your business to identify these risks and monitor each of them for relevance as you plan for and implement your cloud deployments.</span></span>

<span data-ttu-id="93070-121">组织间的风险会有所不同，以下是常见的与安全相关的风险，你可以由此在云治理团队中展开讨论：</span><span class="sxs-lookup"><span data-stu-id="93070-121">Risks will differ between organization, but the following serve as common security-related risks that you can use as a starting point for discussions within your Cloud Governance team:</span></span>

- <span data-ttu-id="93070-122">**数据安全漏洞**。</span><span class="sxs-lookup"><span data-stu-id="93070-122">**Data breach**.</span></span> <span data-ttu-id="93070-123">无意中泄露或丢失敏感的云托管数据可能导致客户流失、合同问题或法律后果。</span><span class="sxs-lookup"><span data-stu-id="93070-123">Inadvertent exposure or loss of sensitive cloud-hosted data can lead to losing customers, contractual issues, or legal consequences.</span></span>
- <span data-ttu-id="93070-124">**服务中断**。</span><span class="sxs-lookup"><span data-stu-id="93070-124">**Service disruption**.</span></span> <span data-ttu-id="93070-125">因不安全的基础结构而导致的中断和其他性能问题会中断正常运行，并可能导致生产力损失或业务丢失。</span><span class="sxs-lookup"><span data-stu-id="93070-125">Outages and other performance issues due to insecure infrastructure interrupts normal operations and can result in lost productivity or lost business.</span></span>

## <a name="next-steps"></a><span data-ttu-id="93070-126">后续步骤</span><span class="sxs-lookup"><span data-stu-id="93070-126">Next steps</span></span>

<span data-ttu-id="93070-127">使用[云管理模板](./template.md)，记录可能由当前云采用计划引入的业务风险。</span><span class="sxs-lookup"><span data-stu-id="93070-127">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="93070-128">确立了对实际业务风险的了解后，下一步是记录业务对风险的容忍度，以及用于监视该容忍度的指示器和关键指标。</span><span class="sxs-lookup"><span data-stu-id="93070-128">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="93070-129">了解指示器、指标和风险容忍度</span><span class="sxs-lookup"><span data-stu-id="93070-129">Understand indicators, metrics, and risk tolerance</span></span>](./metrics-tolerance.md)
