---
title: CAF：大型企业 – 多云发展
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企业 – 多云发展
author: BrianBlanchard
ms.openlocfilehash: 62a2fdd6e340c96494354f4f0cf2f78ab572c251
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641138"
---
# <a name="large-enterprise-multi-cloud-evolution"></a><span data-ttu-id="9faaa-103">大型企业：多云演变</span><span class="sxs-lookup"><span data-stu-id="9faaa-103">Large enterprise: Multi-cloud evolution</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="9faaa-104">叙述性内容的演变</span><span class="sxs-lookup"><span data-stu-id="9faaa-104">Evolution of the narrative</span></span>

<span data-ttu-id="9faaa-105">Microsoft 意识到客户正在为特定的目的采用多个云。</span><span class="sxs-lookup"><span data-stu-id="9faaa-105">Microsoft recognizes that customers are adopting multiple clouds for specific purposes.</span></span> <span data-ttu-id="9faaa-106">在这一过程中虚构的公司也不例外。</span><span class="sxs-lookup"><span data-stu-id="9faaa-106">The fictional company in this journey is no exception.</span></span> <span data-ttu-id="9faaa-107">在采用 Azure 的同时，该公司的成功导致其收购了一个互补的小型企业。</span><span class="sxs-lookup"><span data-stu-id="9faaa-107">In parallel to the Azure adoption journey, the business success has led to the acquisition of a small, but complementary business.</span></span> <span data-ttu-id="9faaa-108">该业务在不同的云服务提供商上运行其所有的 IT 操作。</span><span class="sxs-lookup"><span data-stu-id="9faaa-108">That business is running all of their IT operations on a different cloud provider.</span></span>

<span data-ttu-id="9faaa-109">本文介绍集成这家新组织时的事态变化。</span><span class="sxs-lookup"><span data-stu-id="9faaa-109">This article describes how things change when integrating the new organization.</span></span> <span data-ttu-id="9faaa-110">出于说明的目的，让我们假设该公司已完成此客户旅程中概述的每一项治理变革。</span><span class="sxs-lookup"><span data-stu-id="9faaa-110">For purposes of the narrative, we assume this company has completed each of the governance evolutions outlined in this customer journey.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="9faaa-111">当前状态的演变</span><span class="sxs-lookup"><span data-stu-id="9faaa-111">Evolution of the current state</span></span>

<span data-ttu-id="9faaa-112">在本说明的上一个阶段，该公司已经开始实施成本控制和成本监视，因为云支出已经成为了公司常规运营费用的一部分。</span><span class="sxs-lookup"><span data-stu-id="9faaa-112">In the previous phase of this narrative, the company had begun to implement cost controls and cost monitoring, as cloud spending becomes part of the company's regular operational expenses.</span></span>

<span data-ttu-id="9faaa-113">自此，一些事态已改变，将会影响到治理：</span><span class="sxs-lookup"><span data-stu-id="9faaa-113">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="9faaa-114">标识由 Active Directory 的本地实例控制。</span><span class="sxs-lookup"><span data-stu-id="9faaa-114">Identity is controlled by an on-premises instance of Active Directory.</span></span> <span data-ttu-id="9faaa-115">复制到 Azure Active Directory 有助于实现混合标识。</span><span class="sxs-lookup"><span data-stu-id="9faaa-115">Hybrid Identity is facilitated through replication to Azure Active Directory.</span></span>
- <span data-ttu-id="9faaa-116">IT 操作或云操作很大程度上由 Azure Monitor 和相关的自动化进行管理。</span><span class="sxs-lookup"><span data-stu-id="9faaa-116">IT Operations or Cloud Operations are largely managed by Azure Monitor and related automations.</span></span>
- <span data-ttu-id="9faaa-117">灾难恢复/业务连续性受 Azure Vault 实例控制。</span><span class="sxs-lookup"><span data-stu-id="9faaa-117">Disaster Recovery / Business Continuity is controlled by Azure Vault instances.</span></span>
- <span data-ttu-id="9faaa-118">Azure 安全中心用于监视安全违规和攻击。</span><span class="sxs-lookup"><span data-stu-id="9faaa-118">Azure Security Center is used to monitor security violations and attacks.</span></span>
- <span data-ttu-id="9faaa-119">Azure 安全中心和 Azure Monitor 都用于监视云管理。</span><span class="sxs-lookup"><span data-stu-id="9faaa-119">Azure Security Center and Azure Monitor are both used to monitor governance of the cloud.</span></span>
- <span data-ttu-id="9faaa-120">Azure 蓝图、Azure Policy 和管理组用于自动遵守策略。</span><span class="sxs-lookup"><span data-stu-id="9faaa-120">Azure Blueprints, Azure Policy, and management groups are used to automate compliance to policy.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="9faaa-121">未来状态的演变</span><span class="sxs-lookup"><span data-stu-id="9faaa-121">Evolution of the future state</span></span>

<span data-ttu-id="9faaa-122">目标是尽可能将收购公司整合到现有业务中。</span><span class="sxs-lookup"><span data-stu-id="9faaa-122">The goal is to integrate the acquisition company into existing operations wherever possible.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="9faaa-123">有形风险的演变</span><span class="sxs-lookup"><span data-stu-id="9faaa-123">Evolution of tangible risks</span></span>

<span data-ttu-id="9faaa-124">**业务购置成本**：收购新业务预计将在大约五年内实现盈利。</span><span class="sxs-lookup"><span data-stu-id="9faaa-124">**Business Acquisition Cost**: Acquisition of the new business is slated to be profitable in approximately five years.</span></span> <span data-ttu-id="9faaa-125">由于回报慢，董事会希望尽可能多地控制购置成本。</span><span class="sxs-lookup"><span data-stu-id="9faaa-125">Because of the slow rate of return, the board wants to control acquisition costs, as much as possible.</span></span> <span data-ttu-id="9faaa-126">成本控制和技术集成之间存在相互冲突的风险。</span><span class="sxs-lookup"><span data-stu-id="9faaa-126">There is a risk of cost control and technical integration conflicting with one another.</span></span>

<span data-ttu-id="9faaa-127">该业务风险可以扩展为几种技术风险</span><span class="sxs-lookup"><span data-stu-id="9faaa-127">This business risk can be expanded into a few technical risks</span></span>

- <span data-ttu-id="9faaa-128">云迁移有产生额外购置成本的风险。</span><span class="sxs-lookup"><span data-stu-id="9faaa-128">There is risk of cloud migration producing additional acquisition costs.</span></span>
- <span data-ttu-id="9faaa-129">此外，还有新环境未得到妥善管理或导致违反策略的风险。</span><span class="sxs-lookup"><span data-stu-id="9faaa-129">There is also a risk of the new environment not being properly governed or resulting in policy violations.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="9faaa-130">策略语句的演变</span><span class="sxs-lookup"><span data-stu-id="9faaa-130">Evolution of the policy statements</span></span>

<span data-ttu-id="9faaa-131">以下策略的更改将有助于缓解新的风险，同时有助于实施指南。</span><span class="sxs-lookup"><span data-stu-id="9faaa-131">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="9faaa-132">必须通过现有运营管理和安全监视工具来监视辅助云中的所有资产。</span><span class="sxs-lookup"><span data-stu-id="9faaa-132">All assets in a secondary cloud must be monitored through existing operational management and security monitoring tools.</span></span>
2. <span data-ttu-id="9faaa-133">所有组织单位必须集成到现有标识提供者中。</span><span class="sxs-lookup"><span data-stu-id="9faaa-133">All organizational units must be integrated into the existing identity provider.</span></span>
3. <span data-ttu-id="9faaa-134">主要标识提供者应管理对辅助云中的资产的身份验证。</span><span class="sxs-lookup"><span data-stu-id="9faaa-134">The primary identity provider should govern authentication to assets in the secondary cloud.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="9faaa-135">最佳做法的演变</span><span class="sxs-lookup"><span data-stu-id="9faaa-135">Evolution of the best practices</span></span>

<span data-ttu-id="9faaa-136">本文的这一部分将改进治理 MVP 设计，以包括新的 Azure 策略和 Azure 成本管理的实现。</span><span class="sxs-lookup"><span data-stu-id="9faaa-136">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="9faaa-137">这两项设计变更将共同实现新的公司策略语句。</span><span class="sxs-lookup"><span data-stu-id="9faaa-137">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="9faaa-138">连接网络 - 由网络和 IT 安全人员执行，通过治理提供支持</span><span class="sxs-lookup"><span data-stu-id="9faaa-138">Connect the networks - Executed by Networking and IT Security, supported by governance</span></span>
    1. <span data-ttu-id="9faaa-139">添加从 MPLS/租用线路提供商到新云的连接会集成网络。</span><span class="sxs-lookup"><span data-stu-id="9faaa-139">Adding a connection from the MPLS/Leased line provider to the new cloud will integrate networks.</span></span> <span data-ttu-id="9faaa-140">添加路由表和防火墙配置将控制环境之间的访问和流量。</span><span class="sxs-lookup"><span data-stu-id="9faaa-140">Adding routing tables and firewall configurations will control access and traffic between the environments.</span></span>
2. <span data-ttu-id="9faaa-141">合并标识提供者。</span><span class="sxs-lookup"><span data-stu-id="9faaa-141">Consolidate Identity Providers.</span></span> <span data-ttu-id="9faaa-142">根据托管在辅助云中的工作负荷，标识提供者的合并有多种选择。</span><span class="sxs-lookup"><span data-stu-id="9faaa-142">Depending on the workloads being hosted in the secondary cloud, there are a variety of options to identity provider consolidation.</span></span> <span data-ttu-id="9faaa-143">下面是几个示例：</span><span class="sxs-lookup"><span data-stu-id="9faaa-143">The following are a few examples:</span></span>
    1. <span data-ttu-id="9faaa-144">对于使用 OAuth 2 进行身份验证的应用程序，可轻松地将辅助云内 Active Directory 中的用户复制到现有 Azure AD 租户中。</span><span class="sxs-lookup"><span data-stu-id="9faaa-144">For applications that authenticate using OAuth 2, users in the Active Directory in the secondary cloud could simply be replicated to the existing Azure AD tenant.</span></span>
    2. <span data-ttu-id="9faaa-145">在其他极端情况下，两个本地标识提供者之间的联合允许将新 Active Directory 域中的用户复制到 Azure。</span><span class="sxs-lookup"><span data-stu-id="9faaa-145">On the other extreme, federation between the two on-premises identity providers, would allow users from the new Active Directory domains to be replicated to Azure.</span></span>
3. <span data-ttu-id="9faaa-146">将资产添加到 Azure Site Recovery</span><span class="sxs-lookup"><span data-stu-id="9faaa-146">Add assets to Azure Site Recovery</span></span>
    1. <span data-ttu-id="9faaa-147">Azure Site Recovery 从一开始就是作为混合/多云工具构建的。</span><span class="sxs-lookup"><span data-stu-id="9faaa-147">Azure Site Recovery was built as a hybrid/multi-cloud tool from the beginning.</span></span>
    2. <span data-ttu-id="9faaa-148">辅助云中的虚拟机可以由用于保护本地资产的同一个 Azure Site Recovery 进程进行保护。</span><span class="sxs-lookup"><span data-stu-id="9faaa-148">Virtual machines in the secondary cloud might be able to be protected by the same Azure Site Recovery processes used to protect on-premises assets.</span></span>
4. <span data-ttu-id="9faaa-149">将资产添加到 Azure 成本管理</span><span class="sxs-lookup"><span data-stu-id="9faaa-149">Add assets to Azure Cost Management</span></span>
    1. <span data-ttu-id="9faaa-150">Azure 成本管理从一开始就是作为多云工具构建的。</span><span class="sxs-lookup"><span data-stu-id="9faaa-150">Azure Cost Management was built as a multi-cloud tool from the beginning.</span></span>
    2. <span data-ttu-id="9faaa-151">辅助云中的虚拟机可能与某些云提供商的 Azure 成本管理兼容。</span><span class="sxs-lookup"><span data-stu-id="9faaa-151">Virtual machines in the secondary cloud might be compatible with Azure Cost Management for some cloud providers.</span></span> <span data-ttu-id="9faaa-152">可能会收取额外费用。</span><span class="sxs-lookup"><span data-stu-id="9faaa-152">Additional costs may apply.</span></span>
5. <span data-ttu-id="9faaa-153">将资产添加到 Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="9faaa-153">Add assets to Azure Monitor</span></span>
    1. <span data-ttu-id="9faaa-154">Azure Site Recovery 从一开始就是作为混合云工具构建的。</span><span class="sxs-lookup"><span data-stu-id="9faaa-154">Azure Monitor was built as a hybrid cloud tool from the beginning.</span></span>
    2. <span data-ttu-id="9faaa-155">辅助云中的虚拟机可能与 Azure Monitor 代理兼容，从而使它们可以包含在 Azure Monitor 中以进行操作监视。</span><span class="sxs-lookup"><span data-stu-id="9faaa-155">Virtual machines in the secondary cloud might be compatible with Azure Monitor agents, allowing them to be included in Azure Monitor for operational monitoring.</span></span>
6. <span data-ttu-id="9faaa-156">治理强制执行工具</span><span class="sxs-lookup"><span data-stu-id="9faaa-156">Governance enforcement tools</span></span>
    1. <span data-ttu-id="9faaa-157">治理强制执行特定于云。</span><span class="sxs-lookup"><span data-stu-id="9faaa-157">Governance enforcement is cloud-specific.</span></span>
    2. <span data-ttu-id="9faaa-158">而在治理过程中建立的公司策略不特定于云。</span><span class="sxs-lookup"><span data-stu-id="9faaa-158">The corporate policies established in the governance journey are not.</span></span> <span data-ttu-id="9faaa-159">尽管云和云之间的实现不尽相同，但策略声明可以应用于辅助提供程序。</span><span class="sxs-lookup"><span data-stu-id="9faaa-159">While the implementation may vary from cloud to cloud, the policy statements can be applied to the secondary provider.</span></span>

<span data-ttu-id="9faaa-160">随着多云采用的增长，上述设计演变将继续，变得成熟。</span><span class="sxs-lookup"><span data-stu-id="9faaa-160">As multi-cloud adoption grows, the design evolution above will continue to mature.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9faaa-161">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9faaa-161">Next steps</span></span>

<span data-ttu-id="9faaa-162">在许多大型企业，云监管五个领域可能会阻塞程序的采用。</span><span class="sxs-lookup"><span data-stu-id="9faaa-162">In many large enterprises, the Five Disciplines of Cloud Governance can be blockers to adoption.</span></span> <span data-ttu-id="9faaa-163">下一步的项目将使管理团队运动一些其他的想法以帮助确保在云中的长期成功。</span><span class="sxs-lookup"><span data-stu-id="9faaa-163">The next article has some additional thoughts on making governance a team sport to help ensure long-term success in the cloud.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9faaa-164">治理的多个层面</span><span class="sxs-lookup"><span data-stu-id="9faaa-164">Multiple layers of governance</span></span>](./multiple-layers-of-governance.md)
