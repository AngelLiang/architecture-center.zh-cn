---
title: CAF：大型企业 – 标识基线演变
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企业 – 标识基线演变
author: BrianBlanchard
ms.openlocfilehash: efda14819bfbc70632dc9bb8b4c6c5aca96004c0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900523"
---
# <a name="large-enterprise-identity-baseline-evolution"></a><span data-ttu-id="a6c8e-103">大型企业：标识基线演变</span><span class="sxs-lookup"><span data-stu-id="a6c8e-103">Large enterprise: Identity Baseline evolution</span></span>

<span data-ttu-id="a6c8e-104">本文通过将标识基线控制添加到治理 MVP 中，对叙述性内容进行了演化。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-104">This article evolves the narrative by adding Identity Baseline controls to the governance MVP.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="a6c8e-105">叙述性内容的演变</span><span class="sxs-lookup"><span data-stu-id="a6c8e-105">Evolution of the narrative</span></span>

<span data-ttu-id="a6c8e-106">CFO 批准了两个数据中心云迁移的业务理由。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-106">The business justification for the cloud migration of the two datacenters was approved by the CFO.</span></span> <span data-ttu-id="a6c8e-107">在技术可行性研究中，发现了几个障碍：</span><span class="sxs-lookup"><span data-stu-id="a6c8e-107">During the technical feasibility study, several roadblocks were discovered:</span></span>

- <span data-ttu-id="a6c8e-108">受保护数据和任务关键型应用程序占两个数据中心 25% 的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-108">Protected data and mission-critical applications represent 25% of the workloads in the two datacenters.</span></span> <span data-ttu-id="a6c8e-109">在实现 PII 和任务关键型应用程序相关当前治理策略的现代化之前，无法消除这两项工作负荷。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-109">Neither can be eliminated until the current governance policies regarding PII and mission-critical applications have been modernized.</span></span>
- <span data-ttu-id="a6c8e-110">这两个数据中心内 7% 的资产与云并不兼容。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-110">7% of the assets in those datacenters are not cloud-compatible.</span></span> <span data-ttu-id="a6c8e-111">在终止数据中心协定之前，它们将被移动到备用数据中心。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-111">They will be moved to an alternate datacenter before termination of the datacenter contract.</span></span>
- <span data-ttu-id="a6c8e-112">数据中心内 15% 的资产（750 个虚拟机）依赖于旧身份验证或第三方多重身份验证。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-112">15% of the assets in the datacenter (750 virtual machines) have a dependency on legacy authentication or third-party multi-factor authentication.</span></span>
- <span data-ttu-id="a6c8e-113">连接现有数据中心和 Azure 的 VPN 连接无法提供足够的数据传输速度或延迟，也就无法为停用数据中心在两年的限期内迁移所有资产。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-113">The VPN connection that connects existing datacenters and Azure does not offer sufficient data transmission speeds or latency to migrate the volume of assets within the two-year timeline to retire the datacenter.</span></span>

<span data-ttu-id="a6c8e-114">前两个障碍都已得到解决。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-114">The first two roadblocks are being mitigated in parallel.</span></span> <span data-ttu-id="a6c8e-115">本文将讨论针对后两个障碍的解决方案。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-115">This article will address the resolution of the third and fourth roadblocks.</span></span>

### <a name="evolution-of-the-cloud-governance-team"></a><span data-ttu-id="a6c8e-116">云治理团队的演变</span><span class="sxs-lookup"><span data-stu-id="a6c8e-116">Evolution of the Cloud Governance team</span></span>

<span data-ttu-id="a6c8e-117">云治理团队在发展壮大。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-117">The Cloud Governance team is expanding.</span></span> <span data-ttu-id="a6c8e-118">鉴于在标识管理方面需要额外的支持，现在标识基线团队的系统管理员会参加周会，便于现有团队成员了解各项变化。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-118">Given the need for additional support regarding identity management, a systems administrator from the Identity Baseline team now participates in a weekly meeting to keep the existing team members aware of changes.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="a6c8e-119">当前状态的演变</span><span class="sxs-lookup"><span data-stu-id="a6c8e-119">Evolution of the current state</span></span>

<span data-ttu-id="a6c8e-120">IT 团队已获得批准，准备按照 CIO 和 CFO 的计划停用两个数据中心。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-120">The IT team has approval to move forward with the CIO and CFO's plans to retire two datacenters.</span></span> <span data-ttu-id="a6c8e-121">但却担心必须将这两个数据中心内 750 项 (15%) 资产移动到除云之外的某个位置。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-121">However, IT is concerned that 750 (15%) of the assets in those datacenters will have to be moved somewhere other than the cloud.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="a6c8e-122">未来状态的演变</span><span class="sxs-lookup"><span data-stu-id="a6c8e-122">Evolution of the future state</span></span>

<span data-ttu-id="a6c8e-123">新的未来状态计划需要一个更稳健的标识基线解决方案，用于迁移要求使用旧身份验证的 750 个虚拟机。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-123">The new future state plans require a more robust Identity Baseline solution to migrate the 750 virtual machines with legacy authentication requirements.</span></span> <span data-ttu-id="a6c8e-124">除了这两个数据中心之外，其他数据中心内占差不多比例的资产预计也会受到这一难题的影响。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-124">Beyond these two datacenters, similar percentages of assets in other datacenters are expected to be affected by this challenge.</span></span>
<span data-ttu-id="a6c8e-125">未来状态现在还需要从云提供商连接到公司的 MPLS/租用线解决方案。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-125">The future state now also requires a connection from the cloud provider to the company’s MPLS/leased-line solution.</span></span>

<span data-ttu-id="a6c8e-126">当前和未来状态的变化揭示了新的风险，需要新的策略声明。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-126">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="a6c8e-127">有形风险的演变</span><span class="sxs-lookup"><span data-stu-id="a6c8e-127">Evolution of tangible risks</span></span>

<span data-ttu-id="a6c8e-128">**迁移期间业务中断**。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-128">**Business interruption during migration**.</span></span> <span data-ttu-id="a6c8e-129">迁移到云会产生有时限的可控风险，这样的风险是可管理的。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-129">Migration to the cloud creates a controlled, time-bound risk that can be managed.</span></span> <span data-ttu-id="a6c8e-130">迁移老化硬件的风险要高得多。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-130">Moving aging hardware to another part of the world is much higher risk.</span></span> <span data-ttu-id="a6c8e-131">需要通过风险缓解策略避免业务运营的中断。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-131">A mitigation strategy is needed to avoid interruptions to business operations.</span></span>

<span data-ttu-id="a6c8e-132">**现有标识依赖关系**。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-132">**Existing identity dependencies**.</span></span> <span data-ttu-id="a6c8e-133">对现有身份验证和标识服务的依赖可能会延迟或阻止某些工作负荷迁移到云。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-133">Dependencies on existing authentication and identity services may delay or prevent the migration of some workloads to the cloud.</span></span> <span data-ttu-id="a6c8e-134">未能按时返还两个数据中心会在数据中心租用中产生数百美元的费用。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-134">Failure to return the two datacenters on time will incur millions of dollars in datacenter lease fees.</span></span>

<span data-ttu-id="a6c8e-135">此业务风险可以扩展为几种技术风险：</span><span class="sxs-lookup"><span data-stu-id="a6c8e-135">This business risk can be expanded into a few technical risks:</span></span>

- <span data-ttu-id="a6c8e-136">旧身份验证在云中可能无法使用，这会限制某些应用程序的部署。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-136">Legacy authentication might not be available in the cloud, limiting deployment of some applications.</span></span>
- <span data-ttu-id="a6c8e-137">目前的第三方 MFA 解决方案在云中可能无法使用，这会限制某些应用程序的部署。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-137">The current third-party MFA solution might not be available in the cloud, limiting deployment of some applications.</span></span>
- <span data-ttu-id="a6c8e-138">重新调整或移动可能会造成中断并增加成本。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-138">Retooling or moving either could create outages and add costs.</span></span>
- <span data-ttu-id="a6c8e-139">VPN 的速度和稳定性可能会影响迁移。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-139">The speed and stability of the VPN might impede migration.</span></span>
- <span data-ttu-id="a6c8e-140">传入云的流量可能会导致全球网络的其他部分出现安全问题。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-140">Traffic coming into the cloud could cause security issues in other parts of the global network.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="a6c8e-141">策略语句的演变</span><span class="sxs-lookup"><span data-stu-id="a6c8e-141">Evolution of the policy statements</span></span>

<span data-ttu-id="a6c8e-142">以下策略的更改将有助于缓解新的风险，同时有助于实施指南。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-142">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="a6c8e-143">选择的云提供商必须提供一种通过旧方法进行身份验证的方式。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-143">The chosen cloud provider must offer a means of authenticating via legacy methods.</span></span>
2. <span data-ttu-id="a6c8e-144">选择的云提供商必须提供一种使用目前的第三方 MFA 解决方案进行身份验证的方式。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-144">The chosen cloud provider must offer a means of authentication with the current third-party MFA solution.</span></span>
3. <span data-ttu-id="a6c8e-145">应在云提供商和公司的电信提供商之间建立高速专用连接，将云提供商连接到全球数据中心网络。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-145">A high-speed private connection should be established between the cloud provider and the company’s telco provider, connecting the cloud provider to the global network of datacenters.</span></span>
4. <span data-ttu-id="a6c8e-146">在制定稳妥的安全要求之前，入站公共流量不得访问云中托管的公司资产。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-146">Until sufficient security requirements are established, no inbound public traffic may access company assets hosted in the cloud.</span></span> <span data-ttu-id="a6c8e-147">所有端口都阻止来自全局 WAN 之外的任何源。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-147">All ports are blocked from any source outside of the global WAN.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="a6c8e-148">最佳做法的演变</span><span class="sxs-lookup"><span data-stu-id="a6c8e-148">Evolution of the best practices</span></span>

<span data-ttu-id="a6c8e-149">治理 MVP 设计将不断演进以包括新的 Azure 策略和虚拟上 Active Directory 的实现。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-149">The governance MVP design evolves to include new Azure policies and an implementation of Active Directory on a virtual machine.</span></span> <span data-ttu-id="a6c8e-150">这两项设计变更共同实现新的企业策略声明。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-150">Together, these two design changes fulfill the new corporate policy statements.</span></span>

<span data-ttu-id="a6c8e-151">以下是新的最佳做法：</span><span class="sxs-lookup"><span data-stu-id="a6c8e-151">Here are the new best practices:</span></span>

1. <span data-ttu-id="a6c8e-152">DMZ 蓝图：应将本地 DMZ 配置为允许以下解决方案与本地 Active Directory 服务器通信。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-152">DMZ blueprint: The on-premises side of the DMZ should be configured to allow communication between the following solution and the on-premises Active Directory servers.</span></span> <span data-ttu-id="a6c8e-153">本最佳做法需要 DMZ 才能跨网络边界启用 Active Directory 域服务。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-153">This best practice requires a DMZ to enable Active Directory Domain Services across network boundaries.</span></span>
2. <span data-ttu-id="a6c8e-154">Azure 资源管理器模板：</span><span class="sxs-lookup"><span data-stu-id="a6c8e-154">Azure Resource Manager templates:</span></span>
    1. <span data-ttu-id="a6c8e-155">定义一个 NSG，用于阻止外部流量并将内部流量列入白名单。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-155">Define an NSG to block external traffic and whitelist internal traffic.</span></span>
    1. <span data-ttu-id="a6c8e-156">基于黄金映像在负载平衡对中部署两个 AD 虚拟机。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-156">Deploy two AD virtual machines in a load balanced pair based on a golden image.</span></span> <span data-ttu-id="a6c8e-157">首次启动时，该映像运行 PowerShell 脚本以加入域并注册域服务。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-157">On first boot, that image runs a PowerShell script to join the domain and register with domain services.</span></span> <span data-ttu-id="a6c8e-158">有关详细信息，请参阅[将 Active Directory 域服务 (AD DS) 扩展到 Azure](../../../../reference-architectures/identity/adds-extend-domain.md)。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-158">For more information, see [Extend Active Directory Domain Services (AD DS) to Azure](../../../../reference-architectures/identity/adds-extend-domain.md).</span></span>
3. <span data-ttu-id="a6c8e-159">Azure Policy：将 NSG 应用于所有资源。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-159">Azure Policy: Apply the NSG to all resources.</span></span>
4. <span data-ttu-id="a6c8e-160">Azure 蓝图</span><span class="sxs-lookup"><span data-stu-id="a6c8e-160">Azure blueprint</span></span>
    1. <span data-ttu-id="a6c8e-161">创建名为 `active-directory-virtual-machines` 的蓝图。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-161">Create a blueprint named `active-directory-virtual-machines`.</span></span>
    1. <span data-ttu-id="a6c8e-162">将每个 AD 模板和策略添加到蓝图。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-162">Add each of the AD templates and policies to the blueprint.</span></span>
    1. <span data-ttu-id="a6c8e-163">将蓝图发布到任何适用的管理组。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-163">Publish the blueprint to any applicable management group.</span></span>
    1. <span data-ttu-id="a6c8e-164">将蓝图应用于任何需要旧身份验证或第三方 MFA 身份验证的订阅。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-164">Apply the blueprint to any subscription requiring legacy or third-party MFA authentication.</span></span>
    1. <span data-ttu-id="a6c8e-165">现可将在 Azure 中运行的 AD 实例用作本地 AD 解决方案的扩展，使它能与现有 MFA 工具集成并通过现有的 Active Directory 功能提供基于声明的身份验证。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-165">The instance of AD running in Azure can now be used as an extension of the on-premises AD solution, allowing it to integrate with the existing MFA tool and provide claims-based authentication, both through existing Active Directory functionality.</span></span>

## <a name="conclusion"></a><span data-ttu-id="a6c8e-166">结束语</span><span class="sxs-lookup"><span data-stu-id="a6c8e-166">Conclusion</span></span>

<span data-ttu-id="a6c8e-167">将这些更改添加到治理 MVP 有助于降低本文提到的许多风险，使每个云采用团队能快速跨越此障碍。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-167">Adding these changes to the governance MVP helps mitigate many of the risks in this article, allowing each cloud adoption team to quickly move past this roadblock.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a6c8e-168">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a6c8e-168">Next steps</span></span>

<span data-ttu-id="a6c8e-169">随着云采用的演进和业务附加值的提升，风险和云治理需求也在不断发展。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-169">As cloud adoption evolves and delivers additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="a6c8e-170">以下是一些可能会发生的演变。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-170">The following are a few evolutions that may occur.</span></span> <span data-ttu-id="a6c8e-171">对于本过程中的虚构公司而言，下一个触发器是云采用计划中包含受保护数据。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-171">For the fictional company in this journey, the next trigger is the inclusion of protected data in the cloud adoption plan.</span></span> <span data-ttu-id="a6c8e-172">此更改将要求其他安全控制措施。</span><span class="sxs-lookup"><span data-stu-id="a6c8e-172">This change will require additional security controls.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="a6c8e-173">安全基线演变</span><span class="sxs-lookup"><span data-stu-id="a6c8e-173">Security Baseline evolution</span></span>](./security-baseline-evolution.md)
