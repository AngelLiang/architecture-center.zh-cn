---
title: CAF：大型企业 – 标识基线演变
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企业 – 标识基线演变
author: BrianBlanchard
ms.openlocfilehash: efda14819bfbc70632dc9bb8b4c6c5aca96004c0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900523"
---
# <a name="large-enterprise-identity-baseline-evolution"></a>大型企业：标识基线演变

本文通过将标识基线控制添加到治理 MVP 中，对叙述性内容进行了演化。

## <a name="evolution-of-the-narrative"></a>叙述性内容的演变

CFO 批准了两个数据中心云迁移的业务理由。 在技术可行性研究中，发现了几个障碍：

- 受保护数据和任务关键型应用程序占两个数据中心 25% 的工作负荷。 在实现 PII 和任务关键型应用程序相关当前治理策略的现代化之前，无法消除这两项工作负荷。
- 这两个数据中心内 7% 的资产与云并不兼容。 在终止数据中心协定之前，它们将被移动到备用数据中心。
- 数据中心内 15% 的资产（750 个虚拟机）依赖于旧身份验证或第三方多重身份验证。
- 连接现有数据中心和 Azure 的 VPN 连接无法提供足够的数据传输速度或延迟，也就无法为停用数据中心在两年的限期内迁移所有资产。

前两个障碍都已得到解决。 本文将讨论针对后两个障碍的解决方案。

### <a name="evolution-of-the-cloud-governance-team"></a>云治理团队的演变

云治理团队在发展壮大。 鉴于在标识管理方面需要额外的支持，现在标识基线团队的系统管理员会参加周会，便于现有团队成员了解各项变化。

### <a name="evolution-of-the-current-state"></a>当前状态的演变

IT 团队已获得批准，准备按照 CIO 和 CFO 的计划停用两个数据中心。 但却担心必须将这两个数据中心内 750 项 (15%) 资产移动到除云之外的某个位置。

### <a name="evolution-of-the-future-state"></a>未来状态的演变

新的未来状态计划需要一个更稳健的标识基线解决方案，用于迁移要求使用旧身份验证的 750 个虚拟机。 除了这两个数据中心之外，其他数据中心内占差不多比例的资产预计也会受到这一难题的影响。
未来状态现在还需要从云提供商连接到公司的 MPLS/租用线解决方案。

当前和未来状态的变化揭示了新的风险，需要新的策略声明。

## <a name="evolution-of-tangible-risks"></a>有形风险的演变

**迁移期间业务中断**。 迁移到云会产生有时限的可控风险，这样的风险是可管理的。 迁移老化硬件的风险要高得多。 需要通过风险缓解策略避免业务运营的中断。

**现有标识依赖关系**。 对现有身份验证和标识服务的依赖可能会延迟或阻止某些工作负荷迁移到云。 未能按时返还两个数据中心会在数据中心租用中产生数百美元的费用。

此业务风险可以扩展为几种技术风险：

- 旧身份验证在云中可能无法使用，这会限制某些应用程序的部署。
- 目前的第三方 MFA 解决方案在云中可能无法使用，这会限制某些应用程序的部署。
- 重新调整或移动可能会造成中断并增加成本。
- VPN 的速度和稳定性可能会影响迁移。
- 传入云的流量可能会导致全球网络的其他部分出现安全问题。

## <a name="evolution-of-the-policy-statements"></a>策略语句的演变

以下策略的更改将有助于缓解新的风险，同时有助于实施指南。

1. 选择的云提供商必须提供一种通过旧方法进行身份验证的方式。
2. 选择的云提供商必须提供一种使用目前的第三方 MFA 解决方案进行身份验证的方式。
3. 应在云提供商和公司的电信提供商之间建立高速专用连接，将云提供商连接到全球数据中心网络。
4. 在制定稳妥的安全要求之前，入站公共流量不得访问云中托管的公司资产。 所有端口都阻止来自全局 WAN 之外的任何源。

## <a name="evolution-of-the-best-practices"></a>最佳做法的演变

治理 MVP 设计将不断演进以包括新的 Azure 策略和虚拟上 Active Directory 的实现。 这两项设计变更共同实现新的企业策略声明。

以下是新的最佳做法：

1. DMZ 蓝图：应将本地 DMZ 配置为允许以下解决方案与本地 Active Directory 服务器通信。 本最佳做法需要 DMZ 才能跨网络边界启用 Active Directory 域服务。
2. Azure 资源管理器模板：
    1. 定义一个 NSG，用于阻止外部流量并将内部流量列入白名单。
    1. 基于黄金映像在负载平衡对中部署两个 AD 虚拟机。 首次启动时，该映像运行 PowerShell 脚本以加入域并注册域服务。 有关详细信息，请参阅[将 Active Directory 域服务 (AD DS) 扩展到 Azure](../../../../reference-architectures/identity/adds-extend-domain.md)。
3. Azure Policy：将 NSG 应用于所有资源。
4. Azure 蓝图
    1. 创建名为 `active-directory-virtual-machines` 的蓝图。
    1. 将每个 AD 模板和策略添加到蓝图。
    1. 将蓝图发布到任何适用的管理组。
    1. 将蓝图应用于任何需要旧身份验证或第三方 MFA 身份验证的订阅。
    1. 现可将在 Azure 中运行的 AD 实例用作本地 AD 解决方案的扩展，使它能与现有 MFA 工具集成并通过现有的 Active Directory 功能提供基于声明的身份验证。

## <a name="conclusion"></a>结束语

将这些更改添加到治理 MVP 有助于降低本文提到的许多风险，使每个云采用团队能快速跨越此障碍。

## <a name="next-steps"></a>后续步骤

随着云采用的演进和业务附加值的提升，风险和云治理需求也在不断发展。 以下是一些可能会发生的演变。 对于本过程中的虚构公司而言，下一个触发器是云采用计划中包含受保护数据。 此更改将要求其他安全控制措施。

> [!div class="nextstepaction"]
> [安全基线演变](./security-baseline-evolution.md)
