---
title: CAF：定义公司策略语句
description: 如何建立策略以反映和缓解风险？
author: BrianBlanchard
ms.date: 01/02/2019
ms.openlocfilehash: 97a2c25fea621e5418e505375eb0e80007ddb0de
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900638"
---
<!---
I understand risk and tolerance, now what do I do?
Define the policy... [aspirational statement to move towards 2/1] If you need help defining policies, each discipline includes references to common business risks and policies to mitigate the risks...
--->

# <a name="defining-corporate-policy-for-cloud-governance"></a>为云治理定义公司策略

为组织的云转换过程分析了已知风险和相关风险容忍度之后，下一步是建立策略，以便明确解决这些风险并定义尽可能缓解这些风险所需的步骤。

<!-- markdownlint-disable MD026 -->

## <a name="how-can-corporate-it-policy-become-cloud-ready"></a>如何使企业 IT 策略处于云就绪状态？

在传统治理和增量治理中，公司策略创建了治理的工作定义。 大多数 IT 治理操作以期实现技术来监视、执行、操作和自动化这些公司策略。 云治理基于类似的概念构建而成。

![公司治理和治理规则](../../_images/operational-transformation-govern.png)

*图 1.公司治理和治理规则。*

上图演示了业务风险、策略和符合性、以及监视和强制执行之间的交互，以创建治理策略。 后跟五大云治理原则来实现策略。

云治理是随着时间的推移持续采用工作的产物，因为真正持久的转换不会在一夜之间发生。 在使用快速主动的方法处理关键公司策略更改之前，尝试交付完整的云治理很少会生成期望的结果。 相反，我们建议采用增量方法。

云采用框架的不同之处在于购买周期以及该周期如何真正实现转换的影响。 由于没有大的资本支出 (CapEx) 收购要求，工程师可以更快地开始试验和采用。 在大多数企业文化中，消除采用 CapEx 的障碍可能导致更紧密的反馈循环、有机增长和增量执行。

向云采用转变需要进行治理转变。 在许多组织中，公司策略转换允许通过增量策略更改和这些更改的自动执行来改进治理和提高遵从率，由你与云服务提供商配置的新定义功能提供支持。

<!-- markdownlint-enable MD026 -->

## <a name="review-existing-policies"></a>评审现有策略

随着云部署逐渐成熟以及迁移到云的 IT 资产量不断增加，风险和关联策略需求也会发生变化。 治理是一个持续过程，应与 IT 人员和利益干系人一起定期评审策略，以确保在云中托管的资源继续符合整体公司目标和要求。 对新风险的理解和可接受的容忍度可以促进[对现有策略的审查](what-is-a-cloud-policy-review.md)，从而确定适合贵组织的治理级别。

> [!TIP]
> 如果组织受第三方合规机构的治理，则要考虑的最大业务风险之一可能是遵守[法规](what-is-regulatory-compliance.md)的风险。 通常情况下这种风险无法避免，转而可能要求严格遵从。 请务必在开始策略评审之前了解第三方符合性要求。

## <a name="create-cloud-policy-statements"></a>创建云策略语句

基于云的 IT 策略建立 IT 人员和自动化系统需要支持的要求、标准和目标。 策略决策是[云体系结构设计](align-governance-journeys.md)和[策略遵循过程](processes.md)实现方式中的一个主要因素。

各个云策略语句是解决在风险评估过程中确定的特定风险的指导原则。 这些策略可以集成到更广泛的公司策略文档中，而在整个 CAF 指导中讨论的云策略语句往往是风险和应对计划的更简明摘要。 每个定义应包括以下这些信息：

- **业务风险**。 此策略将解决的风险的摘要。
- **策略语句**。 策略要求和目标的清楚说明。
- **设计或技术指导**。 用于支持和强制执行此策略的可操作建议、规范或其他指导，IT 团队和开发人员在设计和构建其云部署时可以使用这些内容。

如果需要帮助开始定义策略，请查阅治理概述部分中介绍的[治理规则](../governance-disciplines.md)。 适用于每个规则的文章包括有关在迁移到云时遇到的常见业务风险的示例，以及用于缓解这些风险的示例策略（例如， 请参阅成本管理规则的[示例策略定义](../cost-management/policy-statements.md)）。

## <a name="incremental-governance-and-integrating-with-existing-policy"></a>增量治理以及与现有策略集成

云环境的计划增加应始终进行审查以符合现有策略，并且策略应进行更新以应对尚未涵盖的任何问题。 还应执行定期[云策略评审](what-is-a-cloud-policy-review.md)，以确保云策略是最新状态并且与任何新公司策略保持同步。

将云策略与旧 IT 策略集成的需求很大程度上取决于云治理流程的成熟度和云资产的规模。 请参阅有关[增量治理和策略 MVP](overview.md) 的文章，以了解有关在云转换过程中处理策略集成的更广泛讨论。

## <a name="next-steps"></a>后续步骤

定义策略之后，需草拟体系结构设计指南，以便向 IT 人员和开发人员提供可操作的指导。

> [!div class="nextstepaction"]
> [草拟体系结构设计指南](align-governance-journeys.md)
