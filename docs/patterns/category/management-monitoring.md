---
title: 管理和监视模式
titleSuffix: Cloud Design Patterns
description: 云应用程序在远程数据中心内运行，在此中心内，无法完全控制基础架构，或者在某些情况下无法控制操作系统。 与本地部署相比，管理和监视难度更大。 应用程序必须公开运行时信息，以便管理员和操作员管理和监视系统，支持不断变化的业务要求和定制，而无需停止或重新部署应用程序。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 587caf680a884cda208baec50ff914f6c7238b48
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486851"
---
# <a name="management-and-monitoring-patterns"></a><span data-ttu-id="e511c-106">管理和监视模式</span><span class="sxs-lookup"><span data-stu-id="e511c-106">Management and Monitoring patterns</span></span>

<span data-ttu-id="e511c-107">云应用程序在远程数据中心内运行，在此中心内，无法完全控制基础架构，或者在某些情况下无法控制操作系统。</span><span class="sxs-lookup"><span data-stu-id="e511c-107">Cloud applications run in in a remote datacenter where you do not have full control of the infrastructure or, in some cases, the operating system.</span></span> <span data-ttu-id="e511c-108">与本地部署相比，管理和监视难度更大。</span><span class="sxs-lookup"><span data-stu-id="e511c-108">This can make management and monitoring more difficult than an on-premises deployment.</span></span> <span data-ttu-id="e511c-109">应用程序必须公开运行时信息，以便管理员和操作员管理和监视系统，支持不断变化的业务要求和定制，而无需停止或重新部署应用程序。</span><span class="sxs-lookup"><span data-stu-id="e511c-109">Applications must expose runtime information that administrators and operators can use to manage and monitor the system, as well as supporting changing business requirements and customization without requiring the application to be stopped or redeployed.</span></span>

|                              <span data-ttu-id="e511c-110">模式</span><span class="sxs-lookup"><span data-stu-id="e511c-110">Pattern</span></span>                               |                                                              <span data-ttu-id="e511c-111">摘要</span><span class="sxs-lookup"><span data-stu-id="e511c-111">Summary</span></span>                                                              |
|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
|                   [<span data-ttu-id="e511c-112">代表</span><span class="sxs-lookup"><span data-stu-id="e511c-112">Ambassador</span></span>](../ambassador.md)                   |                 <span data-ttu-id="e511c-113">创建代表客户服务或应用程序发送网络请求的帮助程序服务。</span><span class="sxs-lookup"><span data-stu-id="e511c-113">Create helper services that send network requests on behalf of a consumer service or application.</span></span>                 |
|        [<span data-ttu-id="e511c-114">防损层</span><span class="sxs-lookup"><span data-stu-id="e511c-114">Anti-Corruption Layer</span></span>](../anti-corruption-layer.md)        |                       <span data-ttu-id="e511c-115">在现代应用程序与旧系统之间实施外观或适配器层。</span><span class="sxs-lookup"><span data-stu-id="e511c-115">Implement a façade or adapter layer between a modern application and a legacy system.</span></span>                       |
| [<span data-ttu-id="e511c-116">外部配置存储</span><span class="sxs-lookup"><span data-stu-id="e511c-116">External Configuration Store</span></span>](../external-configuration-store.md) |                <span data-ttu-id="e511c-117">将配置信息从应用程序部署包移出，移到一个集中的位置。</span><span class="sxs-lookup"><span data-stu-id="e511c-117">Move configuration information out of the application deployment package to a centralized location.</span></span>                |
|          [<span data-ttu-id="e511c-118">网关聚合</span><span class="sxs-lookup"><span data-stu-id="e511c-118">Gateway Aggregation</span></span>](../gateway-aggregation.md)          |                          <span data-ttu-id="e511c-119">使用网关可将多个单独请求聚合成一个请求。</span><span class="sxs-lookup"><span data-stu-id="e511c-119">Use a gateway to aggregate multiple individual requests into a single request.</span></span>                           |
|           [<span data-ttu-id="e511c-120">网关卸载</span><span class="sxs-lookup"><span data-stu-id="e511c-120">Gateway Offloading</span></span>](../gateway-offloading.md)           |                              <span data-ttu-id="e511c-121">将共享或专用服务功能卸载到网关代理。</span><span class="sxs-lookup"><span data-stu-id="e511c-121">Offload shared or specialized service functionality to a gateway proxy.</span></span>                              |
|              [<span data-ttu-id="e511c-122">网关路由</span><span class="sxs-lookup"><span data-stu-id="e511c-122">Gateway Routing</span></span>](../gateway-routing.md)              |                                   <span data-ttu-id="e511c-123">使用单个终结点将请求路由到多个服务。</span><span class="sxs-lookup"><span data-stu-id="e511c-123">Route requests to multiple services using a single endpoint.</span></span>                                    |
|   [<span data-ttu-id="e511c-124">运行状况终结点监视</span><span class="sxs-lookup"><span data-stu-id="e511c-124">Health Endpoint Monitoring</span></span>](../health-endpoint-monitoring.md)   |   <span data-ttu-id="e511c-125">在应用程序中实施可让外部工具通过公开终结点定期访问的功能检查。</span><span class="sxs-lookup"><span data-stu-id="e511c-125">Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals.</span></span>    |
|                      [<span data-ttu-id="e511c-126">sidecar</span><span class="sxs-lookup"><span data-stu-id="e511c-126">Sidecar</span></span>](../sidecar.md)                      |         <span data-ttu-id="e511c-127">将应用程序的组件部署到单独的进程或容器中，以提供隔离和封装。</span><span class="sxs-lookup"><span data-stu-id="e511c-127">Deploy components of an application into a separate process or container to provide isolation and encapsulation.</span></span>          |
|                    [<span data-ttu-id="e511c-128">Strangler</span><span class="sxs-lookup"><span data-stu-id="e511c-128">Strangler</span></span>](../strangler.md)                    | <span data-ttu-id="e511c-129">通过将特定的功能片断逐渐取代为新的应用程序和服务，逐步迁移旧系统。</span><span class="sxs-lookup"><span data-stu-id="e511c-129">Incrementally migrate a legacy system by gradually replacing specific pieces of functionality with new applications and services.</span></span> |
