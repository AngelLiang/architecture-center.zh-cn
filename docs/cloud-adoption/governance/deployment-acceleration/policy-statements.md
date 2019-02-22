---
title: CAF：部署加速示例策略语句
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 部署加速示例策略语句
author: alexbuckgit
ms.openlocfilehash: 4f7d59e6653c29db03f966d1c7105524b72586fc
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900582"
---
# <a name="deployment-acceleration-sample-policy-statements"></a>部署加速示例策略语句

各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。 这些语句应提供简要的风险摘要，以及应对它们的计划。 每个语句定义应包括以下这些信息：

- **技术风险。** 此策略将解决的风险的摘要。
- **策略语句。** 策略要求的清楚摘要说明。
- **设计选项。** IT 团队和开发人员在实现策略时可以使用的可操作建议、规范或其他指导。

以下示例策略语句解决一些常见的配置相关业务风险，并作为示例提供给你，以便在草拟解决自己组织需求的策略语句时进行参考。 请注意，这些示例并不是禁止性的，并且可能有多个策略选项可用于应对任何特定的风险。 与业务和 IT 团队密切合作来确定适合于独特风险集的最佳策略解决方案。

## <a name="reliance-on-manual-deployment-or-configuration-of-systems"></a>依靠手动部署或系统配置

**技术风险**：部署或配置过程中依赖于人工干预将增加人为错误的可能性，并降低系统部署和配置的可重复性和可预测性。 它通常也会导致系统资源部署较慢。

**策略语句**：如果可能，所有部署到云的资产都应使用模板或自动化脚本进行部署。

**潜在的设计选项**：[Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)提供了将资源部署到 Azure 的基础结构即代码方法。 [Azure 构建基块](https://github.com/mspnp/template-building-blocks/wiki)提供命令行工具和资源管理器模板集，旨在简化 Azure 资源的部署。

## <a name="lack-of-visibility-into-system-issues"></a>缺少系统问题的可见性

**技术风险**：对业务系统监视和诊断不足将阻止操作人员在系统故障发生前确定和修正问题，并能明显增加适当解决故障所需的时间。

**策略语句**：将实施以下策略：

- 将为所有生产系统标识关键指标和诊断度量值，并且将对这些系统应用监视和诊断工具，由操作人员定期监视。
- 运营将考虑在非生产环境（例如暂存和质量保证）中使用监视和诊断工具，以在生产环境中发生系统问题时进行识别。

**潜在的设计选项**：[Azure Monitor](/azure/azure-monitor/)，其中还包含 Log Analytics 和 Application Insight，可提供用于收集和分析遥测数据的工具，以帮助了解应用程序性能如何并主动确定影响它们的问题，以及它们所依赖的资源。

## <a name="configuration-security-reviews"></a>配置安全评审

**技术风险**：随着时间推移，新的安全威胁或忧虑会增大对受保护资源未经授权访问的风险。

**策略语句**：云治理过程必须包括配置管理团队的季度审核，以标识云资产配置应该阻止的恶意执行组件或使用模式。

**潜在的设计选项**：确立季度安全评审会议，包括负责配置云应用程序和资源的治理团队成员和 IT 人员。 评审现有安全数据和指标以发现当前部署加速策略和工具中的缺口，并更新策略以缓解任何新风险。

## <a name="next-steps"></a>后续步骤

使用本文中所述的示例作为起点，来制定解决特定业务风险并与云采用计划保持一致的策略。

若要开始制定与标识管理相关的自己的自定义策略语句，请下载[标识基线模板](template.md)。

若要加速此规则的采用，请选择与你的环境最密切符合的[可操作的治理过程](../journeys/overview.md)。 随后修改设计以整合特定公司策略决策。

> [!div class="nextstepaction"]
> [可操作的治理过程](../journeys/overview.md)