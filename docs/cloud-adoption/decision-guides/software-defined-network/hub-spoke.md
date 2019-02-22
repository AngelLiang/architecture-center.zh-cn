---
title: CAF：软件定义的网络 - 云原生
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 云原生虚拟网络服务的介绍
author: rotycenh
ms.openlocfilehash: e0ad6803f2ddc982ea0c42c59fdf2486e1710433
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900519"
---
# <a name="software-defined-networks-hub-and-spoke"></a>软件定义网络：中心和分支

中心和分支网络模型将基于 Azure 的云网络基础结构组织到多个连接的虚拟网络中。 通过此模型可以更高效地管理常见通信或安全要求，以及应对潜在的订阅限制。

在中心和分支模型中，中心是一个虚拟网络，充当用于管理外部连接和托管多个工作负荷所使用的服务的中央位置。 分支是托管工作负荷并通过[虚拟网络对等互连](/virtual-network/virtual-network-peering-overview)连接到中央中心的虚拟网络。

传入或传出工作负荷分支网络的所有流量都通过中心网络进行路由，在其中可以通过集中管理的 IT 规则或流程对这些流量进行路由、检查或在其他方面进行管理。

此模型旨在解决以下问题：

- 成本节省和管理效率。 将可以由多个工作负荷（例如网络虚拟设备 (NVAs) 和 DNS 服务器）共享的服务集中放置在单个位置中使 IT 可以跨多个工作负荷尽量减少冗余资源和管理工作。
- 克服订阅限制。 基于云的大型工作负荷需要使用的资源可能会多于单个 Azure 订阅中允许使用的资源（请参阅[订阅限制](/azure/azure-subscription-service-limits)）。 将来自不同订阅的工作负荷虚拟网络对等互连到一个中央中心可以克服这些限制。
- 关注点分离。 能够在中央 IT 团队与工作负荷团队之间部署单独的工作负荷。

下图显示一个示例中心和分支体系结构，其中包括集中管理的混合连接。

![中心辐射型网络体系结构](../../../reference-architectures/hybrid-networking/images/hub-spoke.png)

中心和分支体系结构通常与混合网络体系结构一起使用，从而提供与多个工作负荷之间共享的本地环境的集中管理连接。 在此方案中，工作负荷与本地之间传输的所有流量都会经过中心，在其中可以对这些流量进行管理和保护。

## <a name="hub-and-spoke-assumptions"></a>中心和分支假设

实现中心和分支虚拟网络体系结构的假设条件如下：

- 云部署涉及在单独工作环境（如开发、测试和生产）中托管且全部依赖于一组公用服务（例如 DNS 或目录服务）的工作负荷。
- 工作负荷无需相互通信，但具有公用外部通信和共享服务要求。
- 工作负荷需要的资源多于单个 Azure 订阅中可用的资源。
- 需要向工作负荷团队提供对其自己的资源的委托管理权限，同时维护对外部连接的中央安全控制。

## <a name="global-hub-and-spoke"></a>全局中心和分支

中心和分支体系结构通常使用部署到相同 Azure 区域的虚拟网络来实现，以尽量降低网络之间的延迟。 但是，遍布全球的大型组织可能需要跨多个区域部署工作负荷，以实现可用性、灾难恢复或法规要求。 通过使用 Azure [全局虚拟网络对等互连](/azure/virtual-network/virtual-network-peering-overview)，中心和分支模型可以跨区域扩展集中管理和共享的服务，以支持分布在全世界的工作负荷。

## <a name="learn-more"></a>了解详细信息

有关如何在 Azure 上实现中心和分支网络的示例，请参阅 Azure 参考体系结构站点上的以下示例：

- [在 Azure 中实现中心辐射型网络拓扑](../../../reference-architectures/hybrid-networking/hub-spoke.md)
- [在 Azure 中使用共享服务实现中心辐射型网络拓扑](../../../reference-architectures/hybrid-networking/shared-services.md)
