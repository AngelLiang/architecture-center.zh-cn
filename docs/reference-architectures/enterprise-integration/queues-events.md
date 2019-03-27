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
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245508"
---
# <a name="enterprise-integration-on-azure-using-message-queues-and-events"></a>Azure 上使用消息队列和事件的企业集成

此参考体系结构集成企业后端系统，使用消息队列和事件将服务分离，以提高可伸缩性和可靠性。 后端系统可能包括软件即服务 (SaaS) 系统、Azure 服务以及企业中现有的 Web 服务。

![适用于使用队列和事件的企业集成的参考体系结构](./_images/enterprise-integration-queues-events.png)

## <a name="architecture"></a>体系结构

此处显示的体系结构在更简单的体系结构上构建，后者显示在[基本企业集成][basic-enterprise-integration]中。 该体系结构使用用于协调工作流的[逻辑应用][logic-apps]和用于创建 API 目录的 [API 管理][apim]。

此版体系结构添加了两个组件，使系统的可靠性和可伸缩性更高：

- **[Azure 服务总线][service-bus]**。 服务总线是安全且可靠的消息代理。

- **[Azure 事件网格][event-grid]**。 事件网格是事件路由服务。 它使用[发布/订阅](../../patterns/publisher-subscriber.md) (pub/sub) 事件模型。

与对后端服务进行直接的同步调用相比，使用消息代理的异步通信具有许多优势：

- 通过[基于队列的负载均衡模式](../../patterns/queue-based-load-leveling.md)提供负载均衡，以便处理工作负荷突增的情况。
- 可靠地跟踪长时间运行的工作流的进度，此类工作流涉及多个步骤或多个应用程序。
- 有助于分离应用程序。
- 与现有的基于消息的系统集成。
- 当后端系统不可用时，允许将工作排队。

有了事件网格，系统中的各种组件就可以在事件发生时做出反应，不必依赖轮询或计划的任务。 与消息队列一样，它有助于分离应用程序和服务。 应用程序或服务可以发布事件，任何相关的订阅方都会获得通知。 可以在不更新发送方的情况下添加新的订阅方。

许多 Azure 服务支持将事件发送到事件网格。 例如，将新文件添加到 Blob 存储时，逻辑应用可以侦听事件。 此模式可启用反应式工作流，即上传某个文件或将消息置于队列中会启动一系列过程。 这些过程可能并行执行，也可能按特定顺序执行。

## <a name="recommendations"></a>建议

[基本企业集成][basic-enterprise-integration]中描述的建议适用于此体系结构。 以下建议也适用：

### <a name="service-bus"></a>服务总线

服务总线有两种传递模式：拉取或推送。 在拉取模型中，接收方会持续轮询新消息。 轮询可能效率不高，尤其是在有许多队列且每个队列都收到一些消息的情况下，或者是在两个消息的时间间隔很长的情况下。 在推送模型中，服务总线会在有新消息时通过事件网格发送事件。 接收方可订阅事件。 触发事件时，接收方可从服务总线拉取下一批消息。

创建逻辑应用来使用服务总线消息时，建议将推送模型与事件网格集成配合使用。 它通常更经济有效，因为逻辑应用不需轮询服务总线。 有关详细信息，请参阅 [Azure 服务总线到事件网格的集成概述](/azure/service-bus-messaging/service-bus-to-event-grid-integration-concept)。 目前，服务总线[高级层](https://azure.microsoft.com/pricing/details/service-bus/)是事件网格通知所需的。

请使用 [PeekLock](/azure/service-bus-messaging/service-bus-messaging-overview#queues) 来访问一组消息。 使用 PeekLock 时，逻辑应用可以执行步骤来验证每条消息，然后完成或放弃该消息。 此方法可以防范意外的消息丢失。

### <a name="event-grid"></a>事件网格

事件网格触发器的触发意味着发生了“至少 1 个”事件。 例如，逻辑应用在获取服务总线消息的事件网格触发器时，应假定可能需要处理多个消息。

事件网格使用无服务器模型。 根据操作（事件执行）数目计算费用。 有关详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。 目前，事件网格没有层方面的注意事项。

## <a name="scalability-considerations"></a>可伸缩性注意事项

若要提高可伸缩性，服务总线高级层可以扩展消息传送单元数。 高级层配置可以包含 1 个、2 个或 4 个消息传递单位。 有关缩放服务总线的详细信息，请参阅[使用服务总线消息传递改进性能的最佳实践](/azure/service-bus-messaging/service-bus-performance-improvements)。

## <a name="availability-considerations"></a>可用性注意事项

查看每个服务的 SLA：

- [API 管理 SLA][apim-sla]
- [事件网格 SLA][event-grid-sla]
- [逻辑应用 SLA][logic-apps-sla]
- [服务总线 SLA][sb-sla]

为了在发生严重的服务中断时实现故障转移，请考虑在服务总线高级层中实施异地灾难恢复。 有关详细信息，请参阅 [Azure 服务总线异地灾难恢复](/azure/service-bus-messaging/service-bus-geo-dr)。

## <a name="security-considerations"></a>安全注意事项

若要保护服务总线，请使用共享访问签名 (SAS)。 可以使用 [SAS 身份验证](/azure/service-bus-messaging/service-bus-sas)向具有特定权限的用户授予对服务总线资源的访问权限。 有关详细信息，请参阅[服务总线身份验证和授权](/azure/service-bus-messaging/service-bus-authentication-and-authorization)。

如果需要将服务总线队列公开为 HTTP 终结点（例如，以便发布新消息），请使用 API 管理将此终结点前置，以对其提供保护。 然后，可以适当地使用证书或 OAuth 身份验证来保护终结点。 保护终结点的最简单方法是使用包含 HTTP 请求/响应触发器的逻辑应用作为中介。

事件网格服务通过验证代码保护事件传送。 如果通过逻辑应用来使用事件，则会自动执行验证。 有关详细信息，请参阅[事件网格安全性和身份验证](/azure/event-grid/security-authentication)。

[apim]: /azure/api-management
[apim-sla]: https://azure.microsoft.com/support/legal/sla/api-management/
[event-grid]: /azure/event-grid/
[event-grid-sla]: https://azure.microsoft.com/support/legal/sla/event-grid
[logic-apps]: /azure/logic-apps/logic-apps-overview
[logic-apps-sla]: https://azure.microsoft.com/support/legal/sla/logic-apps
[sb-sla]: https://azure.microsoft.com/support/legal/sla/service-bus/
[service-bus]: /azure/service-bus-messaging/
[basic-enterprise-integration]: ./basic-enterprise-integration.md
