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
# <a name="identity-baseline-sample-policy-statements"></a>标识基线示例策略语句

各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。 这些语句应提供简要的风险摘要，以及应对它们的计划。 每个语句定义应包括以下这些信息：

- 技术风险 - 此策略将解决的风险的摘要。
- 策略语句 - 策略要求的清楚摘要说明。
- 设计选项 - IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指导。

以下示例策略语句解决一些常见的身份相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的策略语句时进行参考。 这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何特定的风险。 与业务和 IT 团队密切合作来确定适合于独特风险集的最佳策略解决方案。

## <a name="lack-of-access-controls"></a>缺少访问控制

**技术风险**：不足或临时的访问控制设置可能会引入对敏感或任务关键型资源进行未经授权的访问的风险。

**策略语句**：应使用由当前治理策略批准的标识和角色来控制部署到云的所有资产。

**潜在的设计选项**：[Azure Active Directory 条件访问](/azure/active-directory/conditional-access/overview)是 Azure 中的默认访问控制机制。

## <a name="overprovisioned-access"></a>预配过度的访问

**技术风险**：对其职责范围之外的资源进行控制的用户和组可能会导致未经授权的修改，从而形成中断或安全漏洞。

**策略语句**：将实施以下策略：

- 最小特权访问模型将应用于任务关键型应用程序或受保护数据中所涉及的任何资源。
- 提升的权限应是例外情况，云治理团队必须记录任何这类例外情况。 将定期审核例外情况。

**潜在的设计选项**：请查阅 [Azure 标识管理最佳做法](/azure/security/azure-security-identity-management-best-practices)以实现基于角色的访问控制 (RBAC) 策略，该策略基于[了解需求](https://wikipedia.org/wiki/Need_to_know)和[最小特权安全](https://wikipedia.org/wiki/Principle_of_least_privilege)原则来限制访问。

## <a name="lack-of-shared-management-accounts-between-on-premises-and-the-cloud"></a>本地与云之间缺少共享管理帐户

**技术风险**：使用本地 Active Directory 上的帐户的 IT 管理层或管理人员可能没有足够权限来访问云资源，可能无法高效地解决运营或安全问题。

**策略语句**：本地 Active Directory 基础结构中具有提升的特权的所有组都应映射到已批准的 RBAC 角色。

**潜在的设计选项**：在基于云的 Azure Active Directory 与本地 Active Directory 之间实现混合标识解决方案，并将所需本地组添加到完成其工作所需的 RBAC 角色。

## <a name="weak-authentication-mechanisms"></a>弱身份验证机制

**技术风险**：缺乏足够安全的用户身份验证方法（如基本用户/密码组合）的标识管理系统可能会导致密码泄露或受到攻击，从而形成对安全云系统进行未经授权的访问的重大风险。

**策略语句**：所有帐户都需要使用多重身份验证 (MFA) 方法登录安全资源。

**潜在的设计选项**：对于 Azure Active Directory，实现 [Azure 多重身份验证](/azure/active-directory/authentication/concept-mfa-howitworks)用户授权过程的一部分。

## <a name="isolated-identity-providers"></a>隔离的标识提供者

**技术风险**：不兼容的标识提供者可能会导致无法与客户或其他业务合作伙伴共享资源或服务。

**策略语句**：任何需要客户身份验证的应用程序的部署都必须使用与内部用户的主标识提供者兼容的已批准的标识提供者。

**潜在的设计选项**：在内部与客户标识提供者之间实现[与 Azure Active Directory 的联合](/azure/active-directory/hybrid/whatis-fed)。

## <a name="identity-reviews"></a>标识评审

**技术风险**：随着时间推移，业务变化、新的云部署的添加或其他安全问题可能会增加对安全资源进行未经授权的访问的风险。

**策略语句**：云治理过程必须包括标识管理团队的季度评审，以标识云资产配置应该阻止的恶意执行组件或使用模式。

**潜在的设计选项**：确立季度安全评审会议，包括负责管理标识服务的治理团队成员和 IT 人员。 评审现有安全数据和指标以发现当前标识管理策略和工具中的缺口，并更新策略以缓解任何新风险。

## <a name="next-steps"></a>后续步骤

使用本文中所述的示例作为起点，来制定解决特定业务风险并与云采用计划保持一致的策略。

若要开始制定与标识基线相关的自己的自定义策略语句，请下载[标识基线模板](template.md)。

若要加速此规则的采用，请选择与你的环境最密切符合的[可操作的治理过程](../journeys/overview.md)。 随后修改设计以整合特定公司策略决策。

> [!div class="nextstepaction"]
> [可操作的治理过程](../journeys/overview.md)