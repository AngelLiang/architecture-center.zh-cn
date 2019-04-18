---
title: CAF：大型企业 – 最佳做法所述
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企业 – 最佳做法所述
author: BrianBlanchard
ms.openlocfilehash: 2d52797f1c3541fab1c97d97d0438210d2e66f79
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068984"
---
# <a name="large-enterprise-best-practice-explained"></a>大型企业：最佳做法介绍

治理过程由一组初始[企业策略](./initial-corporate-policy.md)开始。 这些策略用于为治理建立反映[最佳做法](./overview.md)的最小化可行产品 (MVP)。

在本文中，我们将讨论创建治理 MVP 所需的高级策略。 治理 MVP 的核心是[部署加速](../../deployment-acceleration/overview.md)规则。 此阶段应用的工具和模式将实现未来扩大治理所需的增量演变。

## <a name="governance-mvp-cloud-adoption-foundation"></a>治理 MVP（云采用基础）

得益于多个简单原则和基于云的治理工具，可实现快速采用治理和公司策略。 以下是在任何治理过程中都要进行探讨的三个治理规则中的第一个。 本文将充分叙述每个规则。

为了建立基础，本文将讨论创建治理 MVP 所需的标识基线、安全基线和部署加速背后的高级策略，它将作为所有采用的基础。

![增量治理 MVP 的示例](../../../_images/governance/governance-mvp.png)

## <a name="implementation-process"></a>实现进程

治理 MVP 的实现对标识、安全性和网络具有依赖关系。 解决依赖项后，云治理团队将决定治理的几个方面。 来自云治理团队和支持团队的决策将通过强制资产的单个包来实现。

![增量治理 MVP 的示例](../../../_images/governance/governance-mvp-implementation-flow.png)

还可以使用简单的清单来描述此实现：

1. 征求关于核心依赖关系的决定：标识、网络和加密。
2. 确定要在企业策略强制执行期间使用的模式。
3. 确定资源一致性、 资源标记和日志记录和报告领域的相应管理模式。
4. 实施与所选策略强制执行模式一致的治理工具，以应用依赖决策和治理决策。

[!INCLUDE [implementation-process](../../../../../includes/cloud-adoption/governance/implementation-process.md)]

## <a name="application-of-governance-defined-patterns"></a>治理定义模式的应用程序

云治理团队将负责以下决策和实现。 许多人会需要其他团队的意见，但云治理团队可能同时拥有决策和实现。 下列部分概述了针对此用例的决策以及每个决策的详细信息。

### <a name="subscription-model"></a>订阅模型

已为 Azure 订阅选择“混合”模式。

- 由于出现对 Azure 资源的新请求，所以应为每个运营区域中的每个主要业务单元建立一个“部门”。 在每个部门中，应为每个应用程序原型创建“订阅”。
- 应用程序原型是一种对具有类似需求的应用程序进行分组的方法。 常见示例包括：具有受保护数据的应用程序、受治理的应用程序（例如 HIPAA 或 FedRAMP）、低风险应用程序、具有本地依赖项的应用程序、SAP、Azure 中的其他大型机或扩展本地 SAP 或大型机的应用程序。 每个组织都有基于数据分类和支持业务的应用程序类型的独特需求。 数字资产的依赖项映射可帮助在组织中定义应用程序原型。
- 作为订阅设计的一部分，应根据前两点同意共同的命名约定。

### <a name="resource-consistency"></a>资源一致性

已选择“层次一致性”作为“资源一致性”模式。

- 应为每个应用程序创建资源组。 应为每个应用程序原型创建管理组。 Azure Policy 应该应用于关联管理组的所有订阅。
- 作为部署过程的一部分，所有资产的资源一致性模板应存储在源代码管理中。
- 每个资源组应与特定工作负载或应用程序相一致。
- 定义的 Azure 管理组层次结构应表示使用嵌套组的计费责任和应用程序所有权。
- Azure Policy 的广泛实现可能会超出团队的时间承诺，在这一点上可能不会提供太多价值。 但是，应创建简单的默认策略并将其应用于每个资源组，以强制实施少量云治理策略声明。 这用于定义特定治理要求的实现。 可在所有已部署的资产中应用这些实现。

### <a name="resource-tagging"></a>资源标记

“会计”模式已选为资源标记。

- 已部署的资产应使用以下值标记：部门/计费单元、地理位置、数据分类、临界性、SLA、环境、应用程序原型、应用程序和应用程序所有者。
- 这些值以及与已部署资产相关联的 Azure 管理组和订阅将推动治理、运营和安全决策。

### <a name="logging-and-reporting"></a>日志记录和报告

此时，建议使用“混合”模式进行日志记录和报告，但不需要任何开发团队。

- 目前没有针对为记录或报告目的而收集的数据点设置治理要求。 这仅限于此虚构说明，应视为反模式。 应尽快确定并强制执行日志记录标准。
- 在发布任何受保护数据或任务关键型工作负载之前，还需要进行其他分析。
- 支持受保护数据或任务关键型工作负载之前，必须授予现有本地操作监视解决方案对用于日志记录的工作区的访问权。 如果要定义的 SLA 支持应用程序，则需要应用程序满足与该租户的使用相关的安全性和日志记录要求。

## <a name="evolution-of-governance-processes"></a>治理进程的演变

某些策略语句不能或不应受自动化工具控制。 其他策略将需要 IT 安全性和本地标识基线团队的定期工作。 云治理团队将需要监督实现最后 8 条策略声明的以下流程：

**公司策略更改**：云治理团队将更改治理 MVP 设计，以采用新的策略。 治理 MVP 的价值在于其将考虑到新策略的自动执行。

**采用加速**：云治理团队已查看过多个团队的部署脚本。 他们维护一组用作部署模板的脚本。 云采用团队和 DevOps 团队可使用这些模板更迅速地定义部署。 每个脚本都包含强制执行治理策略的需求，不需要云采用工程师的额外工作。 作为这些脚本的管理者，他们可以更迅速地实现策略更改。 此外，他们被视为采用的加速器。 这可确保一致的部署，而无需严格执行遵从性。

**工程师培训**：云治理团队提供双月培训课程，并为工程师创建了两个视频。 这两种资源都可以帮助工程师快速了解治理文化以及如何执行部署。 该团队正在添加培训资产，以显示生产和非生产部署之间的差异，以便工程师了解新策略将如何影响采用。 这可确保一致的部署，而无需严格执行遵从性。

**部署计划**：部署任何资产包含受保护的数据之前，云管理团队将负责检查部署脚本，以验证监管对齐方式。 将使用编程工具审核具有已批准部署的现有团队。

**月度审核和报告**：云治理团队每个月都会对所有云部署进行审核，以验证是否继续与策略保持一致。 如果发现偏差，则会记录并共享给云采用团队。 当强制执行不会导致业务中断或数据泄漏时，策略会自动强制执行。 审核结束时，云治理团队将为云策略团队和每个云采用团队编译一份报告，以传达策略的整体遵从情况。 此外，也将存储本报告用于审核和法律目的。

**季度策略评审**：云治理团队和云策略团队每个季度都会评审审核结果和对企业策略提出更改建议。 其中许多建议是持续改进和观察使用模式得出的结果。 批准的策略变更在后续审计周期中被整合到治理工具中。

## <a name="alternative-patterns"></a>备用模式

如果在此治理过程中选择的任何模式与读者的要求不一致，则可以使用每种模式的替代方法：

- [加密模式](../../../decision-guides/encryption/overview.md)
- [标识模式](../../../decision-guides/identity/overview.md)
- [日志记录和报告模式](../../../decision-guides/log-and-report/overview.md)
- [策略强制执行模式](../../../decision-guides/policy-enforcement/overview.md)
- [资源一致性模式](../../../decision-guides/resource-consistency/overview.md)
- [资源标记模式](../../../decision-guides/resource-tagging/overview.md)
- [软件定义的网络模式](../../../decision-guides/software-defined-network/overview.md)
- [订阅设计模式](../../../decision-guides/subscriptions/overview.md)

## <a name="next-steps"></a>后续步骤

实现本指南后，每个云采用团队都可以建立健全的治理基础。 云治理团队将并行工作，不断更新公司策略和治理规则。

两个团队将使用公差指标来确定继续支持云采用所需的下一步发展。 在此过程中，公司的下一步是发展其治理基线，以支持具有旧版或第三方多重身份验证 (MFA) 需求的应用程序。

> [!div class="nextstepaction"]
> [标识基线演变](./identity-baseline-evolution.md)
