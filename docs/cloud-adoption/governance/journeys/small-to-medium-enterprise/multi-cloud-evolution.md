---
title: CAF：中小型企业 - 多云演变
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 阐释：中小型企业 – 多云演变
author: BrianBlanchard
ms.openlocfilehash: a5b09c92acc4e165590b5a35827b88b0ca099bed
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900639"
---
# <a name="small-to-medium-enterprise-multi-cloud-evolution"></a>中小型企业：多云演变

本文通过针对多云采用添加控制，对叙述性内容进行了演化。

## <a name="evolution-of-the-narrative"></a>叙述性内容的演变

Microsoft 意识到客户正在为特定的目的采用多个云。 在这一过程中虚构的客户也不例外。 在采用 Azure 的同时，该公司的成功导致其收购了一个互补的小型企业。 该业务在不同的云服务提供商上运行其所有的 IT 操作。

本文介绍集成这家新组织时的事态变化。 出于说明的目的，让我们假设该公司已完成此客户旅程中概述的每一项治理变革。

### <a name="evolution-of-the-current-state"></a>当前状态的演变

在此叙述性内容的上一阶段中，公司已开始通过 CI/CD 管道积极地将生产应用程序推送到云。

自此，一些事态已改变，将会影响到治理：

- 标识由 Active Directory 的本地实例控制。 复制到 Azure Active Directory 有助于实现混合标识。
- IT 操作或云操作很大程度上由 Azure Monitor 和相关的自动化进行管理。
- 灾难恢复/业务连续性受 Azure Vault 实例控制。
- Azure 安全中心用于监视安全违规和攻击。
- Azure 安全中心和 Azure Monitor 都用于监视云管理。
- Azure 蓝图、Azure Policy 和 Azure 管理组用于自动遵守策略。

### <a name="evolution-of-the-future-state"></a>未来状态的演变

目标是尽可能将收购公司整合到现有业务中。

## <a name="evolution-of-tangible-risks"></a>有形风险的演变

**业务购置成本**：收购新业务预计将在大约五年内实现盈利。 由于回报慢，董事会希望尽可能多地控制购置成本。 成本控制和技术集成之间存在相互冲突的风险。

此业务风险可以扩展为几种技术风险：

- 云迁移可能会产生额外的购置成本
- 新环境可能未正确进行管理，这可能会导致违反策略。

## <a name="evolution-of-the-policy-statements"></a>策略语句的演变

以下策略的更改将有助于缓解新的风险，同时有助于实施指南。

1. 必须通过现有运营管理和安全监视工具来监视辅助云中的所有资产
2. 所有组织单位必须集成到现有标识提供者中
3. 主要标识提供者应对辅助云中的资产管理身份验证

## <a name="evolution-of-the-best-practices"></a>最佳做法的演变

本文的这一部分将改进治理 MVP 设计，以包括新的 Azure 策略和 Azure 成本管理的实现。 这两项设计变更将共同实现新的公司策略语句。

1. 连接网络。 此步骤由网络和 IT 安全团队执行，并由云治理团队提供支持。 添加从 MPLS/租用线路提供商到新云的连接会集成网络。 添加路由表和防火墙配置将控制环境之间的访问和流量。
2. 合并标识提供者。 根据托管在辅助云中的工作负荷，标识提供者的合并有多种选择。 下面是几个示例：
    1. 对于使用 OAuth 2 进行身份验证的应用程序，辅助云中来自 Active Directory 的用户可以轻松地复制到现有 Azure AD 租户中。 这可确保所有用户都可以在租户中进行身份验证。
    2. 在另一个极端，联合使 OU 可以流入本地 Active Directory，然后流入 Azure AD 实例。
3. 将资产添加到 Azure Site Recovery。
    1. Azure Site Recovery 从一开始就是作为混合/多云工具设计的。
    2. 辅助云中的 VM 可以由用于保护本地资产的同一个 Azure Site Recovery 流程进行保护。
4. 将资产添加到 Azure 成本管理
    1. Azure 成本管理从一开始就是作为多云工具设计的。
    2. 辅助云中的虚拟机可能与某些云提供商的 Azure 成本管理兼容。 可能会收取额外费用。
5. 将资产添加到 Azure Monitor。
    1. Azure Monitor 从一开始就是作为混合云工具设计的。
    2. 辅助云中的虚拟机可能与 Azure Monitor 代理兼容，从而使它们可以包含在 Azure Monitor 中以进行操作监视。
6. 治理强制执行工具：
    1. 治理强制执行特定于云。
    2. 在治理过程中建立的公司策略不特定于云。 尽管云和云之间的实现不尽相同，但策略可以应用于辅助提供程序。

随着多云采用的增长，上述设计演变将继续，变得成熟。

## <a name="conclusion"></a>结束语

本系列的文章概述了治理最佳做法的演变（与此虚构公司的体验保持一致）。 通过从小而正确的基础开始，公司可以快速行动，同时仍在合适的时间应用适量的治理。 MVP 本身并不保护客户。 而是创造基础来缓解风险并增加保护功能。 在此基础上应用了各层治理来缓解具体风险。 此处提供的确切过程不会 100% 符合所有读者的体验。 而是充当一种增量治理模式。 建议读者塑造这些最佳做法，以适合自己的独有约束和治理要求。