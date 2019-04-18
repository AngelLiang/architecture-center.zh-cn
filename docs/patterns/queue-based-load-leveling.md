---
title: 基于队列的负载调节模式
titleSuffix: Cloud Design Patterns
description: 使用队列在任务与所调用的服务之间充当缓冲，从而缓解间歇性负载过大现象。
keywords: 设计模式
author: dragon119
ms.date: 01/02/2019
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 267d630d97a998c76a75a70191a77f9b74e801ef
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641053"
---
# <a name="queue-based-load-leveling-pattern"></a><span data-ttu-id="d107d-104">基于队列的负载调节模式</span><span class="sxs-lookup"><span data-stu-id="d107d-104">Queue-Based Load Leveling pattern</span></span>

<span data-ttu-id="d107d-105">使用队列在任务与所调用的服务之间充当缓冲，从而缓解间歇性负载过大现象，否则会导致服务故障或任务超时。这样可以将需求高峰对任务和服务的可用性和响应能力的影响降至最低。</span><span class="sxs-lookup"><span data-stu-id="d107d-105">Use a queue that acts as a buffer between a task and a service it invokes in order to smooth intermittent heavy loads that can cause the service to fail or the task to time out. This can help to minimize the impact of peaks in demand on availability and responsiveness for both the task and the service.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="d107d-106">上下文和问题</span><span class="sxs-lookup"><span data-stu-id="d107d-106">Context and problem</span></span>

<span data-ttu-id="d107d-107">云中的许多解决方案涉及运行那些调用服务的任务。</span><span class="sxs-lookup"><span data-stu-id="d107d-107">Many solutions in the cloud involve running tasks that invoke services.</span></span> <span data-ttu-id="d107d-108">在这样的环境中，如果某个服务出现间歇性负载过大现象，则可能导致性能或可靠性问题。</span><span class="sxs-lookup"><span data-stu-id="d107d-108">In this environment, if a service is subjected to intermittent heavy loads, it can cause performance or reliability issues.</span></span>

<span data-ttu-id="d107d-109">服务可能与使用服务的任务同属一个解决方案；服务也可能是提供常用资源访问权限的第三方服务，例如缓存或存储服务。</span><span class="sxs-lookup"><span data-stu-id="d107d-109">A service could be part of the same solution as the tasks that use it, or it could be a third-party service providing access to frequently used resources such as a cache or a storage service.</span></span> <span data-ttu-id="d107d-110">如果多个同时运行的任务使用同一服务，则可能难以预测任意时刻向服务发出的请求的数量。</span><span class="sxs-lookup"><span data-stu-id="d107d-110">If the same service is used by a number of tasks running concurrently, it can be difficult to predict the volume of requests to the service at any time.</span></span>

<span data-ttu-id="d107d-111">服务可能因需求高峰而过载，无法及时响应请求。</span><span class="sxs-lookup"><span data-stu-id="d107d-111">A service might experience peaks in demand that cause it to overload and be unable to respond to requests in a timely manner.</span></span> <span data-ttu-id="d107d-112">向服务提交大量并发请求时，如果服务无法处理这些请求导致的争用，则也可能导致服务故障。</span><span class="sxs-lookup"><span data-stu-id="d107d-112">Flooding a service with a large number of concurrent requests can also result in the service failing if it's unable to handle the contention these requests cause.</span></span>

## <a name="solution"></a><span data-ttu-id="d107d-113">解决方案</span><span class="sxs-lookup"><span data-stu-id="d107d-113">Solution</span></span>

<span data-ttu-id="d107d-114">重构解决方案，在任务和服务之间引入队列。</span><span class="sxs-lookup"><span data-stu-id="d107d-114">Refactor the solution and introduce a queue between the task and the service.</span></span> <span data-ttu-id="d107d-115">任务和服务异步运行。</span><span class="sxs-lookup"><span data-stu-id="d107d-115">The task and the service run asynchronously.</span></span> <span data-ttu-id="d107d-116">任务将包含服务所需数据的消息发布到队列。</span><span class="sxs-lookup"><span data-stu-id="d107d-116">The task posts a message containing the data required by the service to a queue.</span></span> <span data-ttu-id="d107d-117">队列充当缓冲区来存储消息，直至消息被服务检索出去。</span><span class="sxs-lookup"><span data-stu-id="d107d-117">The queue acts as a buffer, storing the message until it's retrieved by the service.</span></span> <span data-ttu-id="d107d-118">服务从队列检索消息并对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="d107d-118">The service retrieves the messages from the queue and processes them.</span></span> <span data-ttu-id="d107d-119">多个任务提交的请求（其生成速度可能差异很大）可以通过同一消息队列传递给服务。</span><span class="sxs-lookup"><span data-stu-id="d107d-119">Requests from a number of tasks, which can be generated at a highly variable rate, can be passed to the service through the same message queue.</span></span> <span data-ttu-id="d107d-120">下图显示如何使用队列调节服务的负载。</span><span class="sxs-lookup"><span data-stu-id="d107d-120">This figure shows using a queue to level the load on a service.</span></span>

![图 1 - 使用队列调节服务的负载](./_images/queue-based-load-leveling-pattern.png)

<span data-ttu-id="d107d-122">队列使得任务与服务分离，服务可以按自己的速度处理消息，不管并发任务提供的请求数量如何。</span><span class="sxs-lookup"><span data-stu-id="d107d-122">The queue decouples the tasks from the service, and the service can handle the messages at its own pace regardless of the volume of requests from concurrent tasks.</span></span> <span data-ttu-id="d107d-123">另外，即使在任务向队列发布消息时服务不可用，也不会对任务造成延迟。</span><span class="sxs-lookup"><span data-stu-id="d107d-123">Additionally, there's no delay to a task if the service isn't available at the time it posts a message to the queue.</span></span>

<span data-ttu-id="d107d-124">此模式具有以下优点：</span><span class="sxs-lookup"><span data-stu-id="d107d-124">This pattern provides the following benefits:</span></span>

- <span data-ttu-id="d107d-125">可以最大程度地提高可用性，因为服务出现延迟时，不会立即和直接影响应用程序，后者仍可继续向队列发布消息，即使服务不可用或者当前不处理消息。</span><span class="sxs-lookup"><span data-stu-id="d107d-125">It can help to maximize availability because delays arising in services won't have an immediate and direct impact on the application, which can continue to post messages to the queue even when the service isn't available or isn't currently processing messages.</span></span>
- <span data-ttu-id="d107d-126">可以最大程度地提高可伸缩性，因为队列数和服务数均可按需改变。</span><span class="sxs-lookup"><span data-stu-id="d107d-126">It can help to maximize scalability because both the number of queues and the number of services can be varied to meet demand.</span></span>
- <span data-ttu-id="d107d-127">可以控制成本，因为部署的服务实例数只需满足平均负载而非高峰负载。</span><span class="sxs-lookup"><span data-stu-id="d107d-127">It can help to control costs because the number of service instances deployed only have to be adequate to meet average load rather than the peak load.</span></span>

    >  <span data-ttu-id="d107d-128">当需求达到某个阈值，超出该阈值就可能导致系统故障时，某些服务会实施限制。</span><span class="sxs-lookup"><span data-stu-id="d107d-128">Some services implement throttling when demand reaches a threshold beyond which the system could fail.</span></span> <span data-ttu-id="d107d-129">限制可能导致可用功能减少。</span><span class="sxs-lookup"><span data-stu-id="d107d-129">Throttling can reduce the functionality available.</span></span> <span data-ttu-id="d107d-130">可以对这些服务实施负载调节，确保不会达到该阈值。</span><span class="sxs-lookup"><span data-stu-id="d107d-130">You can implement load leveling with these services to ensure that this threshold isn't reached.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="d107d-131">问题和注意事项</span><span class="sxs-lookup"><span data-stu-id="d107d-131">Issues and considerations</span></span>

<span data-ttu-id="d107d-132">在决定如何实现此模式时，请考虑以下几点：</span><span class="sxs-lookup"><span data-stu-id="d107d-132">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="d107d-133">必须实施应用程序逻辑，对服务处理消息的速度进行控制，避免目标资源过载。</span><span class="sxs-lookup"><span data-stu-id="d107d-133">It's necessary to implement application logic that controls the rate at which services handle messages to avoid overwhelming the target resource.</span></span> <span data-ttu-id="d107d-134">避免将需求高峰带到系统的下一阶段。</span><span class="sxs-lookup"><span data-stu-id="d107d-134">Avoid passing spikes in demand to the next stage of the system.</span></span> <span data-ttu-id="d107d-135">对系统进行负载测试，确保实现所需的均衡，并相应地调整队列数和处理消息的服务实例数。</span><span class="sxs-lookup"><span data-stu-id="d107d-135">Test the system under load to ensure that it provides the required leveling, and adjust the number of queues and the number of service instances that handle messages to achieve this.</span></span>
- <span data-ttu-id="d107d-136">消息队列是一种单向通信机制。</span><span class="sxs-lookup"><span data-stu-id="d107d-136">Message queues are a one-way communication mechanism.</span></span> <span data-ttu-id="d107d-137">如果任务期望从服务获得答复，则可能需要实施一种供服务用来发送响应的机制。</span><span class="sxs-lookup"><span data-stu-id="d107d-137">If a task expects a reply from a service, it might be necessary to implement a mechanism that the service can use to send a response.</span></span> <span data-ttu-id="d107d-138">有关详细信息，请参阅 [Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx)（异步消息传送入门）。</span><span class="sxs-lookup"><span data-stu-id="d107d-138">For more information, see the [Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx).</span></span>
- <span data-ttu-id="d107d-139">如果向服务应用自动缩放，而服务正在侦听队列中的请求，请谨慎操作。</span><span class="sxs-lookup"><span data-stu-id="d107d-139">Be careful if you apply autoscaling to services that are listening for requests on the queue.</span></span> <span data-ttu-id="d107d-140">如果这些服务共享特定的资源，上述操作可能导致资源争用加剧，降低使用队列来均衡负载的有效性。</span><span class="sxs-lookup"><span data-stu-id="d107d-140">This can result in increased contention for any resources that these services share and diminish the effectiveness of using the queue to level the load.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="d107d-141">何时使用此模式</span><span class="sxs-lookup"><span data-stu-id="d107d-141">When to use this pattern</span></span>

<span data-ttu-id="d107d-142">此模式适用于其使用的服务可能发生过载的任何应用程序。</span><span class="sxs-lookup"><span data-stu-id="d107d-142">This pattern is useful to any application that uses services that are subject to overloading.</span></span>

<span data-ttu-id="d107d-143">如果应用程序需要尽量降低从服务收到响应时的延迟，则此模式不适用。</span><span class="sxs-lookup"><span data-stu-id="d107d-143">This pattern isn't useful if the application expects a response from the service with minimal latency.</span></span>

## <a name="example"></a><span data-ttu-id="d107d-144">示例</span><span class="sxs-lookup"><span data-stu-id="d107d-144">Example</span></span>

<span data-ttu-id="d107d-145">Web 应用将数据写入外部数据存储。</span><span class="sxs-lookup"><span data-stu-id="d107d-145">A web app writes data to an external data store.</span></span> <span data-ttu-id="d107d-146">如果该 Web 应用的大量实例同时运行，则数据存储可能无法以足够快的速度响应请求，导致请求超时、受限或发生其他故障。</span><span class="sxs-lookup"><span data-stu-id="d107d-146">If a large number of instances of the web app run concurrently, the data store might be unable to respond to requests quickly enough, causing requests to time out, be throttled, or otherwise fail.</span></span> <span data-ttu-id="d107d-147">下图显示一个数据存储因来自应用程序实例的并发请求过多而出现过载的情况。</span><span class="sxs-lookup"><span data-stu-id="d107d-147">The following diagram shows a data store being overwhelmed by a large number of concurrent requests from instances of an application.</span></span>

![图 2 - 一个服务因来自 Web 应用实例的并发请求过多而出现过载的情况](./_images/queue-based-load-leveling-overwhelmed.png)

<span data-ttu-id="d107d-149">若要解决此问题，可以使用一个队列，在应用程序实例和数据存储之间实现负载均衡。</span><span class="sxs-lookup"><span data-stu-id="d107d-149">To resolve this, you can use a queue to level the load between the application instances and the data store.</span></span> <span data-ttu-id="d107d-150">一个 Azure Functions 应用从队列读取消息，然后向数据存储进行读/写请求。</span><span class="sxs-lookup"><span data-stu-id="d107d-150">An Azure Functions app reads the messages from the queue and performs the read/write requests to the data store.</span></span> <span data-ttu-id="d107d-151">函数应用中的应用程序逻辑可以控制该角色将请求传递给数据存储的速度，防止存储过载。</span><span class="sxs-lookup"><span data-stu-id="d107d-151">The application logic in the function app can control the rate at which it passes requests to the data store, to prevent the store from being overwhelmed.</span></span> <span data-ttu-id="d107d-152">（否则，函数应用会在后端再次引发同一问题。）</span><span class="sxs-lookup"><span data-stu-id="d107d-152">(Otherwise the function app will just re-introduce the same problem at the back end.)</span></span>

![图 3 - 使用队列和函数应用进行负载均衡](./_images/queue-based-load-leveling-function.png)

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="d107d-154">相关模式和指南</span><span class="sxs-lookup"><span data-stu-id="d107d-154">Related patterns and guidance</span></span>

<span data-ttu-id="d107d-155">实施此模式时，可能也会与以下模式和指南相关：</span><span class="sxs-lookup"><span data-stu-id="d107d-155">The following patterns and guidance might also be relevant when implementing this pattern:</span></span>

- <span data-ttu-id="d107d-156">[异步消息传送入门](https://msdn.microsoft.com/library/dn589781.aspx)。</span><span class="sxs-lookup"><span data-stu-id="d107d-156">[Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx).</span></span> <span data-ttu-id="d107d-157">消息队列本质上是异步的。</span><span class="sxs-lookup"><span data-stu-id="d107d-157">Message queues are inherently asynchronous.</span></span> <span data-ttu-id="d107d-158">如果已将任务中的应用程序逻辑从直接与服务通信修改为使用消息队列，则可能需要重新设计该逻辑。</span><span class="sxs-lookup"><span data-stu-id="d107d-158">It might be necessary to redesign the application logic in a task if it's adapted from communicating directly with a service to using a message queue.</span></span> <span data-ttu-id="d107d-159">同样，可能需要重构服务才能接受来自消息队列的请求。</span><span class="sxs-lookup"><span data-stu-id="d107d-159">Similarly, it might be necessary to refactor a service to accept requests from a message queue.</span></span> <span data-ttu-id="d107d-160">也可以实施代理服务，如示例中所述。</span><span class="sxs-lookup"><span data-stu-id="d107d-160">Alternatively, it might be possible to implement a proxy service, as described in the example.</span></span>

- <span data-ttu-id="d107d-161">[使用者竞争模式](./competing-consumers.md)。</span><span class="sxs-lookup"><span data-stu-id="d107d-161">[Competing Consumers pattern](./competing-consumers.md).</span></span> <span data-ttu-id="d107d-162">可以运行多个服务实例，每一个都充当负载均衡队列中消息的使用者。</span><span class="sxs-lookup"><span data-stu-id="d107d-162">It might be possible to run multiple instances of a service, each acting as a message consumer from the load-leveling queue.</span></span> <span data-ttu-id="d107d-163">可以使用此方法来调整接收消息并将其传给给服务的速度。</span><span class="sxs-lookup"><span data-stu-id="d107d-163">You can use this approach to adjust the rate at which messages are received and passed to a service.</span></span>

- <span data-ttu-id="d107d-164">[限制模式](./throttling.md)。</span><span class="sxs-lookup"><span data-stu-id="d107d-164">[Throttling pattern](./throttling.md).</span></span> <span data-ttu-id="d107d-165">若要对服务实施限制，一种简单的方式是进行基于队列的负载均衡，通过消息队列将所有请求路由到服务。</span><span class="sxs-lookup"><span data-stu-id="d107d-165">A simple way to implement throttling with a service is to use queue-based load leveling and route all requests to a service through a message queue.</span></span> <span data-ttu-id="d107d-166">服务可以按适当速度处理请求，确保服务所需资源不会耗竭，并缓解可能会发生的争用情况。</span><span class="sxs-lookup"><span data-stu-id="d107d-166">The service can process requests at a rate that ensures that resources required by the service aren't exhausted, and to reduce the amount of contention that could occur.</span></span>

- <span data-ttu-id="d107d-167">[在 Azure 消息传送服务之间进行选择](/azure/event-grid/compare-messaging-services)。</span><span class="sxs-lookup"><span data-stu-id="d107d-167">[Choose between Azure messaging services](/azure/event-grid/compare-messaging-services).</span></span> <span data-ttu-id="d107d-168">介绍如何在 Azure 应用程序中选择消息传送和排队机制。</span><span class="sxs-lookup"><span data-stu-id="d107d-168">Information about choosing a messaging and queuing mechanism in Azure applications.</span></span>

- <span data-ttu-id="d107d-169">[提高 Azure Web 应用程序的可伸缩性](../reference-architectures/app-service-web-app/scalable-web-app.md)。</span><span class="sxs-lookup"><span data-stu-id="d107d-169">[Improve scalability in an Azure web application](../reference-architectures/app-service-web-app/scalable-web-app.md).</span></span> <span data-ttu-id="d107d-170">此参考体系结构包括基于队列的负载调节。</span><span class="sxs-lookup"><span data-stu-id="d107d-170">This reference architecture includes queue-based load leveling as part of the architecture.</span></span>
