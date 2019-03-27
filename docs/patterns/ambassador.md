---
title: 代表模式
titleSuffix: Cloud Design Patterns
description: 创建代表客户服务或应用程序发送网络请求的帮助程序服务。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: bbf83cdd4bc850c641a0559942f71013e9ce9553
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243918"
---
# <a name="ambassador-pattern"></a><span data-ttu-id="02dea-104">代表模式</span><span class="sxs-lookup"><span data-stu-id="02dea-104">Ambassador pattern</span></span>

<span data-ttu-id="02dea-105">创建代表客户服务或应用程序发送网络请求的帮助程序服务。</span><span class="sxs-lookup"><span data-stu-id="02dea-105">Create helper services that send network requests on behalf of a consumer service or application.</span></span> <span data-ttu-id="02dea-106">代表服务可以看作是与客户端置位于同一位置的进程外代理。</span><span class="sxs-lookup"><span data-stu-id="02dea-106">An ambassador service can be thought of as an out-of-process proxy that is co-located with the client.</span></span>

<span data-ttu-id="02dea-107">此模式可用于以一种与语言无关的方式卸载常见客户端连接任务，如监视、记录、路由、安全（如 TLS）和[复原模式][resiliency-patterns]。</span><span class="sxs-lookup"><span data-stu-id="02dea-107">This pattern can be useful for offloading common client connectivity tasks such as monitoring, logging, routing, security (such as TLS), and [resiliency patterns][resiliency-patterns] in a language agnostic way.</span></span> <span data-ttu-id="02dea-108">它通常用于旧版应用程序或其他很难修改的应用程序，以扩展其网络功能。</span><span class="sxs-lookup"><span data-stu-id="02dea-108">It is often used with legacy applications, or other applications that are difficult to modify, in order to extend their networking capabilities.</span></span> <span data-ttu-id="02dea-109">它还可以使一个专业团队能够实现这些功能。</span><span class="sxs-lookup"><span data-stu-id="02dea-109">It can also enable a specialized team to implement those features.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="02dea-110">上下文和问题</span><span class="sxs-lookup"><span data-stu-id="02dea-110">Context and problem</span></span>

<span data-ttu-id="02dea-111">基于云的可复原应用程序需要[断路](./circuit-breaker.md)、路由、计量和监视等功能，以及能够进行与网络相关的配置更新。</span><span class="sxs-lookup"><span data-stu-id="02dea-111">Resilient cloud-based applications require features such as [circuit breaking](./circuit-breaker.md), routing, metering and monitoring, and the ability to make network-related configuration updates.</span></span> <span data-ttu-id="02dea-112">要更新旧版应用程序或现有代码库来添加这些功能可能比较困难，或者根本不可能，因为开发团队已不再维护或不能轻易修改代码。</span><span class="sxs-lookup"><span data-stu-id="02dea-112">It may be difficult or impossible to update legacy applications or existing code libraries to add these features, because the code is no longer maintained or can't be easily modified by the development team.</span></span>

<span data-ttu-id="02dea-113">网络调用可能也需要大量连接、授权和认证配置。</span><span class="sxs-lookup"><span data-stu-id="02dea-113">Network calls may also require substantial configuration for connection, authentication, and authorization.</span></span> <span data-ttu-id="02dea-114">如果这些调用跨多个应用程序使用，并且是使用多种语言和框架建构的，则必须分别每个实例配置调用。</span><span class="sxs-lookup"><span data-stu-id="02dea-114">If these calls are used across multiple applications, built using multiple languages and frameworks, the calls must be configured for each of these instances.</span></span> <span data-ttu-id="02dea-115">此外，网络和安全功能可能需要组织中的中心团队来管理。</span><span class="sxs-lookup"><span data-stu-id="02dea-115">In addition, network and security functionality may need to be managed by a central team within your organization.</span></span> <span data-ttu-id="02dea-116">由于代码库很大，团队更新不熟悉的应用程序代码时可能会有风险。</span><span class="sxs-lookup"><span data-stu-id="02dea-116">With a large code base, it can be risky for that team to update application code they aren't familiar with.</span></span>

## <a name="solution"></a><span data-ttu-id="02dea-117">解决方案</span><span class="sxs-lookup"><span data-stu-id="02dea-117">Solution</span></span>

<span data-ttu-id="02dea-118">将客户端框架和库放到一个外部进程中，该进程充当应用程序和外部服务之间的代理。</span><span class="sxs-lookup"><span data-stu-id="02dea-118">Put client frameworks and libraries into an external process that acts as a proxy between your application and external services.</span></span> <span data-ttu-id="02dea-119">将代理部署在与应用程序相同的主机环境中，以允许对路由、复原能力、安全功能进行控制，并避免出现与主机相关的访问限制。</span><span class="sxs-lookup"><span data-stu-id="02dea-119">Deploy the proxy on the same host environment as your application to allow control over routing, resiliency, security features, and to avoid any host-related access restrictions.</span></span> <span data-ttu-id="02dea-120">还可以使用代表模式标准化和扩展检测。</span><span class="sxs-lookup"><span data-stu-id="02dea-120">You can also use the ambassador pattern to standardize and extend instrumentation.</span></span> <span data-ttu-id="02dea-121">代理可以监视性能指标（如延迟或资源使用状况），且在与应用程序相同的主机环境中执行此监视。</span><span class="sxs-lookup"><span data-stu-id="02dea-121">The proxy can monitor performance metrics such as latency or resource usage, and this monitoring happens in the same host environment as the application.</span></span>

![代表模式图](./_images/ambassador.png)

<span data-ttu-id="02dea-123">卸载到代表的功能可独立于应用程序进行管理。</span><span class="sxs-lookup"><span data-stu-id="02dea-123">Features that are offloaded to the ambassador can be managed independently of the application.</span></span> <span data-ttu-id="02dea-124">可以更新和修改代表，而不影响应用程序的旧功能。</span><span class="sxs-lookup"><span data-stu-id="02dea-124">You can update and modify the ambassador without disturbing the application's legacy functionality.</span></span> <span data-ttu-id="02dea-125">单独、专业的团队还可以实施和维护已转移给代表的安全、网络或身份验证功能。</span><span class="sxs-lookup"><span data-stu-id="02dea-125">It also allows for separate, specialized teams to implement and maintain security, networking, or authentication features that have been moved to the ambassador.</span></span>

<span data-ttu-id="02dea-126">代表服务可部署为 [sidecar](./sidecar.md) 以伴随使用应用程序或服务的生命周期。</span><span class="sxs-lookup"><span data-stu-id="02dea-126">Ambassador services can be deployed as a [sidecar](./sidecar.md) to accompany the lifecycle of a consuming application or service.</span></span> <span data-ttu-id="02dea-127">或者，如果代表由公共主机上的多个单独进程所共享，则可将其部署为守护程序或 Windows 服务。</span><span class="sxs-lookup"><span data-stu-id="02dea-127">Alternatively, if an ambassador is shared by multiple separate processes on a common host, it can be deployed as a daemon or Windows service.</span></span> <span data-ttu-id="02dea-128">如果使用服务进行了容器化，那么代表应该创建为同一个主机上的单独容器，并且配置适当的链接用于通信。</span><span class="sxs-lookup"><span data-stu-id="02dea-128">If the consuming service is containerized, the ambassador should be created as a separate container on the same host, with the appropriate links configured for communication.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="02dea-129">问题和注意事项</span><span class="sxs-lookup"><span data-stu-id="02dea-129">Issues and considerations</span></span>

- <span data-ttu-id="02dea-130">代理会添加一些延迟开销。</span><span class="sxs-lookup"><span data-stu-id="02dea-130">The proxy adds some latency overhead.</span></span> <span data-ttu-id="02dea-131">请考虑使用应用程序直接调用的客户端库是否是更好的方法。</span><span class="sxs-lookup"><span data-stu-id="02dea-131">Consider whether a client library, invoked directly by the application, is a better approach.</span></span>
- <span data-ttu-id="02dea-132">请考虑在代理中包含通用功能可能带来的影响。</span><span class="sxs-lookup"><span data-stu-id="02dea-132">Consider the possible impact of including generalized features in the proxy.</span></span> <span data-ttu-id="02dea-133">例如，代表可以处理重试操作，但这可能不安全，除非所有操作都是幂等的。</span><span class="sxs-lookup"><span data-stu-id="02dea-133">For example, the ambassador could handle retries, but that might not be safe unless all operations are idempotent.</span></span>
- <span data-ttu-id="02dea-134">请考虑一种允许客户端将一些上下文传递到代理，同时也可传递回客户端的机制。</span><span class="sxs-lookup"><span data-stu-id="02dea-134">Consider a mechanism to allow the client to pass some context to the proxy, as well as back to the client.</span></span> <span data-ttu-id="02dea-135">例如，包含 HTTP 请求标头以选择退出重试，或指定最大重试次数。</span><span class="sxs-lookup"><span data-stu-id="02dea-135">For example, include HTTP request headers to opt out of retry or specify the maximum number of times to retry.</span></span>
- <span data-ttu-id="02dea-136">请考虑如何打包和部署代理。</span><span class="sxs-lookup"><span data-stu-id="02dea-136">Consider how you will package and deploy the proxy.</span></span>
- <span data-ttu-id="02dea-137">考虑是让所有客户端使用一个共享实例还是让每个客户端单独使用一个实例。</span><span class="sxs-lookup"><span data-stu-id="02dea-137">Consider whether to use a single shared instance for all clients or an instance for each client.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="02dea-138">何时使用此模式</span><span class="sxs-lookup"><span data-stu-id="02dea-138">When to use this pattern</span></span>

<span data-ttu-id="02dea-139">在以下情况中使用此模式：</span><span class="sxs-lookup"><span data-stu-id="02dea-139">Use this pattern when you:</span></span>

- <span data-ttu-id="02dea-140">需要为多种语言或框架构建一组通用的客户端连接功能。</span><span class="sxs-lookup"><span data-stu-id="02dea-140">Need to build a common set of client connectivity features for multiple languages or frameworks.</span></span>
- <span data-ttu-id="02dea-141">需要将跨领域客户端连接性问题转移给基础结构开发人员或其他更专业化的团队。</span><span class="sxs-lookup"><span data-stu-id="02dea-141">Need to offload cross-cutting client connectivity concerns to infrastructure developers or other more specialized teams.</span></span>
- <span data-ttu-id="02dea-142">需要在旧版应用程序或难以修改的应用程序中支持云或群集连接需求。</span><span class="sxs-lookup"><span data-stu-id="02dea-142">Need to support cloud or cluster connectivity requirements in a legacy application or an application that is difficult to modify.</span></span>

<span data-ttu-id="02dea-143">此模式可能不适用于以下情况：</span><span class="sxs-lookup"><span data-stu-id="02dea-143">This pattern may not be suitable:</span></span>

- <span data-ttu-id="02dea-144">网络请求延迟严重。</span><span class="sxs-lookup"><span data-stu-id="02dea-144">When network request latency is critical.</span></span> <span data-ttu-id="02dea-145">代理将产生一些开销，虽然是最小开销，但在某些情况下，仍然可能会影响应用程序。</span><span class="sxs-lookup"><span data-stu-id="02dea-145">A proxy will introduce some overhead, although minimal, and in some cases this may affect the application.</span></span>
- <span data-ttu-id="02dea-146">客户端连接功能仅用于一种语言。</span><span class="sxs-lookup"><span data-stu-id="02dea-146">When client connectivity features are consumed by a single language.</span></span> <span data-ttu-id="02dea-147">在这种情况下，最好以包的形式将客户端库分发给开发团队。</span><span class="sxs-lookup"><span data-stu-id="02dea-147">In that case, a better option might be a client library that is distributed to the development teams as a package.</span></span>
- <span data-ttu-id="02dea-148">连接功能无法通用化，且需要与客户端应用程序进行更深层的集成。</span><span class="sxs-lookup"><span data-stu-id="02dea-148">When connectivity features cannot be generalized and require deeper integration with the client application.</span></span>

## <a name="example"></a><span data-ttu-id="02dea-149">示例</span><span class="sxs-lookup"><span data-stu-id="02dea-149">Example</span></span>

<span data-ttu-id="02dea-150">下列图表显示应用程序通过代表代理向远程服务提出请求的情况。</span><span class="sxs-lookup"><span data-stu-id="02dea-150">The following diagram shows an application making a request to a remote service via an ambassador proxy.</span></span> <span data-ttu-id="02dea-151">代表提供路由、断路和记录操作。</span><span class="sxs-lookup"><span data-stu-id="02dea-151">The ambassador provides routing, circuit breaking, and logging.</span></span> <span data-ttu-id="02dea-152">它调用远程服务，然后将响应返回到客户端应用程序:</span><span class="sxs-lookup"><span data-stu-id="02dea-152">It calls the remote service and then returns the response to the client application:</span></span>

![代表模式示例](./_images/ambassador-example.png)

## <a name="related-guidance"></a><span data-ttu-id="02dea-154">相关指南</span><span class="sxs-lookup"><span data-stu-id="02dea-154">Related guidance</span></span>

- [<span data-ttu-id="02dea-155">Sidecar 模式</span><span class="sxs-lookup"><span data-stu-id="02dea-155">Sidecar pattern</span></span>](./sidecar.md)

<!-- links -->

[resiliency-patterns]: ./category/resiliency.md
