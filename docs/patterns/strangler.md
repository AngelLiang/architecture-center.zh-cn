---
title: 绞杀者模式
titleSuffix: Cloud Design Patterns
description: 通过将特定的功能片断逐渐取代为新的应用程序和服务，逐步迁移旧系统。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: f139d368c98256c0190753930983a47df81a5134
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54480541"
---
# <a name="strangler-pattern"></a><span data-ttu-id="11b8d-104">绞杀者模式</span><span class="sxs-lookup"><span data-stu-id="11b8d-104">Strangler pattern</span></span>

<span data-ttu-id="11b8d-105">通过将特定的功能片断逐渐取代为新的应用程序和服务，逐步迁移旧系统。</span><span class="sxs-lookup"><span data-stu-id="11b8d-105">Incrementally migrate a legacy system by gradually replacing specific pieces of functionality with new applications and services.</span></span> <span data-ttu-id="11b8d-106">随着旧系统的功能被替换，新系统最终将取代旧系统的所有功能，抑制旧系统并使其停用。</span><span class="sxs-lookup"><span data-stu-id="11b8d-106">As features from the legacy system are replaced, the new system eventually replaces all of the old system's features, strangling the old system and allowing you to decommission it.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="11b8d-107">上下文和问题</span><span class="sxs-lookup"><span data-stu-id="11b8d-107">Context and problem</span></span>

<span data-ttu-id="11b8d-108">随着系统老化，生成系统所使用的开放工具、托管技术和系统体系结构变得过时。</span><span class="sxs-lookup"><span data-stu-id="11b8d-108">As systems age, the development tools, hosting technology, and even system architectures they were built on can become increasingly obsolete.</span></span> <span data-ttu-id="11b8d-109">添加新功能后，这些应用程序的复杂性可能会大幅增加，这使得维护系统或添加新功能变得困难。</span><span class="sxs-lookup"><span data-stu-id="11b8d-109">As new features and functionality are added, the complexity of these applications can increase dramatically, making them harder to maintain or add new features to.</span></span>

<span data-ttu-id="11b8d-110">完全替换一个复杂系统是一项艰巨的任务。</span><span class="sxs-lookup"><span data-stu-id="11b8d-110">Completely replacing a complex system can be a huge undertaking.</span></span> <span data-ttu-id="11b8d-111">通常情况下，需要逐步迁移到新系统，同时要使旧系统处理尚未迁移的功能。</span><span class="sxs-lookup"><span data-stu-id="11b8d-111">Often, you will need a gradual migration to a new system, while keeping the old system to handle features that haven't been migrated yet.</span></span> <span data-ttu-id="11b8d-112">但是，运行两个不同版本的应用程序意味着客户端必须知道特定功能的具体位置。</span><span class="sxs-lookup"><span data-stu-id="11b8d-112">However, running two separate versions of an application means that clients have to know where particular features are located.</span></span> <span data-ttu-id="11b8d-113">每当进行功能或服务迁移时，都需要更新客户端以指向新位置。</span><span class="sxs-lookup"><span data-stu-id="11b8d-113">Every time a feature or service is migrated, clients need to be updated to point to the new location.</span></span>

## <a name="solution"></a><span data-ttu-id="11b8d-114">解决方案</span><span class="sxs-lookup"><span data-stu-id="11b8d-114">Solution</span></span>

<span data-ttu-id="11b8d-115">用新的应用程序和服务逐步替换特定的功能。</span><span class="sxs-lookup"><span data-stu-id="11b8d-115">Incrementally replace specific pieces of functionality with new applications and services.</span></span> <span data-ttu-id="11b8d-116">创建一个外层来拦截请求前往后端旧版系统。</span><span class="sxs-lookup"><span data-stu-id="11b8d-116">Create a façade that intercepts requests going to the backend legacy system.</span></span> <span data-ttu-id="11b8d-117">外层可将这些请求路由到旧版应用程序或新服务。</span><span class="sxs-lookup"><span data-stu-id="11b8d-117">The façade routes these requests either to the legacy application or the new services.</span></span> <span data-ttu-id="11b8d-118">现有功能可逐步迁移到新系统，使用者可继续使用相同的接口，他们并不知道迁移已发生。</span><span class="sxs-lookup"><span data-stu-id="11b8d-118">Existing features can be migrated to the new system gradually, and consumers can continue using the same interface, unaware that any migration has taken place.</span></span>

![绞杀者模式图](./_images/strangler.png)

<span data-ttu-id="11b8d-120">此模式有助于将迁移的风险降至最低，并可在一段时间内分散开发工作。</span><span class="sxs-lookup"><span data-stu-id="11b8d-120">This pattern helps to minimize risk from the migration, and spread the development effort over time.</span></span> <span data-ttu-id="11b8d-121">由于外层将用户安全路由到正确的应用程序，你可按自己的节奏将功能添加到新系统，同时确保旧版应用程序继续运行。</span><span class="sxs-lookup"><span data-stu-id="11b8d-121">With the façade safely routing users to the correct application, you can add functionality to the new system at whatever pace you like, while ensuring the legacy application continues to function.</span></span> <span data-ttu-id="11b8d-122">随着时间推移，功能迁移到了新系统，旧版系统最终受到抑制，并且没有存在的必要。</span><span class="sxs-lookup"><span data-stu-id="11b8d-122">Over time, as features are migrated to the new system, the legacy system is eventually "strangled" and is no longer necessary.</span></span> <span data-ttu-id="11b8d-123">此过程完成后，可以安全停用旧版系统。</span><span class="sxs-lookup"><span data-stu-id="11b8d-123">Once this process is complete, the legacy system can safely be retired.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="11b8d-124">问题和注意事项</span><span class="sxs-lookup"><span data-stu-id="11b8d-124">Issues and considerations</span></span>

- <span data-ttu-id="11b8d-125">请考虑如何处理可能由新旧系统共同使用的服务和数据存储。</span><span class="sxs-lookup"><span data-stu-id="11b8d-125">Consider how to handle services and data stores that are potentially used by both new and legacy systems.</span></span> <span data-ttu-id="11b8d-126">请确保新旧系统可并行访问这些资源。</span><span class="sxs-lookup"><span data-stu-id="11b8d-126">Make sure both can access these resources side-by-side.</span></span>
- <span data-ttu-id="11b8d-127">构建新的应用程序和服务，使它们能在将来的绞杀者迁移中被轻松拦截和替换。</span><span class="sxs-lookup"><span data-stu-id="11b8d-127">Structure new applications and services in a way that they can easily be intercepted and replaced in future strangler migrations.</span></span>
- <span data-ttu-id="11b8d-128">有时候，迁移完成后，绞杀者外层消失，或进化为旧版客户端的适配器。</span><span class="sxs-lookup"><span data-stu-id="11b8d-128">At some point, when the migration is complete, the strangler façade will either go away or evolve into an adaptor for legacy clients.</span></span>
- <span data-ttu-id="11b8d-129">请确保外层与迁移保持同步。</span><span class="sxs-lookup"><span data-stu-id="11b8d-129">Make sure the façade keeps up with the migration.</span></span>
- <span data-ttu-id="11b8d-130">请确保外层不会成为单一故障点或性能瓶颈。</span><span class="sxs-lookup"><span data-stu-id="11b8d-130">Make sure the façade doesn't become a single point of failure or a performance bottleneck.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="11b8d-131">何时使用此模式</span><span class="sxs-lookup"><span data-stu-id="11b8d-131">When to use this pattern</span></span>

<span data-ttu-id="11b8d-132">将后端应用程序逐渐迁移到新的体系结构中时，可使用此模式。</span><span class="sxs-lookup"><span data-stu-id="11b8d-132">Use this pattern when gradually migrating a back-end application to a new architecture.</span></span>

<span data-ttu-id="11b8d-133">此模式可能不适用于以下情况：</span><span class="sxs-lookup"><span data-stu-id="11b8d-133">This pattern may not be suitable:</span></span>

- <span data-ttu-id="11b8d-134">无法拦截前往后端系统的请求。</span><span class="sxs-lookup"><span data-stu-id="11b8d-134">When requests to the back-end system cannot be intercepted.</span></span>
- <span data-ttu-id="11b8d-135">整体替换的复杂性低的小型系统。</span><span class="sxs-lookup"><span data-stu-id="11b8d-135">For smaller systems where the complexity of wholesale replacement is low.</span></span>

## <a name="related-guidance"></a><span data-ttu-id="11b8d-136">相关指南</span><span class="sxs-lookup"><span data-stu-id="11b8d-136">Related guidance</span></span>

- <span data-ttu-id="11b8d-137">Martin Fowler 撰写的有关 [StranglerApplication](https://www.martinfowler.com/bliki/StranglerApplication.html) 的博客文章</span><span class="sxs-lookup"><span data-stu-id="11b8d-137">Martin Fowler's blog post on [StranglerApplication](https://www.martinfowler.com/bliki/StranglerApplication.html)</span></span>
