---
title: CAF：中小型企业 - 有关治理 MVP 的其他技术详细信息
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 中小型企业的说明 - 有关治理 MVP 的其他技术详细信息
author: BrianBlanchard
ms.openlocfilehash: e726213459c8bee63e3cc77ab54868fe7196b3ac
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900520"
---
# <a name="small-to-medium-enterprise-best-practice-explained"></a>中小型企业：最佳做法介绍

治理过程由一组初始[企业策略](./initial-corporate-policy.md)开始。 这些策略用于建立反映[最佳做法](./overview.md)的治理 MVP。

在本文中，我们将讨论创建治理 MVP 所需的高级策略。 治理 MVP 的核心是[部署加速](../../deployment-acceleration/overview.md)规则。 此阶段应用的工具和模式将实现未来扩大治理所需的增量演变。

## <a name="governance-mvp-cloud-adoption-foundation"></a>治理 MVP（云采用基础）

得益于多个简单原则和基于云的治理工具，可实现快速采用治理和公司策略。 这些是在任何治理进程中的三个云治理规则的第一个。 本文将充分叙述每个规则。

为了建立基础，本文将讨论创建治理 MVP 所需的标识基线、安全基线和部署加速背后的高级策略，它将作为所有采用的基础。

![增量治理 MVP 的示例](../../../_images/governance/governance-mvp.png)

## <a name="implementation-process"></a>实现进程

治理 MVP 的实现对标识、安全性和网络具有依赖关系。 解决依赖项后，云治理团队将决定治理的几个方面。 来自云治理团队和支持团队的决策将通过强制资产的单个包来实现。

![增量治理 MVP 的示例](../../../_images/governance/governance-mvp-implementation-flow.png)

还可以使用简单的清单来描述此实现：

1. 征求关于核心依赖关系的决定：标识、网络和加密。
2. 确定要在企业策略强制执行期间使用的模式。
3. 为资源一致性、资源标记、以及日志记录和报告规则确定合适的治理模式。
4. 实施与所选策略强制执行模式一致的治理工具，以应用依赖决策和治理决策。

[!INCLUDE [implementation-process](../../../../../includes/cloud-adoption/governance/implementation-process.md)]

## <a name="application-of-governance-defined-patterns"></a>治理定义模式的应用程序

云治理团队负责以下决策和实现。 许多人需要其他团队的意见，但云治理团队可能同时拥有决策和实施。 下列部分概述了针对此用例的决策以及每个决策的详细信息。

### <a name="subscription-model"></a>订阅模型

已为 Azure 订阅选择“应用程序类别”模式。

- 应用程序原型是一种对具有类似需求的应用程序进行分组的方法。 常见示例包括：具有受保护数据的应用程序、受治理的应用程序（例如 HIPAA 或 FedRAMP）、低风险应用程序、具有本地依赖项的应用程序、Azure 中的 SAP 或其他大型机、或扩展本地 SAP 或大型机的应用程序。 根据数据分类和支持业务的应用程序类型，这些原型在每个组织中都是独一无二的。 数字资产的依赖项映射可帮助在组织中定义应用程序原型。
- 鉴于目前的重点，不太可能需要部门。 预计部署将限制在单个计费单元内。 在采用阶段，甚至可能没有企业协议来集中计费。 此级别的采用很有可能由单个即用即付的 Azure 订阅进行管理。
- 不考虑 EA 门户的使用或企业协议的存在，仍然应该定义并同意订阅模型，以最大限度地减少管理成本，而不仅仅是计费。
- 在“应用程序类别”模式中，为每个应用程序原型创建订阅。 每个订阅属于每个环境（开发、测试和生产）的帐户。
- 作为订阅设计的一部分，应根据前两点同意共同的命名约定。

### <a name="resource-consistency"></a>资源一致性

已选择“部署一致性”作为资源一致性。

- 为每个应用程序创建资源组。 为每个应用程序原型创建管理组。 Azure Policy 应该应用于所有来自相关管理组的订阅。
- 作为部署过程的一部分，资源组的 Azure 资源一致性模板应该存储在源代码管理中。
- 每个资源组都和特定的工作负荷或应用程序相关联。
- 随着公司政策的成熟，Azure 管理组可以更新治理设计。
- Azure Policy 的广泛实施可能超出团队的时间承诺，目前可能无法提供大量值。 但是，应创建简单的默认策略并将其应用于每个管理组，以强制实施少量的当前云治理策略声明。 此策略将定义特定治理要求的实现。 可在所有已部署的资产中应用这些实现。

### <a name="resource-tagging"></a>资源标记

已选择要标记的“分类”模式作为资源标记的模型。

- 已部署的资产应使用以下值标记：数据分类、紧迫性、SLA 和环境。
- 这四个值将决定治理、操作和安全决策。
- 如果正在为大型公司内的业务部门或团队实施此治理之旅，则标记还应包括计费单元的元数据。

### <a name="logging-and-reporting"></a>日志记录和报告

此时，建议使用“云原生”模式进行日志记录和报告，但不需要任何开发团队。

- 没有针对为日志记录或报告目的而收集的数据设置治理要求。
- 在发布任何受保护数据或任务关键型工作负荷之前，还需要进行其他分析。

## <a name="evolution-of-governance-processes"></a>治理进程的演变

随着治理的发展，一些策略声明不能或不应受自动化工具控制。 其他策略将导致 IT 安全团队和本地标识管理团队随着时间的推移而付出努力。 为了帮助避免出现新的风险，云治理团队将监督以下进程。

**采用加速**：云治理团队已查看过多个团队的部署脚本。 它们维护一组用作部署模板的脚本。 云采用和 DevOps 团队使用这些模板来更快地定义部署。 这些脚本的每一个都包含强制执行多个治理策略的必要需求，且不需要云采用工程师的任何额外协助。 作为这些脚本的管理人，云治理团队可以更快地实现策略更改。 作为脚本管理的结果，云治理团队被视为采用加速的来源。 这将在部署之间创建一致性，但没有严格强制遵守。

**工程师培训**：云治理团队提供双月培训课程，并为工程师创建了两个视频。 这些资料帮助工程师快速了解治理文化和部署期间的工作方式。 该团队正在添加培训资产，以显示生产和非生产部署之间的差异，以便工程师了解新策略将如何影响采用。 这将在部署之间创建一致性，但没有严格强制遵守。

**部署计划**：部署任何包含受保护数据的资产之前，云治理团队将查看部署脚本，以验证治理对齐方式。 将使用编程工具审核具有已批准部署的现有团队。

**月度审核和报告**：云治理团队每个月都会对所有云部署进行审核，以验证是否继续与策略保持一致。 如果发现偏差，则会记录并共享给云采用团队。 当强制执行不会导致业务中断或数据泄漏时，策略会自动强制执行。 审核结束时，云治理团队将为云策略团队和每个云采用团队编译一份报告，以传达策略的整体遵从情况。 此外，也将存储本报告用于审核和法律目的。

**季度策略评审**：云治理团队和云策略团队每个季度都会评审审核结果并对公司策略提出更改建议。 其中许多建议是持续改进和观察使用模式得出的结果。 批准的策略变更在后续审计周期中被整合到治理工具中。

## <a name="alternative-patterns"></a>备用模式

如果在此治理之旅中选择的任何模式与读者的要求不一致，则可以使用每种模式的替代方法：

- [加密模式](../../../decision-guides/encryption/overview.md)
- [标识模式](../../../decision-guides/identity/overview.md)
- [日志记录和报告模式](../../../decision-guides/log-and-report/overview.md)
- [策略强制执行模式](../../../decision-guides/policy-enforcement/overview.md)
- [资源一致性模式](../../../decision-guides/resource-consistency/overview.md)
- [资源标记模式](../../../decision-guides/resource-tagging/overview.md)
- [软件定义的网络模式](../../../decision-guides/software-defined-network/overview.md)
- [订阅设计模式](../../../decision-guides/subscriptions/overview.md)

## <a name="next-steps"></a>后续步骤

实施本指南后，每个云采用团队都可以建立健全的治理基础。 云治理团队将并行工作，不断更新公司策略和治理规则。

两个团队将使用公差指标来确定继续支持云采用所需的下一步发展。 对于此旅程中的虚拟公司而言，下一步是发展安全基线，以支持将受保护的数据移至云端。

> [!div class="nextstepaction"]
> [安全基线演变](./security-baseline-evolution.md)