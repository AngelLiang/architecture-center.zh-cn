---
title: CAF：Azure 中的部署加速工具
description: Azure 中的部署加速工具
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
author: BrianBlanchard
ms.openlocfilehash: 8d2cae87b8a3a11bfde87aafc04d4d2ef55cbbe9
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900613"
---
# <a name="deployment-acceleration-tools-in-azure"></a>Azure 中的部署加速工具

[部署加速](overview.md)是[云治理的五大规范](../governance-disciplines.md)之一。 此规范主要介绍了如何建立治理资产配置或部署的策略。 在云治理的五大规范中，配置治理包括部署、配置调整和 HA/DR 策略。 这可以通过手动活动或全自动 DevOps 活动实现。 无论采用上述哪种活动，策略基本保持不变。

对治理感兴趣的云管理员、云保护者和云架构师都可能会对部署加速规范投入大量时间，这项规范编纂多项云采用工作的策略和要求。 此工具链中的工具对云治理团队非常重要，应成为团队学习路径的重中之重。

下面的 Azure 工具列表有助于完善支持此治理规范的策略和流程。

|  |Azure Policy  |Azure 管理组  |Azure 资源管理器模板  |Azure 蓝图  | Azure Resource Graph | Azure 成本管理 |
|---------|---------|---------|---------|---------|---------|---------|
|实现企业策略     |是 |否  |否  |否 | 否 |否 |
|跨订阅应用策略     |必选 |是  |否  |否 | 否 |否 |
|部署定义的资源     |否 |否  |是  |否 | 否 |否 |
|创建完全合规的环境      |必选 |必选  |必选  |是 | 否 |否 |
|审核策略      |是 |否  |否  |否 | 否 |否 |
|查询 Azure 资源      |否 |否  |否  |否 |是 |否 |
|报告资源成本      |否 |否  |否  |否 |否 |是 |

以下是完成特定部署加速目标可能需要的其他工具。 这些工具通常在治理团队外部使用，但仍被视为部署加速规范的一方面。

|  |Azure 门户  |Azure 资源管理器模板  |Azure Policy  | Azure DevOps | Azure 备份 | Azure Site Recovery |
|---------|---------|---------|---------|---------|---------|---------|
|手动部署（单个资产）     | 是 | 是  | 否  | 效率不高 | 否 | 是 |
|手动部署（整个环境）     | 效率不高 | 是 | 否  | 效率不高 | 否 | 是 |
|自动部署（整个环境）     | 否  | 是  | 否  | 是  | 否 | 是 |
|更新单个资产的配置     | 是 | 是 | 效率不高 | 效率不高 | 否 | 是 - 在复制期间 |
|更新整个环境的配置     | 效率不高 | 是 | 是 | 是  | 否 | 是 - 在复制期间 |
|管理配置偏移     | 效率不高 | 效率不高 | 是  | 是  | 否 | 是 - 在复制期间 |
|创建用于部署代码和配置资产的自动管道 (DevOps)     | 否 | 否 | 否 | 是 | 否 | 否 |
|在发生故障或违反 SLA 期间恢复数据     | 否 | 否 | 否 | 是 | 是 | 是 |
|在发生故障或违反 SLA 期间恢复应用程序和数据     | 否 | 否 | 否 | 是 | 否 | 是 |

除了上面提到的 Azure 本机工具之外，客户通常还使用第三方工具来推进部署加速和 DevOps 部署。
