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
# <a name="security-baseline-sample-policy-statements"></a>安全基线示例策略语句

各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。 这些语句应提供简要的风险摘要，以及应对它们的计划。 每个语句定义应包括以下这些信息：

- 技术风险 - 此策略将解决的风险的摘要。
- 策略语句 - 策略要求的清楚摘要说明。
- 技术选项 - IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指导。

以下示例策略语句解决一些常见的安全相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的实际策略语句时进行参考。 这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何单个确定的风险。 与业务、安全和 IT 团队密切合作来确定适合于特定安全风险的最佳策略解决方案。  

## <a name="asset-classification"></a>资产分类

**技术风险**：未正确标识为任务关键型或涉及敏感数据的资产可能不会受到足够保护，从而导致潜在的数据泄露或业务中断。

**策略语句**：所有已部署的资产必须按照重要程度和数据分类进行分类。 在部署到云之前，云治理团队和应用程序所有者必须对分类进行评审。

**潜在的设计选项**：建立[资源标记标准](../../decision-guides/resource-tagging/overview.md)，并确保 IT 人员使用 [Azure 资源标记](/azure/azure-resource-manager/resource-group-using-tags)将它们一致地应用于任何部署的资源。

## <a name="data-encryption"></a>数据加密

**技术风险**：在存储过程存在泄漏受保护数据的风险。

**策略语句**：所有受保护的数据都必须在静态时加密。

**潜在的设计选项**：请参阅 [Azure 加密概述](/azure/security/security-azure-encryption-overview)一文，以了解有关如何在 Azure 平台上执行静态数据加密的讨论。  

## <a name="network-isolation"></a>网络隔离

**技术风险**：网络和网络中的子网之间的连接引入了潜在漏洞，可能会导致数据泄露或任务关键型服务中断。

**策略语句**：包含受保护数据的网络子网必须独立于任何其他子网。 将定期审核受保护数据子网之间的网络流量。

**潜在的设计选项**：在 Azure 中，通过 [Azure 虚拟网络](/azure/virtual-network/virtual-networks-overview)管理网络和子网隔离。

## <a name="secure-external-access"></a>保护外部访问

**技术风险**：允许从公共 Internet 访问工作负荷引入了入侵风险，从而导致未经授权的数据泄漏或业务中断。

**策略语句**：不允许通过公共 Internet 或跨数据中心直接访问包含受保护数据的子网。 对这些子网的访问必须通过中间子网工作进行路由。 所有对这些子网的访问都必须穿过可以执行包扫描和拦截功能的防火墙解决方案。

**潜在的设计选项**：在 Azure 中，通过部署[公共 Internet 与基于云的网络之间的 DMZ](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)来保护公共终结点。

## <a name="ddos-protection"></a>DDOS 保护

**技术风险**：分布式拒绝服务 (DDoS) 攻击可能会导致业务中断。

**策略语句**：将自动化 DDoS 缓解机制部署到所有可公开访问的网络终结点。

**潜在的设计选项**：使用 [Azure DDoS 防护](/azure/virtual-network/ddos-protection-overview)尽量减少 DDoS 攻击所导致的中断。

## <a name="secure-on-premises-connectivity"></a>保护本地连接性

**技术风险**：云网络与本地之间通过公共 Internet 进行的未加密流量容易被截获，从而引入了数据泄露的风险。

**策略语句**：本地与云网络之间的所有连接都必须通过安全的加密 VPN 连接或专用 WAN 链路建立。

**潜在的设计选项**：在 Azure 中，使用 ExpressRoute 或 Azure VPN 在本地与云网络之间建立专用连接。

## <a name="network-monitoring-and-enforcement"></a>网络监视和强制执行

**技术风险**：对网络配置的更改可能会导致新漏洞和数据泄露风险。

**策略语句**：管理工具必须审核和强制执行安全基准团队定义的网络配置要求。

**潜在的设计选项**：在 Azure 中，网络活动可以使用 [Azure 网络观察程序](/azure/network-watcher/network-watcher-monitoring-overview)进行监视，[Azure 安全中心](/azure/security-center/security-center-network-recommendations)可以帮助确定安全漏洞。 通过 Azure Policy 可以根据安全团队定义的限制来限制网络资源和资源配置策略。

## <a name="security-review"></a>安全评审

**技术风险**：新的安全威胁和攻击类型会随时间推移而出现，从而增加云资源泄露或中断的风险。

**策略语句**：安全团队应定期检查可能影响云部署的趋势和潜在攻击，以更新云中使用的安全基线工具。

**潜在的设计选项**：建立包括相关 IT 和治理团队成员在内的定期安全评审会议。 评审现有安全数据和指标以发现当前策略和安全基线工具中的缺口，并更新策略以缓解任何新风险。

## <a name="next-steps"></a>后续步骤

使用本文中所述的示例作为起点，来制定解决特定安全风险并与云采用计划保持一致的策略。

若要开始制定与安全基线相关的自己的自定义策略语句，请下载[安全基线模板](template.md)。

若要加速此规则的采用，请选择与你的环境最密切符合的[可操作的治理过程](../journeys/overview.md)。 随后修改设计以整合特定公司策略决策。

> [!div class="nextstepaction"]
> [可操作的治理过程](../journeys/overview.md)
