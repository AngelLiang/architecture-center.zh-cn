---
title: CAF：软件定义的网络 - 云原生
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 云原生虚拟网络服务的介绍
author: rotycenh
ms.openlocfilehash: c6200491bc9ba35a9f00e0003e51716b58628980
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242148"
---
# <a name="software-defined-networks-cloud-native"></a>软件定义网络：云原生

在将 IaaS 资源（如虚拟机）部署到云平台时，云原生虚拟网络是必需的。 需要明确预配从外部源（类似于 Web）访问虚拟网络。 这些类型的虚拟网络支持创建子网、传递规则、虚拟防火墙和流量管理设备。

云原生虚拟网络不依赖于组织的本地资源或其他非云资源来支持云托管的工作负荷。 所有必需的资源都可以在虚拟网络本身中进行预配，也可以使用托管 PaaS 产品/服务进行预配。

## <a name="cloud-native-assumptions"></a>云原生假设

部署云原生虚拟网络的假设条件如下：

- 部署到虚拟网络的工作负荷不依赖于只能从本地网络访问的应用程序或服务。 托管在云平台上的资源无法使用本地托管的应用程序和服务，除非这些应用程序和服务提供可通过公共 Internet 访问的终结点。
- 工作负荷的标识管理和访问控制取决于云平台的标识服务或云环境中托管的 IaaS 服务器。 无需直接连接到托管在本地或其他外部位置的标识服务。
- 标识服务不需要支持使用本地目录的单一登录 (SSO)。

云原生虚拟网络没有外部依赖项。 所以，这些网络易于部署和配置。这样的体系结构通常是试验或其他较小的自包含或快速迭代部署的最佳选择。

在讨论云原生虚拟网络体系结构时，云采用团队应考虑的其他问题包括：

- 设计为在本地数据中心运行的现有工作负荷可能需要进行大量修改才能利用基于云的功能（例如，存储或身份验证服务）。
- 云原生网络只能通过云平台管理工具管理，因此随着时间的推移，可能会导致与现有 IT 标准的管理和策略存在差异。

## <a name="learn-more"></a>了解详细信息

有关 Azure 平台中的云原生虚拟网络的详细信息，请参阅以下内容。

- [Azure 虚拟网络：操作指南](/azure/virtual-network/virtual-network-vnet-plan-design-arm)。 默认情况下，新创建的 Azure 虚拟网络为云原生虚拟网络。 使用这些指南来帮助规划虚拟网络的设计和部署。
- [订阅限制：](/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)网络”。 任何一个虚拟网络和连接的资源都只能位于一个订阅中，并受订阅限制的约束。
