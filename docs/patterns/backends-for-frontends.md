---
title: 用于前端的后端模式
titleSuffix: Cloud Design Patterns
description: 创建单独的后端服务，供特定的前端应用程序或接口使用。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.custom: seodec18
ms.openlocfilehash: 1fc597ded3e87ca7b4a200a13af9dce5ba2dbec5
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54009740"
---
# <a name="backends-for-frontends-pattern"></a><span data-ttu-id="3aa07-104">用于前端的后端模式</span><span class="sxs-lookup"><span data-stu-id="3aa07-104">Backends for Frontends pattern</span></span>

<span data-ttu-id="3aa07-105">创建单独的后端服务，供特定的前端应用程序或接口使用。</span><span class="sxs-lookup"><span data-stu-id="3aa07-105">Create separate backend services to be consumed by specific frontend applications or interfaces.</span></span> <span data-ttu-id="3aa07-106">要避免为多个接口自定义一个后端时，此模式十分有用。</span><span class="sxs-lookup"><span data-stu-id="3aa07-106">This pattern is useful when you want to avoid customizing a single backend for multiple interfaces.</span></span> <span data-ttu-id="3aa07-107">此模式最先是由 Sam Newman 描述的。</span><span class="sxs-lookup"><span data-stu-id="3aa07-107">This pattern was first described by Sam Newman.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="3aa07-108">上下文和问题</span><span class="sxs-lookup"><span data-stu-id="3aa07-108">Context and problem</span></span>

<span data-ttu-id="3aa07-109">应用程序最初可能面向桌面 Web UI。</span><span class="sxs-lookup"><span data-stu-id="3aa07-109">An application may initially be targeted at a desktop web UI.</span></span> <span data-ttu-id="3aa07-110">通常并行开发提供该 UI 所需功能的后端服务。</span><span class="sxs-lookup"><span data-stu-id="3aa07-110">Typically, a backend service is developed in parallel that provides the features needed for that UI.</span></span> <span data-ttu-id="3aa07-111">随着应用程序用户群的增长，开发出了必须与同一后端交互的移动应用程序。</span><span class="sxs-lookup"><span data-stu-id="3aa07-111">As the application's user base grows, a mobile application is developed that must interact with the same backend.</span></span> <span data-ttu-id="3aa07-112">后端服务成为一般用途的后端，满足桌面和移动接口的需求。</span><span class="sxs-lookup"><span data-stu-id="3aa07-112">The backend service becomes a general-purpose backend, serving the requirements of both the desktop and mobile interfaces.</span></span>

<span data-ttu-id="3aa07-113">但移动设备和桌面浏览器在屏幕大小、性能和显示限制方面的功能存在显著差异。</span><span class="sxs-lookup"><span data-stu-id="3aa07-113">But the capabilities of a mobile device differ significantly from a desktop browser, in terms of screen size, performance, and display limitations.</span></span> <span data-ttu-id="3aa07-114">因此，移动应用程序和桌面 Web UI 对后端的需求也有所不同。</span><span class="sxs-lookup"><span data-stu-id="3aa07-114">As a result, the requirements for a mobile application backend differ from the desktop web UI.</span></span>

<span data-ttu-id="3aa07-115">这些差异导致两者对后端的需求相互冲突。</span><span class="sxs-lookup"><span data-stu-id="3aa07-115">These differences result in competing requirements for the backend.</span></span> <span data-ttu-id="3aa07-116">为向桌面 Web UI 和移动应用程序提供服务，后端需要进行常规更改和重大更改。</span><span class="sxs-lookup"><span data-stu-id="3aa07-116">The backend requires regular and significant changes to serve both the desktop web UI and the mobile application.</span></span> <span data-ttu-id="3aa07-117">单独的接口团队通常致力于每个前端，导致后端成为开发过程中的瓶颈。</span><span class="sxs-lookup"><span data-stu-id="3aa07-117">Often, separate interface teams work on each frontend, causing the backend to become a bottleneck in the development process.</span></span> <span data-ttu-id="3aa07-118">矛盾的更新需求以及让服务适用于这两个前端的需要会导致在一个可部署资源上花费大量精力。</span><span class="sxs-lookup"><span data-stu-id="3aa07-118">Conflicting update requirements, and the need to keep the service working for both frontends, can result in spending a lot of effort on a single deployable resource.</span></span>

![用于前端的后端模式的上下文和问题图](./_images/backend-for-frontend.png)

<span data-ttu-id="3aa07-120">因为开发活动注重后端服务，所以可能要建立单独的团队来管理和维护后端。</span><span class="sxs-lookup"><span data-stu-id="3aa07-120">As the development activity focuses on the backend service, a separate team may be created to manage and maintain the backend.</span></span> <span data-ttu-id="3aa07-121">这最终导致接口和后端开发团队之间的连接断开，为平衡不同 UI 团队冲突的需求而增加后端团队的负担。</span><span class="sxs-lookup"><span data-stu-id="3aa07-121">Ultimately, this results in a disconnect between the interface and backend development teams, placing a burden on the backend team to balance the competing requirements of the different UI teams.</span></span> <span data-ttu-id="3aa07-122">一个接口团队要求更改后端时，必须先与其他接口团队验证这些更改，然后才能将其集成到后端。</span><span class="sxs-lookup"><span data-stu-id="3aa07-122">When one interface team requires changes to the backend, those changes must be validated with other interface teams before they can be integrated into the backend.</span></span>

## <a name="solution"></a><span data-ttu-id="3aa07-123">解决方案</span><span class="sxs-lookup"><span data-stu-id="3aa07-123">Solution</span></span>

<span data-ttu-id="3aa07-124">为每个用户界面创建一个后端。</span><span class="sxs-lookup"><span data-stu-id="3aa07-124">Create one backend per user interface.</span></span> <span data-ttu-id="3aa07-125">在无需担心影响其他前端体验的情况下，微调每个后端的行为和性能以最大程度地满足前端环境的需求。</span><span class="sxs-lookup"><span data-stu-id="3aa07-125">Fine tune the behavior and performance of each backend to best match the needs of the frontend environment, without worrying about affecting other frontend experiences.</span></span>

![用于前端的后端模式的示意图](./_images/backend-for-frontend-example.png)

<span data-ttu-id="3aa07-127">每个后端特定于一个接口，因此可针对该接口优化后端。</span><span class="sxs-lookup"><span data-stu-id="3aa07-127">Because each backend is specific to one interface, it can be optimized for that interface.</span></span> <span data-ttu-id="3aa07-128">因此，与试图满足所有接口的需求的泛型后端相比，它更小、复杂性更低并且速度可能更快。</span><span class="sxs-lookup"><span data-stu-id="3aa07-128">As a result, it will be smaller, less complex, and likely faster than a generic backend that tries to satisfy the requirements for all interfaces.</span></span> <span data-ttu-id="3aa07-129">每个接口团队可自主控制其后端，无需依赖于集中式后端开发团队。</span><span class="sxs-lookup"><span data-stu-id="3aa07-129">Each interface team has autonomy to control their own backend and doesn't rely on a centralized backend development team.</span></span> <span data-ttu-id="3aa07-130">这向接口团队提供了后端的语言选择、发布节奏、工作负载优先顺序和功能集成方面的灵活性。</span><span class="sxs-lookup"><span data-stu-id="3aa07-130">This gives the interface team flexibility in language selection, release cadence, prioritization of workload, and feature integration in their backend.</span></span>

<span data-ttu-id="3aa07-131">有关详细信息，请参阅 [Pattern:Backends For Frontends](https://samnewman.io/patterns/architectural/bff/)（模式：用于前端的后端）。</span><span class="sxs-lookup"><span data-stu-id="3aa07-131">For more information, see [Pattern: Backends For Frontends](https://samnewman.io/patterns/architectural/bff/).</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="3aa07-132">问题和注意事项</span><span class="sxs-lookup"><span data-stu-id="3aa07-132">Issues and considerations</span></span>

- <span data-ttu-id="3aa07-133">请考虑要部署的后端数量。</span><span class="sxs-lookup"><span data-stu-id="3aa07-133">Consider how many backends to deploy.</span></span>
- <span data-ttu-id="3aa07-134">如果不同接口（如移动客户端）发出相同请求，请考虑是否必须为每个接口实现一个后端，或者一个后端是否可以满足需求。</span><span class="sxs-lookup"><span data-stu-id="3aa07-134">If different interfaces (such as mobile clients) will make the same requests, consider whether it is necessary to implement a backend for each interface, or if a single backend will suffice.</span></span>
- <span data-ttu-id="3aa07-135">实现此模式时，服务之间的代码很可能重复。</span><span class="sxs-lookup"><span data-stu-id="3aa07-135">Code duplication across services is highly likely when implementing this pattern.</span></span>
- <span data-ttu-id="3aa07-136">专注于前端的后端服务应仅包含特定于客户端的逻辑和行为。</span><span class="sxs-lookup"><span data-stu-id="3aa07-136">Frontend-focused backend services should only contain client-specific logic and behavior.</span></span> <span data-ttu-id="3aa07-137">应当在应用程序的其他位置管理常规业务逻辑和其他全局功能。</span><span class="sxs-lookup"><span data-stu-id="3aa07-137">General business logic and other global features should be managed elsewhere in your application.</span></span>
- <span data-ttu-id="3aa07-138">思考此模式在开发团队责任中可能具有的体现。</span><span class="sxs-lookup"><span data-stu-id="3aa07-138">Think about how this pattern might be reflected in the responsibilities of a development team.</span></span>
- <span data-ttu-id="3aa07-139">请考虑实现此模式所需的时间。</span><span class="sxs-lookup"><span data-stu-id="3aa07-139">Consider how long it will take to implement this pattern.</span></span> <span data-ttu-id="3aa07-140">假如你继续支持现有的泛型后端，生成新后端所需的工作量是否会导致出现技术债务？</span><span class="sxs-lookup"><span data-stu-id="3aa07-140">Will the effort of building the new backends incur technical debt, while you continue to support the existing generic backend?</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="3aa07-141">何时使用此模式</span><span class="sxs-lookup"><span data-stu-id="3aa07-141">When to use this pattern</span></span>

<span data-ttu-id="3aa07-142">在以下情况下使用此模式：</span><span class="sxs-lookup"><span data-stu-id="3aa07-142">Use this pattern when:</span></span>

- <span data-ttu-id="3aa07-143">必须使用大量开发开销维护共享或常规用途的后端服务。</span><span class="sxs-lookup"><span data-stu-id="3aa07-143">A shared or general purpose backend service must be maintained with significant development overhead.</span></span>
- <span data-ttu-id="3aa07-144">想要优化后端以满足特定客户端接口的需求。</span><span class="sxs-lookup"><span data-stu-id="3aa07-144">You want to optimize the backend for the requirements of specific client interfaces.</span></span>
- <span data-ttu-id="3aa07-145">自定义一般用途的后端以适应多个接口。</span><span class="sxs-lookup"><span data-stu-id="3aa07-145">Customizations are made to a general-purpose backend to accommodate multiple interfaces.</span></span>
- <span data-ttu-id="3aa07-146">另一种语言更适合另一用户界面的后端。</span><span class="sxs-lookup"><span data-stu-id="3aa07-146">An alternative language is better suited for the backend of a different user interface.</span></span>

<span data-ttu-id="3aa07-147">此模式可能不适用于以下情况：</span><span class="sxs-lookup"><span data-stu-id="3aa07-147">This pattern may not be suitable:</span></span>

- <span data-ttu-id="3aa07-148">接口向后端发出相同或类似的请求时。</span><span class="sxs-lookup"><span data-stu-id="3aa07-148">When interfaces make the same or similar requests to the backend.</span></span>
- <span data-ttu-id="3aa07-149">仅使用一个接口与后端交互时。</span><span class="sxs-lookup"><span data-stu-id="3aa07-149">When only one interface is used to interact with the backend.</span></span>

## <a name="related-guidance"></a><span data-ttu-id="3aa07-150">相关指南</span><span class="sxs-lookup"><span data-stu-id="3aa07-150">Related guidance</span></span>

- [<span data-ttu-id="3aa07-151">网关聚合模式</span><span class="sxs-lookup"><span data-stu-id="3aa07-151">Gateway Aggregation pattern</span></span>](./gateway-aggregation.md)
- [<span data-ttu-id="3aa07-152">网关卸载模式</span><span class="sxs-lookup"><span data-stu-id="3aa07-152">Gateway Offloading pattern</span></span>](./gateway-offloading.md)
- [<span data-ttu-id="3aa07-153">网关路由模式</span><span class="sxs-lookup"><span data-stu-id="3aa07-153">Gateway Routing pattern</span></span>](./gateway-routing.md)
