---
title: CAF：软件定义网络
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 关于软件定义的网络作为 Azure 迁移中的核心服务的讨论
author: rotycenh
ms.openlocfilehash: d164ba488552715dc97719329ae9de3fcf5d83ed
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243338"
---
# <a name="caf-software-defined-network-decision-guide"></a>CAF：软件定义网络决策指南

软件定义网络 (SDN) 是一种网络体系结构，旨在实现可通过软件集中管理、配置和修改的虚拟化网络功能。 SDN 通过物理网络基础结构提供抽象层，并支持物理路由器、防火墙和其他可在本地网络中找到的网络硬件的虚拟化等效项。

SDN 允许 IT 人员使用虚拟资源配置和部署支持工作负荷需求的网络结构和功能。 基于软件的部署管理的灵活性支持快速修改网络资源并能够同时支持敏捷和传统部署模型。 使用 SDN 技术创建的虚拟网络对在公有云平台上创建安全的网络至关重要。

## <a name="networking-decision-guide"></a>网络决策指南

![绘制复杂性从最低到最高的网络选项，并与下面的跳转链接保持一致](../../_images/discovery-guides/discovery-guide-sdn.png)

跳转到：[仅限 PaaS](paas-only.md) | [云原生](cloud-native.md) | | [云外围网络](cloud-dmz.md)[混合](hybrid.md) | [中心/分支模型](hub-spoke.md) | [了解更多](#learn-more)

SDN 提供具有不同程度的定价和复杂性的多个选项。 上述发现指南提供了对这些选项快速进行个性化的参考，以便符合具体的业务和技术策略。

本指南中的转折点取决于云策略团队就网络基础结构作出决策前所作的多个关键决策。 其中最重要的是涉及[数字资产定义](../../digital-estate/overview.md)和[订阅设计](../subscriptions/overview.md)的决策（可能还需要与云帐户和全球市场策略相关的决策提供意见）。

具有少于 1,000 个 VM 的小型、单区域部署不太可能会显著影响此转折点。 相反，具有超过 1,000 个 VM、多个业务单位或多个地缘政治市场的大规模采用可能会因 SDN 决策和此关键转折点受到显著影响。

## <a name="choosing-the-right-virtual-networking-architectures"></a>选择适当的虚拟网络体系结构

本部分就决策指南进行展开，帮助你选择适当的虚拟网络体系结构。

可通过多种方法来实现 SDN 技术，用于创建基于云的虚拟网络。 如何构建迁移中使用的虚拟网络以及这些网络如何与现有 IT 基础结构进行交互，具体取决于工作负荷要求和治理要求的结合。

计划在规划云迁移时考虑哪种虚拟网络体系结构或体系结构组合时，请考虑以下问题，帮助确定最适合组织的体系结构：

| 问题 | 仅限 PaaS | 云原生 | 云外围网络 | 混合 | 中心和分支 |
|-----|-----|-----|-----|-----|-----|
| 除服务自身提供的网络功能外，工作负荷是否仅使用 PaaS 服务，不需要其他任何网络功能？ | 是 | 否 | 否 | 否 | 否 |
| 工作负荷是否要求与本地应用程序集成？ | 否 | 否 | 是 | 是 | 是 |
| 是否已在本地和云网络之间建立成熟的安全策略和安全连接？ | 否 | 否 | 否 | 是 | 是 |
| 工作负荷是否需要不通过云标识服务支持的身份验证服务，或者是否需要直接访问本地域控制器？ | 否 | 否 | 否 | 是 | 是 |
| 是否需要部署和管理大量 VM 和工作负荷？ | 否 | 否 | 否 | 否 | 是 |
| 向各个工作负荷团队委派对资源的控制时，是否需要提供集中管理和本地连接？ | 否 | 否 | 否 | 否 | 是 |

## <a name="virtual-networking-architectures"></a>虚拟网络体系结构

详细了解主软件定义的网络体系结构：

- [**仅限 PaaS**](paas-only.md)：平台即服务 (PaaS) 产品支持有限的内置网络功能集，并可能不需要显式定义的软件定义网络即可支持工作负荷要求。
- [**云原生**](cloud-native.md)：云原生虚拟网络是将资源部署到云平台时默认的软件定义网络体系结构。
- [**云外围网络**](cloud-dmz.md)：在本地和云网络之间提供有限的连接，通过云环境上的某个外围安全区域进行保护。
- [**混合**](hybrid.md)：混合云网络体系结构允许虚拟网络访问本地资源，反之亦然。
- [**中心和分支**](hub-spoke.md)：使用中心和分支体系结构，可以集中管理外部连接和共享服务，隔离各个工作负荷并克服潜在的订阅限制。

## <a name="learn-more"></a>了解详细信息

有关 Azure 平台中的软件定义网络的详细信息，请参阅以下内容。

- [Azure 虚拟网络](/azure/virtual-network/virtual-networks-overview)。 在 Azure 上，由 Azure 虚拟网络提供核心的 SDN 功能，作为物理本地网络的云模拟。 虚拟网络还在平台上充当资源之间的默认隔离边界。
- [Azure 网络安全最佳做法](/azure/security/azure-security-network-security-best-practices)。 Azure 安全团队关于如何配置虚拟网络的建议，以最大程度减少安全漏洞。

## <a name="next-steps"></a>后续步骤

了解运营团队如何利用日志、监视和报告来管理云工作负荷的运行状况和策略符合性。

> [!div class="nextstepaction"]
> [日志和报告](../log-and-report/overview.md)
