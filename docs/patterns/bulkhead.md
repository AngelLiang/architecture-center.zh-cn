---
title: 隔舱模式
titleSuffix: Cloud Design Patterns
description: 将应用程序的元素隔离到池中，这样，如果一个元素发生故障，其他元素可继续工作。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 1ad646f5c8f4b329b0d0e9a29c83e86848b13ab0
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54488335"
---
# <a name="bulkhead-pattern"></a><span data-ttu-id="220fe-104">隔舱模式</span><span class="sxs-lookup"><span data-stu-id="220fe-104">Bulkhead pattern</span></span>

<span data-ttu-id="220fe-105">将应用程序的元素隔离到池中，这样，如果一个元素发生故障，其他元素可继续工作。</span><span class="sxs-lookup"><span data-stu-id="220fe-105">Isolate elements of an application into pools so that if one fails, the others will continue to function.</span></span>

<span data-ttu-id="220fe-106">此模式之所以称为“隔舱”(Bulkhead)，是因为它类似于船体的分段区。</span><span class="sxs-lookup"><span data-stu-id="220fe-106">This pattern is named *Bulkhead* because it resembles the sectioned partitions of a ship's hull.</span></span> <span data-ttu-id="220fe-107">如果船体受到破坏，只有受损的分段才会进水，从而可以防止船只下沉。</span><span class="sxs-lookup"><span data-stu-id="220fe-107">If the hull of a ship is compromised, only the damaged section fills with water, which prevents the ship from sinking.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="220fe-108">上下文和问题</span><span class="sxs-lookup"><span data-stu-id="220fe-108">Context and problem</span></span>

<span data-ttu-id="220fe-109">基于云的应用程序可以包含多个服务，其中每个服务具有一个或多个使用者。</span><span class="sxs-lookup"><span data-stu-id="220fe-109">A cloud-based application may include multiple services, with each service having one or more consumers.</span></span> <span data-ttu-id="220fe-110">服务过载或发生故障会影响服务的所有使用者。</span><span class="sxs-lookup"><span data-stu-id="220fe-110">Excessive load or failure in a service will impact all consumers of the service.</span></span>

<span data-ttu-id="220fe-111">此外，一个使用者可以使用每个请求的资源同时向多个服务发送请求。</span><span class="sxs-lookup"><span data-stu-id="220fe-111">Moreover, a consumer may send requests to multiple services simultaneously, using resources for each request.</span></span> <span data-ttu-id="220fe-112">当使用者向配置不当或无响应的服务发送请求时，可能无法及时释放客户端请求所用的资源。</span><span class="sxs-lookup"><span data-stu-id="220fe-112">When the consumer sends a request to a service that is misconfigured or not responding, the resources used by the client's request may not be freed in a timely manner.</span></span> <span data-ttu-id="220fe-113">随着不断地向服务发送请求，这些资源可能会耗尽。</span><span class="sxs-lookup"><span data-stu-id="220fe-113">As requests to the service continue, those resources may be exhausted.</span></span> <span data-ttu-id="220fe-114">例如，客户端的连接池可能会耗尽。</span><span class="sxs-lookup"><span data-stu-id="220fe-114">For example, the client's connection pool may be exhausted.</span></span> <span data-ttu-id="220fe-115">此时，使用者向其他服务发出的请求会受到影响。</span><span class="sxs-lookup"><span data-stu-id="220fe-115">At that point, requests by the consumer to other services are impacted.</span></span> <span data-ttu-id="220fe-116">最终，使用者不再能够向其他服务（而不仅仅是原始的无响应服务）发送请求。</span><span class="sxs-lookup"><span data-stu-id="220fe-116">Eventually the consumer can no longer send requests to other services, not just the original unresponsive service.</span></span>

<span data-ttu-id="220fe-117">资源耗尽问题同样会影响具有多个使用者的服务。</span><span class="sxs-lookup"><span data-stu-id="220fe-117">The same issue of resource exhaustion affects services with multiple consumers.</span></span> <span data-ttu-id="220fe-118">源自一个客户端的大量请求可能耗尽服务中的可用资源。</span><span class="sxs-lookup"><span data-stu-id="220fe-118">A large number of requests originating from one client may exhaust available resources in the service.</span></span> <span data-ttu-id="220fe-119">其他使用者不再能够使用该服务，从而导致连锁故障效应。</span><span class="sxs-lookup"><span data-stu-id="220fe-119">Other consumers are no longer able to consume the service, causing a cascading failure effect.</span></span>

## <a name="solution"></a><span data-ttu-id="220fe-120">解决方案</span><span class="sxs-lookup"><span data-stu-id="220fe-120">Solution</span></span>

<span data-ttu-id="220fe-121">根据使用者负载和可用性要求，将服务实例分区成不同的组。</span><span class="sxs-lookup"><span data-stu-id="220fe-121">Partition service instances into different groups, based on consumer load and availability requirements.</span></span> <span data-ttu-id="220fe-122">此设计有助于隔离故障，即使在发生故障期间，也能为某些使用者保留服务功能。</span><span class="sxs-lookup"><span data-stu-id="220fe-122">This design helps to isolate failures, and allows you to sustain service functionality for some consumers, even during a failure.</span></span>

<span data-ttu-id="220fe-123">使用者也可以将资源分区，确保用于调用一个服务的资源不会影响用于调用另一个服务的资源。</span><span class="sxs-lookup"><span data-stu-id="220fe-123">A consumer can also partition resources, to ensure that resources used to call one service don't affect the resources used to call another service.</span></span> <span data-ttu-id="220fe-124">例如，对于调用多个服务的使用者，可为其分配每个服务的连接池。</span><span class="sxs-lookup"><span data-stu-id="220fe-124">For example, a consumer that calls multiple services may be assigned a connection pool for each service.</span></span> <span data-ttu-id="220fe-125">如果某个服务开始发生故障，只有分配给该服务的连接池才会受到影响，因此，使用者可继续使用其他服务。</span><span class="sxs-lookup"><span data-stu-id="220fe-125">If a service begins to fail, it only affects the connection pool assigned for that service, allowing the consumer to continue using the other services.</span></span>

<span data-ttu-id="220fe-126">此模式的优势包括：</span><span class="sxs-lookup"><span data-stu-id="220fe-126">The benefits of this pattern include:</span></span>

- <span data-ttu-id="220fe-127">隔离使用者和服务，防止发生连锁故障。</span><span class="sxs-lookup"><span data-stu-id="220fe-127">Isolates consumers and services from cascading failures.</span></span> <span data-ttu-id="220fe-128">可在使用者或服务自身的隔舱中隔离对其造成影响的问题，防止整个解决方案发生故障。</span><span class="sxs-lookup"><span data-stu-id="220fe-128">An issue affecting a consumer or service can be isolated within its own bulkhead, preventing the entire solution from failing.</span></span>
- <span data-ttu-id="220fe-129">在发生服务故障时，可以保留一部分功能。</span><span class="sxs-lookup"><span data-stu-id="220fe-129">Allows you to preserve some functionality in the event of a service failure.</span></span> <span data-ttu-id="220fe-130">应用程序的其他服务和功能可继续工作。</span><span class="sxs-lookup"><span data-stu-id="220fe-130">Other services and features of the application will continue to work.</span></span>
- <span data-ttu-id="220fe-131">可以部署能够为使用方应用程序提供不同服务质量的服务。</span><span class="sxs-lookup"><span data-stu-id="220fe-131">Allows you to deploy services that offer a different quality of service for consuming applications.</span></span> <span data-ttu-id="220fe-132">可以配置高优先级使用者池来利用高优先级服务。</span><span class="sxs-lookup"><span data-stu-id="220fe-132">A high-priority consumer pool can be configured to use high-priority services.</span></span>

<span data-ttu-id="220fe-133">下图显示了围绕调用单个服务的连接池构建的隔舱。</span><span class="sxs-lookup"><span data-stu-id="220fe-133">The following diagram shows bulkheads structured around connection pools that call individual services.</span></span> <span data-ttu-id="220fe-134">如果服务 A 发生故障或导致其他某种问题，该连接池将被隔离，因此，只有使用分配给服务 A 的线程池的工作负荷才受影响。</span><span class="sxs-lookup"><span data-stu-id="220fe-134">If Service A fails or causes some other issue, the connection pool is isolated, so only workloads using the thread pool assigned to Service A are affected.</span></span> <span data-ttu-id="220fe-135">使用服务 B 和 C 的工作负载不受影响，可继续工作而不会中断。</span><span class="sxs-lookup"><span data-stu-id="220fe-135">Workloads that use Service B and C are not affected and can continue working without interruption.</span></span>

![第一个隔舱模式图](./_images/bulkhead-1.png)

<span data-ttu-id="220fe-137">下图显示了调用单个服务的多个客户端。</span><span class="sxs-lookup"><span data-stu-id="220fe-137">The next diagram shows multiple clients calling a single service.</span></span> <span data-ttu-id="220fe-138">为每个客户端分配了独立的服务实例。</span><span class="sxs-lookup"><span data-stu-id="220fe-138">Each client is assigned a separate service instance.</span></span> <span data-ttu-id="220fe-139">客户端 1 发出了过多的请求，使其实例近乎瘫痪。</span><span class="sxs-lookup"><span data-stu-id="220fe-139">Client 1 has made too many requests and overwhelmed its instance.</span></span> <span data-ttu-id="220fe-140">由于每个服务实例相互隔离，其他客户端可继续发出调用。</span><span class="sxs-lookup"><span data-stu-id="220fe-140">Because each service instance is isolated from the others, the other clients can continue making calls.</span></span>

![第一个隔舱模式图](./_images/bulkhead-2.png)

## <a name="issues-and-considerations"></a><span data-ttu-id="220fe-142">问题和注意事项</span><span class="sxs-lookup"><span data-stu-id="220fe-142">Issues and considerations</span></span>

- <span data-ttu-id="220fe-143">围绕应用程序的业务和技术要求定义分区。</span><span class="sxs-lookup"><span data-stu-id="220fe-143">Define partitions around the business and technical requirements of the application.</span></span>
- <span data-ttu-id="220fe-144">将服务或使用者分区到隔舱时，请考虑相应技术提供的隔离级别，以及成本、性能和可管理性方面的开销。</span><span class="sxs-lookup"><span data-stu-id="220fe-144">When partitioning services or consumers into bulkheads, consider the level of isolation offered by the technology as well as the overhead in terms of cost, performance and manageability.</span></span>
- <span data-ttu-id="220fe-145">考虑将隔舱与重试、断路器和限制模式合并，提供更周密的故障处理。</span><span class="sxs-lookup"><span data-stu-id="220fe-145">Consider combining bulkheads with retry, circuit breaker, and throttling patterns to provide more sophisticated fault handling.</span></span>
- <span data-ttu-id="220fe-146">将使用者分区到隔舱时，请考虑使用进程、线程池和信号灯。</span><span class="sxs-lookup"><span data-stu-id="220fe-146">When partitioning consumers into bulkheads, consider using processes, thread pools, and semaphores.</span></span> <span data-ttu-id="220fe-147">[Netflix Hystrix][hystrix] 和 [Polly][polly] 等项目提供了一个框架用于创建使用者隔舱。</span><span class="sxs-lookup"><span data-stu-id="220fe-147">Projects like [Netflix Hystrix][hystrix] and [Polly][polly] offer a framework for creating consumer bulkheads.</span></span>
- <span data-ttu-id="220fe-148">将服务分区到隔舱时，请考虑将这些服务部署到独立的虚拟机、容器或进程。</span><span class="sxs-lookup"><span data-stu-id="220fe-148">When partitioning services into bulkheads, consider deploying them into separate virtual machines, containers, or processes.</span></span> <span data-ttu-id="220fe-149">容器能够以相当低的开销合理平衡资源隔离。</span><span class="sxs-lookup"><span data-stu-id="220fe-149">Containers offer a good balance of resource isolation with fairly low overhead.</span></span>
- <span data-ttu-id="220fe-150">使用异步消息通信的服务可以通过不同的队列集进行隔离。</span><span class="sxs-lookup"><span data-stu-id="220fe-150">Services that communicate using asynchronous messages can be isolated through different sets of queues.</span></span> <span data-ttu-id="220fe-151">每个队列可以包含专用的实例集用于处理该队列中的消息，或者包含单个实例组，以通过某种算法来取消排队和调度处理负载。</span><span class="sxs-lookup"><span data-stu-id="220fe-151">Each queue can have a dedicated set of instances processing messages on the queue, or a single group of instances using an algorithm to dequeue and dispatch processing.</span></span>
- <span data-ttu-id="220fe-152">确定隔舱的粒度级。</span><span class="sxs-lookup"><span data-stu-id="220fe-152">Determine the level of granularity for the bulkheads.</span></span> <span data-ttu-id="220fe-153">例如，若要将租户分配到不同的分区，可将每个租户放入独立的分区，或者将多个租户放入一个分区。</span><span class="sxs-lookup"><span data-stu-id="220fe-153">For example, if you want to distribute tenants across partitions, you could place each tenant into a separate partition, or put several tenants into one partition.</span></span>
- <span data-ttu-id="220fe-154">监视每个分区的性能和 SLA。</span><span class="sxs-lookup"><span data-stu-id="220fe-154">Monitor each partition’s performance and SLA.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="220fe-155">何时使用此模式</span><span class="sxs-lookup"><span data-stu-id="220fe-155">When to use this pattern</span></span>

<span data-ttu-id="220fe-156">使用此模式可以：</span><span class="sxs-lookup"><span data-stu-id="220fe-156">Use this pattern to:</span></span>

- <span data-ttu-id="220fe-157">隔离使用一组后端服务所用的资源，尤其是应用程序可以提供某种功能级别时，即使某个服务未能响应。</span><span class="sxs-lookup"><span data-stu-id="220fe-157">Isolate resources used to consume a set of backend services, especially if the application can provide some level of functionality even when one of the services is not responding.</span></span>
- <span data-ttu-id="220fe-158">将关键使用者与标准使用者相隔离。</span><span class="sxs-lookup"><span data-stu-id="220fe-158">Isolate critical consumers from standard consumers.</span></span>
- <span data-ttu-id="220fe-159">防止应用程序发生连锁故障。</span><span class="sxs-lookup"><span data-stu-id="220fe-159">Protect the application from cascading failures.</span></span>

<span data-ttu-id="220fe-160">此模式可能不适用于以下情况：</span><span class="sxs-lookup"><span data-stu-id="220fe-160">This pattern may not be suitable when:</span></span>

- <span data-ttu-id="220fe-161">项目中可能不接受资源的低效利用。</span><span class="sxs-lookup"><span data-stu-id="220fe-161">Less efficient use of resources may not be acceptable in the project.</span></span>
- <span data-ttu-id="220fe-162">没有必要提高复杂性</span><span class="sxs-lookup"><span data-stu-id="220fe-162">The added complexity is not necessary</span></span>

## <a name="example"></a><span data-ttu-id="220fe-163">示例</span><span class="sxs-lookup"><span data-stu-id="220fe-163">Example</span></span>

<span data-ttu-id="220fe-164">以下 Kubernetes 配置文件创建一个隔离的容器用于运行单个服务，该容器具有自身的 CPU 和内存资源与限制。</span><span class="sxs-lookup"><span data-stu-id="220fe-164">The following Kubernetes configuration file creates an isolated container to run a single service, with its own CPU and memory resources and limits.</span></span>

```yml
apiVersion: v1
kind: Pod
metadata:
  name: drone-management
spec:
  containers:
  - name: drone-management-container
    image: drone-service
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "1"
```

## <a name="related-guidance"></a><span data-ttu-id="220fe-165">相关指南</span><span class="sxs-lookup"><span data-stu-id="220fe-165">Related guidance</span></span>

- [<span data-ttu-id="220fe-166">设计适用于 Azure 的弹性应用程序</span><span class="sxs-lookup"><span data-stu-id="220fe-166">Designing resilient applications for Azure</span></span>](../resiliency/index.md)
- [<span data-ttu-id="220fe-167">断路器模式</span><span class="sxs-lookup"><span data-stu-id="220fe-167">Circuit Breaker pattern</span></span>](./circuit-breaker.md)
- [<span data-ttu-id="220fe-168">重试模式</span><span class="sxs-lookup"><span data-stu-id="220fe-168">Retry pattern</span></span>](./retry.md)
- [<span data-ttu-id="220fe-169">限制模式</span><span class="sxs-lookup"><span data-stu-id="220fe-169">Throttling pattern</span></span>](./throttling.md)

<!-- links -->

[hystrix]: https://github.com/Netflix/Hystrix
[polly]: https://github.com/App-vNext/Polly
