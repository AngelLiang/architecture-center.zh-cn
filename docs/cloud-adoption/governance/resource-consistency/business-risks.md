---
title: CAF：资源一致性动机和业务风险
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 资源一致性动机和业务风险
author: alexbuckgit
ms.openlocfilehash: 19e0d761e4afa3473099bde2edc960c8b9eadb79
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242458"
---
# <a name="resource-consistency-motivations-and-business-risks"></a>资源一致性动机和业务风险

本文讨论客户通常在云治理策略中采用资源一致性规则的原因。 此外，还提供了可能驱动策略语句的几个潜在业务风险示例。

<!-- markdownlint-disable MD026 -->

## <a name="is-resource-consistency-relevant"></a>资源一致性是否相关？

涉及到部署资源和工作负荷时，与大多数传统本地数据中心相比，云可提供更高的敏捷性和灵活性。 但是，这些基于云的潜在优势也带有潜在的管理缺陷，这些缺陷可能会严重损害云采用的成功。 部署了哪些资产？ 哪些团队拥有哪些资产？ 是否有足够的资源来支持工作负荷？ 如何知道工作负荷是否状态良好？

对于确保始终如一地重复部署、更新和配置资源，以及确保尽量减少服务中断并在尽可能少的时间内修复，资源一致性至关重要。

资源一致性规则涉及到确定并缓解与云部署运营方面相关的业务风险。 资源一致性包括监视应用程序、工作负荷和资产性能。 资源一致性还包括满足规模需求、纠正性能服务级别协议 (SLA) 违规以及通过自动补救主动避免性能 SLA 违规所需的任务。

初始测试部署可能不需要比采用一些粗略的命名和标记标准超出太多，即可支持资源一致性需求。 随着云采用逐渐成熟并且部署更复杂的任务关键型资产，对投资资源一致性规则的需求会快速增加。

## <a name="business-risk"></a>业务风险

资源一致性规则尝试解决核心运营业务风险。 与企业和 IT 团队合作来确定这些风险，并在规划和实现云部署时监视每个风险的相关性。

组织间的风险会有所不同，不过以下这些常见风险可以用作在云治理团队中展开讨论的起点：

- **不必要的运营成本**。 已过时或未使用的资源或是在需求较低时间内预配过度的资源会增加不必要的运营成本。
- **预配不足的资源**。 对于这类资源，高于预期需求的体验可能会在云资源不足以应对需求时，导致业务中断。
- **管理效率低下**。 缺少与资源相关联的一致的命名和标记元数据可能会导致 IT 人员难以查找用于管理任务的资源或标识与资产相关的所有权和计帐信息。 这样会导致管理效率低下，从而可能会增加成本并减慢针对服务中断或其他运营问题的 IT 响应速度。
- **业务中断**。 会导致违反组织建立的服务级别协议 (SLA) 的服务中断可能会导致失去业务或对公司造成其他财务影响。

## <a name="next-steps"></a>后续步骤

使用[云管理模板](./template.md)，记录可能由当前云采用计划引入的业务风险。

确立了对实际业务风险的了解后，下一步是记录业务对风险的容忍度，以及用于监视该容忍度的指示器和关键指标。

> [!div class="nextstepaction"]
> [了解指示器、指标和风险容忍度](./metrics-tolerance.md)
