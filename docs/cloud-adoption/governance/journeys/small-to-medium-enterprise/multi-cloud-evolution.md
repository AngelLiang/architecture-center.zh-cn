---
title: CAF：中小型企业 - 多云演变
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 阐释：中小型企业 – 多云演变
author: BrianBlanchard
ms.openlocfilehash: a5b09c92acc4e165590b5a35827b88b0ca099bed
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900639"
---
# <a name="small-to-medium-enterprise-multi-cloud-evolution"></a><span data-ttu-id="4d8ab-103">中小型企业：多云演变</span><span class="sxs-lookup"><span data-stu-id="4d8ab-103">Small-to-medium enterprise: Multi-cloud evolution</span></span>

<span data-ttu-id="4d8ab-104">本文通过针对多云采用添加控制，对叙述性内容进行了演化。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-104">This article evolves the narrative by adding controls for multi-cloud adoption.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="4d8ab-105">叙述性内容的演变</span><span class="sxs-lookup"><span data-stu-id="4d8ab-105">Evolution of the narrative</span></span>

<span data-ttu-id="4d8ab-106">Microsoft 意识到客户正在为特定的目的采用多个云。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-106">Microsoft recognizes that customers are adopting multiple clouds for specific purposes.</span></span> <span data-ttu-id="4d8ab-107">在这一过程中虚构的客户也不例外。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-107">The fictional customer in this journey is no exception.</span></span> <span data-ttu-id="4d8ab-108">在采用 Azure 的同时，该公司的成功导致其收购了一个互补的小型企业。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-108">In parallel to the Azure adoption journey, the business success has led to the acquisition of a small, but complementary business.</span></span> <span data-ttu-id="4d8ab-109">该业务在不同的云服务提供商上运行其所有的 IT 操作。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-109">That business is running all of their IT operations on a different cloud provider.</span></span>

<span data-ttu-id="4d8ab-110">本文介绍集成这家新组织时的事态变化。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-110">This article describes how things change when integrating the new organization.</span></span> <span data-ttu-id="4d8ab-111">出于说明的目的，让我们假设该公司已完成此客户旅程中概述的每一项治理变革。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-111">For purposes of the narrative, we assume this company has completed each of the governance evolutions outlined in this customer journey.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="4d8ab-112">当前状态的演变</span><span class="sxs-lookup"><span data-stu-id="4d8ab-112">Evolution of the current state</span></span>

<span data-ttu-id="4d8ab-113">在此叙述性内容的上一阶段中，公司已开始通过 CI/CD 管道积极地将生产应用程序推送到云。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-113">In the previous phase of this narrative, the company had begun actively pushing production applications to the cloud through CI/CD pipelines.</span></span>

<span data-ttu-id="4d8ab-114">自此，一些事态已改变，将会影响到治理：</span><span class="sxs-lookup"><span data-stu-id="4d8ab-114">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="4d8ab-115">标识由 Active Directory 的本地实例控制。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-115">Identity is controlled by an on-premises instance of Active Directory.</span></span> <span data-ttu-id="4d8ab-116">复制到 Azure Active Directory 有助于实现混合标识。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-116">Hybrid identity is facilitated through replication to Azure Active Directory.</span></span>
- <span data-ttu-id="4d8ab-117">IT 操作或云操作很大程度上由 Azure Monitor 和相关的自动化进行管理。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-117">IT Operations or Cloud Operations are largely managed by Azure Monitor and related automations.</span></span>
- <span data-ttu-id="4d8ab-118">灾难恢复/业务连续性受 Azure Vault 实例控制。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-118">Disaster Recovery / Business Continuity is controlled by Azure Vault instances.</span></span>
- <span data-ttu-id="4d8ab-119">Azure 安全中心用于监视安全违规和攻击。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-119">Azure Security Center is used to monitor security violations and attacks.</span></span>
- <span data-ttu-id="4d8ab-120">Azure 安全中心和 Azure Monitor 都用于监视云管理。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-120">Azure Security Center and Azure Monitor are both used to monitor governance of the cloud.</span></span>
- <span data-ttu-id="4d8ab-121">Azure 蓝图、Azure Policy 和 Azure 管理组用于自动遵守策略。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-121">Azure Blueprints, Azure Policy, and Azure management groups are used to automate compliance with policy.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="4d8ab-122">未来状态的演变</span><span class="sxs-lookup"><span data-stu-id="4d8ab-122">Evolution of the future state</span></span>

<span data-ttu-id="4d8ab-123">目标是尽可能将收购公司整合到现有业务中。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-123">The goal is to integrate the acquisition company into existing operations wherever possible.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="4d8ab-124">有形风险的演变</span><span class="sxs-lookup"><span data-stu-id="4d8ab-124">Evolution of tangible risks</span></span>

<span data-ttu-id="4d8ab-125">**业务购置成本**：收购新业务预计将在大约五年内实现盈利。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-125">**Business acquisition cost**: Acquisition of the new business is slated to be profitable in approximately five years.</span></span> <span data-ttu-id="4d8ab-126">由于回报慢，董事会希望尽可能多地控制购置成本。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-126">Because of the slow rate of return, the board wants to control acquisition costs, as much as possible.</span></span> <span data-ttu-id="4d8ab-127">成本控制和技术集成之间存在相互冲突的风险。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-127">There is a risk of cost control and technical integration conflicting with one another.</span></span>

<span data-ttu-id="4d8ab-128">此业务风险可以扩展为几种技术风险：</span><span class="sxs-lookup"><span data-stu-id="4d8ab-128">This business risk can be expanded into a few technical risks:</span></span>

- <span data-ttu-id="4d8ab-129">云迁移可能会产生额外的购置成本</span><span class="sxs-lookup"><span data-stu-id="4d8ab-129">Cloud migration might produce additional acquisition costs</span></span>
- <span data-ttu-id="4d8ab-130">新环境可能未正确进行管理，这可能会导致违反策略。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-130">The new environment might not be properly governed which could result in policy violations.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="4d8ab-131">策略语句的演变</span><span class="sxs-lookup"><span data-stu-id="4d8ab-131">Evolution of the policy statements</span></span>

<span data-ttu-id="4d8ab-132">以下策略的更改将有助于缓解新的风险，同时有助于实施指南。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-132">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="4d8ab-133">必须通过现有运营管理和安全监视工具来监视辅助云中的所有资产</span><span class="sxs-lookup"><span data-stu-id="4d8ab-133">All assets in a secondary cloud must be monitored through existing operational management and security monitoring tools</span></span>
2. <span data-ttu-id="4d8ab-134">所有组织单位必须集成到现有标识提供者中</span><span class="sxs-lookup"><span data-stu-id="4d8ab-134">All Organization Units must be integrated into the existing identity provider</span></span>
3. <span data-ttu-id="4d8ab-135">主要标识提供者应对辅助云中的资产管理身份验证</span><span class="sxs-lookup"><span data-stu-id="4d8ab-135">The primary identity provider should govern authentication to assets in the secondary cloud</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="4d8ab-136">最佳做法的演变</span><span class="sxs-lookup"><span data-stu-id="4d8ab-136">Evolution of the best practices</span></span>

<span data-ttu-id="4d8ab-137">本文的这一部分将改进治理 MVP 设计，以包括新的 Azure 策略和 Azure 成本管理的实现。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-137">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="4d8ab-138">这两项设计变更将共同实现新的公司策略语句。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-138">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="4d8ab-139">连接网络。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-139">Connect the networks.</span></span> <span data-ttu-id="4d8ab-140">此步骤由网络和 IT 安全团队执行，并由云治理团队提供支持。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-140">This step is executed by the Networking and IT Security teams, and supported by the Cloud Governance team.</span></span> <span data-ttu-id="4d8ab-141">添加从 MPLS/租用线路提供商到新云的连接会集成网络。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-141">Adding a connection from the MPLS/leased-line provider to the new cloud will integrate networks.</span></span> <span data-ttu-id="4d8ab-142">添加路由表和防火墙配置将控制环境之间的访问和流量。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-142">Adding routing tables and firewall configurations will control access and traffic between the environments.</span></span>
2. <span data-ttu-id="4d8ab-143">合并标识提供者。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-143">Consolidate identity providers.</span></span> <span data-ttu-id="4d8ab-144">根据托管在辅助云中的工作负荷，标识提供者的合并有多种选择。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-144">Depending on the workloads being hosted in the secondary cloud, there are a variety of options to identity provider consolidation.</span></span> <span data-ttu-id="4d8ab-145">下面是几个示例：</span><span class="sxs-lookup"><span data-stu-id="4d8ab-145">The following are a few examples:</span></span>
    1. <span data-ttu-id="4d8ab-146">对于使用 OAuth 2 进行身份验证的应用程序，辅助云中来自 Active Directory 的用户可以轻松地复制到现有 Azure AD 租户中。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-146">For applications that authenticate using OAuth 2, users from Active Directory in the secondary cloud can simply be replicated to the existing Azure AD tenant.</span></span> <span data-ttu-id="4d8ab-147">这可确保所有用户都可以在租户中进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-147">This ensures all users can be authenticated in the tenant.</span></span>
    2. <span data-ttu-id="4d8ab-148">在另一个极端，联合使 OU 可以流入本地 Active Directory，然后流入 Azure AD 实例。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-148">At the other extreme, federation allows OUs to flow into Active Directory on-premises, then into the Azure AD instance.</span></span>
3. <span data-ttu-id="4d8ab-149">将资产添加到 Azure Site Recovery。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-149">Add assets to Azure Site Recovery.</span></span>
    1. <span data-ttu-id="4d8ab-150">Azure Site Recovery 从一开始就是作为混合/多云工具设计的。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-150">Azure Site Recovery was designed from the beginning as a hybrid/multi-cloud tool.</span></span>
    2. <span data-ttu-id="4d8ab-151">辅助云中的 VM 可以由用于保护本地资产的同一个 Azure Site Recovery 流程进行保护。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-151">VMs in the secondary cloud might be able to be protected by the same Azure Site Recovery processes used to protect on-premises assets.</span></span>
4. <span data-ttu-id="4d8ab-152">将资产添加到 Azure 成本管理</span><span class="sxs-lookup"><span data-stu-id="4d8ab-152">Add assets to Azure Cost Management</span></span>
    1. <span data-ttu-id="4d8ab-153">Azure 成本管理从一开始就是作为多云工具设计的。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-153">Azure Cost Management was designed from the beginning as a multi-cloud tool.</span></span>
    2. <span data-ttu-id="4d8ab-154">辅助云中的虚拟机可能与某些云提供商的 Azure 成本管理兼容。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-154">Virtual machines in the secondary cloud may be compatible with Azure Cost Management for some cloud providers.</span></span> <span data-ttu-id="4d8ab-155">可能会收取额外费用。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-155">Additional costs may apply.</span></span>
5. <span data-ttu-id="4d8ab-156">将资产添加到 Azure Monitor。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-156">Add assets to Azure Monitor.</span></span>
    1. <span data-ttu-id="4d8ab-157">Azure Monitor 从一开始就是作为混合云工具设计的。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-157">Azure Monitor was designed as a hybrid cloud tool from inception.</span></span>
    2. <span data-ttu-id="4d8ab-158">辅助云中的虚拟机可能与 Azure Monitor 代理兼容，从而使它们可以包含在 Azure Monitor 中以进行操作监视。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-158">Virtual machines in the secondary cloud may be compatible with Azure Monitor agents, allowing them to be included in Azure Monitor for operational monitoring.</span></span>
6. <span data-ttu-id="4d8ab-159">治理强制执行工具：</span><span class="sxs-lookup"><span data-stu-id="4d8ab-159">Governance enforcement tools:</span></span>
    1. <span data-ttu-id="4d8ab-160">治理强制执行特定于云。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-160">Governance enforcement is cloud-specific.</span></span>
    2. <span data-ttu-id="4d8ab-161">在治理过程中建立的公司策略不特定于云。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-161">The corporate policies established in the governance journey are not cloud-specific.</span></span> <span data-ttu-id="4d8ab-162">尽管云和云之间的实现不尽相同，但策略可以应用于辅助提供程序。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-162">While the implementation may vary from cloud to cloud, the policies can be applied to the secondary provider.</span></span>

<span data-ttu-id="4d8ab-163">随着多云采用的增长，上述设计演变将继续，变得成熟。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-163">As multi-cloud adoption grows, the design evolution above will continue to mature.</span></span>

## <a name="conclusion"></a><span data-ttu-id="4d8ab-164">结束语</span><span class="sxs-lookup"><span data-stu-id="4d8ab-164">Conclusion</span></span>

<span data-ttu-id="4d8ab-165">本系列的文章概述了治理最佳做法的演变（与此虚构公司的体验保持一致）。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-165">This series of articles outlined the evolution of governance best practices, aligned with the experiences of this fictional company.</span></span> <span data-ttu-id="4d8ab-166">通过从小而正确的基础开始，公司可以快速行动，同时仍在合适的时间应用适量的治理。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-166">By starting small, but with the right foundation, the company could move quickly and yet still apply the right amount of governance at the right time.</span></span> <span data-ttu-id="4d8ab-167">MVP 本身并不保护客户。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-167">The MVP by itself did not protect the customer.</span></span> <span data-ttu-id="4d8ab-168">而是创造基础来缓解风险并增加保护功能。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-168">Instead, it created the foundation to mitigate risk and add protections.</span></span> <span data-ttu-id="4d8ab-169">在此基础上应用了各层治理来缓解具体风险。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-169">From there, layers of governance were applied to mitigate tangible risks.</span></span> <span data-ttu-id="4d8ab-170">此处提供的确切过程不会 100% 符合所有读者的体验。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-170">The exact journey presented here won't align 100% with the experiences of any reader.</span></span> <span data-ttu-id="4d8ab-171">而是充当一种增量治理模式。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-171">Rather, it serves as a pattern for incremental governance.</span></span> <span data-ttu-id="4d8ab-172">建议读者塑造这些最佳做法，以适合自己的独有约束和治理要求。</span><span class="sxs-lookup"><span data-stu-id="4d8ab-172">The reader is advised to mold these best practices to fit their own unique constraints and governance requirements.</span></span>