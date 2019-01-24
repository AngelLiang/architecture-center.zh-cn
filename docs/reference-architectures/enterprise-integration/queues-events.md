---
title: 使用消息队列和事件的企业集成
titleSuffix: Azure Reference Architectures
description: 建议用于通过 Azure 逻辑应用、Azure API 管理、Azure 服务总线和 Azure 事件网格来实现企业集成模式的体系结构。
author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: reference-architecture
ms.date: 12/03/2018
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: integration-services
ms.openlocfilehash: 4c9d2e201bcfc077990d746a1decd55ede2f220a
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54480668"
---
# <a name="enterprise-integration-on-azure-using-message-queues-and-events"></a><span data-ttu-id="4d389-103">Azure 上使用消息队列和事件的企业集成</span><span class="sxs-lookup"><span data-stu-id="4d389-103">Enterprise integration on Azure using message queues and events</span></span>

<span data-ttu-id="4d389-104">此参考体系结构集成企业后端系统，使用消息队列和事件将服务分离，以提高可伸缩性和可靠性。</span><span class="sxs-lookup"><span data-stu-id="4d389-104">This reference architecture integrates enterprise backend systems, using message queues and events to decouple services for greater scalability and reliability.</span></span> <span data-ttu-id="4d389-105">后端系统可能包括软件即服务 (SaaS) 系统、Azure 服务以及企业中现有的 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="4d389-105">The backend systems may include software as a service (SaaS) systems, Azure services, and existing web services in your enterprise.</span></span>

![适用于使用队列和事件的企业集成的参考体系结构](./_images/enterprise-integration-queues-events.png)

## <a name="architecture"></a><span data-ttu-id="4d389-107">体系结构</span><span class="sxs-lookup"><span data-stu-id="4d389-107">Architecture</span></span>

<span data-ttu-id="4d389-108">此处显示的体系结构在更简单的体系结构上构建，后者显示在[基本企业集成][basic-enterprise-integration]中。</span><span class="sxs-lookup"><span data-stu-id="4d389-108">The architecture shown here builds on a simpler architecture that is shown in [Basic enterprise integration][basic-enterprise-integration].</span></span> <span data-ttu-id="4d389-109">该体系结构使用用于协调工作流的[逻辑应用][logic-apps]和用于创建 API 目录的 [API 管理][apim]。</span><span class="sxs-lookup"><span data-stu-id="4d389-109">That architecture uses [Logic Apps][logic-apps] to orchestrate workflows and [API Management][apim] to create catalogs of APIs.</span></span>

<span data-ttu-id="4d389-110">此版体系结构添加了两个组件，使系统的可靠性和可伸缩性更高：</span><span class="sxs-lookup"><span data-stu-id="4d389-110">This version of the architecture adds two components that help make the system more reliable and scalable:</span></span>

- <span data-ttu-id="4d389-111">**[Azure 服务总线][service-bus]**。</span><span class="sxs-lookup"><span data-stu-id="4d389-111">**[Azure Service Bus][service-bus]**.</span></span> <span data-ttu-id="4d389-112">服务总线是安全且可靠的消息代理。</span><span class="sxs-lookup"><span data-stu-id="4d389-112">Service Bus is a secure, reliable message broker.</span></span>

- <span data-ttu-id="4d389-113">**[Azure 事件网格][event-grid]**。</span><span class="sxs-lookup"><span data-stu-id="4d389-113">**[Azure Event Grid][event-grid]**.</span></span> <span data-ttu-id="4d389-114">事件网格是事件路由服务。</span><span class="sxs-lookup"><span data-stu-id="4d389-114">Event Grid is an event routing service.</span></span> <span data-ttu-id="4d389-115">它使用[发布/订阅](../../patterns/publisher-subscriber.md) (pub/sub) 事件模型。</span><span class="sxs-lookup"><span data-stu-id="4d389-115">It uses a [publish/subscribe](../../patterns/publisher-subscriber.md) (pub/sub) eventing model.</span></span>

<span data-ttu-id="4d389-116">与对后端服务进行直接的同步调用相比，使用消息代理的异步通信具有许多优势：</span><span class="sxs-lookup"><span data-stu-id="4d389-116">Asynchronous communication using a message broker provides a number of advantages over making direct, synchronous calls to backend services:</span></span>

- <span data-ttu-id="4d389-117">通过[基于队列的负载均衡模式](../../patterns/queue-based-load-leveling.md)提供负载均衡，以便处理工作负荷突增的情况。</span><span class="sxs-lookup"><span data-stu-id="4d389-117">Provides load-leveling to handle bursts in workloads, using the [Queue-Based Load Leveling pattern](../../patterns/queue-based-load-leveling.md).</span></span>
- <span data-ttu-id="4d389-118">可靠地跟踪长时间运行的工作流的进度，此类工作流涉及多个步骤或多个应用程序。</span><span class="sxs-lookup"><span data-stu-id="4d389-118">Reliably tracks the progress of long-running workflows that involve multiple steps or multiple applications.</span></span>
- <span data-ttu-id="4d389-119">有助于分离应用程序。</span><span class="sxs-lookup"><span data-stu-id="4d389-119">Helps to decouple applications.</span></span>
- <span data-ttu-id="4d389-120">与现有的基于消息的系统集成。</span><span class="sxs-lookup"><span data-stu-id="4d389-120">Integrates with existing message-based systems.</span></span>
- <span data-ttu-id="4d389-121">当后端系统不可用时，允许将工作排队。</span><span class="sxs-lookup"><span data-stu-id="4d389-121">Allows work to be queued when a backend system is not available.</span></span>

<span data-ttu-id="4d389-122">有了事件网格，系统中的各种组件就可以在事件发生时做出反应，不必依赖轮询或计划的任务。</span><span class="sxs-lookup"><span data-stu-id="4d389-122">Event Grid enables the various components in the system to react to events as they happen, rather than relying on polling or scheduled tasks.</span></span> <span data-ttu-id="4d389-123">与消息队列一样，它有助于分离应用程序和服务。</span><span class="sxs-lookup"><span data-stu-id="4d389-123">As with a message queue, it helps decouple applications and services.</span></span> <span data-ttu-id="4d389-124">应用程序或服务可以发布事件，任何相关的订阅方都会获得通知。</span><span class="sxs-lookup"><span data-stu-id="4d389-124">An application or service can publish events, and any interested subscribers will be notified.</span></span> <span data-ttu-id="4d389-125">可以在不更新发送方的情况下添加新的订阅方。</span><span class="sxs-lookup"><span data-stu-id="4d389-125">New subscribers can be added without updating the sender.</span></span>

<span data-ttu-id="4d389-126">许多 Azure 服务支持将事件发送到事件网格。</span><span class="sxs-lookup"><span data-stu-id="4d389-126">Many Azure services support sending events to Event Grid.</span></span> <span data-ttu-id="4d389-127">例如，将新文件添加到 Blob 存储时，逻辑应用可以侦听事件。</span><span class="sxs-lookup"><span data-stu-id="4d389-127">For example, a logic app can listen for an event when new files are added to a blob store.</span></span> <span data-ttu-id="4d389-128">此模式可启用反应式工作流，即上传某个文件或将消息置于队列中会启动一系列过程。</span><span class="sxs-lookup"><span data-stu-id="4d389-128">This pattern enables reactive workflows, where uploading a file or putting a message on a queue kicks off a series of processes.</span></span> <span data-ttu-id="4d389-129">这些过程可能并行执行，也可能按特定顺序执行。</span><span class="sxs-lookup"><span data-stu-id="4d389-129">The processes might be executed in parallel or in a specific sequence.</span></span>

## <a name="recommendations"></a><span data-ttu-id="4d389-130">建议</span><span class="sxs-lookup"><span data-stu-id="4d389-130">Recommendations</span></span>

<span data-ttu-id="4d389-131">[基本企业集成][basic-enterprise-integration]中描述的建议适用于此体系结构。</span><span class="sxs-lookup"><span data-stu-id="4d389-131">The recommendations described in [Basic enterprise integration][basic-enterprise-integration] apply to this architecture.</span></span> <span data-ttu-id="4d389-132">以下建议也适用：</span><span class="sxs-lookup"><span data-stu-id="4d389-132">The following recommendations also apply:</span></span>

### <a name="service-bus"></a><span data-ttu-id="4d389-133">服务总线</span><span class="sxs-lookup"><span data-stu-id="4d389-133">Service Bus</span></span>

<span data-ttu-id="4d389-134">服务总线有两种传递模式：拉取或推送。</span><span class="sxs-lookup"><span data-stu-id="4d389-134">Service Bus has two delivery modes, *pull* or *push*.</span></span> <span data-ttu-id="4d389-135">在拉取模型中，接收方会持续轮询新消息。</span><span class="sxs-lookup"><span data-stu-id="4d389-135">In the pull model, the receiver continuously polls for new messages.</span></span> <span data-ttu-id="4d389-136">轮询可能效率不高，尤其是在有许多队列且每个队列都收到一些消息的情况下，或者是在两个消息的时间间隔很长的情况下。</span><span class="sxs-lookup"><span data-stu-id="4d389-136">Polling can be inefficient, especially if you have many queues that each receive a few messages, or if there a lot of time between messages.</span></span> <span data-ttu-id="4d389-137">在推送模型中，服务总线会在有新消息时通过事件网格发送事件。</span><span class="sxs-lookup"><span data-stu-id="4d389-137">In the push model, Service Bus sends an event through Event Grid when there are new messages.</span></span> <span data-ttu-id="4d389-138">接收方可订阅事件。</span><span class="sxs-lookup"><span data-stu-id="4d389-138">The receiver subscribes to the event.</span></span> <span data-ttu-id="4d389-139">触发事件时，接收方可从服务总线拉取下一批消息。</span><span class="sxs-lookup"><span data-stu-id="4d389-139">When the event is triggered, the receiver pulls the next batch of messages from Service Bus.</span></span>

<span data-ttu-id="4d389-140">创建逻辑应用来使用服务总线消息时，建议将推送模型与事件网格集成配合使用。</span><span class="sxs-lookup"><span data-stu-id="4d389-140">When you create a logic app to consume Service Bus messages, we recommend using the push model with Event Grid integration.</span></span> <span data-ttu-id="4d389-141">它通常更经济有效，因为逻辑应用不需轮询服务总线。</span><span class="sxs-lookup"><span data-stu-id="4d389-141">It's often more cost efficient, because the logic app doesn't need to poll Service Bus.</span></span> <span data-ttu-id="4d389-142">有关详细信息，请参阅 [Azure 服务总线到事件网格的集成概述](/azure/service-bus-messaging/service-bus-to-event-grid-integration-concept)。</span><span class="sxs-lookup"><span data-stu-id="4d389-142">For more information, see [Azure Service Bus to Event Grid integration overview](/azure/service-bus-messaging/service-bus-to-event-grid-integration-concept).</span></span> <span data-ttu-id="4d389-143">目前，服务总线[高级层](https://azure.microsoft.com/pricing/details/service-bus/)是事件网格通知所需的。</span><span class="sxs-lookup"><span data-stu-id="4d389-143">Currently, Service Bus [Premium tier](https://azure.microsoft.com/pricing/details/service-bus/) is required for Event Grid notifications.</span></span>

<span data-ttu-id="4d389-144">请使用 [PeekLock](/azure/service-bus-messaging/service-bus-messaging-overview#queues) 来访问一组消息。</span><span class="sxs-lookup"><span data-stu-id="4d389-144">Use [PeekLock](/azure/service-bus-messaging/service-bus-messaging-overview#queues) for accessing a group of messages.</span></span> <span data-ttu-id="4d389-145">使用 PeekLock 时，逻辑应用可以执行步骤来验证每条消息，然后完成或放弃该消息。</span><span class="sxs-lookup"><span data-stu-id="4d389-145">When you use PeekLock, the logic app can perform steps to validate each message before completing or abandoning the message.</span></span> <span data-ttu-id="4d389-146">此方法可以防范意外的消息丢失。</span><span class="sxs-lookup"><span data-stu-id="4d389-146">This approach protects against accidental message loss.</span></span>

### <a name="event-grid"></a><span data-ttu-id="4d389-147">事件网格</span><span class="sxs-lookup"><span data-stu-id="4d389-147">Event Grid</span></span>

<span data-ttu-id="4d389-148">事件网格触发器的触发意味着发生了“至少 1 个”事件。</span><span class="sxs-lookup"><span data-stu-id="4d389-148">When an Event Grid trigger fires, it means *at least one* event happened.</span></span> <span data-ttu-id="4d389-149">例如，逻辑应用在获取服务总线消息的事件网格触发器时，应假定可能需要处理多个消息。</span><span class="sxs-lookup"><span data-stu-id="4d389-149">For example, when a logic app gets an Event Grid triggers for a Service Bus message, it should assume that several messages might be available to process.</span></span>

<span data-ttu-id="4d389-150">事件网格使用无服务器模型。</span><span class="sxs-lookup"><span data-stu-id="4d389-150">Event Grid uses a serverless model.</span></span> <span data-ttu-id="4d389-151">根据操作（事件执行）数目计算费用。</span><span class="sxs-lookup"><span data-stu-id="4d389-151">Billing is calculated based on the number of operations (event executions).</span></span> <span data-ttu-id="4d389-152">有关详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。</span><span class="sxs-lookup"><span data-stu-id="4d389-152">For more information, see [Event Grid pricing](https://azure.microsoft.com/pricing/details/event-grid/).</span></span> <span data-ttu-id="4d389-153">目前，事件网格没有层方面的注意事项。</span><span class="sxs-lookup"><span data-stu-id="4d389-153">Currently, there are no tier considerations for Event Grid.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="4d389-154">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="4d389-154">Scalability considerations</span></span>

<span data-ttu-id="4d389-155">若要提高可伸缩性，服务总线高级层可以扩展消息传送单元数。</span><span class="sxs-lookup"><span data-stu-id="4d389-155">To achieve higher scalability, the Service Bus Premium tier can scale out the number of messaging units.</span></span> <span data-ttu-id="4d389-156">高级层配置可以包含 1 个、2 个或 4 个消息传递单位。</span><span class="sxs-lookup"><span data-stu-id="4d389-156">Premium tier configurations can have one, two, or four messaging units.</span></span> <span data-ttu-id="4d389-157">有关缩放服务总线的详细信息，请参阅[使用服务总线消息传递改进性能的最佳实践](/azure/service-bus-messaging/service-bus-performance-improvements)。</span><span class="sxs-lookup"><span data-stu-id="4d389-157">For more information about scaling Service Bus, see [Best practices for performance improvements by using Service Bus Messaging](/azure/service-bus-messaging/service-bus-performance-improvements).</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="4d389-158">可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="4d389-158">Availability considerations</span></span>

<span data-ttu-id="4d389-159">查看每个服务的 SLA：</span><span class="sxs-lookup"><span data-stu-id="4d389-159">Review the SLA for each service:</span></span>

- <span data-ttu-id="4d389-160">[API 管理 SLA][apim-sla]</span><span class="sxs-lookup"><span data-stu-id="4d389-160">[API Management SLA][apim-sla]</span></span>
- <span data-ttu-id="4d389-161">[事件网格 SLA][event-grid-sla]</span><span class="sxs-lookup"><span data-stu-id="4d389-161">[Event Grid SLA][event-grid-sla]</span></span>
- <span data-ttu-id="4d389-162">[逻辑应用 SLA][logic-apps-sla]</span><span class="sxs-lookup"><span data-stu-id="4d389-162">[Logic Apps SLA][logic-apps-sla]</span></span>
- <span data-ttu-id="4d389-163">[服务总线 SLA][sb-sla]</span><span class="sxs-lookup"><span data-stu-id="4d389-163">[Service Bus SLA][sb-sla]</span></span>

<span data-ttu-id="4d389-164">为了在发生严重的服务中断时实现故障转移，请考虑在服务总线高级层中实施异地灾难恢复。</span><span class="sxs-lookup"><span data-stu-id="4d389-164">To enable failover if a serious outage occurs, consider implementing geo-disaster recovery in Service Bus Premium.</span></span> <span data-ttu-id="4d389-165">有关详细信息，请参阅 [Azure 服务总线异地灾难恢复](/azure/service-bus-messaging/service-bus-geo-dr)。</span><span class="sxs-lookup"><span data-stu-id="4d389-165">For more information, see [Azure Service Bus geo-disaster recovery](/azure/service-bus-messaging/service-bus-geo-dr).</span></span>

## <a name="security-considerations"></a><span data-ttu-id="4d389-166">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="4d389-166">Security considerations</span></span>

<span data-ttu-id="4d389-167">若要保护服务总线，请使用共享访问签名 (SAS)。</span><span class="sxs-lookup"><span data-stu-id="4d389-167">To secure Service Bus, use shared access signature (SAS).</span></span> <span data-ttu-id="4d389-168">可以使用 [SAS 身份验证](/azure/service-bus-messaging/service-bus-sas)向具有特定权限的用户授予对服务总线资源的访问权限。</span><span class="sxs-lookup"><span data-stu-id="4d389-168">You can grant a user access to Service Bus resources with specific rights by using [SAS authentication](/azure/service-bus-messaging/service-bus-sas).</span></span> <span data-ttu-id="4d389-169">有关详细信息，请参阅[服务总线身份验证和授权](/azure/service-bus-messaging/service-bus-authentication-and-authorization)。</span><span class="sxs-lookup"><span data-stu-id="4d389-169">For more information, see [Service Bus authentication and authorization](/azure/service-bus-messaging/service-bus-authentication-and-authorization).</span></span>

<span data-ttu-id="4d389-170">如果需要将服务总线队列公开为 HTTP 终结点（例如，以便发布新消息），请使用 API 管理将此终结点前置，以对其提供保护。</span><span class="sxs-lookup"><span data-stu-id="4d389-170">If you need to expose a Service Bus queue as an HTTP endpoint, for example, to post new messages, use API Management to secure the queue by fronting the endpoint.</span></span> <span data-ttu-id="4d389-171">然后，可以适当地使用证书或 OAuth 身份验证来保护终结点。</span><span class="sxs-lookup"><span data-stu-id="4d389-171">You can then secure the endpoint with certificates or OAuth authentication as appropriate.</span></span> <span data-ttu-id="4d389-172">保护终结点的最简单方法是使用包含 HTTP 请求/响应触发器的逻辑应用作为中介。</span><span class="sxs-lookup"><span data-stu-id="4d389-172">The easiest way to secure an endpoint is using a logic app with an HTTP request/response trigger as an intermediary.</span></span>

<span data-ttu-id="4d389-173">事件网格服务通过验证代码保护事件传送。</span><span class="sxs-lookup"><span data-stu-id="4d389-173">The Event Grid service secures event delivery through a validation code.</span></span> <span data-ttu-id="4d389-174">如果通过逻辑应用来使用事件，则会自动执行验证。</span><span class="sxs-lookup"><span data-stu-id="4d389-174">If you use Logic Apps to consume the event, validation is automatically performed.</span></span> <span data-ttu-id="4d389-175">有关详细信息，请参阅[事件网格安全性和身份验证](/azure/event-grid/security-authentication)。</span><span class="sxs-lookup"><span data-stu-id="4d389-175">For more information, see [Event Grid security and authentication](/azure/event-grid/security-authentication).</span></span>

[apim]: /azure/api-management
[apim-sla]: https://azure.microsoft.com/support/legal/sla/api-management/
[event-grid]: /azure/event-grid/
[event-grid-sla]: https://azure.microsoft.com/support/legal/sla/event-grid
[logic-apps]: /azure/logic-apps/logic-apps-overview
[logic-apps-sla]: https://azure.microsoft.com/support/legal/sla/logic-apps
[sb-sla]: https://azure.microsoft.com/support/legal/sla/service-bus/
[service-bus]: /azure/service-bus-messaging/
[basic-enterprise-integration]: ./basic-enterprise-integration.md
