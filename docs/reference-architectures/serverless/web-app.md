---
title: 无服务器 Web 应用程序
description: 演示无服务器 Web 应用程序和 Web API 的参考体系结构
author: MikeWasson
ms.date: 10/16/2018
ms.openlocfilehash: c2b46a60a57381ac3fd3f77cffe53b2dab2dacd6
ms.sourcegitcommit: 113a7248b9793c670b0f2d4278d30ad8616abe6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49349949"
---
# <a name="serverless-web-application"></a>无服务器 Web 应用程序 

本参考体系结构演示一个无服务器 Web 应用程序。 该应用程序提供 Azure Blob 存储中的静态内容，并使用 Azure Functions 来实现一个 API。 该 API 从 Cosmos DB 读取数据，并将结果返回到 Web 应用。 [GitHub][github] 中提供了本体系结构的参考实现。

![](./_images/serverless-web-app.png)
 
术语“无服务器”有两种不同但相关的含义：

- **后端即服务** (BaaS)。 后端云服务（例如数据库和存储）提供 API，使客户端应用程序能够直接连接到这些服务。 
- **函数即服务** (FaaS)。 在此模型中，“函数”是部署到云的一段代码，在托管环境中运行，用于完全抽象化运行代码的服务器。 

这两个定义在概念上有共同之处，即，开发人员和 DevOps 人员无需部署、配置或管理服务器。 本参考体系结构侧重于使用 Azure Functions 的 FaaS，不过，由一个示例 BaaS 提供 Azure Blob 存储中的 Web 内容。 FaaS 的部分重要特征包括：

1. 计算资源由平台按需动态分配。
1. 基于消耗量的定价：你只需支付执行代码所用的计算资源的费用。
1. 可根据流量按需缩放计算资源，开发人员无需执行任何配置。

函数在激发外部触发器时执行，例如，当 HTTP 请求或消息抵达队列时。 这样可以得到一种[事件驱动的体系结构样式][event-driven]，即无服务器体系结构的原生样式。 若要协调体系结构中组件之间的工作，请考虑使用消息中转站或发布/订阅模式。 在选择 Azure 中的消息传送技术时如需帮助，请参阅[选择用于传送消息的 Azure 服务][azure-messaging]。

## <a name="architecture"></a>体系结构
该体系结构包括以下组件。

**Blob 存储**。 静态 Web 内容（例如 HTML、CSS 和 JavaScript 文件）存储在 Azure Blob 存储中，并通过[静态网站托管][static-hosting]提供给客户端。 所有动态交互通过调用后端 API 的 JavaScript 代码进行。 没有任何服务器端代码可以呈现网页。 静态网站托管支持索引文档和自定义的 404 错误页。

> [!NOTE]
> 静态网站托管目前以[预览版][static-hosting-preview]提供。

**CDN**。 使用 [Azure 内容分发网络][cdn] (CDN) 缓存内容，以降低延迟和加快内容传送速度，并提供 HTTPS 终结点。

**函数应用**。 [Azure Functions][functions] 是一个无服务器计算选项。 它使用事件驱动的模型，其中的一段代码（“函数”）由触发器调用。 在此体系结构中，当客户端发出 HTTP 请求时，会调用函数。 请求始终通过 API 网关路由，如下所述。

**API 管理**。 [API 管理][apim]在 HTTP 函数的前面提供 API 网关。 可以使用 API 管理来发布和管理客户端应用程序所用的 API。 使用网关有助于将前端应用程序与后端 API 分离。 例如，API 管理可以重新编写 URL、在请求抵达后端之前转换请求、设置请求或响应标头，等等。

API 管理还可用于实现横切任务，例如：

- 强制实施用量配额和速率限制
- 验证 OAuth 身份验证令牌
- 启用跨域请求 (CORS)
- 缓存响应
- 监视和记录请求  

如果不需要 API 管理提供的所有功能，可以选择使用 [Functions 代理][functions-proxy]。 使用 Azure Functions 的此功能可以通过创建后端函数的路由，为多个函数应用定义单个 API 接口。 函数代理还可以针对 HTTP 请求和响应执行有限转换。 但是，这些代理提供的基于策略的功能不如 API 管理那样丰富。

**Cosmos DB**。 [Cosmos DB][cosmosdb] 是一个多模型数据库服务。 在此方案中，函数应用程序将从 Cosmos DB 中提取文档，以响应客户端发出的 HTTP GET 请求。

**Azure Active Directory** (Azure AD)。 用户使用其 Azure AD 凭据登录到 Web 应用程序。 Azure AD 返回 API 的访问令牌，Web 应用程序使用该令牌对 API 请求进行身份验证（请参阅[身份验证](#authentication)）。

**Azure Monitor**。 [Monitor][monitor] 收集解决方案中部署的 Azure 服务的性能指标。 在仪表板中可视化这些指标可以洞察解决方案的运行状况。 Monitor 还收集应用程序日志。

**Azure Pipelines**。 [Pipelines][pipelines] 是用于生成、测试和部署应用程序的持续集成 (CI) 和持续交付 (CD) 服务。

## <a name="recommendations"></a>建议

### <a name="function-app-plans"></a>函数应用计划

Azure Functions 支持两种托管模型。 **消耗量计划**：运行代码时自动分配计算能力。  **应用服务计划**：为代码分配一组 VM。 应用服务计划定义 VM 数目和 VM 大小。 

请注意，根据上述定义，应用服务计划在严格意义上并非无服务器计划。 编程模型相同，但是 &mdash; 相同的函数代码既可以在消耗量计划中运行，也可以在应用服务计划中运行。

下面是选择要使用的计划类型时要考虑的一些因素：

- **冷启动**。 使用消耗量计划时，最近未调用过的函数将在下次运行时发生额外的延迟。 发生此额外延迟的原因是需要分配和准备运行时环境。 此延迟通常为几秒钟，具体时间取决于多种因素，包括需要加载的依赖项数目。 有关详细信息，请参阅[了解无服务器冷启动][functions-cold-start]。 通常，冷启动主要与交互式工作负荷（HTTP 触发器）而不是异步消息驱动的工作负荷（队列或事件中心触发器）有关，因为用户可直接注意到额外的延迟。
- **超时期限**。  在消耗量计划中，函数执行会在一段[可配置][functions-timeout]的时间（最多 10 分钟）后超时
- **虚拟网络隔离**。 使用应用服务计划可让函数在[应用服务环境][ase]（专用的隔离托管环境）中运行。
- **定价模型**。 消耗量计划根据运行次数和资源消耗量（内存 &times; 运行时间）计费。 应用服务计划根据 VM 实例 SKU 按小时计费。 通常，消耗量计划比应用服务计划更便宜，因为你只需为所用的计算资源付费。 流量会出现高峰和低谷时，这一优势更为明显。 但是，如果应用程序一贯遇到高吞吐量，则应用服务计划可能比消耗量计划更划算。
- **缩放**。 消耗量模型的一大优势在于，它可以根据传入流量按需动态缩放。 尽管这种缩放可快速进行，但仍然存在一个负载攀升阶段。 对于某些工作负荷，你可能会有意过度预配 VM，以便能够应对流量突发，同时避免负载攀升。 对于这种情况，请考虑使用应用服务计划。

### <a name="function-app-boundaries"></a>函数应用边界

函数应用程序托管一个或多个函数的执行。 可以使用函数应用将多个函数作为一个逻辑单元分组到一起。 在函数应用中，函数共享相同的应用程序设置、托管计划和部署生命周期。 每个函数应用有自身的主机名。  

使用函数应用将共享相同生命周期和设置的函数分组到一起。 不共享相同生命周期的函数应该托管在不同的函数应用中。 

考虑采用微服务方法。在这种情况下，每个函数应用代表一个可能包含多个相关函数的微服务。 在微服务体系结构中，服务应具有松散耦合和高功能内聚的特点。 松散耦合意味着无需同时更新其他服务即可更改一个服务。 内聚表示服务具有一种妥善定义的用途。 有关这些概念的详细介绍，请参阅[设计微服务：域分析][microservices-domain-analysis]。

### <a name="function-bindings"></a>函数绑定

尽量使用函数[绑定][functions-bindings]。 绑定提供声明方式用于将代码连接到数据并与其他 Azure 服务集成。 输入绑定从外部数据源填充输入参数。 输出绑定将函数的返回值发送到队列或数据库等数据接收器。

例如，参考实现中的 `GetStatus` 函数使用 Cosmos DB [输入绑定][cosmosdb-input-binding]。 此绑定配置为使用取自 HTTP 请求中查询字符串的查询参数，在 Cosmos DB 中查找某个文档。 如果找到该文档，则将其作为参数传递给函数。

```csharp
[FunctionName("GetStatusFunction")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req, 
    [CosmosDB(
        databaseName: "%COSMOSDB_DATABASE_NAME%",
        collectionName: "%COSMOSDB_DATABASE_COL%",
        ConnectionStringSetting = "COSMOSDB_CONNECTION_STRING",
        Id = "{Query.deviceId}",
        PartitionKey = "{Query.deviceId}")] dynamic deviceStatus, 
    ILogger log)
{
    ...
}
```

如果使用绑定，则无需编写直接与服务通信的代码，这可以简化函数代码，同时可以抽象化数据源或接收器的详细信息。 但在某些情况下，所需的逻辑可能比绑定提供的逻辑更复杂。 对于这种情况，请直接使用 Azure 客户端 SDK。

## <a name="scalability-considerations"></a>可伸缩性注意事项

**Functions**。 对于消耗量计划，HTTP 触发器根据流量缩放。 并发函数实例数有限制，但每个实例一次可以处理多个请求。 对于应用服务计划，HTTP 触发器根据 VM 实例数缩放，该数目可以是固定值，也可以根据一组自动缩放规则自动缩放。 有关信息，请参阅 [Azure Functions 的缩放和托管][functions-scale]。 

**Cosmos DB**。 Cosmos DB 的吞吐量容量以[请求单位][ru] (RU) 来度量。 1-RU 吞吐量相当于获取 1KB 文档所需的吞吐量。 若要将某个 Cosmos DB 容器扩展到超过 10,000 RU，必须在创建该容器时指定[分区键][partition-key]，并在创建的每个文档中包含该分区键。 有关分区键的详细信息，请参阅 [Azure Cosmos DB 中的分区和缩放][cosmosdb-scale]。

**API 管理**。 API 管理可横向扩展，并支持基于规则的自动缩放。 请注意，缩放过程至少需要花费 20 分钟。 如果流量突发，应针对预期的最大突发流量进行预配。 但是，自动缩放可用于处理每小时或每日流量变化。 有关详细信息，请参阅[自动缩放 Azure API 管理实例][apim-scale]。

## <a name="disaster-recovery-considerations"></a>灾难恢复注意事项

下面所示的部署驻留在单个 Azure 区域中。 更具弹性的灾难恢复方法利用不同服务中的异地分发功能：

- API 管理支持多区域部署，这种部署可用于在任意数量的 Azure 区域之间分发单个 API 管理实例。 有关详细信息，请参阅[如何将 Azure API 管理服务实例部署到多个 Azure 区域][api-geo]。

- 使用[流量管理器][tm]可将 HTTP 请求路由到主要区域。 如果该区域中运行的函数应用不可用，则流量管理器可以故障转移到次要区域。

- Cosmos DB 支持[多主数据库区域][cosmosdb-geo]，因此可以向添加到 Cosmos DB 帐户中的任何区域写入数据。 如果未启用多主数据库，仍可以故障转移主要写入区域。 Cosmos DB 客户端 SDK 和 Azure 函数绑定会自动处理故障转移，因此你无需更新任何应用程序配置设置。

## <a name="security-considerations"></a>安全注意事项

### <a name="authentication"></a>身份验证

参考实现中的 `GetStatus` API 使用 Azure AD 对请求进行身份验证。 Azure AD 支持 Open ID Connect 协议（基于 OAuth 2 协议生成的身份验证协议）。

在此体系结构中，客户端应用程序是在浏览器中运行的单页应用程序 (SPA)。 此类客户端应用程序无法将客户端保密或者隐藏授权代码，因此，隐式授权流比较合适。 （请参阅[应使用哪种 OAuth 2.0 流程？][oauth-flow]）。 整体流如下所述：

1. 用户在 Web 应用程序中单击“登录”链接。
1. 浏览器重定向到 Azure AD 登录页。 
1. 用户登录。
1. Azure AD 重定向回到客户端应用程序，并在 URL 片段中包含访问令牌。
1. 当 Web 应用程序调用 API 时，会在 Authentication 标头中包含访问令牌。 应用程序 ID 在访问令牌中作为受众（“aud”）声明发送。 
1. 后端 API 验证访问令牌。

配置身份验证：

- 在 Azure AD 租户中注册应用程序。 这会生成应用程序 ID，而客户端会将此 ID 与登录 URL 包含在一起。

- 在函数应用中启用 Azure AD 身份验证。 有关详细信息，请参阅 [Azure 应用服务中的身份验证和授权][app-service-auth]。

- 将策略添加到 API 管理，以通过验证访问令牌来为请求预先授权：

    ```xml
    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.microsoftonline.com/[Azure AD tenant ID]/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>[Application ID]</value>
            </claim>
        </required-claims>
    </validate-jwt>
    ```

有关更多详细信息，请参阅 [GitHub 自述文件][readme]。

### <a name="authorization"></a>授权

在许多应用程序中，后端 API 必须检查用户是否有权执行给定的操作。 建议使用[基于声明的授权][claims]，其中，有关用户的信息将由标识提供者（在本例中为 Azure AD）传递，并用于做出授权决策。 

某些声明在 Azure AD 返回给客户端的 ID 令牌中提供。 可以通过检查请求中的 X-MS-CLIENT-PRINCIPAL 标头，从函数应用内部获取这些声明。 对于其他声明，可使用 [Microsoft Graph][graph] 来查询 Azure AD（登录期间需要用户许可）。 

例如，在 Azure AD 中注册应用程序时，可在应用程序的注册清单中定义一组应用程序角色。 当用户登录到应用程序时，Azure AD 会针对授予用户的每个角色（包括通过组成员身份继承的角色）包含一个“roles”声明。 

在参考实现中，函数会检查经过身份验证的用户是否为 `GetStatus` 应用程序角色的成员。 如果不是，则函数会返回“HTTP 未授权”(401) 响应。 

```csharp
[FunctionName("GetStatusFunction")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [CosmosDB(
        databaseName: "%COSMOSDB_DATABASE_NAME%",
        collectionName: "%COSMOSDB_DATABASE_COL%",
        ConnectionStringSetting = "COSMOSDB_CONNECTION_STRING",
        Id = "{Query.deviceId}",
        PartitionKey = "{Query.deviceId}")] dynamic deviceStatus, 
    ILogger log)
{
    log.LogInformation("Processing GetStatus request.");

    return req.HandleIfAuthorizedForRoles(new[] { GetDeviceStatusRoleName },
        async () =>
        {
            string deviceId = req.Query["deviceId"];
            if (deviceId == null)
            {
                return new BadRequestObjectResult("Missing DeviceId");
            }

            return await Task.FromResult<IActionResult>(deviceStatus != null
                    ? (ActionResult)new OkObjectResult(deviceStatus)
                    : new NotFoundResult());
        },
        log);
}
```

在此代码示例中，`HandleIfAuthorizedForRoles` 是一个扩展方法，用于检查角色声明并在找不到声明时返回 HTTP 401。 可在[此处][HttpRequestAuthorizationExtensions]找到源代码。 请注意，`HandleIfAuthorizedForRoles` 采用 `ILogger` 参数。 应记录未授权的请求以获取审核线索，并可根据需要诊断问题。 同时，请避免泄露 HTTP 401 响应中的任何详细信息。

### <a name="cors"></a>CORS

在此参考体系结构中，Web 应用程序和 API 不共享相同的源。 这意味着，应用程序调用 API 属于一个跨域请求。 浏览器安全性将阻止网页向另一个域发出 AJAX 请求。 此限制称为“同域策略”，可防止恶意站点从另一站点读取敏感数据。 若要启用跨域请求，请将跨域资源共享 (CORS) [策略][cors-policy]添加到 API 管理网关：

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <origin>[Website URL]</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
</cors>
```

在此示例中，**allow-credentials** 属性为 **true**。 这会授权浏览器通过请求发送凭据（包括 Cookie）。 否则，浏览器默认不会通过跨域请求发送凭据。

> [!NOTE] 
> 将 **allow-credentials** 设置为 **true** 时请小心，因为这意味着网站可以在用户不知情的情况下，代表用户将用户的凭据发送到 API。 必须信任允许的源。

### <a name="enforce-https"></a>实施 HTTPS

为获得最高安全性，必须在整个请求管道中使用 HTTPS：

- **CDN**。 Azure CDN 默认支持在 `*.azureedge.net` 子域中使用 HTTPS。 若要在 CDN 中为自定义域名启用 HTTPS，请参阅[教程：在 Azure CDN 自定义域中配置 HTTPS][cdn-https]。 

- **静态网站托管**。 在存储帐户中启用“[需要安全传输][storage-https]”选项。 启用此选项后，存储帐户只允许来自安全 HTTPS 连接的请求。 

- **API 管理**。 将 API 配置为仅使用 HTTPS 协议。 可以通过 Azure 门户或资源管理器模板进行此配置：

    ```json
    {
        "apiVersion": "2018-01-01",
        "type": "apis",
        "name": "dronedeliveryapi",
        "dependsOn": [
            "[concat('Microsoft.ApiManagement/service/', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "displayName": "Drone Delivery API",
            "description": "Drone Delivery API",
            "path": "api",
            "protocols": [ "HTTPS" ]
        },
        ...
    }
    ```

- **Azure Functions**。 启用“[仅限 HTTPS][functions-https]”设置。 

### <a name="lock-down-the-function-app"></a>锁定函数应用

对函数的所有调用应通过 API 网关发出。 可按如下所述实现此目的：

- 将函数应用配置为要求提供函数密钥。 API 管理网关在调用函数应用时会包含函数密钥。 这可以防止客户端绕过网关直接调用函数。 

- API 管理网关采用一个[静态 IP 地址][apim-ip]。 将 Azure 函数限制为仅允许来自该静态 IP 地址的调用。 有关详细信息，请参阅 [Azure 应用服务静态 IP 限制][app-service-ip-restrictions]。 （此功能仅适用于标准层服务。） 

### <a name="protect-application-secrets"></a>保护应用程序机密

不要在代码或配置文件中存储应用程序机密（例如数据库凭据）。 请改用 Azure 中加密存储的应用设置。 有关详细信息，请参阅 [Azure 应用服务和 Azure Functions 中的安全性][app-service-security]。

或者，可将应用程序机密存储在 Key Vault 中。 这样，便可以集中存储机密、控制其分发，并监视何时以何种方式访问机密。 有关详细信息，请参阅[将 Azure Web 应用程序配置为从 Key Vault 读取机密][key-vault-web-app]。 但请注意，Functions 触发器和绑定从应用设置加载其配置设置。 没有任何内置的方法可将触发器和绑定配置为使用 Key Vault 机密。

## <a name="devops-considerations"></a>DevOps 注意事项

### <a name="api-versioning"></a>API 版本控制

API 是服务与客户端之间的协定，或该服务的使用者。 支持 API 协定中的版本控制。 如果做了重大的 API 更改，请推出新的 API 版本。 在独立的函数应用中，连同原始版本一样部署新版本。 这样可将现有客户端迁移到新 API，且不中断客户端应用程序。 最终可以弃用旧版本。 有关 API 版本控制的详细信息，请参阅[对 RESTful Web API 进行版本控制][api-versioning]。

对于不属于重大 API 更改的更新，请将新版本部署到同一函数应用中的过渡槽。 验证部署是否成功，然后将过渡版本交换为生产版本。

## <a name="deploy-the-solution"></a>部署解决方案

若要部署此参考体系结构，请查看 [GitHub 自述文件][readme]。 

<!-- links -->

[api-versioning]: ../../best-practices/api-design.md#versioning-a-restful-web-api
[apim]: /azure/api-management/api-management-key-concepts
[apim-ip]: /azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules
[api-geo]: /azure/api-management/api-management-howto-deploy-multi-region
[apim-scale]: /azure/api-management/api-management-howto-autoscale
[app-service-auth]: /azure/app-service/app-service-authentication-overview
[app-service-ip-restrictions]: /azure/app-service/app-service-ip-restrictions
[app-service-security]: /azure/app-service/app-service-security
[ase]: /azure/app-service/environment/intro
[azure-messaging]: /azure/event-grid/compare-messaging-services
[claims]: https://en.wikipedia.org/wiki/Claims-based_identity
[cdn]: https://azure.microsoft.com/services/cdn/
[cdn-https]: /azure/cdn/cdn-custom-ssl
[cors-policy]: /azure/api-management/api-management-cross-domain-policies
[cosmosdb]: /azure/cosmos-db/introduction
[cosmosdb-geo]: /azure/cosmos-db/distribute-data-globally
[cosmosdb-input-binding]: /azure/azure-functions/functions-bindings-cosmosdb-v2#input
[cosmosdb-scale]: /azure/cosmos-db/partition-data
[event-driven]: ../../guide/architecture-styles/event-driven.md
[functions]: /azure/azure-functions/functions-overview
[functions-bindings]: /azure/azure-functions/functions-triggers-bindings
[functions-cold-start]: https://blogs.msdn.microsoft.com/appserviceteam/2018/02/07/understanding-serverless-cold-start/
[functions-https]: /azure/app-service/app-service-web-tutorial-custom-ssl#enforce-https
[functions-proxy]: /azure-functions/functions-proxies
[functions-scale]: /azure/azure-functions/functions-scale
[functions-timeout]: /azure/azure-functions/functions-scale#consumption-plan
[graph]: https://developer.microsoft.com/graph/docs/concepts/overview
[key-vault-web-app]: /azure/key-vault/tutorial-web-application-keyvault
[microservices-domain-analysis]: ../../microservices/domain-analysis.md
[monitor]: /azure/azure-monitor/overview
[oauth-flow]: https://auth0.com/docs/api-auth/which-oauth-flow-to-use
[partition-key]: /azure/cosmos-db/partition-data
[pipelines]: /azure/devops/pipelines/index
[ru]: /azure/cosmos-db/request-units
[static-hosting]: /azure/storage/blobs/storage-blob-static-website
[static-hosting-preview]: https://azure.microsoft.com/blog/azure-storage-static-web-hosting-public-preview/
[storage-https]: /azure/storage/common/storage-require-secure-transfer
[tm]: /azure/traffic-manager/traffic-manager-overview

[github]: https://github.com/mspnp/serverless-reference-implementation
[HttpRequestAuthorizationExtensions]: https://github.com/mspnp/serverless-reference-implementation/blob/master/src/DroneStatus/dotnet/DroneStatusFunctionApp/HttpRequestAuthorizationExtensions.cs
[readme]: https://github.com/mspnp/serverless-reference-implementation/blob/master/README.md