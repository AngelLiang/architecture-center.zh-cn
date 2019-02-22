---
title: CAF：资源一致性示例策略语句
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 资源一致性示例策略语句
author: BrianBlanchard
ms.openlocfilehash: 9217ae73b0edf5b40bedac1cdc961b7a34da87d1
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900502"
---
# <a name="resource-consistency-sample-policy-statements"></a>资源一致性示例策略语句

各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。 这些语句应提供简要的风险摘要，以及应对它们的计划。 每个语句定义应包括以下这些信息：

- 技术风险 - 此策略将解决的风险的摘要。
- 策略语句 - 策略要求的清楚摘要说明。
- 设计选项 - IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指南。

以下示例策略语句解决一些常见的资源一致性相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的策略语句时进行参考。 请注意，这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何特定的风险。 与业务和 IT 团队密切合作来确定适合于独特风险集的最佳策略解决方案。

## <a name="tagging"></a>标记

**技术风险**：如果没有与已部署资源相关联的适当元数据标记，IT 运营就无法根据所需的 SLA、业务运营的重要性或运营成本来优先支持或优化资源。 这可能导致 IT 资源的错误分配，并可能延误事件的解决。

**策略语句**：将实施以下策略：

- 部署的资产应使用以下值进行标记：成本、关键性、SLA 和环境。
- 治理工具必须验证与成本、关键性、SLA、应用程序和环境相关的标记。 所有值都必须与治理团队管理的预定义值保持一致。

**潜在的设计选项**：在 Azure 中，大多数资源类型都支持[标准名称-值元数据标记](/azure/azure-resource-manager/resource-group-using-tags)。 [Azure Policy](/azure/governance/policy/overview) 用于在资源创建过程中强制执行特定标记。

## <a name="ungoverned-subscriptions"></a>不受控制的订阅

**技术风险**：任意创建订阅和管理组可能会导致云资产中存在独立的部分，这些部分不受治理策略的约束。

**策略语句**：为任何任务关键型应用程序或受保护数据创建新的订阅或管理组将需要云治理团队的审核。 批准的变更将被集成到适当的蓝图分配中。

**潜在的设计选项**：将对组织 [Azure 管理组](/azure/governance/management-groups/)的管理访问权限锁定为仅批准的治理团队成员，这些成员将控制订阅创建和访问控制流程。

## <a name="manage-updates-to-virtual-machines"></a>管理虚拟机更新

**技术风险**：没有使用最新更新和软件修补程序保持处于最新状态的虚拟机 (VM) 容易受到安全问题或性能问题的影响，这可能会导致服务中断。

**策略语句**：治理工具必须强制执行在所有部署的 VM 上启用的自动更新。 必须与运营管理团队协同审查违规行为，并根据运营策略进行补救。 不会自动更新的资产必须包含在归 IT 运营的流程中。

**潜在的设计选项**：对于 Azure 托管的 VM，可以使用 [Azure 自动化中的更新管理解决方案](/azure/automation/automation-update-management)提供一致的更新管理。

## <a name="deployment-compliance"></a>部署符合性

**技术风险**：如果部署脚本和自动化工具没有经过云治理团队的全面审查，则可能导致违反策略的资源部署。

**策略语句**：将实施以下策略：

- 部署工具必须得到云治理团队的批准，以确保对部署的资产进行持续管理。
- 必须在云治理团队可访问的中央存储库中维护部署脚本，以便进行定期检查和审核。

**潜在的设计选项**：持续使用 [Azure 蓝图](/azure/governance/blueprints/)来管理自动化部署可一致部署符合组织治理标准和策略的 Azure 资源。

## <a name="monitoring"></a>监视

**技术风险**：不正确地实现或不一致地检测监视会阻止检测工作负载运行状况问题或其他违反策略符合性的情况。

**策略语句**：将实施以下策略：

- 治理工具必须确认将与任务关键型应用程序或受保护数据相关的所有资产均已纳入资源消耗和优化监视范围。
- 治理工具必须确认为所有任务关键型应用程序或受保护数据收集适当级别的日志记录数据。

**潜在的设计选项**：[Azure Monitor](/azure/azure-monitor/overview) 是 Azure 平台中的默认监视服务，在部署资源时，可以通过使用 [Azure 蓝图](/azure/governance/blueprints/)强制执行一致的监视。

## <a name="disaster-recovery"></a>灾难恢复

**技术风险**：资源故障、删除或损坏可能导致任务关键型应用程序或服务中断和敏感数据丢失。

**策略语句**：所有任务关键型应用程序和受保护的数据必须实现备份和恢复解决方案，以最大限度地减少中断或系统故障对业务的影响。

**潜在的设计选项**：[Azure Site Recovery] 提供了备份、恢复和复制功能，目的是在业务连续性和灾难恢复 (BCDR) 场景中最大限度地减少停机时间。

## <a name="next-steps"></a>后续步骤

使用本文所述的示例作为起始点，制定策略，用于解决符合云采用计划的特定业务风险。

要开始制定与资源一致性相关的自定义策略语句，请下载[资源一致性模板](template.md)。

若要加速此规则的采用，请选择与环境最密切符合的[可操作的治理过程](../journeys/overview.md)。 随后修改设计以整合特定公司策略决策。

> [!div class="nextstepaction"]
> [可操作的治理过程](../journeys/overview.md)
