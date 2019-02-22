---
title: CAF：大型企业 - 治理策略背后的初始公司策略
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: 大型企业 - 治理策略背后的初始公司策略。
author: BrianBlanchard
ms.openlocfilehash: d3bc31f95229a82cbc2f6ac6e684a87107783c07
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900628"
---
# <a name="large-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a>大型企业：治理策略背后的初始公司策略

下面的公司策略定义了初始治理位置，即此过程的起点。 本文定义了早期风险、初始策略声明和强制执行策略声明的早期流程。

> [!NOTE]
>公司策略不是技术文档，但它将推动许多技术决策。 [概述](./overview.md)中所描述的治理 MVP 根本上源于此策略。 组织应该先基于自己的目标和业务风险制定公司策略，然后再实施治理 MVP。

## <a name="cloud-governance-team"></a>云治理团队

CIO 最近与 IT 治理团队开了一次会，以了解 PII 和任务关键型策略的历史记录，并评审更改这些策略的影响。 她还讨论了面向 IT 和公司的云的整体潜力。

会后，IT 治理团队的两名成员请求获得权限，以展开云规划的研究和支持工作。 认识到治理的必要性和限制影子 IT 的机会，IT 治理总监表示支持这一想法。 云治理团队就这样诞生了。 在接下来的几个月里，他们将从治理的角度继承清理在云探索过程中犯下的诸多错误。 这将为他们赢得云管理员的称谓。 在以后的演进中，该过程将显示他们的角色是如何随时间变化的。

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a>容忍度指标

当前风险容忍度较高，而投入云治理的意愿很低。 因此，容忍度指标可作为早期预警系统，以促使投入时间和精力。 如果观察到以下指标，明智的做法是改进治理策略。

- 成本管理：部署到云上的资产规模超过 1,000 项，或者每月开销超过 10,000 美元。
- 标识基线：包含具有传统或第三方多重身份验证 (MFA) 需求的应用程序。
- 安全基线：包含已定义的云采用计划中的受保护数据。
- 资源一致性：包含已定义的云采用计划中的任何任务关键型应用程序。

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a>后续步骤

此公司策略使得云治理团队对实现治理 MVP 有所准备，这将是云采用的基础。 下一步是实现此 MVP。

> [!div class="nextstepaction"]
> [最佳做法介绍](./best-practice-explained.md)