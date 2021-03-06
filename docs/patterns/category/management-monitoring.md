---
title: 管理和监视模式
titleSuffix: Cloud Design Patterns
description: 云应用程序在远程数据中心内运行，在此中心内，无法完全控制基础结构，或者在某些情况下无法控制操作系统。 与本地部署相比，管理和监视难度更大。 应用程序必须公开运行时信息，以便管理员和操作员管理和监视系统，支持不断变化的业务要求和定制，而无需停止或重新部署应用程序。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 2b141a39b17f97ece011e93ca2f05cf5d2baeeea
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641087"
---
# <a name="management-and-monitoring-patterns"></a>管理和监视模式

云应用程序在远程数据中心内运行，在此中心内，无法完全控制基础结构，或者在某些情况下无法控制操作系统。 与本地部署相比，管理和监视难度更大。 应用程序必须公开运行时信息，以便管理员和操作员管理和监视系统，支持不断变化的业务要求和定制，而无需停止或重新部署应用程序。

|                              模式                               |                                                              摘要                                                              |
|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
|                   [代表](../ambassador.md)                   |                 创建代表客户服务或应用程序发送网络请求的帮助程序服务。                 |
|        [防损层](../anti-corruption-layer.md)        |                       在现代应用程序与旧系统之间实施外观或适配器层。                       |
| [外部配置存储](../external-configuration-store.md) |                将配置信息从应用程序部署包移出，移到一个集中的位置。                |
|          [网关聚合](../gateway-aggregation.md)          |                          使用网关可将多个单独请求聚合成一个请求。                           |
|           [网关卸载](../gateway-offloading.md)           |                              将共享或专用服务功能卸载到网关代理。                              |
|              [网关路由](../gateway-routing.md)              |                                   使用单个终结点将请求路由到多个服务。                                    |
|   [运行状况终结点监视](../health-endpoint-monitoring.md)   |   在应用程序中实施可让外部工具通过公开终结点定期访问的功能检查。    |
|                      [sidecar](../sidecar.md)                      |         将应用程序的组件部署到单独的进程或容器中，以提供隔离和封装。          |
|                    [Strangler](../strangler.md)                    | 通过将特定的功能片断逐渐取代为新的应用程序和服务，逐步迁移旧系统。 |
