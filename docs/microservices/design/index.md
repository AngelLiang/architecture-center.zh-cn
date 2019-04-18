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
# <a name="designing-a-microservices-architecture"></a><span data-ttu-id="4727e-103">设计微服务体系结构</span><span class="sxs-lookup"><span data-stu-id="4727e-103">Designing a microservices architecture</span></span>

<span data-ttu-id="4727e-104">微服务已成为一种流行的体系结构类型，可用于构建可复原、高度可缩放、可独立部署且能快速演变的云应用程序。</span><span class="sxs-lookup"><span data-stu-id="4727e-104">Microservices have become a popular architectural style for building cloud applications that are resilient, highly scalable, independently deployable, and able to evolve quickly.</span></span> <span data-ttu-id="4727e-105">但是，微服务不仅仅是一种潮流，我们还需要利用不同的方法来设计和构建应用程序。</span><span class="sxs-lookup"><span data-stu-id="4727e-105">To be more than just a buzzword, however, microservices require a different approach to designing and building applications.</span></span>

<span data-ttu-id="4727e-106">在本系列文章中，我们将探讨如何在 Azure 中构建和运行微服务体系结构。</span><span class="sxs-lookup"><span data-stu-id="4727e-106">In this set of articles, we explore how to build and run a microservices architecture on Azure.</span></span> <span data-ttu-id="4727e-107">主题包括：</span><span class="sxs-lookup"><span data-stu-id="4727e-107">Topics include:</span></span>

- [<span data-ttu-id="4727e-108">服务间通信</span><span class="sxs-lookup"><span data-stu-id="4727e-108">Interservice communication</span></span>](./interservice-communication.md)
- [<span data-ttu-id="4727e-109">API 设计</span><span class="sxs-lookup"><span data-stu-id="4727e-109">API design</span></span>](./api-design.md)
- [<span data-ttu-id="4727e-110">API 网关</span><span class="sxs-lookup"><span data-stu-id="4727e-110">API gateways</span></span>](./gateway.md)
- [<span data-ttu-id="4727e-111">数据注意事项</span><span class="sxs-lookup"><span data-stu-id="4727e-111">Data considerations</span></span>](./data-considerations.md)
- [<span data-ttu-id="4727e-112">设计模式</span><span class="sxs-lookup"><span data-stu-id="4727e-112">Design patterns</span></span>](./patterns.md)

## <a name="prerequisites"></a><span data-ttu-id="4727e-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="4727e-113">Prerequisites</span></span>

<span data-ttu-id="4727e-114">在阅读上述文章之前，可以先阅读以下文章：</span><span class="sxs-lookup"><span data-stu-id="4727e-114">Before reading these articles, you might start with the following:</span></span>

- <span data-ttu-id="4727e-115">[Introduction to microservices architectures](../introduction.md)（微服务体系结构简介）。</span><span class="sxs-lookup"><span data-stu-id="4727e-115">[Introduction to microservices architectures](../introduction.md).</span></span> <span data-ttu-id="4727e-116">了解微服务的优点和难点，以及何时使用此样式的体系结构。</span><span class="sxs-lookup"><span data-stu-id="4727e-116">Understand the benefits and challenges of microservices, and when to use this style of architecture.</span></span>
- <span data-ttu-id="4727e-117">[Using domain analysis to model microservices](../model/domain-analysis.md)（使用域分析为微服务建模）。</span><span class="sxs-lookup"><span data-stu-id="4727e-117">[Using domain analysis to model microservices](../model/domain-analysis.md).</span></span> <span data-ttu-id="4727e-118">了解如何通过域驱动的方法为微服务建模。</span><span class="sxs-lookup"><span data-stu-id="4727e-118">Learn a domain-driven approach to modeling microservices.</span></span>

## <a name="reference-implementation"></a><span data-ttu-id="4727e-119">参考实现</span><span class="sxs-lookup"><span data-stu-id="4727e-119">Reference implementation</span></span>

<span data-ttu-id="4727e-120">为了演示微服务体系结构的最佳做法，我们创建了一个名为“无人机交付应用程序”的参考实现。</span><span class="sxs-lookup"><span data-stu-id="4727e-120">To illustrate best practices for a microservices architecture, we created a reference implementation that we call the Drone Delivery application.</span></span> <span data-ttu-id="4727e-121">此实现在使用 Azure Kubernetes 服务 (AKS) 的 Kubernetes 上运行。</span><span class="sxs-lookup"><span data-stu-id="4727e-121">This implementation runs on Kubernetes using Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="4727e-122">可以在 [GitHub][drone-ri] 上找到该参考实现。</span><span class="sxs-lookup"><span data-stu-id="4727e-122">You can find the reference implementation on [GitHub][drone-ri].</span></span>

![无人机交付应用程序的体系结构](../images/drone-delivery.png)

## <a name="scenario"></a><span data-ttu-id="4727e-124">场景</span><span class="sxs-lookup"><span data-stu-id="4727e-124">Scenario</span></span>

<span data-ttu-id="4727e-125">Fabrikam, Inc. 正在推出无人机交付服务。</span><span class="sxs-lookup"><span data-stu-id="4727e-125">Fabrikam, Inc. is starting a drone delivery service.</span></span> <span data-ttu-id="4727e-126">该公司经营无人机群。</span><span class="sxs-lookup"><span data-stu-id="4727e-126">The company manages a fleet of drone aircraft.</span></span> <span data-ttu-id="4727e-127">各商家注册该服务，用户可以请求无人机收取要交付的商品。</span><span class="sxs-lookup"><span data-stu-id="4727e-127">Businesses register with the service, and users can request a drone to pick up goods for delivery.</span></span> <span data-ttu-id="4727e-128">当客户安排取件时，后端系统会分配一架无人机，并将估计的交付时间告知用户。</span><span class="sxs-lookup"><span data-stu-id="4727e-128">When a customer schedules a pickup, a backend system assigns a drone and notifies the user with an estimated delivery time.</span></span> <span data-ttu-id="4727e-129">在交付过程中，客户可以通过持续更新的 ETA 跟踪无人机的位置。</span><span class="sxs-lookup"><span data-stu-id="4727e-129">While the delivery is in progress, the customer can track the location of the drone, with a continuously updated ETA.</span></span>

<span data-ttu-id="4727e-130">此方案涉及到一个相当复杂的域。</span><span class="sxs-lookup"><span data-stu-id="4727e-130">This scenario involves a fairly complicated domain.</span></span> <span data-ttu-id="4727e-131">部分业务难题包括安排无人机、跟踪包裹、管理用户帐户，以及存储和分析历史数据。</span><span class="sxs-lookup"><span data-stu-id="4727e-131">Some of the business concerns include scheduling drones, tracking packages, managing user accounts, and storing and analyzing historical data.</span></span> <span data-ttu-id="4727e-132">此外，Fabrikam 希望快速投放市场和扩张，同时添加新功能。</span><span class="sxs-lookup"><span data-stu-id="4727e-132">Moreover, Fabrikam wants to get to market quickly and then iterate quickly, adding new functionality and capabilities.</span></span> <span data-ttu-id="4727e-133">该应用程序需要以云的规模运行，并附带较高的服务级别目标 (SLO)。</span><span class="sxs-lookup"><span data-stu-id="4727e-133">The application needs to operate at cloud scale, with a high service level objective (SLO).</span></span> <span data-ttu-id="4727e-134">此外，Fabrikam 预期系统的不同部件在数据存储和查询方面具有截然不同的要求。</span><span class="sxs-lookup"><span data-stu-id="4727e-134">Fabrikam also expects that different parts of the system will have very different requirements for data storage and querying.</span></span> <span data-ttu-id="4727e-135">所有这些考虑因素促使 Fabrikam 为无人机交付应用程序选择了微服务体系结构。</span><span class="sxs-lookup"><span data-stu-id="4727e-135">All of these considerations lead Fabrikam to choose a microservices architecture for the Drone Delivery application.</span></span>

> [!NOTE]
> <span data-ttu-id="4727e-136">如需在微服务体系结构与其他体系结构样式之间做出选择的帮助，请参阅 [Azure 应用程序体系结构指南](../../guide/index.md)。</span><span class="sxs-lookup"><span data-stu-id="4727e-136">For help in choosing between a microservices architecture and other architectural styles, see the [Azure Application Architecture Guide](../../guide/index.md).</span></span>

<span data-ttu-id="4727e-137">我们的参考实现将 Kubernetes 与 [Azure Kubernetes 服务](/azure/aks/) (AKS) 配合使用。</span><span class="sxs-lookup"><span data-stu-id="4727e-137">Our reference implementation uses Kubernetes with [Azure Kubernetes Service](/azure/aks/) (AKS).</span></span> <span data-ttu-id="4727e-138">但是，许多高层面的体系结构决策和难题也适用于任何容器业务流程协调程序，包括 [Azure Service Fabric](/azure/service-fabric/)。</span><span class="sxs-lookup"><span data-stu-id="4727e-138">However, many of the high-level architectural decisions and challenges will apply to any container orchestrator, including [Azure Service Fabric](/azure/service-fabric/).</span></span>

<!-- links -->

[drone-ri]: https://github.com/mspnp/microservices-reference-implementation/tree/v0.1.0-orig

## <a name="next-steps"></a><span data-ttu-id="4727e-139">后续步骤</span><span class="sxs-lookup"><span data-stu-id="4727e-139">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="4727e-140">选择计算选项</span><span class="sxs-lookup"><span data-stu-id="4727e-140">Choose a compute option</span></span>](./compute-options.md)