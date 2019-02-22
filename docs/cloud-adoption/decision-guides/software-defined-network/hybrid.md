---
title: CAF：软件定义的网络 - 混合网络
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 讨论混合网络如何允许云虚拟网络连接到本地资源
author: rotycenh
ms.openlocfilehash: 02d181db0ae9baef3b453b8623d212b624f6b16a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900462"
---
# <a name="software-defined-networks-hybrid-network"></a>软件定义的网络：混合网络

混合云网络体系结构允许虚拟网络访问本地资源和服务，反之亦然，使用专用 WAN 连接（如 ExpressRoute 或其他连接方法）可以直接连接网络。

![混合网络](../../../reference-architectures/hybrid-networking/images/expressroute.png)

混合虚拟网络在云原生虚拟网络体系结构基础上构建，它在最初创建时是独立的。 虽然需要显式允许虚拟网络中所有其他以资源为目标的入站流量，但向本地环境添加连接性将授予访问本地网络和通过本地网络访问的权限。 对于连接的安全性，你可以通过虚拟防火墙设备和路由规则限制访问来实现，也可以使用云原生路由功能或部署网络虚拟设备 (NVA) 来管理流量，从而准确地指定在两个网络之间可以访问的具体服务。

虽然混合网络体系结构支持 VPN 连接，但像 ExpressRoute 这样的专用 WAN 连接通常是首选，因为它具有更高的性能和安全性。

## <a name="hybrid-assumptions"></a>混合假设

部署混合虚拟网络的假设条件如下：

- IT 安全团队已经调整了本地和基于云的网络安全策略，以确保可以信任基于云的虚拟网络，进而与本地系统直接通信。
- 基于云的工作负载需要访问托管在本地或第三方网络上的存储、应用程序和服务，或者驻留在本地的用户或应用程序需要访问云托管的资源。
- 你需要迁移依赖于本地资源的现有应用程序和服务，但不希望在重新开发上消耗资源来删除这些依赖项。
- 在本地网络和云提供商之间实现 VPN 或专用 WAN 连接不受公司策略、法规要求或技术兼容性问题的限制。
- 工作负载要么不需要多个订阅来绕过订阅资源限制，要么涉及多个订阅，但不需要集中管理分布在多个订阅中的资源所使用的连接或共享服务。

在考虑实现混合虚拟网络体系结构时，云采用团队应考虑以下问题：

- 将本地网络与云网络连接会增加安全需求的复杂性。 这两个网络都需要保护，以防止来自双方混合环境的外部漏洞和未经授权的访问。
- 在混合云环境中，扩展工作负载的数量和大小会大大增加路由和流量管理的复杂性。
- 需要开发兼容的管理和访问控制策略，以在整个组织中维护一致治理。

## <a name="learn-more"></a>了解详细信息

有关 Azure 平台中的混合网络的详细信息，请参阅以下内容。

- [混合网络参考体系结构](../../../reference-architectures/hybrid-networking/expressroute.md)。 Azure 混合虚拟网络使用 ExpressRoute 线路或 Azure VPN 将虚拟网络与组织现有的非 Azure 托管的 IT 资产连接起来。 本文讨论了在 Azure 中创建混合网络的选项。
