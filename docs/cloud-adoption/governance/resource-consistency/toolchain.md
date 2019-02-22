---
title: CAF：Azure 中的资源一致性工具
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Azure 中的资源一致性工具
author: BrianBlanchard
ms.openlocfilehash: 68503289f60fbb3682264ff39546ca7b7700cef5
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900607"
---
# <a name="resource-consistency-tools-in-azure"></a>Azure 中的资源一致性工具

[资源一致性](overview.md)是[云治理五项规则](../governance-disciplines.md)之一。 此规则侧重于建立与环境、应用程序或工作负荷的操作管理相关的策略的方法。 在云治理的五项规则中，资源一致性规则包括应用程序、工作负载和资产性能的监视。 还包括满足规模需求、纠正性能 SLA 违规以及通过自动补救主动避免性能 SLA 违规所需的任务。

下面的 Azure 工具列表有助于完善支持此治理规范的策略和流程。

|    | [Azure 门户](https://azure.microsoft.com/features/azure-portal/)  | [Azure 资源管理器](/azure/azure-resource-manager/resource-group-overview)  | [Azure 蓝图](/azure/governance/blueprints/overview) | [Azure 自动化](/azure/automation/automation-intro) | [Azure AD](/azure/active-directory/fundamentals/active-directory-whatis) |
|---------|---------|---------|---------|---------|---------|
| 部署资源                             | 是 | 是 | 是 | 是 | 否  |
| 管理资源                             | 是 | 是 | 是 | 是 | 否  |
| 使用模板部署资源             | 否  | 是 | 否  | 是 | 否  |
| 已安排的环境部署          | 否  | 否  | 是 | 否  | 否  |
| 定义资源组                       | 是 | 是 | 是 | 否  | 否  |
| 管理工作负荷和帐户所有者           | 是 | 是 | 是 | 否  | 否  |
| 管理对资源的条件访问       | 是 | 是 | 是 | 否  | 否  |
| 配置 RBAC 用户                         | 是 | 否  | 否  | 否  | 是 |
| 将角色和权限分配给资源 | 是 | 是 | 是 | 否  | 是 |
| 定义资源之间的依赖关系        | 否  | 是 | 是 | 否  | 否  |
| 应用访问控制                         | 是 | 是 | 是 | 否  | 是 |
| 评估可用性和可伸缩性          | 否  | 否  | 否  | 是 | 否  |
| 向资源应用标记                      | 是 | 是 | 是 | 否  | 否  |
| 分配 Azure Policy 规则                    | 是 | 是 | 是 | 否  | 否  |
| 为灾难恢复计划资源         | 是 | 是 | 是 | 否  | 否  |
| 应用自动补救措施                  | 否  | 否  | 否  | 是 | 否  |
| 管理账单                               | 是 | 否  | 否  | 否  | 否  |

除了这些资源一致性工具和功能外，还需要监视已部署的资源，以了解性能和运行状况问题。 [Azure Monitor](/azure/azure-monitor/overview) 是 Azure 中的默认监视和报告解决方案。 它提供了许多可用于监视云资源的独立功能，以下列表显示了可满足常见监视需求的功能。

|                                                    | [Azure 门户](https://azure.microsoft.com/features/azure-portal/) | [Application Insights](/azure/application-insights/app-insights-overview) | [Log Analytics](/azure/azure-monitor/log-query/log-query-overview) | [Azure Monitor Rest API](/rest/api/monitor/) |
|----------------------------------------------------|--------------|----------------------|---------------|------------------------|
| 记录虚拟机遥测数据                 | 否           | 否                   | 是           | 否                     |
| 记录虚拟网络遥测数据              | 否           | 否                   | 是           | 否                     |
| 记录 PaaS 服务遥测数据                   | 否           | 否                   | 是           | 否                     |
| 记录应用程序遥测数据                     | 否           | 是                  | 否            | 否                     |
| 配置报表和警报                       | 是          | 否                   | 否            | 是                    |
| 计划定期报告或自定义分析        | 否           | 否                   | 否            | 否                     |
| 可视化并分析日志和性能数据     | 是          | 否                   | 否            | 否                     |
| 集成本地或第三方监视解决方案     | 否           | 否                   | 否            | 是                    |

规划部署时，需要考虑存储日志记录数据的位置以及如何将基于云的[报告和监视服务](../../decision-guides/log-and-report/overview.md)与现有流程和工具集成。

> [!NOTE]
> 组织还使用第三方 DevOps 工具监视工作负荷和资源。 有关详细信息，请参阅 [DevOps 工具集成](https://azure.microsoft.com/products/devops-tool-integrations/)。

# <a name="next-steps"></a>后续步骤

了解如何在 Azure 中创建、分配和管理[策略定义](/azure/governance/policy/)。
