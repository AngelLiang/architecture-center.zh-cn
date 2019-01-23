---
title: 将本地网络连接到 Azure
titleSuffix: Azure Reference Architectures
description: 比较用于将本地网络连接到 Azure 的参考体系结构。
author: telmosampaio
ms.date: 07/02/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: networking
ms.openlocfilehash: 6172866b08197b0ca1cd3aabb3c14c01b4f06f9c
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486825"
---
# <a name="choose-a-solution-for-connecting-an-on-premises-network-to-azure"></a>选择用于将本地网络连接到 Azure 的解决方案

本文比较了用于将本地网络连接到 Azure 虚拟网络 (VNet) 的选项。 每个选项都有可用的更详细的参考体系结构。

## <a name="vpn-connection"></a>VPN 连接

[VPN 网关](/azure/vpn-gateway/vpn-gateway-about-vpngateways)是一种虚拟网关，可在 Azure 虚拟网络和本地位置之间发送加密的流量。 加密的流量流经公共 Internet。

此体系结构适合满足以下条件的混合应用程序：本地硬件与云之间的流量可能较小，或者用户愿意用略微延长的延迟换得云的灵活性和处理能力。

### <a name="benefits"></a>优点

- 易于配置。

### <a name="challenges"></a>挑战

- 需要本地 VPN 设备。
- 尽管 Microsoft 保证每个 VPN 网关 99.9% 的可用性，但此 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 仅涵盖 VPN 网关，并不涉及与网关之间的网络连接。
- 通过 Azure VPN 网关的 VPN 连接当前最多支持 1.25 Gbps 的带宽。 如果预期会超过此吞吐量，可能需要跨多个 VPN 连接对 Azure 虚拟网络进行分区。

### <a name="reference-architecture"></a>参考体系结构

- [使用 VPN 网关的混合网络](./vpn.md)

<!-- markdownlint-disable MD024 -->

## <a name="azure-expressroute-connection"></a>Azure ExpressRoute 连接

[ExpressRoute](/azure/expressroute/) 连接通过第三方连接提供商使用专用连接。 该专用连接将本地网络扩展到 Azure 中。

此体系结构适合满足以下条件的混合应用程序：运行需要较高程度可伸缩性的大规模、任务关键型工作负荷。

### <a name="benefits"></a>优点

- 有很高的带宽可用；最高可达 10 Gbps，具体取决于连接提供商。
- 支持动态缩放带宽以帮助在需求较低的时段降低成本。 但是，并非所有连接提供商都提供此选项。
- 可能会允许组织直接访问国家/地区云，具体取决于连接提供商。
- 跨整个连接的 99.9% 可用性 SLA。

### <a name="challenges"></a>挑战

- 设置可能复杂。 创建 ExpressRoute 连接需要使用第三方连接提供商。 该提供商负责预配网络连接。
- 需要本地高带宽路由器。

### <a name="reference-architecture"></a>参考体系结构

- [使用 ExpressRoute 的混合网络](./expressroute.md)

## <a name="expressroute-with-vpn-failover"></a>使用 ExpressRoute 和 VPN 实现故障转移

此选项合并了前面两个选项，在正常情况下使用 ExpressRoute，但在 ExpressRoute 线路中发生连接丢失时故障转移到 VPN 连接。

此体系结构适合需要 ExpressRoute 的较高带宽并且还需要高度可用的网络连接的混合应用程序。

### <a name="benefits"></a>优点

- 在 ExpressRoute 线路出现故障时具有高可用性，虽然回退连接位于带宽较低的网络上。

### <a name="challenges"></a>挑战

- 配置复杂。 需要设置 VPN 连接和 ExpressRoute 线路。
- 需要冗余硬件（VPN 设备），以及你需要为其付费的冗余 Azure VPN 网关连接。

### <a name="reference-architecture"></a>参考体系结构

- [使用 ExpressRoute 和 VPN 故障转移的混合网络](./expressroute-vpn-failover.md)

<!-- markdownlint-disable MD024 -->

## <a name="hub-spoke-network-topology"></a>中心辐射型网络拓扑

可以使用中心辐射型网络拓扑，在隔离工作负荷的同时共享标识和安全性之类的服务。 中心是 Azure 中的一个虚拟网络 (VNet)，充当到本地网络的连接的中心点。 辐射是与中心对等互连的 VNet。 共享服务部署在中心，而各个工作负荷则以辐射的形式部署。

### <a name="reference-architectures"></a>参考体系结构

- [中心辐射型拓扑](./hub-spoke.md)
- [带共享服务的中心辐射](./shared-services.md)
