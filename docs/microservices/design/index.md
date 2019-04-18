---
title: Azure Kubernetes 服务的微服务参考实现
description: 本参考实现演示了微服务体系结构的最佳做法
author: MikeWasson
ms.date: 02/26/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: 17e275e5b5f45233f7467192402cb28fce35c57b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068899"
---
# <a name="designing-a-microservices-architecture"></a>设计微服务体系结构

微服务已成为一种流行的体系结构类型，可用于构建可复原、高度可缩放、可独立部署且能快速演变的云应用程序。 但是，微服务不仅仅是一种潮流，我们还需要利用不同的方法来设计和构建应用程序。

在本系列文章中，我们将探讨如何在 Azure 中构建和运行微服务体系结构。 主题包括：

- [服务间通信](./interservice-communication.md)
- [API 设计](./api-design.md)
- [API 网关](./gateway.md)
- [数据注意事项](./data-considerations.md)
- [设计模式](./patterns.md)

## <a name="prerequisites"></a>先决条件

在阅读上述文章之前，可以先阅读以下文章：

- [Introduction to microservices architectures](../introduction.md)（微服务体系结构简介）。 了解微服务的优点和难点，以及何时使用此样式的体系结构。
- [Using domain analysis to model microservices](../model/domain-analysis.md)（使用域分析为微服务建模）。 了解如何通过域驱动的方法为微服务建模。

## <a name="reference-implementation"></a>参考实现

为了演示微服务体系结构的最佳做法，我们创建了一个名为“无人机交付应用程序”的参考实现。 此实现在使用 Azure Kubernetes 服务 (AKS) 的 Kubernetes 上运行。 可以在 [GitHub][drone-ri] 上找到该参考实现。

![无人机交付应用程序的体系结构](../images/drone-delivery.png)

## <a name="scenario"></a>场景

Fabrikam, Inc. 正在推出无人机交付服务。 该公司经营无人机群。 各商家注册该服务，用户可以请求无人机收取要交付的商品。 当客户安排取件时，后端系统会分配一架无人机，并将估计的交付时间告知用户。 在交付过程中，客户可以通过持续更新的 ETA 跟踪无人机的位置。

此方案涉及到一个相当复杂的域。 部分业务难题包括安排无人机、跟踪包裹、管理用户帐户，以及存储和分析历史数据。 此外，Fabrikam 希望快速投放市场和扩张，同时添加新功能。 该应用程序需要以云的规模运行，并附带较高的服务级别目标 (SLO)。 此外，Fabrikam 预期系统的不同部件在数据存储和查询方面具有截然不同的要求。 所有这些考虑因素促使 Fabrikam 为无人机交付应用程序选择了微服务体系结构。

> [!NOTE]
> 如需在微服务体系结构与其他体系结构样式之间做出选择的帮助，请参阅 [Azure 应用程序体系结构指南](../../guide/index.md)。

我们的参考实现将 Kubernetes 与 [Azure Kubernetes 服务](/azure/aks/) (AKS) 配合使用。 但是，许多高层面的体系结构决策和难题也适用于任何容器业务流程协调程序，包括 [Azure Service Fabric](/azure/service-fabric/)。

<!-- links -->

[drone-ri]: https://github.com/mspnp/microservices-reference-implementation/tree/v0.1.0-orig

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [选择计算选项](./compute-options.md)