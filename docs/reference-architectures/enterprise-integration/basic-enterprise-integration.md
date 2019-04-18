---
title: 使用 Azure 的基本企业集成
titleSuffix: Azure Reference Architectures
description: 建议用于通过 Azure 逻辑应用和 Azure API 管理实现简单企业集成模式的体系结构。
services: logic-apps
author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: reference-architecture
ms.date: 12/03/2018
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: integration-services
ms.openlocfilehash: 16fce3a85cbc0a94dd93277d942fae51ae0e4c04
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640832"
---
# <a name="basic-enterprise-integration-on-azure"></a>Azure 上的基本企业集成

本引用体系结构使用 [Azure Integration Services][integration-services] 来协调对企业后端系统的调用。 后端系统可能包括软件即服务 (SaaS) 系统、Azure 服务以及企业中现有的 Web 服务。

Azure Integration Services 是用于集成应用程序和数据的服务的集合。 此体系结构使用下述服务中的两种：用于协调工作流的[逻辑应用][logic-apps]和用于创建 API 目录的 [API 管理][apim]。 本体系结构足以处理基本的集成方案，即对后端服务进行同步调用时触发工作流的情况。 使用[队列和事件](./queues-events.md)的更复杂体系结构基于这个基本的体系结构而构建。

![体系结构图 - 简单的企业集成](./_images/simple-enterprise-integration.png)

## <a name="architecture"></a>体系结构

此体系结构具有以下组件：

- **后端系统**。 上图的右侧显示企业部署的或依赖的各种后端系统。 其中可能包括 SaaS 系统、其他 Azure 服务或公开 REST 或 SOAP 终结点的 Web 服务。

- **Azure 逻辑应用**。 [逻辑应用][logic-apps]是一种用于生成企业工作流的无服务器平台，企业工作流可集成应用程序、数据和服务。 在本体系结构中，逻辑应用由 HTTP 请求触发。 也可嵌套工作流，以便实现更复杂的业务流程。 逻辑应用使用[连接器][logic-apps-connectors]与常用的服务集成。 逻辑应用提供数百个连接器，但你可以创建自定义连接器。

- **Azure API 管理**。 [API 管理][apim]是用于发布 HTTP API 目录的托管服务，可以促进重复使用和可发现性。 API 管理包含两个相关的组件：

  - **API 网关**。 API 网关接受 HTTP 调用并将其路由到后端。

  - **开发人员门户**。 Azure API 管理的每个实例都可以访问[开发人员门户][apim-dev-portal]。 开发人员可以通过此门户访问有关 API 调用的文档和代码示例。 还可以在开发人员门户中测试 API。

  在本体系结构中，可以通过[导入逻辑应用][apim-logic-app]作为 API 来生成复合 API。 也可导入现有的 Web 服务，方法是[导入 OpenAPI][apim-openapi] (Swagger) 规范或从 WSDL 规范[导入 SOAP API][apim-soap]。

  API 网关用于将前端客户端与后端分离。 例如，它可以重新编写 URL 或在请求抵达后端之前转换请求。 它还处理许多跨领域问题，例如身份验证、跨域资源共享 (CORS) 支持以及响应缓存。

- **Azure DNS**。 [Azure DNS][dns] 是 DNS 域的托管服务。 Azure DNS 使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 若要使用自定义域名（例如 contoso.com），请创建可将自定义域名映射到 IP 地址的 DNS 记录。 有关详细信息，请参阅[在 API 管理中配置自定义域名][apim-domain]。

- **Azure Active Directory (Azure AD)**。 使用 [Azure AD][aad] 对调用 API 网关的客户端进行身份验证。 Azure AD 支持 OpenID Connect (OIDC) 协议。 客户端从 Azure AD 获取访问令牌，API 网关在[验证令牌][apim-jwt]后对请求授权。 使用标准层或高级层 API 管理时，Azure AD 也可确保开发人员门户的访问安全性。

## <a name="recommendations"></a>建议

你的具体要求可能与此处显示的通用体系结构不同。 请使用本部分中的建议作为入手点。

### <a name="api-management"></a>API 管理

使用 API 管理基本层、标准层或高级层。 这些层提供生产服务级别协议 (SLA)，并支持 Azure 区域中的横向扩展。 API 管理的吞吐量容量以单位来度量。 每个定价层的横向扩展存在上限。高级层还支持跨多个 Azure 区域横向扩展。 请根据功能集和所需的吞吐量级别选择层。 有关详细信息，请参阅 [API 管理定价][apim-pricing]和 [Azure API 管理实例的容量][apim-capacity]。

每个 Azure API 管理实例都有一个默认的域名，它是 `azure-api.net` 的子域，例如 `contoso.azure-api.net`。 考虑为组织配置[自定义域][apim-domain]。

### <a name="logic-apps"></a>逻辑应用

如果不需要保证较低的延迟进行响应，则非常适合使用逻辑应用，例如执行异步操作，或者运行时间适中的 API 调用。 如果需要保证低延迟（例如，在调用会导致用户界面停滞的情况下），请使用其他技术。 例如，使用 Azure Functions 或部署到 Azure 应用服务的 Web API。 对于 API 使用者，请优先 API 管理而不是 API。

### <a name="region"></a>区域

若要尽量降低网络延迟，可将 API 管理和逻辑应用置于同一区域。 一般情况下，请选择离用户最近（或离后端服务最近）的区域。

资源组也有一个区域。 此区域指定了部署元数据的存储位置，以及执行部署模板的位置。 为了提高部署期间的可用性，请将资源组和资源放在同一区域。

## <a name="scalability-considerations"></a>可伸缩性注意事项

为了提高 API 管理的可伸缩性，请添加[缓存策略][apim-caching]（如果适用）。 缓存还有助于减少后端服务的负载。

若要提高容量，可在 Azure 区域中横向扩展 Azure API 管理基本层、标准层和高级层。 若要分析服务的使用情况，请在“指标”菜单中选择“容量指标”选项，然后相应地纵向扩展或缩减。 升级或缩放过程可能需要 15 到 45 分钟才能完成。

有关缩放 API 管理服务的建议：

- 缩放时请考虑流量模式。 采用较高易失性流量模式的客户需要更多的容量。

- 如果容量一贯超过 66%，则可能表示需要纵向扩展。

- 如果容量一贯低于 20%，则可能表示能够缩减。

- 在生产环境中启用负载之前，请始终使用有代表性的负载对 API 管理服务进行负载测试。

使用高级层时，可以跨多个 Azure 区域缩放 API 管理实例。 这样可以让 API 管理有资格获得更高的 SLA，让你可以在多个区域靠近用户的位置预配服务。

逻辑应用的无服务器模型意味着管理员无需规划服务可伸缩性。 服务会根据需求自动缩放。

## <a name="availability-considerations"></a>可用性注意事项

查看每个服务的 SLA：

- [API 管理 SLA][apim-sla]
- [逻辑应用 SLA][logic-apps-sla]

如果在使用高级层时跨至少两个区域来部署 API 管理，则有资格获得更高的 SLA。 请参阅 [API 管理定价][apim-pricing]。

### <a name="backups"></a>备份

定期[备份][apim-backup] API 管理配置。 将备份文件存储在不同于服务部署区域的某个位置或 Azure 区域。 根据 [RTO][rto] 选择灾难恢复策略：

- 在灾难恢复事件中，预配新的 API 管理实例，将备份还原到新实例，并重新指向 DNS 记录。

- 在另一 Azure 区域中保留 API 管理服务的被动实例。 定期将备份还原到该实例，使之与主动服务同步。 若要在发生灾难恢复事件期间还原服务，只需重新指向 DNS 记录。 此方法会产生额外的成本，因为需为被动实例付费，但会缩短恢复时间。

对于逻辑应用，建议使用配置即代码方法进行备份和还原。 由于逻辑应用无服务器，因此可以通过 Azure 资源管理器模板快速地重新创建它们。 请将模板保存在源代码管理中，并将模板与持续集成/持续部署 (CI/CD) 过程相集成。 在灾难恢复事件中，请将模板部署到新区域。

如果将逻辑应用部署到另一区域，请在 API 管理中更新配置。 可以使用一个基本的 PowerShell 脚本来更新 API 的**后端**属性。

## <a name="manageability-considerations"></a>可管理性注意事项

为生产、开发和测试环境创建单独的资源组。 使用单独的资源组可以更方便地管理部署、删除测试部署，以及分配访问权限。

将资源分配到资源组时，请考虑以下因素：

- **生命周期**。 一般情况下，应将具有相同生命周期的资源放入同一个资源组。

- **访问**。 若要对组中的资源应用访问策略，可以使用[基于角色的访问控制][rbac] (RBAC)。

- **计费**。 可以查看资源组的累计成本。

- **API 管理的定价层**。 对开发和测试环境使用开发人员层。 为了尽量降低预生产期间的成本，请部署生产环境的副本、运行测试，然后关闭。

### <a name="deployment"></a>部署

使用 [Azure 资源管理器模板][arm]部署 Azure 资源。 使用模板可以通过 PowerShell 或 Azure CLI 更轻松地自动完成部署。

将 API 管理和任何单个逻辑应用放在其自身独立的资源管理器模板中。 使用独立的模板可将资源存储在源代码管理系统中。 可以在 CI/CD 过程中统一或者逐个部署这些模板。

### <a name="versions"></a>版本

每当更改逻辑应用的配置或者通过资源管理器模板部署更新时，Azure 都会保留该版本的副本，并保留具有运行历史记录的所有版本。 可以使用这些版本来跟踪历史更改，或者将某个版本提升为逻辑应用的当前配置。 例如，可以将逻辑应用回退到以前的版本。

API 管理支持两个不同但互补的版本控制概念：

- 版本：API 使用者可以根据需要选择 API 版本（例如 v1、v2、beta 或 production）。

- 修订版：API 管理员可以在 API 中进行非重大更改并部署这些更改。另外还可以提供更改日志，将所做的更改告知 API 使用者。

可在开发环境中创建修订版，并使用资源管理器模板在其他环境中部署此项更改。 有关详细信息，请参阅[发布 API 的多个版本][apim-versions]

也可在将某个 API 指定为“当前”版本并提供给用户访问之前，使用修订版来测试该 API。 但是，建议不要使用此方法进行负载测试或集成测试。 请改用独立的测试环境或预生产环境。

## <a name="diagnostics-and-monitoring"></a>诊断和监控

在 API 管理和逻辑应用中使用 [Azure Monitor][monitor] 进行操作监视。 Azure Monitor 根据为每个服务配置的指标提供信息，默认已启用。 有关详细信息，请参阅：

- [监视已发布的 API][apim-monitor]
- [针对 Azure 逻辑应用监视状态、设置诊断日志记录，并启用警报][logic-apps-monitor]

每个服务还提供以下选项：

- 若要进行更深入的分析和仪表板显示，请将逻辑应用日志发送到 [Azure Log Analytics][logic-apps-log-analytics]。

- 若要进行 DevOps 监视，请为 API 管理配置 Azure Application Insights。

- API 管理支持[使用 Power BI 解决方案模板进行自定义 API 分析][apim-pbi]。 可以使用此解决方案模板来创建自己的分析解决方案。 业务用户可在 Power BI 中查看报表。

## <a name="security-considerations"></a>安全注意事项

下面是一些针对本体系结构的安全注意事项，虽然此列表并未完整描述所有的安全最佳做法：

- Azure API 管理服务具有固定的公共 IP 地址。 只有 API 管理的 IP 地址能调用逻辑应用终结点。 有关详细信息，请参阅[限制传入 IP 地址][logic-apps-restrict-ip]。

- 为确保用户拥有适当的访问级别，请使用基于角色的访问控制 (RBAC)。

- 使用 OAuth 或 OpenID Connect 保护 API 管理中的公共 API 终结点。 若要保护公共 API 终结点，请配置标识提供者并添加 JSON Web 令牌 (JWT) 验证策略。 有关详细信息，请参阅[结合 Azure Active Directory 和 API 管理使用 OAuth 2.0 保护 API][apim-oauth]。

- 使用相互身份验证证书从 API 管理连接到后端服务。

- 在 API 管理 API 上强制实施 HTTPS。

### <a name="storing-secrets"></a>存储机密

切勿将密码、访问密钥或连接字符串签入源代码管理。 如果需要这些值，请使用适当的技术保护和部署这些值。

如果逻辑应用所需的任何敏感值不能在连接器中创建，请将这些值存储在 Azure Key Vault 中，并从资源管理器模板引用它们。 请为每个环境使用部署模板参数和参数文件。 有关详细信息，请参阅[保护工作流中的参数和输入][logic-apps-secure]。

API 管理使用称作“命名值”或“属性”的对象管理机密。 这些对象安全地存储可通过 API 管理策略访问的值。 有关详细信息，请参阅[如何在 Azure API 管理策略中使用命名值][apim-properties]。

## <a name="cost-considerations"></a>成本注意事项

需要对所有运行的 API 管理实例付费。 如果已纵向扩展，但始终不需要该性能级别，请手动进行纵向缩减，或者配置[自动缩放][apim-autoscale]。

逻辑应用使用[无服务器](/azure/logic-apps/logic-apps-serverless-overview)模型。 根据操作和连接器执行计算费用。 有关详细信息，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 目前，逻辑应用没有层方面的注意事项。

## <a name="next-steps"></a>后续步骤

若要提高可靠性和可伸缩性，请使用消息队列和事件将后端系统分离。 此模式在下一参考体系结构中进行了介绍，详见以下系列：[使用消息队列和事件的企业集成](./queues-events.md)。

<!-- links -->

[aad]: /azure/active-directory
[apim]: /azure/api-management
[apim-autoscale]: /azure/api-management/api-management-howto-autoscale
[apim-backup]: /azure/api-management/api-management-howto-disaster-recovery-backup-restore
[apim-caching]: /azure/api-management/api-management-howto-cache
[apim-capacity]: /azure/api-management/api-management-capacity
[apim-dev-portal]: /azure/api-management/api-management-key-concepts#a-namedeveloper-portal-a-developer-portal
[apim-domain]: /azure/api-management/configure-custom-domain
[apim-jwt]: /azure/api-management/policies/authorize-request-based-on-jwt-claims
[apim-logic-app]: /azure/api-management/import-logic-app-as-api
[apim-monitor]: /azure/api-management/api-management-howto-use-azure-monitor
[apim-oauth]: /azure/api-management/api-management-howto-protect-backend-with-aad
[apim-openapi]: /azure/api-management/import-api-from-oas
[apim-pbi]: https://aka.ms/apimpbi
[apim-pricing]: https://azure.microsoft.com/pricing/details/api-management/
[apim-properties]: /azure/api-management/api-management-howto-properties
[apim-sla]: https://azure.microsoft.com/support/legal/sla/api-management/
[apim-soap]: /azure/api-management/import-soap-api
[apim-versions]: /azure/api-management/api-management-get-started-publish-versions
[arm]: /azure/azure-resource-manager/resource-group-authoring-templates
[dns]: /azure/dns/
[integration-services]: https://azure.microsoft.com/product-categories/integration/
[logic-apps]: /azure/logic-apps/logic-apps-overview
[logic-apps-connectors]: /azure/connectors/apis-list
[logic-apps-log-analytics]: /azure/logic-apps/logic-apps-monitor-your-logic-apps-oms
[logic-apps-monitor]: /azure/logic-apps/logic-apps-monitor-your-logic-apps
[logic-apps-restrict-ip]: /azure/logic-apps/logic-apps-securing-a-logic-app#restrict-incoming-ip-addresses
[logic-apps-secure]: /azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow
[logic-apps-sla]: https://azure.microsoft.com/support/legal/sla/logic-apps
[monitor]: /azure/azure-monitor/overview
[rbac]: /azure/role-based-access-control/overview
[rto]: ../../reliability/requirements.md#recovery-metrics