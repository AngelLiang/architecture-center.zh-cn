---
title: CAF：Azure 中的安全基线工具
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 介绍 Azure 中有助于提升安全基线的工具
author: BrianBlanchard
ms.openlocfilehash: b316626c8ad717514f7f592abefa0f33a92afdca
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243168"
---
# <a name="security-baseline-tools-in-azure"></a><span data-ttu-id="cd2e2-103">Azure 中的安全基线工具</span><span class="sxs-lookup"><span data-stu-id="cd2e2-103">Security Baseline tools in Azure</span></span>

<span data-ttu-id="cd2e2-104">[安全基线](overview.md)是[云治理的五项规则](../governance-disciplines.md)之一。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-104">[Security Baseline](overview.md) is one of the [Five Disciplines of Cloud Governance](../governance-disciplines.md).</span></span> <span data-ttu-id="cd2e2-105">该规则侧重于如何建立策略来保护网络、资产，以及最重要的将驻留在云提供商解决方案中的数据。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-105">This discipline focuses on ways of establishing policies that protect the network, assets, and most importantly the data that will reside on a Cloud Provider's solution.</span></span> <span data-ttu-id="cd2e2-106">在云治理的五项规则中，安全基线包括数字资产和数据的分类。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-106">Within the five disciplines of Cloud Governance, Security Baseline includes classification of the digital estate and data.</span></span> <span data-ttu-id="cd2e2-107">此外，它还包括与数据、资产和网络安全性相关联的风险、业务容忍度和缓解策略的说明。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-107">It also includes documentation of risks, business tolerance, and mitigation strategies associated with the security of the data, assets, and network.</span></span> <span data-ttu-id="cd2e2-108">从技术角度而言，它还涉及[加密](../../decision-guides/encryption/overview.md)决策、[网络要求](../../decision-guides/software-defined-network/overview.md)、[混合标识策略](../../decision-guides/identity/overview.md)和跨[资源组](../../decision-guides/resource-consistency/overview.md)[自动强制执行](../../decision-guides/policy-enforcement/overview.md)安全策略的工具。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-108">From a technical perspective, this also includes involvement in decisions regarding [encryption](../../decision-guides/encryption/overview.md), [network requirements](../../decision-guides/software-defined-network/overview.md), [hybrid identity strategies](../../decision-guides/identity/overview.md), and tools to [automate enforcement](../../decision-guides/policy-enforcement/overview.md) of security policies across [resource groups](../../decision-guides/resource-consistency/overview.md).</span></span>

<span data-ttu-id="cd2e2-109">下面是 Azure 工具的列表，可帮助完善支持安全基线的策略和进程。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-109">The following is a list of Azure tools that can help mature the policies and processes that support Security Baseline.</span></span>

|                                                            | <span data-ttu-id="cd2e2-110">[Azure 门户](https://azure.microsoft.com/features/azure-portal/) / [资源管理器](/azure/azure-resource-manager/resource-group-overview)</span><span class="sxs-lookup"><span data-stu-id="cd2e2-110">[Azure portal](https://azure.microsoft.com/features/azure-portal/) / [Resource Manager](/azure/azure-resource-manager/resource-group-overview)</span></span>  | [<span data-ttu-id="cd2e2-111">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="cd2e2-111">Azure Key Vault</span></span>](/azure/key-vault)  | [<span data-ttu-id="cd2e2-112">Azure AD</span><span class="sxs-lookup"><span data-stu-id="cd2e2-112">Azure AD</span></span>](/azure/active-directory/fundamentals/active-directory-whatis) | [<span data-ttu-id="cd2e2-113">Azure Policy</span><span class="sxs-lookup"><span data-stu-id="cd2e2-113">Azure Policy</span></span>](/azure/governance/policy/overview) | [<span data-ttu-id="cd2e2-114">Azure 安全中心</span><span class="sxs-lookup"><span data-stu-id="cd2e2-114">Azure Security Center</span></span>](/azure/security-center/security-center-intro) | [<span data-ttu-id="cd2e2-115">Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="cd2e2-115">Azure Monitor</span></span>](/azure/azure-monitor/overview) |
|------------------------------------------------------------|---------------------------------|-----------------|----------|--------------|-----------------------|---------------|
| <span data-ttu-id="cd2e2-116">将访问控制用于资源和资源创建</span><span class="sxs-lookup"><span data-stu-id="cd2e2-116">Apply access controls to resources and resource creation</span></span>   | <span data-ttu-id="cd2e2-117">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-117">Yes</span></span>                             | <span data-ttu-id="cd2e2-118">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-118">No</span></span>              | <span data-ttu-id="cd2e2-119">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-119">Yes</span></span>      | <span data-ttu-id="cd2e2-120">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-120">No</span></span>           | <span data-ttu-id="cd2e2-121">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-121">No</span></span>                    | <span data-ttu-id="cd2e2-122">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-122">No</span></span>            |
| <span data-ttu-id="cd2e2-123">保护虚拟网络</span><span class="sxs-lookup"><span data-stu-id="cd2e2-123">Secure virtual networks</span></span>                                    | <span data-ttu-id="cd2e2-124">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-124">Yes</span></span>                             | <span data-ttu-id="cd2e2-125">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-125">No</span></span>              | <span data-ttu-id="cd2e2-126">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-126">No</span></span>       | <span data-ttu-id="cd2e2-127">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-127">Yes</span></span>          | <span data-ttu-id="cd2e2-128">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-128">No</span></span>                    | <span data-ttu-id="cd2e2-129">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-129">No</span></span>            |
| <span data-ttu-id="cd2e2-130">加密虚拟驱动器</span><span class="sxs-lookup"><span data-stu-id="cd2e2-130">Encrypt virtual drives</span></span>                                     | <span data-ttu-id="cd2e2-131">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-131">No</span></span>                              | <span data-ttu-id="cd2e2-132">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-132">Yes</span></span>             | <span data-ttu-id="cd2e2-133">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-133">No</span></span>       | <span data-ttu-id="cd2e2-134">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-134">No</span></span>           | <span data-ttu-id="cd2e2-135">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-135">No</span></span>                    | <span data-ttu-id="cd2e2-136">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-136">No</span></span>            |
| <span data-ttu-id="cd2e2-137">加密 PaaS 存储和数据库</span><span class="sxs-lookup"><span data-stu-id="cd2e2-137">Encrypt PaaS storage and databases</span></span>                         | <span data-ttu-id="cd2e2-138">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-138">No</span></span>                              | <span data-ttu-id="cd2e2-139">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-139">Yes</span></span>             | <span data-ttu-id="cd2e2-140">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-140">No</span></span>       | <span data-ttu-id="cd2e2-141">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-141">No</span></span>           | <span data-ttu-id="cd2e2-142">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-142">No</span></span>                    | <span data-ttu-id="cd2e2-143">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-143">No</span></span>            |
| <span data-ttu-id="cd2e2-144">管理混合标识服务</span><span class="sxs-lookup"><span data-stu-id="cd2e2-144">Manage hybrid identity services</span></span>                            | <span data-ttu-id="cd2e2-145">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-145">No</span></span>                              | <span data-ttu-id="cd2e2-146">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-146">No</span></span>              | <span data-ttu-id="cd2e2-147">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-147">Yes</span></span>      | <span data-ttu-id="cd2e2-148">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-148">No</span></span>           | <span data-ttu-id="cd2e2-149">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-149">No</span></span>                    | <span data-ttu-id="cd2e2-150">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-150">No</span></span>            |
| <span data-ttu-id="cd2e2-151">限制允许的资源类型</span><span class="sxs-lookup"><span data-stu-id="cd2e2-151">Restrict allowed types of resource</span></span>                         | <span data-ttu-id="cd2e2-152">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-152">No</span></span>                              | <span data-ttu-id="cd2e2-153">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-153">No</span></span>              | <span data-ttu-id="cd2e2-154">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-154">No</span></span>       | <span data-ttu-id="cd2e2-155">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-155">Yes</span></span>          | <span data-ttu-id="cd2e2-156">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-156">No</span></span>                    | <span data-ttu-id="cd2e2-157">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-157">No</span></span>            |
| <span data-ttu-id="cd2e2-158">强制执行地理区域限制</span><span class="sxs-lookup"><span data-stu-id="cd2e2-158">Enforce geo-regional restrictions</span></span>                          | <span data-ttu-id="cd2e2-159">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-159">No</span></span>                              | <span data-ttu-id="cd2e2-160">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-160">No</span></span>              | <span data-ttu-id="cd2e2-161">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-161">No</span></span>       | <span data-ttu-id="cd2e2-162">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-162">Yes</span></span>          | <span data-ttu-id="cd2e2-163">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-163">No</span></span>                    | <span data-ttu-id="cd2e2-164">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-164">No</span></span>            |
| <span data-ttu-id="cd2e2-165">监视网络和资源的安全运行状况</span><span class="sxs-lookup"><span data-stu-id="cd2e2-165">Monitor security health of networks and resources</span></span>          | <span data-ttu-id="cd2e2-166">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-166">No</span></span>                              | <span data-ttu-id="cd2e2-167">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-167">No</span></span>              | <span data-ttu-id="cd2e2-168">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-168">No</span></span>       | <span data-ttu-id="cd2e2-169">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-169">No</span></span>           | <span data-ttu-id="cd2e2-170">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-170">Yes</span></span>                   | <span data-ttu-id="cd2e2-171">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-171">Yes</span></span>           |
| <span data-ttu-id="cd2e2-172">检测恶意活动</span><span class="sxs-lookup"><span data-stu-id="cd2e2-172">Detect malicious activity</span></span>                                  | <span data-ttu-id="cd2e2-173">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-173">No</span></span>                              | <span data-ttu-id="cd2e2-174">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-174">No</span></span>              | <span data-ttu-id="cd2e2-175">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-175">No</span></span>       | <span data-ttu-id="cd2e2-176">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-176">No</span></span>           | <span data-ttu-id="cd2e2-177">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-177">Yes</span></span>                   | <span data-ttu-id="cd2e2-178">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-178">Yes</span></span>           |
| <span data-ttu-id="cd2e2-179">预先检测漏洞</span><span class="sxs-lookup"><span data-stu-id="cd2e2-179">Preemptively detect vulnerabilities</span></span>                        | <span data-ttu-id="cd2e2-180">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-180">No</span></span>                              | <span data-ttu-id="cd2e2-181">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-181">No</span></span>              | <span data-ttu-id="cd2e2-182">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-182">No</span></span>       | <span data-ttu-id="cd2e2-183">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-183">No</span></span>           | <span data-ttu-id="cd2e2-184">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-184">Yes</span></span>                   | <span data-ttu-id="cd2e2-185">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-185">No</span></span>            |
| <span data-ttu-id="cd2e2-186">配置备份和灾难恢复</span><span class="sxs-lookup"><span data-stu-id="cd2e2-186">Configure backup and disaster recovery</span></span>                     | <span data-ttu-id="cd2e2-187">是</span><span class="sxs-lookup"><span data-stu-id="cd2e2-187">Yes</span></span>                             | <span data-ttu-id="cd2e2-188">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-188">No</span></span>              | <span data-ttu-id="cd2e2-189">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-189">No</span></span>       | <span data-ttu-id="cd2e2-190">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-190">No</span></span>           | <span data-ttu-id="cd2e2-191">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-191">No</span></span>                    | <span data-ttu-id="cd2e2-192">否</span><span class="sxs-lookup"><span data-stu-id="cd2e2-192">No</span></span>            |

<span data-ttu-id="cd2e2-193">有关 Azure 安全工具和服务的完整列表，请参阅 [Azure 上可用的安全服务和技术](/azure/security/azure-security-services-technologies)。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-193">For a complete list of Azure security tools and services, see [Security services and technologies available on Azure](/azure/security/azure-security-services-technologies).</span></span>

<span data-ttu-id="cd2e2-194">此外，客户通常还会使用第三方工具来推动安全基线活动。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-194">It is also extremely common for customers to use third-party tools for facilitating Security Baseline activities.</span></span> <span data-ttu-id="cd2e2-195">有关详细信息，请参阅[在 Azure 安全中心集成安全解决方案](/azure/security-center/security-center-partner-integration)。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-195">For more information, see the article [Integrate security solutions in Azure Security Center](/azure/security-center/security-center-partner-integration).</span></span>

<span data-ttu-id="cd2e2-196">除安全工具外，[Microsoft 信任中心](https://www.microsoft.com/trustcenter/guidance/risk-assessment)也包含大量的指南、报告以及相关文档，有助于你在迁移计划过程中执行风险评估。</span><span class="sxs-lookup"><span data-stu-id="cd2e2-196">In addition to security tools, the [Microsoft Trust Center](https://www.microsoft.com/trustcenter/guidance/risk-assessment) contains extensive guidance, reports, and related documentation that can help you perform risk assessments as part of your migration planning process.</span></span>