---
title: CAF：架构决策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解云采用框架中的架构决策指南。
author: rotycenh
ms.openlocfilehash: b43047b5fc5b636bc84b9f28ec24846730e63672
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900615"
---
# <a name="architectural-decision-guides"></a>架构决策指南

云采用框架中的架构决策指南介绍了在创建云治理设计指南时可以提供帮助的模式和模型。 每个决策指南侧重于云部署的一个核心基础结构组件，并列出用于支持特定云部署场景的潜在模式或模型。

当开始为组织建立云治理时，可操作的治理过程将提供一个基线路线图。 然而，这些过程做出有关需求和优先级的假设，但这些假设可能并不能反映组织的需求和优先级。
这些决策指南通过提供可选的模式和模型来补充示例治理过程，这些模式和模型可有助于使示例设计指南中所做的架构设计选择符合自己的需求。

## <a name="design-guidance-categories"></a>设计指南类别

以下每个类别分别代表所有云部署的一项基础技术。 对于示例设计过程中与需求不匹配的每个选择，请检查下面相关部分中的选项，以选择更适合你需求的模式或模型。

[订阅](./subscriptions/overview.md)：规划云部署的订阅设计和帐户结构，以匹配组织的所有权、计费和管理功能。

[标识](./identity/overview.md)：将基于云的标识服务与现有标识资源集成，以支持 IT 环境中的访问控制。

[策略强制实施](./policy-enforcement/overview.md)：为部署到云的资源和工作负载定义和强制实施组织策略规则。

[资源一致性](./resource-consistency/overview.md)：确保基于云的资源的部署和组织一致，以实现资源管理和策略需求。

[资源标记](./resource-tagging/overview.md)：组织基于云的资源以支持计费模型、云核算方法、管理、访问控制和运营效率。 资源标记需要一致的、有条理的命名和元数据方案。

[软件定义的网络](./software-defined-network/overview.md)：使用虚拟网络功能的快速部署和修改，将安全工作负载部署到云中。 软件定义的网络 (SDN) 可以支持敏捷工作流、隔离资源，并将基于云的系统与现有 IT 基础结构集成。

[加密](./encryption/overview.md)：使用加密保护敏感数据，这是云部署中的一个重要安全方面。

[日志和报告](./log-and-report/overview.md)：监视由基于云的资源生成的日志数据。 分析数据为工作负载的操作、维护和策略强制实施状态提供了与运行状况相关的见解。

## <a name="next-steps"></a>后续步骤

了解订阅和帐户如何作为云部署的基础。

> [!div class="nextstepaction"]
> [订阅设计](subscriptions/overview.md)
