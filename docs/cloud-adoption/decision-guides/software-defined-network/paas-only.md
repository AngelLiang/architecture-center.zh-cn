---
title: CAF：软件定义网络 - 仅限 PaaS
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 讨论基于云的网络功能的仅限 PaaS 模型
author: rotycenh
ms.openlocfilehash: 2f3f82d781ddb6544721e82e7b7d795222a2f8ff
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900557"
---
# <a name="software-defined-networks-paas-only"></a>软件定义网络：仅限 PaaS

实现平台即服务 (PaaS) 资源时，部署过程会自动创建一个假设的底层网络，该网络上的控制数量有限，包括负载均衡、端口阻止以及与其他 PaaS 服务的连接。

在 Azure 中，可以将多个 PaaS 资源类型[部署到](/azure/virtual-network/virtual-network-for-azure-services)或[连接到](/azure/virtual-network/virtual-network-service-endpoints-overview)虚拟网络中，从而允许这些资源与现有的虚拟网络基础结构集成。 但是，在许多情况下，仅依赖于 PaaS 资源本身提供的这些默认网络功能的仅限 PaaS 的网络体系结构足以满足工作负载要求。

如果你正在考虑仅限 PaaS 的网络体系结构，请确保验证所需的假设符合要求。

## <a name="paas-only-assumptions"></a>仅限 PaaS 的假设

部署仅限 PaaS 的网络体系结构的前提是：

- 部署的应用程序是独立应用程序，或仅依赖于其他 PaaS 资源。
- IT 运营团队可以更新其工具、培训和流程，以支持独立 PaaS 应用程序的管理、配置和部署。
- PaaS 应用程序不是包含 IaaS 资源的更广泛的云迁移工作的一部分。

这些假设是与部署仅限 PaaS 的网络一致的最低限定符。 虽然此方法可能符合单个应用程序部署的要求，但云采用团队应研究这些长期问题：

- 这种部署是否会扩大范围或规模，以要求访问其他非 PaaS 资源？
- 是否计划在当前解决方案之外部署其他 PaaS？
- 组织是否有计划进行其他未来的云迁移？

这些问题的答案不会妨碍团队选择仅限 PaaS 选项，但在做出最终决定之前应予以考虑。
