---
title: 微服务体系结构样式
titleSuffix: Azure Application Architecture Guide
description: 介绍 Azure 上微服务体系结构的优点、挑战和最佳做法。
author: MikeWasson
ms.date: 11/13/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19, microservices
ms.openlocfilehash: cc72f61003f4146fd65e501feebda0c0d1d27993
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245088"
---
# <a name="microservices-architecture-style"></a><span data-ttu-id="e7441-103">微服务体系结构样式</span><span class="sxs-lookup"><span data-stu-id="e7441-103">Microservices architecture style</span></span>

<span data-ttu-id="e7441-104">微服务体系结构由一系列小型的自治服务组成。</span><span class="sxs-lookup"><span data-stu-id="e7441-104">A microservices architecture consists of a collection of small, autonomous services.</span></span> <span data-ttu-id="e7441-105">每个服务都是自包含服务，并且应实现单个业务功能。</span><span class="sxs-lookup"><span data-stu-id="e7441-105">Each service is self-contained and should implement a single business capability.</span></span>

![微服务体系结构样式的逻辑图](./images/microservices-logical.svg)

<span data-ttu-id="e7441-107">在某些方面，微服务是面向服务的体系结构 (SOA) 的自然演变，但微服务与 SOA 之间也存在一些差异。</span><span class="sxs-lookup"><span data-stu-id="e7441-107">In some ways, microservices are the natural evolution of service oriented architectures (SOA), but there are differences between microservices and SOA.</span></span> <span data-ttu-id="e7441-108">下面是微服务的一些典型特征：</span><span class="sxs-lookup"><span data-stu-id="e7441-108">Here are some defining characteristics of a microservice:</span></span>

- <span data-ttu-id="e7441-109">在微服务体系结构中，服务具有规模小、独立和松散耦合的特点。</span><span class="sxs-lookup"><span data-stu-id="e7441-109">In a microservices architecture, services are small, independent, and loosely coupled.</span></span>

- <span data-ttu-id="e7441-110">每个服务都是一个单独的基本代码，可由小型开发团队管理。</span><span class="sxs-lookup"><span data-stu-id="e7441-110">Each service is a separate codebase, which can be managed by a small development team.</span></span>

- <span data-ttu-id="e7441-111">服务可独立部署。</span><span class="sxs-lookup"><span data-stu-id="e7441-111">Services can be deployed independently.</span></span> <span data-ttu-id="e7441-112">团队可以更新现有服务，而无需重新生成和重新部署整个应用程序。</span><span class="sxs-lookup"><span data-stu-id="e7441-112">A team can update an existing service without rebuilding and redeploying the entire application.</span></span>

- <span data-ttu-id="e7441-113">服务负责暂留自己的数据或外部状态。</span><span class="sxs-lookup"><span data-stu-id="e7441-113">Services are responsible for persisting their own data or external state.</span></span> <span data-ttu-id="e7441-114">这一点与传统模型不同，后者由单独的数据层处理数据暂留。</span><span class="sxs-lookup"><span data-stu-id="e7441-114">This differs from the traditional model, where a separate data layer handles data persistence.</span></span>

- <span data-ttu-id="e7441-115">服务通过定义完善的 API 相互通信。</span><span class="sxs-lookup"><span data-stu-id="e7441-115">Services communicate with each other by using well-defined APIs.</span></span> <span data-ttu-id="e7441-116">每个服务的内部实现细节均对其他服务隐藏。</span><span class="sxs-lookup"><span data-stu-id="e7441-116">Internal implementation details of each service are hidden from other services.</span></span>

- <span data-ttu-id="e7441-117">服务无需共享相同的技术堆栈、库或框架。</span><span class="sxs-lookup"><span data-stu-id="e7441-117">Services don't need to share the same technology stack, libraries, or frameworks.</span></span>

<span data-ttu-id="e7441-118">除了服务本身，典型微服务体系结构中还会出现其他组件：</span><span class="sxs-lookup"><span data-stu-id="e7441-118">Besides for the services themselves, some other components appear in a typical microservices architecture:</span></span>

<span data-ttu-id="e7441-119">**管理**。</span><span class="sxs-lookup"><span data-stu-id="e7441-119">**Management**.</span></span> <span data-ttu-id="e7441-120">管理组件负责将服务放置在节点上、标识故障、跨节点重新平衡服务等等。</span><span class="sxs-lookup"><span data-stu-id="e7441-120">The management component is responsible for placing services on nodes, identifying failures, rebalancing services across nodes, and so forth.</span></span>

<span data-ttu-id="e7441-121">**服务发现**。</span><span class="sxs-lookup"><span data-stu-id="e7441-121">**Service Discovery**.</span></span> <span data-ttu-id="e7441-122">维护一个包含服务及其所在节点的列表。</span><span class="sxs-lookup"><span data-stu-id="e7441-122">Maintains a list of services and which nodes they are located on.</span></span> <span data-ttu-id="e7441-123">支持使用服务查找功能查找服务的终结点。</span><span class="sxs-lookup"><span data-stu-id="e7441-123">Enables service lookup to find the endpoint for a service.</span></span>

<span data-ttu-id="e7441-124">**API 网关**。</span><span class="sxs-lookup"><span data-stu-id="e7441-124">**API Gateway**.</span></span> <span data-ttu-id="e7441-125">API 网关是客户端的入口点。</span><span class="sxs-lookup"><span data-stu-id="e7441-125">The API gateway is the entry point for clients.</span></span> <span data-ttu-id="e7441-126">客户端不直接调用服务，</span><span class="sxs-lookup"><span data-stu-id="e7441-126">Clients don't call services directly.</span></span> <span data-ttu-id="e7441-127">而是调用 API 网关，网关再将调用转发到后端上的相应服务。</span><span class="sxs-lookup"><span data-stu-id="e7441-127">Instead, they call the API gateway, which forwards the call to the appropriate services on the back end.</span></span> <span data-ttu-id="e7441-128">API 网关可以聚合来自多个服务的响应，并返回聚合的响应。</span><span class="sxs-lookup"><span data-stu-id="e7441-128">The API gateway might aggregate the responses from several services and return the aggregated response.</span></span>

<span data-ttu-id="e7441-129">使用 API 网关的优点如下：</span><span class="sxs-lookup"><span data-stu-id="e7441-129">The advantages of using an API gateway include:</span></span>

- <span data-ttu-id="e7441-130">它分离了客户端与服务。</span><span class="sxs-lookup"><span data-stu-id="e7441-130">It decouples clients from services.</span></span> <span data-ttu-id="e7441-131">无需更新所有客户端，便可对服务进行版本控制或重构。</span><span class="sxs-lookup"><span data-stu-id="e7441-131">Services can be versioned or refactored without needing to update all of the clients.</span></span>

- <span data-ttu-id="e7441-132">服务可以使用对 Web 不友好的消息传递协议，比如 AMQP。</span><span class="sxs-lookup"><span data-stu-id="e7441-132">Services can use messaging protocols that are not web friendly, such as AMQP.</span></span>

- <span data-ttu-id="e7441-133">API 网关可执行身份验证、日志记录、SSL 终止和负载均衡等其他跨领域功能。</span><span class="sxs-lookup"><span data-stu-id="e7441-133">The API Gateway can perform other cross-cutting functions such as authentication, logging, SSL termination, and load balancing.</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="e7441-134">何时使用此架构</span><span class="sxs-lookup"><span data-stu-id="e7441-134">When to use this architecture</span></span>

<span data-ttu-id="e7441-135">请对以下情况考虑使用此体系结构样式：</span><span class="sxs-lookup"><span data-stu-id="e7441-135">Consider this architecture style for:</span></span>

- <span data-ttu-id="e7441-136">需要较高发布速度的大型应用程序。</span><span class="sxs-lookup"><span data-stu-id="e7441-136">Large applications that require a high release velocity.</span></span>

- <span data-ttu-id="e7441-137">需要高度可缩放的复杂应用程序。</span><span class="sxs-lookup"><span data-stu-id="e7441-137">Complex applications that need to be highly scalable.</span></span>

- <span data-ttu-id="e7441-138">具有大量域或多个子域的应用程序。</span><span class="sxs-lookup"><span data-stu-id="e7441-138">Applications with rich domains or many subdomains.</span></span>

- <span data-ttu-id="e7441-139">由小型开发团队组成的组织。</span><span class="sxs-lookup"><span data-stu-id="e7441-139">An organization that consists of small development teams.</span></span>

## <a name="benefits"></a><span data-ttu-id="e7441-140">优点</span><span class="sxs-lookup"><span data-stu-id="e7441-140">Benefits</span></span>

- <span data-ttu-id="e7441-141">**独立部署**。</span><span class="sxs-lookup"><span data-stu-id="e7441-141">**Independent deployments**.</span></span> <span data-ttu-id="e7441-142">无需重新部署整个应用程序便可更新服务，出现问题时可回滚或前滚更新。</span><span class="sxs-lookup"><span data-stu-id="e7441-142">You can update a service without redeploying the entire application, and roll back or roll forward an update if something goes wrong.</span></span> <span data-ttu-id="e7441-143">Bug 修复和功能发布更易管理，风险更低。</span><span class="sxs-lookup"><span data-stu-id="e7441-143">Bug fixes and feature releases are more manageable and less risky.</span></span>

- <span data-ttu-id="e7441-144">**独立开发**。</span><span class="sxs-lookup"><span data-stu-id="e7441-144">**Independent development**.</span></span> <span data-ttu-id="e7441-145">单个开发团队便可生成、测试和部署服务，</span><span class="sxs-lookup"><span data-stu-id="e7441-145">A single development team can build, test, and deploy a service.</span></span> <span data-ttu-id="e7441-146">从而推动持续创新，加快发布节奏。</span><span class="sxs-lookup"><span data-stu-id="e7441-146">The result is continuous innovation and a faster release cadence.</span></span>

- <span data-ttu-id="e7441-147">**小型专属团队**。</span><span class="sxs-lookup"><span data-stu-id="e7441-147">**Small, focused teams**.</span></span> <span data-ttu-id="e7441-148">团队可专注于一个服务。</span><span class="sxs-lookup"><span data-stu-id="e7441-148">Teams can focus on one service.</span></span> <span data-ttu-id="e7441-149">缩小每个服务的范围后，基本代码变得更好理解，新的团队成员也能更快上手。</span><span class="sxs-lookup"><span data-stu-id="e7441-149">The smaller scope of each service makes the code base easier to understand, and it's easier for new team members to ramp up.</span></span>

- <span data-ttu-id="e7441-150">**错误隔离**。</span><span class="sxs-lookup"><span data-stu-id="e7441-150">**Fault isolation**.</span></span> <span data-ttu-id="e7441-151">某个服务中断不会影响整个应用程序。</span><span class="sxs-lookup"><span data-stu-id="e7441-151">If a service goes down, it won't take out the entire application.</span></span> <span data-ttu-id="e7441-152">但是，这并不意味着用户可以无偿复原。</span><span class="sxs-lookup"><span data-stu-id="e7441-152">However, that doesn't mean you get resiliency for free.</span></span> <span data-ttu-id="e7441-153">用户仍需遵循复原最佳做法和设计模式。</span><span class="sxs-lookup"><span data-stu-id="e7441-153">You still need to follow resiliency best practices and design patterns.</span></span> <span data-ttu-id="e7441-154">请参阅[设计适用于 Azure 的可复原应用程序][resiliency-overview]。</span><span class="sxs-lookup"><span data-stu-id="e7441-154">See [Designing resilient applications for Azure][resiliency-overview].</span></span>

- <span data-ttu-id="e7441-155">**混合技术堆栈**。</span><span class="sxs-lookup"><span data-stu-id="e7441-155">**Mixed technology stacks**.</span></span> <span data-ttu-id="e7441-156">团队可选取最适合其服务的技术。</span><span class="sxs-lookup"><span data-stu-id="e7441-156">Teams can pick the technology that best fits their service.</span></span>

- <span data-ttu-id="e7441-157">**精细缩放**。</span><span class="sxs-lookup"><span data-stu-id="e7441-157">**Granular scaling**.</span></span> <span data-ttu-id="e7441-158">服务可独立缩放。</span><span class="sxs-lookup"><span data-stu-id="e7441-158">Services can be scaled independently.</span></span> <span data-ttu-id="e7441-159">与此同时，每个 VM 的较高服务密度也意味着 VM 资源得到充分利用。</span><span class="sxs-lookup"><span data-stu-id="e7441-159">At the same time, the higher density of services per VM means that VM resources are fully utilized.</span></span> <span data-ttu-id="e7441-160">使用放置约束，可将服务与 VM 配置文件（高 CPU、高内存等等）匹配。</span><span class="sxs-lookup"><span data-stu-id="e7441-160">Using placement constraints, a services can be matched to a VM profile (high CPU, high memory, and so on).</span></span>

## <a name="challenges"></a><span data-ttu-id="e7441-161">挑战</span><span class="sxs-lookup"><span data-stu-id="e7441-161">Challenges</span></span>

- <span data-ttu-id="e7441-162">**复杂性**。</span><span class="sxs-lookup"><span data-stu-id="e7441-162">**Complexity**.</span></span> <span data-ttu-id="e7441-163">与同等的单一式应用程序相比，微服务应用程序具有更多移动部件。</span><span class="sxs-lookup"><span data-stu-id="e7441-163">A microservices application has more moving parts than the equivalent monolithic application.</span></span> <span data-ttu-id="e7441-164">每个服务更简单，但整个系统作为整体来说更复杂。</span><span class="sxs-lookup"><span data-stu-id="e7441-164">Each service is simpler, but the entire system as a whole is more complex.</span></span>

- <span data-ttu-id="e7441-165">**开发和测试**。</span><span class="sxs-lookup"><span data-stu-id="e7441-165">**Development and test**.</span></span> <span data-ttu-id="e7441-166">针对服务依赖关系的开发需要采用不同的方法。</span><span class="sxs-lookup"><span data-stu-id="e7441-166">Developing against service dependencies requires a different approach.</span></span> <span data-ttu-id="e7441-167">现有工具不一定能处理这些服务依赖关系。</span><span class="sxs-lookup"><span data-stu-id="e7441-167">Existing tools are not necessarily designed to work with service dependencies.</span></span> <span data-ttu-id="e7441-168">跨服务边界进行重构可能很困难。</span><span class="sxs-lookup"><span data-stu-id="e7441-168">Refactoring across service boundaries can be difficult.</span></span> <span data-ttu-id="e7441-169">测试服务依赖关系也有一定难度，尤其是在应用程序快速发展之时。</span><span class="sxs-lookup"><span data-stu-id="e7441-169">It is also challenging to test service dependencies, especially when the application is evolving quickly.</span></span>

- <span data-ttu-id="e7441-170">**缺乏监管**。</span><span class="sxs-lookup"><span data-stu-id="e7441-170">**Lack of governance**.</span></span> <span data-ttu-id="e7441-171">用于生成微服务的分散式方法具有一定优势，但也可能导致许多问题。</span><span class="sxs-lookup"><span data-stu-id="e7441-171">The decentralized approach to building microservices has advantages, but it can also lead to problems.</span></span> <span data-ttu-id="e7441-172">用户在生成过程中可能采用了许多不同的语言和框架，从而使应用程序变得难以维护。</span><span class="sxs-lookup"><span data-stu-id="e7441-172">You may end up with so many different languages and frameworks that the application becomes hard to maintain.</span></span> <span data-ttu-id="e7441-173">这种情况下可以实施一些项目范围内的标准，不过分限制团队的灵活性。</span><span class="sxs-lookup"><span data-stu-id="e7441-173">It may be useful to put some project-wide standards in place, without overly restricting teams' flexibility.</span></span> <span data-ttu-id="e7441-174">这尤其适用于日志记录等跨领域功能。</span><span class="sxs-lookup"><span data-stu-id="e7441-174">This especially applies to cross-cutting functionality such as logging.</span></span>

- <span data-ttu-id="e7441-175">**网络拥塞和延迟**。</span><span class="sxs-lookup"><span data-stu-id="e7441-175">**Network congestion and latency**.</span></span> <span data-ttu-id="e7441-176">使用大量小型的精细服务可能会增加服务间的通信量。</span><span class="sxs-lookup"><span data-stu-id="e7441-176">The use of many small, granular services can result in more interservice communication.</span></span> <span data-ttu-id="e7441-177">此外，如果服务依赖关系链变得太长（服务 A 调用 B，B 调用 C...），额外延迟可能会成为一个问题。</span><span class="sxs-lookup"><span data-stu-id="e7441-177">Also, if the chain of service dependencies gets too long (service A calls B, which calls C...), the additional latency can become a problem.</span></span> <span data-ttu-id="e7441-178">用户需要精心设计 API。</span><span class="sxs-lookup"><span data-stu-id="e7441-178">You will need to design APIs carefully.</span></span> <span data-ttu-id="e7441-179">应避免过于繁琐的 API，考虑使用序列化格式，并找到可以使用异步通信模式的地方。</span><span class="sxs-lookup"><span data-stu-id="e7441-179">Avoid overly chatty APIs, think about serialization formats, and look for places to use asynchronous communication patterns.</span></span>

- <span data-ttu-id="e7441-180">**数据完整性**。</span><span class="sxs-lookup"><span data-stu-id="e7441-180">**Data integrity**.</span></span> <span data-ttu-id="e7441-181">每个微服务负责自己的数据暂留。</span><span class="sxs-lookup"><span data-stu-id="e7441-181">With each microservice responsible for its own data persistence.</span></span> <span data-ttu-id="e7441-182">因此，数据一致性可能是个挑战。</span><span class="sxs-lookup"><span data-stu-id="e7441-182">As a result, data consistency can be a challenge.</span></span> <span data-ttu-id="e7441-183">如果可能，请采用最终一致性。</span><span class="sxs-lookup"><span data-stu-id="e7441-183">Embrace eventual consistency where possible.</span></span>

- <span data-ttu-id="e7441-184">**管理**。</span><span class="sxs-lookup"><span data-stu-id="e7441-184">**Management**.</span></span> <span data-ttu-id="e7441-185">成功使用微服务需要有成熟的 DevOps 区域性。</span><span class="sxs-lookup"><span data-stu-id="e7441-185">To be successful with microservices requires a mature DevOps culture.</span></span> <span data-ttu-id="e7441-186">跨服务的关联日志记录可能很难。</span><span class="sxs-lookup"><span data-stu-id="e7441-186">Correlated logging across services can be challenging.</span></span> <span data-ttu-id="e7441-187">通常情况下，日志记录必须为单个用户操作关联多个服务调用。</span><span class="sxs-lookup"><span data-stu-id="e7441-187">Typically, logging must correlate multiple service calls for a single user operation.</span></span>

- <span data-ttu-id="e7441-188">**版本控制**。</span><span class="sxs-lookup"><span data-stu-id="e7441-188">**Versioning**.</span></span> <span data-ttu-id="e7441-189">对某个服务的更新不应中断依赖于它的其他服务。</span><span class="sxs-lookup"><span data-stu-id="e7441-189">Updates to a service must not break services that depend on it.</span></span> <span data-ttu-id="e7441-190">多个服务可在任意给定时间更新，因此，若不精心设计，可能会遇到向后或向前兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="e7441-190">Multiple services could be updated at any given time, so without careful design, you might have problems with backward or forward compatibility.</span></span>

- <span data-ttu-id="e7441-191">**技能组合**。</span><span class="sxs-lookup"><span data-stu-id="e7441-191">**Skillset**.</span></span> <span data-ttu-id="e7441-192">微服务是一种高度分布式系统。</span><span class="sxs-lookup"><span data-stu-id="e7441-192">Microservices are highly distributed systems.</span></span> <span data-ttu-id="e7441-193">请仔细评估团队是否具有成功使用微服务所需的技能和经验。</span><span class="sxs-lookup"><span data-stu-id="e7441-193">Carefully evaluate whether the team has the skills and experience to be successful.</span></span>

## <a name="best-practices"></a><span data-ttu-id="e7441-194">最佳做法</span><span class="sxs-lookup"><span data-stu-id="e7441-194">Best practices</span></span>

- <span data-ttu-id="e7441-195">围绕业务域对服务建模。</span><span class="sxs-lookup"><span data-stu-id="e7441-195">Model services around the business domain.</span></span>

- <span data-ttu-id="e7441-196">分散所有资源。</span><span class="sxs-lookup"><span data-stu-id="e7441-196">Decentralize everything.</span></span> <span data-ttu-id="e7441-197">单个团队负责设计和生成服务。</span><span class="sxs-lookup"><span data-stu-id="e7441-197">Individual teams are responsible for designing and building services.</span></span> <span data-ttu-id="e7441-198">避免共享代码或数据架构。</span><span class="sxs-lookup"><span data-stu-id="e7441-198">Avoid sharing code or data schemas.</span></span>

- <span data-ttu-id="e7441-199">拥有数据的服务应当有专用的数据存储。</span><span class="sxs-lookup"><span data-stu-id="e7441-199">Data storage should be private to the service that owns the data.</span></span> <span data-ttu-id="e7441-200">为每个服务和数据类型使用最合适的存储。</span><span class="sxs-lookup"><span data-stu-id="e7441-200">Use the best storage for each service and data type.</span></span>

- <span data-ttu-id="e7441-201">服务通过设计完善的 API 进行通信。</span><span class="sxs-lookup"><span data-stu-id="e7441-201">Services communicate through well-designed APIs.</span></span> <span data-ttu-id="e7441-202">避免泄露实现细节。</span><span class="sxs-lookup"><span data-stu-id="e7441-202">Avoid leaking implementation details.</span></span> <span data-ttu-id="e7441-203">API 应对域建模，而不是对服务的内部实现建模。</span><span class="sxs-lookup"><span data-stu-id="e7441-203">APIs should model the domain, not the internal implementation of the service.</span></span>

- <span data-ttu-id="e7441-204">避免服务之间耦合。</span><span class="sxs-lookup"><span data-stu-id="e7441-204">Avoid coupling between services.</span></span> <span data-ttu-id="e7441-205">耦合的原因包括共享的数据库架构和严格的通信协议。</span><span class="sxs-lookup"><span data-stu-id="e7441-205">Causes of coupling include shared database schemas and rigid communication protocols.</span></span>

- <span data-ttu-id="e7441-206">将身份验证和 SSL 终止等跨领域操作分流到网关。</span><span class="sxs-lookup"><span data-stu-id="e7441-206">Offload cross-cutting concerns, such as authentication and SSL termination, to the gateway.</span></span>

- <span data-ttu-id="e7441-207">让网关不必了解域。</span><span class="sxs-lookup"><span data-stu-id="e7441-207">Keep domain knowledge out of the gateway.</span></span> <span data-ttu-id="e7441-208">网关应处理和路由客户端请求，而无需了解业务规则或域逻辑。</span><span class="sxs-lookup"><span data-stu-id="e7441-208">The gateway should handle and route client requests without any knowledge of the business rules or domain logic.</span></span> <span data-ttu-id="e7441-209">否则，网关会变成一个从属物，从而导致服务之间耦合。</span><span class="sxs-lookup"><span data-stu-id="e7441-209">Otherwise, the gateway becomes a dependency and can cause coupling between services.</span></span>

- <span data-ttu-id="e7441-210">服务应具有松散耦合和高功能内聚的特点。</span><span class="sxs-lookup"><span data-stu-id="e7441-210">Services should have loose coupling and high functional cohesion.</span></span> <span data-ttu-id="e7441-211">应当将可能会一起更改的函数打包并部署在一起。</span><span class="sxs-lookup"><span data-stu-id="e7441-211">Functions that are likely to change together should be packaged and deployed together.</span></span> <span data-ttu-id="e7441-212">如果它们驻留在不同的服务中，这些服务最终会紧密耦合，因为一个服务中的更改将需要更新其他服务。</span><span class="sxs-lookup"><span data-stu-id="e7441-212">If they reside in separate services, those services end up being tightly coupled, because a change in one service will require updating the other service.</span></span> <span data-ttu-id="e7441-213">两个服务之间的通信过于频繁可能是紧密耦合和低内聚的征兆。</span><span class="sxs-lookup"><span data-stu-id="e7441-213">Overly chatty communication between two services may be a symptom of tight coupling and low cohesion.</span></span>

- <span data-ttu-id="e7441-214">隔离故障。</span><span class="sxs-lookup"><span data-stu-id="e7441-214">Isolate failures.</span></span> <span data-ttu-id="e7441-215">使用复原策略可防止某个服务中的故障级联。</span><span class="sxs-lookup"><span data-stu-id="e7441-215">Use resiliency strategies to prevent failures within a service from cascading.</span></span> <span data-ttu-id="e7441-216">请参阅[复原模式][resiliency-patterns]和[设计可复原应用程序][resiliency-overview]。</span><span class="sxs-lookup"><span data-stu-id="e7441-216">See [Resiliency patterns][resiliency-patterns] and [Designing resilient applications][resiliency-overview].</span></span>

## <a name="next-steps"></a><span data-ttu-id="e7441-217">后续步骤</span><span class="sxs-lookup"><span data-stu-id="e7441-217">Next steps</span></span>

<span data-ttu-id="e7441-218">有关在 Azure 上构建微服务体系结构的详细指导，请参阅[在 Azure 中设计、构建和操作微服务](../../microservices/index.md)。</span><span class="sxs-lookup"><span data-stu-id="e7441-218">For detailed guidance about building a microservices architecture on Azure, see [Designing, building, and operating microservices on Azure](../../microservices/index.md).</span></span>

<!-- links -->

[resiliency-overview]: ../../resiliency/index.md
[resiliency-patterns]: ../../patterns/category/resiliency.md
