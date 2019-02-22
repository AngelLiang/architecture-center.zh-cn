---
title: CAF：成本管理示例策略语句
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 1/4/2019
description: 成本管理示例策略语句
author: BrianBlanchard
ms.openlocfilehash: 1c8b94ae5285fa26cdf9760892beaced2487af8b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900640"
---
# <a name="cost-management-sample-policy-statements"></a>成本管理示例策略语句

各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。 这些语句应提供简要的风险摘要，以及应对它们的计划。 每个语句定义应包括以下这些信息：

- 业务风险 - 此策略将解决的风险的摘要。
- 策略语句 - 策略要求的清楚摘要说明。
- 设计选项 - IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指导。

以下示例策略语句解决一些常见的成本相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的实际策略语句时进行参考。 这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何单个确定的风险。 与业务和 IT 团队密切合作来确定适合于特定成本相关风险的最佳策略解决方案。  

## <a name="future-proofing"></a>面向将来

**业务风险**。 不保证治理团队对成本管理规则的投资的当前条件。 但是，预期会在将来进行这类投资。

**策略语句**。 应将部署到云的所有资产与记帐单位、应用程序/工作负荷相关联，并满足命名标准。 此策略将确保未来的成本管理工作是有效的。

**设计选项**。 有关建立面向未来的基础的信息，请参阅[可操作的设计指南](../journeys/overview.md)（包含在 CAF 指导中）中与创建治理 MVP 相关的讨论。

## <a name="budget-overruns"></a>预算溢出

**业务风险：** 自助服务部署形成超支风险。

**策略语句：** 必须将任何云部署分配给具有已批准的预算和预算限制机制的记帐单位。

**设计选项：** 在 Azure 中，可以使用 [Azure 成本管理](/azure/cost-management/manage-budgets)控制预算

## <a name="underutilization"></a>使用不足

**业务风险：** 公司对云服务进行了预付，或购买了花费特定金额的包年套餐。 这样便存在未使用商定金额的风险，从而导致投资损失。

**策略语句：** 分配了云预算的每个记帐单位会每年设置预算，每季度调整预算，以及每月分配时间以便评审计划支出与实际支出。 每月与记帐单位主管讨论大于 20% 的任何偏差。 为进行跟踪，将所有资产都分配给记帐单位。

**设计选项：**

- 在 Azure 中，计划支出与实际支出可以通过 [Azure 成本管理](/azure/cost-management/quick-acm-cost-analysis)进行管理
- 有多个选项用于按记帐单位对资源进行分组。 在 Azure 中，应与治理团队协作选择[资源一致性模型](../../decision-guides/resource-consistency/overview.md)并应用于所有资产。

## <a name="overprovisioned-assets"></a>预配过度的资产

**业务风险：** 在传统本地数据中心内，常见做法是在部署资产时针对遥远未来的增长进行额外容量规划。 云的缩放速度可以比传统设备更快。 云中的资产还基于技术容量进行定价。 存在旧式本地做法人为地扩大云支出的风险。

**策略语句：** 任何部署到云的资产都必须在可以监视利用率并报告超过 50% 利用率的任何容量的程序中进行注册。 任何部署到云的资产都必须以逻辑方式进行分组或标记，使治理团队成员可以在预配过度资产的任何优化方面与工作负荷所有者进行接洽。

**设计选项：**

- 在 Azure 中，[Azure 顾问](/azure/advisor/advisor-cost-recommendations)可以提供优化建议。
- 有多个选项用于按记帐单位对资源进行分组。 在 Azure 中，应与治理团队协作选择[资源一致性模型](../../decision-guides/resource-consistency/overview.md)并应用于所有资产。

## <a name="overoptimization"></a>过度优化

**业务风险：** 实际上，有效的成本管理可能会形成新风险。 支出的优化与系统性能成反比。 降低成本时，存在过度紧缩支出并形成糟糕的用户体验的风险。

**策略语句：** 必须通过分组或标记来标识直接影响客户体验的任何资产。 优化影响客户体验的任何资产之前，云治理团队必须基于不少于 90 天的利用率趋势来调整优化。 优化资产时，记录任何季节性或事件驱动的突发情况。

**设计选项：**

- 在 Azure 中，[Azure Monitor 的见解功能](/azure/azure-monitor/insights/vminsights-performance)可以帮助分析系统利用率。
- 有多个选项用于基于角色对资源进行分组和标记。 在 Azure 中，应与治理团队协作选择[资源一致性模型](../../decision-guides/resource-consistency/overview.md)并将此模型应用于所有资产。

## <a name="next-steps"></a>后续步骤

使用本文中所述的示例作为起点，来制定解决特定业务风险并与云采用计划保持一致的策略。

若要开始制定与成本管理相关的自己的自定义策略语句，请下载[成本管理模板](template.md)。

若要加速此规则的采用，请选择与你的环境最密切符合的[可操作的治理过程](../journeys/overview.md)。 随后修改设计以整合特定公司策略决策。

> [!div class="nextstepaction"]
> [可操作的治理过程](../journeys/overview.md)
