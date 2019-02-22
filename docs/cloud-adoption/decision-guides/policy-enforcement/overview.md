---
title: CAF：策略强制实施决策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解作为 Azure 迁移中的核心设计优先级的策略强制实施订阅。
author: rotycenh
ms.openlocfilehash: 372926453ee4ae0502250e9b69fe8a0ea94f0ffe
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900466"
---
# <a name="policy-enforcement-decision-guide"></a><span data-ttu-id="b31f2-103">策略强制实施决策指南</span><span class="sxs-lookup"><span data-stu-id="b31f2-103">Policy enforcement decision guide</span></span>

<span data-ttu-id="b31f2-104">除非可以在整个组织中强制实施策略，否则定义组织策略无效。</span><span class="sxs-lookup"><span data-stu-id="b31f2-104">Defining organizational policy is not effective unless there is a way to enforce it across your organization.</span></span> <span data-ttu-id="b31f2-105">规划任何云迁移的一个重要方面是，确定如何以最佳方式将云平台提供的工具与现有 IT 流程结合起来，以在整个云资产中最大限度地实现策略符合性。</span><span class="sxs-lookup"><span data-stu-id="b31f2-105">A key aspect to planning any cloud migration is determining how best to combine tools provided by the cloud platform with your existing IT processes to maximize policy compliance across your entire cloud estate.</span></span>

![按复杂性从低到高的顺序绘制了策略强制实施选项（与下面的跳转链接保持一致）](../../_images/discovery-guides/discovery-guide-policy-enforcement.png)

<span data-ttu-id="b31f2-107">跳转到：[基线建议的做法](#baseline-recommended-practices) | [策略符合性监视](#policy-compliance-monitoring) | [策略强制实施](#policy-enforcement) | [跨组织策略](#cross-organization-policy) | [自动强制实施](#automated-enforcement)</span><span class="sxs-lookup"><span data-stu-id="b31f2-107">Jump to: [Baseline recommended practices](#baseline-recommended-practices) | [Policy compliance monitoring](#policy-compliance-monitoring) | [Policy enforcement](#policy-enforcement) | [Cross-organization policy](#cross-organization-policy) | [Automated enforcement](#automated-enforcement)</span></span>

<span data-ttu-id="b31f2-108">随着云资产的增长，你将面临在更大的资源、订阅和租户数组中维护和实施策略的相应需求。</span><span class="sxs-lookup"><span data-stu-id="b31f2-108">As your cloud estate grows, you will be faced with a corresponding need to maintain and enforce policy across a larger array of resources, subscriptions, and tenants.</span></span> <span data-ttu-id="b31f2-109">资产越大，强制实施机制就要越复杂，以确保一致的遵从性和快速违规检测。</span><span class="sxs-lookup"><span data-stu-id="b31f2-109">The larger your estate, the more complex your enforcement mechanisms will need to be to ensure consistent adherence and fast violation detection.</span></span> <span data-ttu-id="b31f2-110">在资源或订阅级别上由平台提供的策略强制实施机制通常足以满足较小的云部署，而较大的部署可能需要利用更复杂的机制，包括部署标准、资源分组和组织，以及将策略实施与日志记录和报告系统集成。</span><span class="sxs-lookup"><span data-stu-id="b31f2-110">Platform-provided policy enforcement mechanisms at the resource or subscription level are usually sufficient for smaller cloud deployments, while larger deployments may need to take advantage of more sophisticated mechanisms involving deployment standards, resource grouping and organization, and integrating policy enforcement with your logging and reporting systems.</span></span>

<span data-ttu-id="b31f2-111">在选择策略强制实施策略的复杂性时，关键拐点主要集中在[订阅设计](../subscriptions/overview.md)所需的订阅或租户的数量上。</span><span class="sxs-lookup"><span data-stu-id="b31f2-111">The key inflection point when choosing the complexity of your policy enforcement strategy is primarily focused on the number of subscriptions or tenants required by your [subscription design](../subscriptions/overview.md).</span></span> <span data-ttu-id="b31f2-112">向云资产中各种用户角色授予的控制数量也可能会影响这些决策。</span><span class="sxs-lookup"><span data-stu-id="b31f2-112">The amount of control granted to various user roles within your cloud estate might influence these decisions as well.</span></span>

## <a name="baseline-recommended-practices"></a><span data-ttu-id="b31f2-113">基线建议的做法</span><span class="sxs-lookup"><span data-stu-id="b31f2-113">Baseline recommended practices</span></span>

<span data-ttu-id="b31f2-114">对于单个订阅和简单的云部署，可以使用大多数云平台原生的功能来实施许多公司策略。</span><span class="sxs-lookup"><span data-stu-id="b31f2-114">For single subscription and simple cloud deployments, many corporate policies can be enforced using features that are native to most cloud platforms.</span></span> <span data-ttu-id="b31f2-115">即使在这种相对较低的部署复杂性级别上，在 CAF [决策指南](../overview.md)中讨论的模式的一致使用也可以帮助建立策略符合性的基线级别。</span><span class="sxs-lookup"><span data-stu-id="b31f2-115">Even at this relatively low level of deployment complexity, the consistent use of the patterns discussed throughout the CAF [decision guides](../overview.md) can help establish a baseline level of policy compliance.</span></span>

<span data-ttu-id="b31f2-116">例如：</span><span class="sxs-lookup"><span data-stu-id="b31f2-116">For example:</span></span>

- <span data-ttu-id="b31f2-117">[部署模板](../resource-consistency/overview.md)可以预配具有标准化结构和配置的资源。</span><span class="sxs-lookup"><span data-stu-id="b31f2-117">[Deployment templates](../resource-consistency/overview.md) can provision resources with standardized structure and configuration.</span></span>
- <span data-ttu-id="b31f2-118">[标记和命名标准](../resource-tagging/overview.md)可有助于组织操作，并支持核算和业务需求。</span><span class="sxs-lookup"><span data-stu-id="b31f2-118">[Tagging and naming standards](../resource-tagging/overview.md) can help organize operations and support accounting and business requirements.</span></span>
- <span data-ttu-id="b31f2-119">流量管理和网络限制可以通过[软件定义的网络](../software-defined-network/overview.md)来实现。</span><span class="sxs-lookup"><span data-stu-id="b31f2-119">Traffic management and networking restrictions can be implemented through [software defined networking](../software-defined-network/overview.md).</span></span>
- <span data-ttu-id="b31f2-120">[基于角色的访问控制](../identity/overview.md)可保护和隔离云资源。</span><span class="sxs-lookup"><span data-stu-id="b31f2-120">[Role-based access control](../identity/overview.md) can secure and isolate your cloud resources.</span></span>

<span data-ttu-id="b31f2-121">通过查看在这些指南中讨论的标准模式的应用程序如何帮助满足组织需求，来开始进行云策略强制实施规划。</span><span class="sxs-lookup"><span data-stu-id="b31f2-121">Start your cloud policy enforcement planning by examining how the application of the standard patterns discussed throughout these guides can help meet your organizational requirements.</span></span>

## <a name="policy-compliance-monitoring"></a><span data-ttu-id="b31f2-122">策略符合性监视</span><span class="sxs-lookup"><span data-stu-id="b31f2-122">Policy compliance monitoring</span></span>

<span data-ttu-id="b31f2-123">另一个关键因素（即使对于相对较小的云部署）是验证基于云的应用程序和服务是否符合组织策略的能力，在资源不符合要求时及时通知责任方。</span><span class="sxs-lookup"><span data-stu-id="b31f2-123">Another key factor, even for relatively small cloud deployments, is the ability to verify that cloud-based applications and services comply with organizational policy, promptly notifying the responsible parties if a resource becomes noncompliant.</span></span> <span data-ttu-id="b31f2-124">有效地[记录和报告](../log-and-report/overview.md)云工作负载的符合性状态是公司策略强制实施战略的关键部分。</span><span class="sxs-lookup"><span data-stu-id="b31f2-124">Effectively [logging and reporting](../log-and-report/overview.md) the compliance status of your cloud workloads is a critical part of a corporate policy enforcement strategy.</span></span>

<span data-ttu-id="b31f2-125">随着云资产的增长，[Azure 安全中心](/azure/security-center/)等其他工具可提供集成的安全性和威胁检测，并帮助对本地资产和云资产应用集中式策略管理和警报。</span><span class="sxs-lookup"><span data-stu-id="b31f2-125">As your cloud estate grows, additional tools such as [Azure Security Center](/azure/security-center/) can provide integrated security and threat detection, and help apply centralized policy management and alerting for both your on-premises and cloud assets.</span></span>

## <a name="policy-enforcement"></a><span data-ttu-id="b31f2-126">策略强制执行</span><span class="sxs-lookup"><span data-stu-id="b31f2-126">Policy enforcement</span></span>

<span data-ttu-id="b31f2-127">还可以在订阅级别应用配置设置和资源创建规则，以帮助确保策略一致性。</span><span class="sxs-lookup"><span data-stu-id="b31f2-127">You can also apply configuration settings and resource creation rules at the subscription level to help ensure policy alignment.</span></span>

<span data-ttu-id="b31f2-128">[Azure Policy](/azure/governance/policy/overview) 是用于创建、分配和管理策略的 Azure 服务。</span><span class="sxs-lookup"><span data-stu-id="b31f2-128">[Azure Policy](/azure/governance/policy/overview) is an Azure service for creating, assigning, and managing policies.</span></span> <span data-ttu-id="b31f2-129">这些策略将在整个资源中强制实施不同的规则和效果，以便这些资源符合公司标准和服务级别协议。</span><span class="sxs-lookup"><span data-stu-id="b31f2-129">These policies enforce different rules and effects over your resources, so those resources stay compliant with your corporate standards and service level agreements.</span></span> <span data-ttu-id="b31f2-130">Azure Policy 评估资源是否符合指定策略。</span><span class="sxs-lookup"><span data-stu-id="b31f2-130">Azure Policy evaluates your resources for noncompliance with assigned policies.</span></span> <span data-ttu-id="b31f2-131">例如，你可能希望限制环境中虚拟机的 SKU 大小。</span><span class="sxs-lookup"><span data-stu-id="b31f2-131">For example, you might want to limit the SKU size of virtual machines in your environment.</span></span> <span data-ttu-id="b31f2-132">实施相应策略后，将评估新资源和现有资源的符合性。</span><span class="sxs-lookup"><span data-stu-id="b31f2-132">Once a corresponding policy is implemented, new and existing resources would be evaluated for compliance.</span></span> <span data-ttu-id="b31f2-133">通过使用正确的策略，可以确保现有资源的符合性。</span><span class="sxs-lookup"><span data-stu-id="b31f2-133">With the right policy, existing resources can be brought into compliance.</span></span>

## <a name="cross-organization-policy"></a><span data-ttu-id="b31f2-134">跨组织策略</span><span class="sxs-lookup"><span data-stu-id="b31f2-134">Cross-organization policy</span></span>

<span data-ttu-id="b31f2-135">随着云资产增长到跨越许多需强制实施的订阅，你将需要专注于租户范围内的强制实施策略，以确保策略一致性。</span><span class="sxs-lookup"><span data-stu-id="b31f2-135">As your cloud estate grows to span many subscriptions that require enforcement, you will need to focus on a tenant-wide enforcement strategy to ensure policy consistency.</span></span>

<span data-ttu-id="b31f2-136">[订阅设计](../subscriptions/overview.md)需要考虑策略，因为策略与组织结构相关。</span><span class="sxs-lookup"><span data-stu-id="b31f2-136">Your [subscription design](../subscriptions/overview.md) will need to account for policy as it relates to your organizational structure.</span></span> <span data-ttu-id="b31f2-137">除了帮助支持订阅设计中的复杂组织之外，[Azure 管理组](../subscriptions/overview.md#management-groups)还可以用于跨多个订阅分配 Azure Policy 规则。</span><span class="sxs-lookup"><span data-stu-id="b31f2-137">In addition to helping support complex organization within your subscription design, [Azure management groups](../subscriptions/overview.md#management-groups) can be used to assign Azure Policy rules across multiple subscriptions.</span></span>

## <a name="automated-enforcement"></a><span data-ttu-id="b31f2-138">自动强制实施</span><span class="sxs-lookup"><span data-stu-id="b31f2-138">Automated enforcement</span></span>

<span data-ttu-id="b31f2-139">虽然标准化的部署模板在较小范围内是有效的，但借助 [Azure 蓝图](/azure/governance/blueprints/overview)，Azure 解决方案可进行大规模标准化预配和部署业务流程。</span><span class="sxs-lookup"><span data-stu-id="b31f2-139">While standardized deployment templates are effective at a smaller scale, [Azure Blueprints](/azure/governance/blueprints/overview) allows large-scale standardized provisioning and deployment orchestration of Azure solutions.</span></span> <span data-ttu-id="b31f2-140">可使用创建的任何资源的一致策略设置部署跨多个订阅的工作负载。</span><span class="sxs-lookup"><span data-stu-id="b31f2-140">Workloads across multiple subscriptions can be deployed with consistent policy settings for any resources created.</span></span>

<span data-ttu-id="b31f2-141">对于集成云和本地资源的 IT 环境，可能需要使用日志记录和报告系统来提供混合监视功能。</span><span class="sxs-lookup"><span data-stu-id="b31f2-141">For IT environments integrating cloud and on-premises resources, you may need use logging and reporting systems to provide hybrid monitoring capabilities.</span></span> <span data-ttu-id="b31f2-142">第三方或自定义操作监视系统可以提供额外的策略强制实施功能。</span><span class="sxs-lookup"><span data-stu-id="b31f2-142">Your third-party or custom operational monitoring systems may offer additional policy enforcement capabilities.</span></span> <span data-ttu-id="b31f2-143">对于复杂的云资产，考虑如何以最佳方式将这些系统与云资产集成。</span><span class="sxs-lookup"><span data-stu-id="b31f2-143">For complicated cloud estates, consider how best to integrate these systems with your cloud assets.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b31f2-144">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b31f2-144">Next steps</span></span>

<span data-ttu-id="b31f2-145">了解如何使用资源一致性来组织和标准化云部署，以支持订阅设计和治理目标。</span><span class="sxs-lookup"><span data-stu-id="b31f2-145">Learn how resource consistency is used to organize and standardize cloud deployments in support of subscription design and governance goals.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b31f2-146">资源一致性</span><span class="sxs-lookup"><span data-stu-id="b31f2-146">Resource consistency</span></span>](../resource-consistency/overview.md)
