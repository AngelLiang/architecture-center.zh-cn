---
title: 可缩放的 Web 应用程序
titleSuffix: Azure Reference Architectures
description: 提高在 Microsoft Azure 中运行的 Web 应用程序的可伸缩性。
author: MikeWasson
ms.date: 10/25/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: b015a130a74f3160c0e737b436d41e1b1ea7b5bf
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241368"
---
# <a name="improve-scalability-in-an-azure-web-application"></a>提高 Azure Web 应用程序的可伸缩性

此参考体系结构显示的经验证做法可以改进 Azure 应用服务 Web 应用程序的可伸缩性和性能。

![Azure 中改进了可伸缩性的 Web 应用程序](./images/scalable-web-app.png)

下载此体系结构的 [Visio 文件][visio-download]。

## <a name="architecture"></a>体系结构

此体系结构基于[基本 Web 应用程序][basic-web-app]中显示的体系结构。 它包括以下组件：

- 资源组。 [资源组][resource-group]是 Azure 资源的逻辑容器。
- **[Web 应用][app-service-web-app]**。 典型的现代应用程序可能包括一个网站以及一个或多个 RESTful Web API。 Web API 可供浏览器客户端通过 AJAX 来使用，也可供本机客户端应用程序或服务器端应用程序使用。 有关设计 Web API 的注意事项，请参阅 [API 设计指南][api-guidance]。
- **函数应用**。 使用 [Function App][functions] 运行后台任务。 Functions 由触发器（例如计时器事件或将消息置于队列中的事件）调用。 对于长时间运行的有状态任务，请使用 [Durable Functions][durable-functions]。
- **队列**。 在此处显示的体系结构中，应用程序通过向 [Azure 队列存储][queue-storage]队列放置消息，将后台任务排队。 消息会触发函数应用。 也可使用服务总线队列。 如需比较，请参阅 [Azure 队列和服务总线队列 - 比较与对照][queues-compared]。
- **缓存**。 在 [Azure Redis 缓存][azure-redis]中存储半静态数据。
- **CDN**。 使用 [Azure 内容分发网络][azure-cdn] (CDN) 缓存公开提供的内容，以便降低延迟并加快内容分发速度。
- **数据存储**。 对关系数据使用 [Azure SQL 数据库][sql-db]。 对于非关系数据，可考虑 [Cosmos DB][cosmosdb]。
- **Azure 搜索**。 使用 [Azure 搜索][azure-search]添加搜索功能，例如搜索建议、模糊搜索、特定于语言的搜索。 Azure 搜索通常与其他数据存储结合使用，尤其是在主数据存储对一致性要求严格的情况下。 此方法将权威数据存储在其他数据存储中，将搜索索引存储在 Azure 搜索中。 也可使用 Azure 搜索合并来自多个数据存储的单一搜索索引。
- **Azure DNS**。 [Azure DNS][azure-dns] 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。
- **应用程序网关**。 [应用程序网关](/azure/application-gateway/)是 7 层负载均衡器。 在此体系结构中，它将 HTTP 请求路由到 Web 前端。 应用程序网关还提供一个 [Web 应用程序防火墙](/azure/application-gateway/waf-overview) (WAF)，出现常见的漏洞和攻击时，WAF 可以保护应用程序。

## <a name="recommendations"></a>建议

你的要求可能不同于此处描述的体系结构。 一开始可使用此部分的建议。

### <a name="app-service-apps"></a>应用服务应用

建议以独立应用服务应用的形式创建 Web 应用程序和 Web API。 此设计允许你按独立的应用服务计划运行它们，以便对它们进行单独缩放。 如果一开始不需要该级别的可伸缩性，可以先将应用部署到同一计划中，再在以后根据需要将其移至独立的计划中。

> [!NOTE]
> “基本”、“标准”和“高级”计划按计划中的 VM 实例计费，而不是按应用计费。 请参阅[应用服务定价][app-service-pricing]
>

### <a name="cache"></a>缓存

可以使用 [Azure Redis 缓存][azure-redis]来缓存一些数据，从而改进性能和可伸缩性。 考虑将 Redis 缓存用于：

- 半静态事务数据。
- 会话状态。
- HTML 输出。 这适用于可呈现复杂 HTML 输出的应用程序。

有关缓存策略设计的更详细指南，请参阅[缓存指南][caching-guidance]。

### <a name="cdn"></a>CDN

使用 [Azure CDN][azure-cdn] 来缓存静态内容。 CDN 的主要优势是降低用户的延迟，因为内容缓存在靠近用户的边缘服务器上。 CDN 还可以减轻应用程序的负载，因为相应的流量不是由应用程序处理。

如果应用包含的内容大部分为静态页面，可考虑使用 [CDN 来缓存整个应用][cdn-app-service]。 否则，请将静态内容（例如图像、CSS 和 HTML 文件）置于 [Azure 存储中，并使用 CDN 缓存这些文件][cdn-storage-account]。

> [!NOTE]
> Azure CDN 不能提供需要身份验证的内容。
>

如需更详细的指南，请参阅[内容分发网络 (CDN) 指南][cdn-guidance]。

### <a name="storage"></a>存储

现代应用程序通常处理大量的数据。 若要进行适合云的缩放，请务必选择适当的存储类型。 以下是一些基线建议。

| 要存储的内容 | 示例 | 建议的存储 |
| --- | --- | --- |
| 文件 |图像、文档、PDF |Azure Blob 存储 |
| 键值对 |按用户 ID 查找的用户配置文件数据 |Azure 表存储 |
| 旨在触发进一步处理的短消息 |订单请求 |Azure 队列存储、服务总线队列或服务总线主题 |
| 架构灵活但只需要进行基本查询的非关系数据 |产品目录 |文档数据库，例如 Azure Cosmos DB、MongoDB 或 Apache CouchDB |
| 需要更丰富的查询支持、严格的架构和/或高一致性的关系数据 |产品清单 |Azure SQL 数据库 |

请参阅[选择正确的数据存储][datastore]。

## <a name="scalability-considerations"></a>可伸缩性注意事项

Azure 应用服务的主要优势是能够根据负载缩放应用程序。 下面是在计划缩放应用程序时需要考虑的一些注意事项。

### <a name="app-service-app"></a>应用服务应用

如果解决方案包括多个应用服务应用，可考虑将其部署到不同的应用服务计划。 这种方法允许独立缩放应用，因为应用在不同的实例上运行。

同样，可以考虑将函数应用置于其自己的计划中，使后台任务不在处理 HTTP 请求的实例上运行。 如果后台任务间歇性运行，可以考虑使用[消耗量计划][functions-consumption-plan]，该计划按执行数而不是小时数收费。

### <a name="sql-database"></a>SQL 数据库

通过数据库分片，增加 SQL 数据库的可伸缩性。 分片是指将数据库水平分区。 可以通过分片，使用[弹性数据库工具][sql-elastic]横向扩展数据库。 分片的潜在好处包括：

- 提高事务吞吐量。
- 对数据子集运行查询可以提高速度。

### <a name="azure-search"></a>Azure 搜索

Azure 搜索没有在主数据存储中执行复杂的数据搜索所需的开销，并可通过缩放来处理负载。 请参阅[在 Azure 搜索中缩放用于查询和索引工作负荷的资源级别][azure-search-scaling]。

## <a name="security-considerations"></a>安全注意事项

本部分列出专门与本文中所述 Azure 服务相关的安全注意事项， 内容并非安全最佳做法的完整列表。 有关其他一些安全注意事项，请参阅[保护 Azure 应用服务中的应用][app-service-security]。

### <a name="cross-origin-resource-sharing-cors"></a>跨源资源共享 (CORS)

如果将网站和 Web API 作为独立应用创建，则网站不能向 API 进行客户端 AJAX 调用，除非启用 CORS。

> [!NOTE]
> 浏览器安全性将阻止网页向另一个域发出 AJAX 请求。 这种限制称为同域策略，可阻止恶意站点读取另一个站点中的敏感数据。 CORS 是一项 W3C 标准，可让服务器放宽同域策略，在拒绝某些跨域请求的同时，允许另一些跨域请求。
>

应用服务内置了对 CORS 的支持，不需编写任何应用程序代码。 请参阅[借助 CORS 从 JavaScript 使用 API 应用][cors]。 请将网站添加到 API 允许域的列表。

### <a name="sql-database-encryption"></a>SQL 数据库加密

如需加密数据库中的静态数据，请使用[透明数据加密][sql-encryption]。 此功能对整个数据库（包括备份和事务日志文件）执行实时加密和解密，不需对应用程序进行更改。 加密会增加一些延迟，因此最好将必须保护的数据单独放置在自己的数据库中，仅对该数据库启用加密。

<!-- links -->

[api-guidance]: ../../best-practices/api-design.md
[app-service-security]: /azure/app-service-web/web-sites-security
[app-service-web-app]: /azure/app-service-web/app-service-web-overview
[app-service-api-app]: /azure/app-service-api/app-service-api-apps-why-best-platform
[app-service-pricing]: https://azure.microsoft.com/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/services/cdn/
[azure-dns]: /azure/dns/dns-overview
[azure-redis]: https://azure.microsoft.com/services/cache/
[azure-search]: /azure/search
[azure-search-scaling]: /azure/search/search-capacity-planning
[basic-web-app]: basic-web-app.md
[basic-web-app-scalability]: basic-web-app.md#scalability-considerations
[caching-guidance]: ../../best-practices/caching.md
[cdn-app-service]: /azure/app-service-web/cdn-websites-with-cdn
[cdn-storage-account]: /azure/cdn/cdn-create-a-storage-account-with-cdn
[cdn-guidance]: ../../best-practices/cdn.md
[cors]: /azure/app-service-api/app-service-api-cors-consume-javascript
[cosmosdb]: /azure/cosmos-db/
[datastore]: ../..//guide/technology-choices/data-store-overview.md
[durable-functions]: /azure/azure-functions/durable-functions-overview
[functions]: /azure/azure-functions/functions-overview
[functions-consumption-plan]: /azure/azure-functions/functions-scale#consumption-plan
[queue-storage]: /azure/storage/storage-dotnet-how-to-use-queues
[queues-compared]: /azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted
[resource-group]: /azure/azure-resource-manager/resource-group-overview#resource-groups
[sql-db]: /azure/sql-database/
[sql-elastic]: /azure/sql-database/sql-database-elastic-scale-introduction
[sql-encryption]: https://msdn.microsoft.com/library/dn948096.aspx
[tm]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/app-service-reference-architectures.vsdx
[web-app-multi-region]: ./multi-region.md
