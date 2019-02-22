---
title: CAF：资源一致性决策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解规划 Azure 迁移时的资源一致性。
author: rotycenh
ms.openlocfilehash: 8170bfd09218a451e086a57e0631b7e567eb2b82
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900516"
---
# <a name="caf-resource-consistency-decision-guide"></a>CAF：资源一致性决策指南

Azure [订阅设计](../subscriptions/overview.md)定义了如何根据组织的整体结构组织云资产。 此外，如何整合现有的 IT 管理标准和组织策略，要取决于在订阅中部署和组织云资源的方式。

可用于实现资源部署、分组和管理设计的工具将因云平台而异。 通常，每个解决方案包括以下功能：

- 订阅或帐户级别下的逻辑分组机制。
- 使用 API 以编程方式部署资源的功能。
- 用于创建标准化部署的模板。
- 在订阅、帐户和资源分组级别部署策略规则的功能。

![按复杂性从最低到最高绘制的资源一致性选项，与下面的跳转链接保持一致](../../_images/discovery-guides/discovery-guide-resource-consistency.png)

跳转到：[基本分组](#basic-grouping) | [部署一致性](#deployment-consistency) | [策略一致性](#policy-consistency) | [层次结构一致性](#hierarchical-consistency)  | [自动一致性](#automated-consistency)

资源部署和分组决策主要受以下因素驱动：迁移后的数字资产规模，不完全适合现有订阅设计方法的业务或环境复杂性，或者在部署资源后逐步强制执行治理的需求。 更高级的资源分组设计需要更多的努力来确保准确的分组，这会增加在更改管理和跟踪上所花费的时间。

## <a name="basic-grouping"></a>基本分组

在 Azure 中，[资源组](/azure/azure-resource-manager/resource-group-overview#resource-groups)是一种核心资源组织机制，用于在订阅中对资源进行逻辑分组。

资源组充当具有共同生命周期或共享管理约束（如策略或基于角色的访问控制 (RBAC) 要求）的资源的容器。 资源组无法进行嵌套，资源只能属于一个资源组。 某些操作可对资源组中的所有资源执行操作。 例如，删除某个资源组会删除该组中的所有资源。 创建资源组时有常见的模式，通常分为两类：

- “传统 IT”工作负载：通常按相同生命周期中的项（如某个应用程序）分组。 由于可按应用程序分组，因此可以管理每个应用程序。
- “敏捷 IT”工作负载：侧重于面向外部客户的云应用程序。 资源组通常反映部署的功能层（如 Web 层、应用层）和管理层。

## <a name="deployment-consistency"></a>部署一致性

基于基础资源分组机制，大多数云平台提供了一个使用模板将资源部署到云环境的系统。 在部署工作负载时，可以使用模板创建一致的组织和命名约定，强制执行资源部署和管理设计的这些方面。

借助 [Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)，可以使用预定的配置和资源组结构以一致的状态重复部署资源。 资源管理器模板可帮助用户定义一组标准作为部署的基础。

例如，你可以使用标准模板来部署包含两个虚拟机的 Web 服务器工作负载，将其作为 Web 服务器与负载均衡器结合使用，以管理服务器之间的流量。 然后，只要需要新的 Web 服务器工作负载，就可以重用此模板来创建结构相同的部署，仅更改所涉及的部署名称和 IP 地址即可。

请注意，还能够以编程方式部署这些模板并将它们与 CI/CD 系统集成。

## <a name="policy-consistency"></a>策略一致性

为确保在创建资源时应用治理策略，资源分组设计的一部分涉及在部署资源时使用通用配置。

通过组合资源组和标准化资源管理器模板，你可以强制执行部署中所需设置的标准，以及对每个资源组或资源应用的 [Azure Policy](/azure/governance/policy/overview) 规则的标准。

例如，用户可能要求订阅中部署的所有虚拟机都连接到由核心 IT 团队管理的公共子网。 你可以创建一个用于部署工作负载 VM 的标准模板，这将为工作负载创建单独的资源组，并在其中部署所需的 VM。 此资源组将具有仅允许资源组中的网络接口加入共享子网的策略规则。

有关在云部署中强制执行策略决策的更深入讨论，请参阅[策略实施](../policy-enforcement/overview.md)。

## <a name="hierarchical-consistency"></a>层次结构一致性

随着云资产规模的增长，相比使用 Azure 企业协议的企业/部门/帐户/订阅层次结构支持的需求，用户可能需要支持更为复杂的治理需求。 使用资源组，你可以在组织内支持其他级别的层次结构，在资源组级别应用 Azure Policy 规则和访问控制。

[Azure 管理组](../subscriptions/overview.md#management-groups)通过在企业协议结构之上覆盖备用层次结构可以支持更复杂的组织结构。 这使订阅及其包含的资源能够支持组织的访问控制和策略实施机制，以满足业务组织需求。

## <a name="automated-consistency"></a>自动一致性

对于大型云部署，全局治理变得更加重要和复杂。 在部署资源时自动应用和强制执行治理要求，以及满足现有部署的更新要求，这一点至关重要。

[Azure 蓝图](/azure/governance/blueprints/overview)使组织能够支持 Azure 中大型云资产的全局治理。 蓝图超越了标准 Azure 资源管理器模板提供的功能，可创建能够部署资源和应用策略规则的完整部署业务流程。 蓝图支持版本控制，能够对使用蓝图的所有订阅应用更新，以及锁定已部署订阅，以避免未经授权创建和修改资源。

使用这些部署包，IT 和开发团队能够快速部署新工作负载和网络资产，顺应不断变化的组织策略要求。 此外，蓝图还可以整合到 CI/CD 管道中，以便在更新时将修订的治理标准应用于部署。

## <a name="next-steps"></a>后续步骤

了解如何使用资源命名和标记进一步组织和管理云资源。

> [!div class="nextstepaction"]
> [资源命名和标记](../resource-tagging/overview.md)
