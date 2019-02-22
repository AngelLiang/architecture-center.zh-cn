---
title: CAF：安全基线示例策略语句
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 安全基线示例策略语句
author: BrianBlanchard
ms.openlocfilehash: ac40e022f8dff0c3021c04cd9d6ae42d28afaf1f
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900484"
---
# <a name="security-baseline-sample-policy-statements"></a><span data-ttu-id="d99fe-103">安全基线示例策略语句</span><span class="sxs-lookup"><span data-stu-id="d99fe-103">Security Baseline sample policy statements</span></span>

<span data-ttu-id="d99fe-104">各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。</span><span class="sxs-lookup"><span data-stu-id="d99fe-104">Individual cloud policy statements are guidelines for addressing specific risks identified during your risk assessment process.</span></span> <span data-ttu-id="d99fe-105">这些语句应提供简要的风险摘要，以及应对它们的计划。</span><span class="sxs-lookup"><span data-stu-id="d99fe-105">These statements should provide a concise summary of risks and plans to deal with them.</span></span> <span data-ttu-id="d99fe-106">每个语句定义应包括以下这些信息：</span><span class="sxs-lookup"><span data-stu-id="d99fe-106">Each statement definition should include these pieces of information:</span></span>

- <span data-ttu-id="d99fe-107">技术风险 - 此策略将解决的风险的摘要。</span><span class="sxs-lookup"><span data-stu-id="d99fe-107">Technical risk - A summary of the risk this policy will address.</span></span>
- <span data-ttu-id="d99fe-108">策略语句 - 策略要求的清楚摘要说明。</span><span class="sxs-lookup"><span data-stu-id="d99fe-108">Policy statement - A clear summary explanation of the policy requirements.</span></span>
- <span data-ttu-id="d99fe-109">技术选项 - IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指导。</span><span class="sxs-lookup"><span data-stu-id="d99fe-109">Technical options - Actionable recommendations, specifications, or other guidance that IT teams and developers can use when implementing the policy.</span></span>

<span data-ttu-id="d99fe-110">以下示例策略语句解决一些常见的安全相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的实际策略语句时进行参考。</span><span class="sxs-lookup"><span data-stu-id="d99fe-110">The following sample policy statements address a number of common security-related business risks, and are provided as examples for you to reference when drafting actual policy statements addressing your own organization's needs.</span></span> <span data-ttu-id="d99fe-111">这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何单个确定的风险。</span><span class="sxs-lookup"><span data-stu-id="d99fe-111">These examples are not meant to be proscriptive, and there are potentially several policy options for dealing with any single identified risk.</span></span> <span data-ttu-id="d99fe-112">与业务、安全和 IT 团队密切合作来确定适合于特定安全风险的最佳策略解决方案。</span><span class="sxs-lookup"><span data-stu-id="d99fe-112">Work closely with business, security, and IT teams to identify the best policy solutions for your particular security risks.</span></span>  

## <a name="asset-classification"></a><span data-ttu-id="d99fe-113">资产分类</span><span class="sxs-lookup"><span data-stu-id="d99fe-113">Asset classification</span></span>

<span data-ttu-id="d99fe-114">**技术风险**：未正确标识为任务关键型或涉及敏感数据的资产可能不会受到足够保护，从而导致潜在的数据泄露或业务中断。</span><span class="sxs-lookup"><span data-stu-id="d99fe-114">**Technical risk**: Assets that are not correctly identified as mission-critical or involving sensitive data may not receive sufficient protections, leading to potential data leaks or business disruptions.</span></span>

<span data-ttu-id="d99fe-115">**策略语句**：所有已部署的资产必须按照重要程度和数据分类进行分类。</span><span class="sxs-lookup"><span data-stu-id="d99fe-115">**Policy statement**: All deployed assets must be categorized by criticality and data classification.</span></span> <span data-ttu-id="d99fe-116">在部署到云之前，云治理团队和应用程序所有者必须对分类进行评审。</span><span class="sxs-lookup"><span data-stu-id="d99fe-116">Classifications must be reviewed by the Cloud Governance team and the application owner before deployment to the cloud.</span></span>

<span data-ttu-id="d99fe-117">**潜在的设计选项**：建立[资源标记标准](../../decision-guides/resource-tagging/overview.md)，并确保 IT 人员使用 [Azure 资源标记](/azure/azure-resource-manager/resource-group-using-tags)将它们一致地应用于任何部署的资源。</span><span class="sxs-lookup"><span data-stu-id="d99fe-117">**Potential design option**: Establish [resource tagging standards](../../decision-guides/resource-tagging/overview.md) and ensure IT staff apply them consistently to any deployed resources using [Azure resource tags](/azure/azure-resource-manager/resource-group-using-tags).</span></span>

## <a name="data-encryption"></a><span data-ttu-id="d99fe-118">数据加密</span><span class="sxs-lookup"><span data-stu-id="d99fe-118">Data encryption</span></span>

<span data-ttu-id="d99fe-119">**技术风险**：在存储过程存在泄漏受保护数据的风险。</span><span class="sxs-lookup"><span data-stu-id="d99fe-119">**Technical risk**: There is a risk of protected data being exposed during storage.</span></span>

<span data-ttu-id="d99fe-120">**策略语句**：所有受保护的数据都必须在静态时加密。</span><span class="sxs-lookup"><span data-stu-id="d99fe-120">**Policy statement**: All protected data must be encrypted when at rest.</span></span>

<span data-ttu-id="d99fe-121">**潜在的设计选项**：请参阅 [Azure 加密概述](/azure/security/security-azure-encryption-overview)一文，以了解有关如何在 Azure 平台上执行静态数据加密的讨论。</span><span class="sxs-lookup"><span data-stu-id="d99fe-121">**Potential design option**: See the [Azure encryption overview](/azure/security/security-azure-encryption-overview) article for a discussion of how data at rest encryption is performed on the Azure platform.</span></span>  

## <a name="network-isolation"></a><span data-ttu-id="d99fe-122">网络隔离</span><span class="sxs-lookup"><span data-stu-id="d99fe-122">Network isolation</span></span>

<span data-ttu-id="d99fe-123">**技术风险**：网络和网络中的子网之间的连接引入了潜在漏洞，可能会导致数据泄露或任务关键型服务中断。</span><span class="sxs-lookup"><span data-stu-id="d99fe-123">**Technical risk**: Connectivity between networks and subnets within networks introduces potential vulnerabilities that can result in data leaks or disruption of mission-critical services.</span></span>

<span data-ttu-id="d99fe-124">**策略语句**：包含受保护数据的网络子网必须独立于任何其他子网。</span><span class="sxs-lookup"><span data-stu-id="d99fe-124">**Policy statement**: Network subnets containing protected data must be isolated from any other subnets.</span></span> <span data-ttu-id="d99fe-125">将定期审核受保护数据子网之间的网络流量。</span><span class="sxs-lookup"><span data-stu-id="d99fe-125">Network traffic between protected data subnets is to be audited regularly.</span></span>

<span data-ttu-id="d99fe-126">**潜在的设计选项**：在 Azure 中，通过 [Azure 虚拟网络](/azure/virtual-network/virtual-networks-overview)管理网络和子网隔离。</span><span class="sxs-lookup"><span data-stu-id="d99fe-126">**Potential design option**: In Azure, network and subnet isolation is managed through [Azure Virtual Networks](/azure/virtual-network/virtual-networks-overview).</span></span>

## <a name="secure-external-access"></a><span data-ttu-id="d99fe-127">保护外部访问</span><span class="sxs-lookup"><span data-stu-id="d99fe-127">Secure external access</span></span>

<span data-ttu-id="d99fe-128">**技术风险**：允许从公共 Internet 访问工作负荷引入了入侵风险，从而导致未经授权的数据泄漏或业务中断。</span><span class="sxs-lookup"><span data-stu-id="d99fe-128">**Technical risk**: Allowing access to workloads from the public internet introduces a risk of intrusion resulting in unauthorized data exposure or business disruption.</span></span>

<span data-ttu-id="d99fe-129">**策略语句**：不允许通过公共 Internet 或跨数据中心直接访问包含受保护数据的子网。</span><span class="sxs-lookup"><span data-stu-id="d99fe-129">**Policy statement**: No subnet containing protected data can be directly accessed over public internet or across datacenters.</span></span> <span data-ttu-id="d99fe-130">对这些子网的访问必须通过中间子网工作进行路由。</span><span class="sxs-lookup"><span data-stu-id="d99fe-130">Access to those subnets must be routed through intermediate subnet works.</span></span> <span data-ttu-id="d99fe-131">所有对这些子网的访问都必须穿过可以执行包扫描和拦截功能的防火墙解决方案。</span><span class="sxs-lookup"><span data-stu-id="d99fe-131">All access into those subnets must come through a firewall solution capable of performing packet scanning and blocking functions.</span></span>

<span data-ttu-id="d99fe-132">**潜在的设计选项**：在 Azure 中，通过部署[公共 Internet 与基于云的网络之间的 DMZ](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)来保护公共终结点。</span><span class="sxs-lookup"><span data-stu-id="d99fe-132">**Potential design option**: In Azure, secure public endpoints by deploying a [DMZ between the public internet and your cloud-based network](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).</span></span>

## <a name="ddos-protection"></a><span data-ttu-id="d99fe-133">DDOS 保护</span><span class="sxs-lookup"><span data-stu-id="d99fe-133">DDoS protection</span></span>

<span data-ttu-id="d99fe-134">**技术风险**：分布式拒绝服务 (DDoS) 攻击可能会导致业务中断。</span><span class="sxs-lookup"><span data-stu-id="d99fe-134">**Technical risk**: Distributed denial of service (DDoS) attacks can result in a business interruption.</span></span>

<span data-ttu-id="d99fe-135">**策略语句**：将自动化 DDoS 缓解机制部署到所有可公开访问的网络终结点。</span><span class="sxs-lookup"><span data-stu-id="d99fe-135">**Policy statement**: Deploy automated DDoS mitigation mechanisms to all publicly accessible network endpoints.</span></span>

<span data-ttu-id="d99fe-136">**潜在的设计选项**：使用 [Azure DDoS 防护](/azure/virtual-network/ddos-protection-overview)尽量减少 DDoS 攻击所导致的中断。</span><span class="sxs-lookup"><span data-stu-id="d99fe-136">**Potential design option**: Use [Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview) to minimize disruptions caused by DDoS attacks.</span></span>

## <a name="secure-on-premises-connectivity"></a><span data-ttu-id="d99fe-137">保护本地连接性</span><span class="sxs-lookup"><span data-stu-id="d99fe-137">Secure on-premises connectivity</span></span>

<span data-ttu-id="d99fe-138">**技术风险**：云网络与本地之间通过公共 Internet 进行的未加密流量容易被截获，从而引入了数据泄露的风险。</span><span class="sxs-lookup"><span data-stu-id="d99fe-138">**Technical risk**: Unencrypted traffic between your cloud network and on-premises over the public internet is vulnerable to interception, introducing the risk of data exposure.</span></span>

<span data-ttu-id="d99fe-139">**策略语句**：本地与云网络之间的所有连接都必须通过安全的加密 VPN 连接或专用 WAN 链路建立。</span><span class="sxs-lookup"><span data-stu-id="d99fe-139">**Policy statement**: All connections between the on-premises and cloud networks must take place either through a secure encrypted VPN connection or a dedicated private WAN link.</span></span>

<span data-ttu-id="d99fe-140">**潜在的设计选项**：在 Azure 中，使用 ExpressRoute 或 Azure VPN 在本地与云网络之间建立专用连接。</span><span class="sxs-lookup"><span data-stu-id="d99fe-140">**Potential design option**: In Azure, use ExpressRoute or Azure VPN to establish private connections between your on-premises and cloud networks.</span></span>

## <a name="network-monitoring-and-enforcement"></a><span data-ttu-id="d99fe-141">网络监视和强制执行</span><span class="sxs-lookup"><span data-stu-id="d99fe-141">Network monitoring and enforcement</span></span>

<span data-ttu-id="d99fe-142">**技术风险**：对网络配置的更改可能会导致新漏洞和数据泄露风险。</span><span class="sxs-lookup"><span data-stu-id="d99fe-142">**Technical risk**: Changes to network configuration can lead to new vulnerabilities and data exposure risks.</span></span>

<span data-ttu-id="d99fe-143">**策略语句**：管理工具必须审核和强制执行安全基准团队定义的网络配置要求。</span><span class="sxs-lookup"><span data-stu-id="d99fe-143">**Policy statement**: Governance tooling must audit and enforce network configuration requirements defined by the Security Baseline team.</span></span>

<span data-ttu-id="d99fe-144">**潜在的设计选项**：在 Azure 中，网络活动可以使用 [Azure 网络观察程序](/azure/network-watcher/network-watcher-monitoring-overview)进行监视，[Azure 安全中心](/azure/security-center/security-center-network-recommendations)可以帮助确定安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="d99fe-144">**Potential design option**: In Azure, network activity can be monitored using [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview), and [Azure Security Center](/azure/security-center/security-center-network-recommendations) can help identify security vulnerabilities.</span></span> <span data-ttu-id="d99fe-145">通过 Azure Policy 可以根据安全团队定义的限制来限制网络资源和资源配置策略。</span><span class="sxs-lookup"><span data-stu-id="d99fe-145">Azure Policy allows you to restrict network resources and resource configuration policy according to limits defined by the security team.</span></span>

## <a name="security-review"></a><span data-ttu-id="d99fe-146">安全评审</span><span class="sxs-lookup"><span data-stu-id="d99fe-146">Security review</span></span>

<span data-ttu-id="d99fe-147">**技术风险**：新的安全威胁和攻击类型会随时间推移而出现，从而增加云资源泄露或中断的风险。</span><span class="sxs-lookup"><span data-stu-id="d99fe-147">**Technical risk**: Over time, new security threats and attack types emerge, increasing the risk of exposure or disruption of your cloud resources.</span></span>

<span data-ttu-id="d99fe-148">**策略语句**：安全团队应定期检查可能影响云部署的趋势和潜在攻击，以更新云中使用的安全基线工具。</span><span class="sxs-lookup"><span data-stu-id="d99fe-148">**Policy statement**: Trends and potential exploits that could affect cloud deployments should be reviewed regularly by the security team to provide updates to Security Baseline tooling used in the cloud.</span></span>

<span data-ttu-id="d99fe-149">**潜在的设计选项**：建立包括相关 IT 和治理团队成员在内的定期安全评审会议。</span><span class="sxs-lookup"><span data-stu-id="d99fe-149">**Potential design option**: Establish a regular security review meeting that includes relevant IT and governance team members.</span></span> <span data-ttu-id="d99fe-150">评审现有安全数据和指标以发现当前策略和安全基线工具中的缺口，并更新策略以缓解任何新风险。</span><span class="sxs-lookup"><span data-stu-id="d99fe-150">Review existing security data and metrics to establish gaps in current policy and Security Baseline tooling, and update policy to mitigate any new risks.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d99fe-151">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d99fe-151">Next steps</span></span>

<span data-ttu-id="d99fe-152">使用本文中所述的示例作为起点，来制定解决特定安全风险并与云采用计划保持一致的策略。</span><span class="sxs-lookup"><span data-stu-id="d99fe-152">Use the samples mentioned in this article as a starting point to develop policies that address specific security risks that align with your cloud adoption plans.</span></span>

<span data-ttu-id="d99fe-153">若要开始制定与安全基线相关的自己的自定义策略语句，请下载[安全基线模板](template.md)。</span><span class="sxs-lookup"><span data-stu-id="d99fe-153">To begin developing your own custom policy statements related to Security Baseline, download the [Security Baseline template](template.md).</span></span>

<span data-ttu-id="d99fe-154">若要加速此规则的采用，请选择与你的环境最密切符合的[可操作的治理过程](../journeys/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="d99fe-154">To accelerate adoption of this discipline, choose the [Actionable Governance Journey](../journeys/overview.md) that most closely aligns with your environment.</span></span> <span data-ttu-id="d99fe-155">随后修改设计以整合特定公司策略决策。</span><span class="sxs-lookup"><span data-stu-id="d99fe-155">Then modify the design to incorporate your specific corporate policy decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d99fe-156">可操作的治理过程</span><span class="sxs-lookup"><span data-stu-id="d99fe-156">Actionable Governance Journeys</span></span>](../journeys/overview.md)
