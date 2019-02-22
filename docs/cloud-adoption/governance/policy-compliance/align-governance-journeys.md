---
title: CAF：使设计指南与策略保持一致。
description: 如何使设计指南与策略保持一致？
author: BrianBlanchard
ms.date: 01/04/2019
ms.openlocfilehash: 77a35597585e5f58967ea79d3c7b0fa17b6ab80e
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900463"
---
<!---
I've established policies. How to help developers adopt these policies?
Draft an architecture design guide.

[Aspirational statement] If you're using Azure, you can use one of ours as a starting point. The choose one of the following 6 as a starting point and mold it to fit your policies.
--->

<!-- markdownlint-disable MD026 -->

# <a name="how-do-you-align-design-guides-with-policy"></a>如何使设计指南与策略保持一致？

在基于[标识的风险](define-policy.md)[定义云策略](understanding-business-risk.md)后，你将需要为你的 IT 人员和开发者生成与这些策略一致的可操作指南，以供参考。 通过起草云治理设计指南，使你能够基于为这五个治理规则分别生成的策略声明来指定特定结构化、技术和过程选择。

云治理设计指南应为每个云部署的核心基础结构组件建立体系结构选择和设计模式，以最好地满足你的策略要求。 除此之外，你应针对支持每个设计决策的技术、工具和过程提供概括说明。

尽管在某种程度上，你的风险分析和策略声明可能与云平台无关，但你的设计指南应提供特定于平台的实现细节，以供 IT 人员和开发者使用。 在制定设计决策和提供指南时，请重点说明体系结构、工具以及所选的平台的功能。

尽管云设计指南应考虑与每个基础结构组件相关联的部分技术细节，但其并不是全面的技术文档或规范。 请确保指南采用员工易于理解和参考的方式阐释所有策略声明和明确的设计决策。

<!-- markdownlint-enable MD033 -->

## <a name="using-the-actionable-governance-journeys"></a>使用可操作的治理过程

如果你使用 Azure 平台才采用云，CAF 将提供[治理过程](../journeys/overview.md)，以说明 CAF 治理模型的增量方法。 这些描述性过程涵盖了一系列常见的采用方案，其中包括业务风险、容忍度要求，以及用于创建治理最小可行产品 (MVP) 的策略声明。 这些过程相当于在 Azure 中采用云过程的综合实际客户体验。

虽然每个云采用都具有唯一的目标、优先级和挑战，但这些示例应提供了将你的策略转换为指南的理想模板。 请选择最适合你情况的方案作为起点，并按照你的具体策略需要进行调整。

## <a name="next-steps"></a>后续步骤

有了设计指南后，制定策略遵循过程，以确保策略的合规性。

> [!div class="nextstepaction"]
> [策略遵循过程](processes.md)