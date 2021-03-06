---
title: CAF：大型企业 - 成本管理演变
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企业 - 成本管理演变
author: BrianBlanchard
ms.openlocfilehash: 6bf63e36f6fb19dd0e5f9a16a7f66eb6e0ed54ff
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900547"
---
# <a name="large-enterprise-cost-management-evolution"></a>大型企业：成本管理演变

本文将成本控制添加到最小可行产品 (MVP) 治理中，对叙述性内容进行了演化。

## <a name="evolution-of-the-narrative"></a>叙述性内容的演变

采用率已超过治理 MVP 中定义的容差指标。 现在，支出的增加需要云治理团队投入时间来监视和控制支出模式。

作为创新的驱动力，IT 不再被企业主要作为成本中心来看待。 鉴于 IT 组织实现了更多的价值，CIO 和 CFO 都认为现在是时候提升 IT 在公司中的地位了。 CFO 希望测试某个业务部门加拿大分支会计云计算的一种直接支付方法，除此之外还作出许多其他变化。 两个已停用的数据中心之一曾是该业务部门加拿大分支的独家托管资产。 在此模型中，将向该业务部门的加拿大子公司直接收取与托管资产相关的运营费用。 此模型使 IT 将工作重心更多地放在创造价值方面，而不是管理其他人的支出方面。 但在开始转变前，成本管理工具需首先就位。

### <a name="evolution-of-current-state"></a>当前状态的演变

在本说明的上一阶段，IT 团队积极地将带有受保护数据的生产工作负荷迁移到到 Azure 中。

自此，一些事态已改变，将会影响到治理：

- 已从标记为停用状态的两个数据中心删除了 5,000 项资产。 采购和 IT 安全部门现在正在取消设置剩余的实物资产。
- 应用程序开发团队已为部署大量云原生应用程序实现了 CI/CD 管道，对客户体验带来的影响显著。
- 商业智能团队创建了聚合、特选、见解和预测流程，为业务运营带来了切实的利益。 现在，这些预测已转化为富有创意的新产品和新服务。

### <a name="evolution-of-future-state"></a>未来状态的演变

- 成本监视和报告要被添加到云解决方案中。 报告应将直接运营费用与产生云成本的职能部门关联起来。 额外的报告使 IT 能够监视支出并为成本管理提供技术指导。 将直接对加拿大分支计费。

## <a name="evolution-of-tangible-risks"></a>有形风险的演变

**预算控制**：自助服务功能存在固有的风险，会导致新平台出现过多的意外成本。 必须建立监测成本和降低当前成本风险的治理流程，确保继续与计划的预算保持一致。

此业务风险可以扩展为几种技术风险：

- 实际成本超出计划之外。
- 业务条件发生变化。 如果发生，便会出现业务职能部门需要使用比预期更多的云服务的情况，进而导致支出异常。 这就存在这将这些额外成本视为超额成本（而不是对计划的必要调整）的风险。 如果成功，这项加拿大试验将有助于降低此风险。
- 系统预配过度，造成超支。

## <a name="evolution-of-the-policy-statements"></a>策略语句的演变

以下策略的更改将有助于缓解新的风险，同时有助于实施指南。

1. 云治理团队应每周按计划对所有云成本进行一次监视。 应每月与 IT 领导层和财务部门共享一次云成本与计划偏差报告。 IT 领导层和财务部门应每月检查一次所有云成本和计划更新。
2. 为了实现问责，必须将所有成本都分配到业务职能部门。
3. 应持续监视云资产，以发现优化机会。
4. 云治理工具必须将“资产规模”选项限制为批准的配置列表。 此工具必须确保所有资产都是可发现的，并受成本监视解决方案跟踪。
5. 在部署计划期间，应记录与托管生产工作负载相关的所有必需的云资源。 此项记录有助于优化预算，并做好采用额外自动化的准备，以防使用费用更高的选项。 在此过程中，应考虑云提供商提供的不同折扣工具，如预留实例或许可证成本降低。
6. 为了更好地控制云成本，所有应用程序所有者都必须参加关于优化工作负载的实践培训。

## <a name="evolution-of-the-best-practices"></a>最佳做法的演变

本文的这一部分将改进治理 MVP 设计，以包括新的 Azure 策略和 Azure 成本管理的实现。 这两项设计变更将共同实现新的公司策略语句。

1. 对 Azure 企业门户做了一些更改，以便对部门管理员收取加拿大部署的相关费用。
2. 实现 Azure 成本管理。
    1. 建立适当级别的访问范围以与订阅模式和资源分组模式保持一致。 假设与先前几篇文章中定义的治理 MVP 保持一致，这就要求对执行高层报告的云治理团队建立“注册帐户范围”访问权限。 治理团队之外的其他团队（例如加拿大采购团队）则需要“资源组范围”访问权限。
    2. 在 Azure 成本管理中设定预算。
    3. 查看并采纳初始建议。 建议制定一个定期过程来支持报告过程。
    4. 配置和执行 Azure 成本管理报告，包括初始报告和重复报告。
3. 更新 Azure Policy。
    1. 审核标记、管理组、订阅和资源组值，以确定是否有任何偏差。
    2. 设定 SKU 大小选项，将部署限制为采用部署计划文档中列出的 SKU。

## <a name="conclusion"></a>结束语

将上述流程和变更添加到治理 MVP 中，有助于降低许多与成本治理相关的风险。 它们共同实现了控制成本所需的可见性、问责和优化。

## <a name="next-steps"></a>后续步骤

随着云采用的不断演进和业务附加值的提升，风险和云治理需求也在不断发展。 对于治理之旅中的这家虚构公司，下一步是使用此治理投资来管理多个云。

> [!div class="nextstepaction"]
> [多云演变](./multi-cloud-evolution.md)