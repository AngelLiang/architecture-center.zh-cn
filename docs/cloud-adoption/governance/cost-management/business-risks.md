---
title: CAF：成本管理动机和业务风险
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 成本管理动机和业务风险
author: BrianBlanchard
ms.openlocfilehash: b9bf31a796ea1a7530c6a668a231d74b9b765509
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900548"
---
# <a name="cost-management-motivations-and-business-risks"></a>成本管理动机和业务风险

本文讨论客户通常在云治理策略中采用成本管理规则的原因。 此外，还提供了驱动策略语句的几个示例业务风险。

<!-- markdownlint-disable MD026 -->

## <a name="is-cost-management-relevant"></a>成本管理是否相关？

在成本治理方面，云采用形成了范例转移。 传统本地环境中的成本管理基于刷新周期、数据中心购置、主机续订和定期维护问题。 可以预测、规划和优化其中每个成本，以便与每年资本支出预算保持一致。

对于云解决方案，许多企业倾向于采用更具响应性的成本管理方法。 在许多情况下，企业会预先购买或承诺使用一定数量的云服务。 此模型假设，根据企业计划对特定云供应商的支出金额来最大化折扣会形成对主动计划成本周期的认知。 但是，仅当企业还实现了成熟的成本管理规则时，该认知才会成为现实。

云提供了以前在传统本地数据中心内前所未闻的自助服务功能。 这些新功能使企业在采用新技术方面可以更灵活、限制更少且更开放。 但是，自助服务的缺点是最终用户可能在不知不觉中超出分配的预算。 相反，相同的用户可能会遇到计划发生变化，意外地不使用预测的云服务量。 任一方向的可能转变都证明在治理团队中投资成本管理规则是合理的。

## <a name="business-risk"></a>业务风险

成本管理规则尝试解决与托管基于云的工作负荷时所产生的费用相关的核心业务风险。 与企业合作来确定这些风险，并在规划和实现云部署时监视每个风险的相关性。

组织间的风险会有所不同，不过以下这些常见的成本相关风险可以用作在云治理团队中展开讨论的起点：

- **预算控制**。 未控制预算可能会导致对云供应商过度支出。
- **利用率损失**。 未使用的预先购买或预先承诺会形成投资浪费。
- **支出异常**：任一方向上的意外激增都可能指示不正确的使用情况。
- **预配过度的资产**。 如果在配置中部署的资产超过应用程序或虚拟机 (VM) 的需求，则它们可能会增加成本并形成浪费。

## <a name="next-steps"></a>后续步骤

使用[云管理模板](./template.md)，记录可能由当前云采用计划引入的业务风险。

确立了对实际业务风险的了解后，下一步是记录业务对风险的容忍度，以及用于监视该容忍度的指示器和关键指标。

> [!div class="nextstepaction"]
> [了解指示器、指标和风险容忍度](./metrics-tolerance.md)
