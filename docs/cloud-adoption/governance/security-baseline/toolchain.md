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
# <a name="security-baseline-tools-in-azure"></a>Azure 中的安全基线工具

[安全基线](overview.md)是[云治理的五项规则](../governance-disciplines.md)之一。 该规则侧重于如何建立策略来保护网络、资产，以及最重要的将驻留在云提供商解决方案中的数据。 在云治理的五项规则中，安全基线包括数字资产和数据的分类。 此外，它还包括与数据、资产和网络安全性相关联的风险、业务容忍度和缓解策略的说明。 从技术角度而言，它还涉及[加密](../../decision-guides/encryption/overview.md)决策、[网络要求](../../decision-guides/software-defined-network/overview.md)、[混合标识策略](../../decision-guides/identity/overview.md)和跨[资源组](../../decision-guides/resource-consistency/overview.md)[自动强制执行](../../decision-guides/policy-enforcement/overview.md)安全策略的工具。

下面是 Azure 工具的列表，可帮助完善支持安全基线的策略和进程。

|                                                            | [Azure 门户](https://azure.microsoft.com/features/azure-portal/) / [资源管理器](/azure/azure-resource-manager/resource-group-overview)  | [Azure Key Vault](/azure/key-vault)  | [Azure AD](/azure/active-directory/fundamentals/active-directory-whatis) | [Azure Policy](/azure/governance/policy/overview) | [Azure 安全中心](/azure/security-center/security-center-intro) | [Azure Monitor](/azure/azure-monitor/overview) |
|------------------------------------------------------------|---------------------------------|-----------------|----------|--------------|-----------------------|---------------|
| 将访问控制用于资源和资源创建   | 是                             | 否              | 是      | 否           | 否                    | 否            |
| 保护虚拟网络                                    | 是                             | 否              | 否       | 是          | 否                    | 否            |
| 加密虚拟驱动器                                     | 否                              | 是             | 否       | 否           | 否                    | 否            |
| 加密 PaaS 存储和数据库                         | 否                              | 是             | 否       | 否           | 否                    | 否            |
| 管理混合标识服务                            | 否                              | 否              | 是      | 否           | 否                    | 否            |
| 限制允许的资源类型                         | 否                              | 否              | 否       | 是          | 否                    | 否            |
| 强制执行地理区域限制                          | 否                              | 否              | 否       | 是          | 否                    | 否            |
| 监视网络和资源的安全运行状况          | 否                              | 否              | 否       | 否           | 是                   | 是           |
| 检测恶意活动                                  | 否                              | 否              | 否       | 否           | 是                   | 是           |
| 预先检测漏洞                        | 否                              | 否              | 否       | 否           | 是                   | 否            |
| 配置备份和灾难恢复                     | 是                             | 否              | 否       | 否           | 否                    | 否            |

有关 Azure 安全工具和服务的完整列表，请参阅 [Azure 上可用的安全服务和技术](/azure/security/azure-security-services-technologies)。

此外，客户通常还会使用第三方工具来推动安全基线活动。 有关详细信息，请参阅[在 Azure 安全中心集成安全解决方案](/azure/security-center/security-center-partner-integration)。

除安全工具外，[Microsoft 信任中心](https://www.microsoft.com/trustcenter/guidance/risk-assessment)也包含大量的指南、报告以及相关文档，有助于你在迁移计划过程中执行风险评估。
