---
title: CAF：标识基线示例策略语句
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 标识基线示例策略语句
author: BrianBlanchard
ms.openlocfilehash: 5fad9265b9c048ee502c7e084ddd03faa0ad3e23
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900634"
---
# <a name="identity-baseline-sample-policy-statements"></a><span data-ttu-id="0d666-103">标识基线示例策略语句</span><span class="sxs-lookup"><span data-stu-id="0d666-103">Identity Baseline sample policy statements</span></span>

<span data-ttu-id="0d666-104">各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。</span><span class="sxs-lookup"><span data-stu-id="0d666-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="0d666-105">这些语句应提供简要的风险摘要，以及应对它们的计划。</span><span class="sxs-lookup"><span data-stu-id="0d666-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="0d666-106">每个语句定义应包括以下这些信息：</span><span class="sxs-lookup"><span data-stu-id="0d666-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="0d666-107">技术风险 - 此策略将解决的风险的摘要。</span><span class="sxs-lookup"><span data-stu-id="0d666-107">Technical risk - A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="0d666-108">策略语句 - 策略要求的清楚摘要说明。</span><span class="sxs-lookup"><span data-stu-id="0d666-108">Policy statement - A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="0d666-109">设计选项 - IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指导。</span><span class="sxs-lookup"><span data-stu-id="0d666-109">Design options - Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="0d666-110">以下示例策略语句解决一些常见的身份相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的策略语句时进行参考。</span><span class="sxs-lookup"><span data-stu-id="0d666-110">The following sample policy statements address a number of common identity-related business risks, and are provided as examples for you to reference when drafting policy statements to address your own organization's needs.</span></span> <span data-ttu-id="0d666-111">这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何特定的风险。</span><span class="sxs-lookup"><span data-stu-id="0d666-111">These examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any particular risk.</span></span> <span data-ttu-id="0d666-112">与业务和 IT 团队密切合作来确定适合于独特风险集的最佳策略解决方案。</span><span class="sxs-lookup"><span data-stu-id="0d666-112">Work closely with business and IT teams to identify the best policy solutions for your unique set of risks.</span></span>

## <a name="lack-of-access-controls"></a><span data-ttu-id="0d666-113">缺少访问控制</span><span class="sxs-lookup"><span data-stu-id="0d666-113">Lack of access controls</span></span>

<span data-ttu-id="0d666-114">**技术风险**：不足或临时的访问控制设置可能会引入对敏感或任务关键型资源进行未经授权的访问的风险。</span><span class="sxs-lookup"><span data-stu-id="0d666-114">**Technical risk**: Insufficient or ad-hoc access control settings can introduce risk of unauthorized access to sensitive or mission-critical resources.</span></span>

<span data-ttu-id="0d666-115">**策略语句**：应使用由当前治理策略批准的标识和角色来控制部署到云的所有资产。</span><span class="sxs-lookup"><span data-stu-id="0d666-115">**Policy statement**: All assets deployed to the cloud should be controlled using identities and roles approved by current governance policies.</span></span>

<span data-ttu-id="0d666-116">**潜在的设计选项**：[Azure Active Directory 条件访问](/azure/active-directory/conditional-access/overview)是 Azure 中的默认访问控制机制。</span><span class="sxs-lookup"><span data-stu-id="0d666-116">**Potential design options**: [Azure Active Directory conditional access](/azure/active-directory/conditional-access/overview) is the default access control mechanism in Azure.</span></span>

## <a name="overprovisioned-access"></a><span data-ttu-id="0d666-117">预配过度的访问</span><span class="sxs-lookup"><span data-stu-id="0d666-117">Overprovisioned access</span></span>

<span data-ttu-id="0d666-118">**技术风险**：对其职责范围之外的资源进行控制的用户和组可能会导致未经授权的修改，从而形成中断或安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="0d666-118">**Technical risk**: Users and groups with control over resources beyond their area of responsibility can result in unauthorized modifications leading to outages or security vulnerabilities.</span></span>

<span data-ttu-id="0d666-119">**策略语句**：将实施以下策略：</span><span class="sxs-lookup"><span data-stu-id="0d666-119">**Policy statement**: The following policies will be implemented:</span></span>

- <span data-ttu-id="0d666-120">最小特权访问模型将应用于任务关键型应用程序或受保护数据中所涉及的任何资源。</span><span class="sxs-lookup"><span data-stu-id="0d666-120">A least privilege access model will be applied to any resources involved in mission-critical applications or protected data.</span></span>
- <span data-ttu-id="0d666-121">提升的权限应是例外情况，云治理团队必须记录任何这类例外情况。</span><span class="sxs-lookup"><span data-stu-id="0d666-121">Elevated permissions should be an exception, and any such exceptions must be recorded with the Cloud Governance team.</span></span> <span data-ttu-id="0d666-122">将定期审核例外情况。</span><span class="sxs-lookup"><span data-stu-id="0d666-122">Exceptions will be audited regularly.</span></span>

<span data-ttu-id="0d666-123">**潜在的设计选项**：请查阅 [Azure 标识管理最佳做法](/azure/security/azure-security-identity-management-best-practices)以实现基于角色的访问控制 (RBAC) 策略，该策略基于[了解需求](https://wikipedia.org/wiki/Need_to_know)和[最小特权安全](https://wikipedia.org/wiki/Principle_of_least_privilege)原则来限制访问。</span><span class="sxs-lookup"><span data-stu-id="0d666-123">**Potential design options**: Consult the [Azure Identity Management best practices](/azure/security/azure-security-identity-management-best-practices) to implement a role-based access control (RBAC) strategy that restricts access based on the [need to know](https://wikipedia.org/wiki/Need_to_know) and [least privilege security](https://wikipedia.org/wiki/Principle_of_least_privilege) principles.</span></span>

## <a name="lack-of-shared-management-accounts-between-on-premises-and-the-cloud"></a><span data-ttu-id="0d666-124">本地与云之间缺少共享管理帐户</span><span class="sxs-lookup"><span data-stu-id="0d666-124">Lack of shared management accounts between on-premises and the cloud</span></span>

<span data-ttu-id="0d666-125">**技术风险**：使用本地 Active Directory 上的帐户的 IT 管理层或管理人员可能没有足够权限来访问云资源，可能无法高效地解决运营或安全问题。</span><span class="sxs-lookup"><span data-stu-id="0d666-125">**Technical risk**: IT management or administrative staff with accounts on your on-premises Active Directory may not have sufficient access to cloud resources may not be able to efficiently resolve operational or security issues.</span></span>

<span data-ttu-id="0d666-126">**策略语句**：本地 Active Directory 基础结构中具有提升的特权的所有组都应映射到已批准的 RBAC 角色。</span><span class="sxs-lookup"><span data-stu-id="0d666-126">**Policy statement**: All groups in the on-premises Active Directory infrastructure that have elevated privileges should be mapped to an approved RBAC role.</span></span>

<span data-ttu-id="0d666-127">**潜在的设计选项**：在基于云的 Azure Active Directory 与本地 Active Directory 之间实现混合标识解决方案，并将所需本地组添加到完成其工作所需的 RBAC 角色。</span><span class="sxs-lookup"><span data-stu-id="0d666-127">**Potential design options**: Implement a hybrid identity solution between your cloud-based Azure Active Directory and your on-premise Active Directory, and add the required on-premises groups to the RBAC roles necessary to do their work.</span></span>

## <a name="weak-authentication-mechanisms"></a><span data-ttu-id="0d666-128">弱身份验证机制</span><span class="sxs-lookup"><span data-stu-id="0d666-128">Weak authentication mechanisms</span></span>

<span data-ttu-id="0d666-129">**技术风险**：缺乏足够安全的用户身份验证方法（如基本用户/密码组合）的标识管理系统可能会导致密码泄露或受到攻击，从而形成对安全云系统进行未经授权的访问的重大风险。</span><span class="sxs-lookup"><span data-stu-id="0d666-129">**Technical risk**: Identity management systems with insufficiently secure user authentication methods, such as basic user/password combinations, can lead to compromised or hacked passwords, providing a major risk of unauthorized access to secure cloud systems.</span></span>

<span data-ttu-id="0d666-130">**策略语句**：所有帐户都需要使用多重身份验证 (MFA) 方法登录安全资源。</span><span class="sxs-lookup"><span data-stu-id="0d666-130">**Policy statement**: All accounts are required to login to secured resources using a multi-factor authentication (MFA) method.</span></span>

<span data-ttu-id="0d666-131">**潜在的设计选项**：对于 Azure Active Directory，实现 [Azure 多重身份验证](/azure/active-directory/authentication/concept-mfa-howitworks)用户授权过程的一部分。</span><span class="sxs-lookup"><span data-stu-id="0d666-131">**Potential design options**: For Azure Active Directory, implement [Azure Multi-Factor Authentication](/azure/active-directory/authentication/concept-mfa-howitworks) as part of your user authorization process.</span></span>

## <a name="isolated-identity-providers"></a><span data-ttu-id="0d666-132">隔离的标识提供者</span><span class="sxs-lookup"><span data-stu-id="0d666-132">Isolated identity providers</span></span>

<span data-ttu-id="0d666-133">**技术风险**：不兼容的标识提供者可能会导致无法与客户或其他业务合作伙伴共享资源或服务。</span><span class="sxs-lookup"><span data-stu-id="0d666-133">**Technical risk**: Incompatible identity providers can result in the inability to share resources or services with customers or other business partners.</span></span>

<span data-ttu-id="0d666-134">**策略语句**：任何需要客户身份验证的应用程序的部署都必须使用与内部用户的主标识提供者兼容的已批准的标识提供者。</span><span class="sxs-lookup"><span data-stu-id="0d666-134">**Policy statement**: Deployment of any applications that require customer authentication must use an approved identity provider that is compatible with the primary identity provider for internal users.</span></span>

<span data-ttu-id="0d666-135">**潜在的设计选项**：在内部与客户标识提供者之间实现[与 Azure Active Directory 的联合](/azure/active-directory/hybrid/whatis-fed)。</span><span class="sxs-lookup"><span data-stu-id="0d666-135">**Potential design options**: Implement [Federation with Azure Active Directory](/azure/active-directory/hybrid/whatis-fed) between your internal and customer identity providers.</span></span>

## <a name="identity-reviews"></a><span data-ttu-id="0d666-136">标识评审</span><span class="sxs-lookup"><span data-stu-id="0d666-136">Identity reviews</span></span>

<span data-ttu-id="0d666-137">**技术风险**：随着时间推移，业务变化、新的云部署的添加或其他安全问题可能会增加对安全资源进行未经授权的访问的风险。</span><span class="sxs-lookup"><span data-stu-id="0d666-137">**Technical risk**: Over time business change, the addition of new cloud deployments or other security concerns can increase the risks of unauthorized access to secure resources.</span></span>

<span data-ttu-id="0d666-138">**策略语句**：云治理过程必须包括标识管理团队的季度评审，以标识云资产配置应该阻止的恶意执行组件或使用模式。</span><span class="sxs-lookup"><span data-stu-id="0d666-138">**Policy statement**: Cloud Governance processes must include quarterly review with identity management teams to identify malicious actors or usage patterns that should be prevented by cloud asset configuration.</span></span>

<span data-ttu-id="0d666-139">**潜在的设计选项**：确立季度安全评审会议，包括负责管理标识服务的治理团队成员和 IT 人员。</span><span class="sxs-lookup"><span data-stu-id="0d666-139">**Potential design options**: Establish a quarterly security review meeting that includes both governance team members and IT staff responsible for managing identity services.</span></span> <span data-ttu-id="0d666-140">评审现有安全数据和指标以发现当前标识管理策略和工具中的缺口，并更新策略以缓解任何新风险。</span><span class="sxs-lookup"><span data-stu-id="0d666-140">Review existing security data and metrics to establish gaps in current identity management policy and tooling, and update policy to mitigate any new risks.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0d666-141">后续步骤</span><span class="sxs-lookup"><span data-stu-id="0d666-141">Next steps</span></span>

<span data-ttu-id="0d666-142">使用本文中所述的示例作为起点，来制定解决特定业务风险并与云采用计划保持一致的策略。</span><span class="sxs-lookup"><span data-stu-id="0d666-142">Use the samples mentioned in this article as a starting point to develop policies that address specific business risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="0d666-143">若要开始制定与标识基线相关的自己的自定义策略语句，请下载[标识基线模板](template.md)。</span><span class="sxs-lookup"><span data-stu-id="0d666-143">To begin developing your own custom policy statements related to Identity Baseline, download the [Identity Baseline template](template.md).</span></span>

<span data-ttu-id="0d666-144">若要加速此规则的采用，请选择与你的环境最密切符合的[可操作的治理过程](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="0d666-144">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="0d666-145">随后修改设计以整合特定公司策略决策。</span><span class="sxs-lookup"><span data-stu-id="0d666-145">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0d666-146">可操作的治理过程</span><span class="sxs-lookup"><span data-stu-id="0d666-146">Actionable Governance Journeys</span></span>](../journeys/overview.md)