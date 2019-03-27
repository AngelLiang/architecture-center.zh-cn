---
title: CAF：安全基线动机和业务风险
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 安全基线动机和业务风险
author: BrianBlanchard
ms.openlocfilehash: 8407ed358e5862e466176096ee6a82ad792027cb
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58247308"
---
# <a name="security-baseline-motivations-and-business-risks"></a>安全基线动机和业务风险

本文讨论客户通常在云治理策略中采用安全基线规则的原因。 此外，还提供了可能驱动策略语句的几个潜在业务风险示例。

<!-- markdownlint-disable MD026 -->

## <a name="is-a-security-baseline-relevant"></a>与安全基线相关吗？

安全性是所有 IT 组织的关键考虑事项。 云部署面临许多与传统本地数据中心内托管的工作负荷相同的安全风险。 但是，公共云平台的性质（即缺乏对存储和运行工作负荷的物理硬件的直接所有权）意味着云安全需要自己的策略和流程。

将云安全治理与传统安全策略区分开来的主要区别之一在于是否可以轻松选择要创建的资源，如果在部署之前未考虑安全性，则可能会增加漏洞。 [软件定义网络 (SDN)](../../decision-guides/software-defined-network/overview.md) 等技术为快速更改基于云的网络拓扑而提供的灵活性也可能以不可预见的方式轻松修改整个网络攻击面。 云平台还提供能够以在本地环境中无法实现的方式提高安全性能的工具和功能。

投入安全策略和流程的金额将在很大程度上取决于云部署的性质。 初始测试部署可能只需要最基本的安全策略，而任务关键工作负荷则需要解决复杂而广泛的安全需求问题。 所有部署都需要在某种程度上使用该规则。

安全基线规则涵盖可用于保护云部署免受安全风险影响的企业策略和手动流程。

> [!NOTE]
>虽然在安全基线的上下文中理解[标识基线](../identity-baseline/overview.md)及其如何与访问控制相关很重要，但[云治理的五项规则](../overview.md)将[标识基线](../identity-baseline/overview.md)称为独立于安全基线的独立规则。

## <a name="business-risk"></a>业务风险

安全基线规则尝试解决安全相关的核心业务风险问题。 与企业合作来确定这些风险，并在规划和实现云部署时监视每个风险的相关性。

组织间的风险会有所不同，以下是常见的与安全相关的风险，你可以由此在云治理团队中展开讨论：

- **数据安全漏洞**。 无意中泄露或丢失敏感的云托管数据可能导致客户流失、合同问题或法律后果。
- **服务中断**。 因不安全的基础结构而导致的中断和其他性能问题会中断正常运行，并可能导致生产力损失或业务丢失。

## <a name="next-steps"></a>后续步骤

使用[云管理模板](./template.md)，记录可能由当前云采用计划引入的业务风险。

确立了对实际业务风险的了解后，下一步是记录业务对风险的容忍度，以及用于监视该容忍度的指示器和关键指标。

> [!div class="nextstepaction"]
> [了解指示器、指标和风险容忍度](./metrics-tolerance.md)
