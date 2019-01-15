---
title: 网关卸载模式
titleSuffix: Cloud Design Patterns
description: 将共享或专用服务功能卸载到网关代理。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.custom: seodec18
ms.openlocfilehash: 50af3d8593279986ed6efee55667187424c18e56
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54010200"
---
# <a name="gateway-offloading-pattern"></a><span data-ttu-id="c744b-104">网关卸载模式</span><span class="sxs-lookup"><span data-stu-id="c744b-104">Gateway Offloading pattern</span></span>

<span data-ttu-id="c744b-105">将共享或专用服务功能卸载到网关代理。</span><span class="sxs-lookup"><span data-stu-id="c744b-105">Offload shared or specialized service functionality to a gateway proxy.</span></span> <span data-ttu-id="c744b-106">此模式可以通过将共享服务功能（如 SSL 证书的使用）从应用程序的其他部分移动到网关，简化应用程序开发。</span><span class="sxs-lookup"><span data-stu-id="c744b-106">This pattern can simplify application development by moving shared service functionality, such as the use of SSL certificates, from other parts of the application into the gateway.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="c744b-107">上下文和问题</span><span class="sxs-lookup"><span data-stu-id="c744b-107">Context and problem</span></span>

<span data-ttu-id="c744b-108">一些功能通常跨多个服务使用，并且这些功能需要配置、管理和维护。</span><span class="sxs-lookup"><span data-stu-id="c744b-108">Some features are commonly used across multiple services, and these features require configuration, management, and maintenance.</span></span> <span data-ttu-id="c744b-109">与每个应用程序部署一起分发的共享或特殊化服务会增加管理开支和部署错误的可能性。</span><span class="sxs-lookup"><span data-stu-id="c744b-109">A shared or specialized service that is distributed with every application deployment increases the administrative overhead and increases the likelihood of deployment error.</span></span> <span data-ttu-id="c744b-110">对共享功能的任何更新必须跨共享该功能的所有服务部署。</span><span class="sxs-lookup"><span data-stu-id="c744b-110">Any updates to a shared feature must be deployed across all services that share that feature.</span></span>

<span data-ttu-id="c744b-111">正确处理安全问题（令牌验证、加密、SSL 证书管理）和其他复杂任务可能需要具备高度专业技能的团队成员。</span><span class="sxs-lookup"><span data-stu-id="c744b-111">Properly handling security issues (token validation, encryption, SSL certificate management) and other complex tasks can require team members to have highly specialized skills.</span></span> <span data-ttu-id="c744b-112">例如，应用程序所需的证书必须在所有应用程序实例上配置和部署。</span><span class="sxs-lookup"><span data-stu-id="c744b-112">For example, a certificate needed by an application must be configured and deployed on all application instances.</span></span> <span data-ttu-id="c744b-113">对于每个新部署，必须管理证书以确保它未过期。</span><span class="sxs-lookup"><span data-stu-id="c744b-113">With each new deployment, the certificate must be managed to ensure that it does not expire.</span></span> <span data-ttu-id="c744b-114">任何即将到期的通用证书必须针对每个应用程序部署进行更新、测试和验证。</span><span class="sxs-lookup"><span data-stu-id="c744b-114">Any common certificate that is due to expire must be updated, tested, and verified on every application deployment.</span></span>

<span data-ttu-id="c744b-115">其他通用服务（如身份验证、授权、日志记录、监视或[限制](./throttling.md)）可能很难在大量部署中进行实施和管理。</span><span class="sxs-lookup"><span data-stu-id="c744b-115">Other common services such as authentication, authorization, logging, monitoring, or [throttling](./throttling.md) can be difficult to implement and manage across a large number of deployments.</span></span> <span data-ttu-id="c744b-116">可能更好的做法是合并此类型功能，以便减少开销和降低错误概率。</span><span class="sxs-lookup"><span data-stu-id="c744b-116">It may be better to consolidate this type of functionality, in order to reduce overhead and the chance of errors.</span></span>

## <a name="solution"></a><span data-ttu-id="c744b-117">解决方案</span><span class="sxs-lookup"><span data-stu-id="c744b-117">Solution</span></span>

<span data-ttu-id="c744b-118">将一些功能卸载到 API 网关，尤其是横切关注点（如证书管理、身份验证、SSL 终端、监视、协议转换或限制）。</span><span class="sxs-lookup"><span data-stu-id="c744b-118">Offload some features into an API gateway, particularly cross-cutting concerns such as certificate management, authentication, SSL termination, monitoring, protocol translation, or throttling.</span></span>

<span data-ttu-id="c744b-119">下图显示终止入站 SSL 连接的 API 网关。</span><span class="sxs-lookup"><span data-stu-id="c744b-119">The following diagram shows an API gateway that terminates inbound SSL connections.</span></span> <span data-ttu-id="c744b-120">它代表 API 网关上游的 HTTP 服务器中的原始请求程序请求数据。</span><span class="sxs-lookup"><span data-stu-id="c744b-120">It requests data on behalf of the original requestor from any HTTP server upstream of the API gateway.</span></span>

 ![网关卸载模式图](./_images/gateway-offload.png)

<span data-ttu-id="c744b-122">此模式的优点包括：</span><span class="sxs-lookup"><span data-stu-id="c744b-122">Benefits of this pattern include:</span></span>

- <span data-ttu-id="c744b-123">通过消除对分发和维护支持资源的需求，简化服务的部署，如 Web 服务器证书和安全网站的配置。</span><span class="sxs-lookup"><span data-stu-id="c744b-123">Simplify the development of services by removing the need to distribute and maintain supporting resources, such as web server certificates and configuration for secure websites.</span></span> <span data-ttu-id="c744b-124">通过简化配置，简化管理、可伸缩性和服务升级。</span><span class="sxs-lookup"><span data-stu-id="c744b-124">Simpler configuration results in easier management and scalability and makes service upgrades simpler.</span></span>

- <span data-ttu-id="c744b-125">允许专业团队实施需要专业技能的功能，如安全性。</span><span class="sxs-lookup"><span data-stu-id="c744b-125">Allow dedicated teams to implement features that require specialized expertise, such as security.</span></span> <span data-ttu-id="c744b-126">这样一来，核心团队可专注于应用程序功能，而这些特殊化横切关注点可交给相关专家解决。</span><span class="sxs-lookup"><span data-stu-id="c744b-126">This allows your core team to focus on the application functionality, leaving these specialized but cross-cutting concerns to the relevant experts.</span></span>

- <span data-ttu-id="c744b-127">提供请求和响应记录和监视的一致性。</span><span class="sxs-lookup"><span data-stu-id="c744b-127">Provide some consistency for request and response logging and monitoring.</span></span> <span data-ttu-id="c744b-128">即使未正确检测服务，也可以通过配置网关来确保最低级别的监视和记录。</span><span class="sxs-lookup"><span data-stu-id="c744b-128">Even if a service is not correctly instrumented, the gateway can be configured to ensure a minimum level of monitoring and logging.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="c744b-129">问题和注意事项</span><span class="sxs-lookup"><span data-stu-id="c744b-129">Issues and considerations</span></span>

- <span data-ttu-id="c744b-130">确保 API 网关的高度可用性和应对故障的灵活性。</span><span class="sxs-lookup"><span data-stu-id="c744b-130">Ensure the API gateway is highly available and resilient to failure.</span></span> <span data-ttu-id="c744b-131">运行多个 API 网关实例以避免单一故障点。</span><span class="sxs-lookup"><span data-stu-id="c744b-131">Avoid single points of failure by running multiple instances of your API gateway.</span></span>
- <span data-ttu-id="c744b-132">确保网关可满足应用程序和终结点的容量和缩放要求。</span><span class="sxs-lookup"><span data-stu-id="c744b-132">Ensure the gateway is designed for the capacity and scaling requirements of your application and endpoints.</span></span> <span data-ttu-id="c744b-133">确保网关不会成为应用程序的瓶颈，并具有足够的可缩放性。</span><span class="sxs-lookup"><span data-stu-id="c744b-133">Make sure the gateway does not become a bottleneck for the application and is sufficiently scalable.</span></span>
- <span data-ttu-id="c744b-134">仅卸载由整个应用程序使用的功能，如安全性或数据传输。</span><span class="sxs-lookup"><span data-stu-id="c744b-134">Only offload features that are used by the entire application, such as security or data transfer.</span></span>
- <span data-ttu-id="c744b-135">请勿将业务逻辑卸载到 API 网关。</span><span class="sxs-lookup"><span data-stu-id="c744b-135">Business logic should never be offloaded to the API gateway.</span></span>
- <span data-ttu-id="c744b-136">如果需要跟踪事务，请考虑生成用于记录的关联 ID。</span><span class="sxs-lookup"><span data-stu-id="c744b-136">If you need to track transactions, consider generating correlation IDs for logging purposes.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="c744b-137">何时使用此模式</span><span class="sxs-lookup"><span data-stu-id="c744b-137">When to use this pattern</span></span>

<span data-ttu-id="c744b-138">在以下情况下使用此模式：</span><span class="sxs-lookup"><span data-stu-id="c744b-138">Use this pattern when:</span></span>

- <span data-ttu-id="c744b-139">应用程序部署具有共享关注点，如 SSL 证书或加密。</span><span class="sxs-lookup"><span data-stu-id="c744b-139">An application deployment has a shared concern such as SSL certificates or encryption.</span></span>
- <span data-ttu-id="c744b-140">应用程序部署中的一个常用功能，可能具有不同的资源要求，例如内存资源、存储容量或网络连接。</span><span class="sxs-lookup"><span data-stu-id="c744b-140">A feature that is common across application deployments that may have different resource requirements, such as memory resources, storage capacity or network connections.</span></span>
- <span data-ttu-id="c744b-141">希望将问题（如网络安全、限制或其他网络限制问题）的责任转移到一个更专业的团队。</span><span class="sxs-lookup"><span data-stu-id="c744b-141">You wish to move the responsibility for issues such as network security, throttling, or other network boundary concerns to a more specialized team.</span></span>

<span data-ttu-id="c744b-142">如果此模式跨服务引入耦合度，则它可能不适合。</span><span class="sxs-lookup"><span data-stu-id="c744b-142">This pattern may not be suitable if it introduces coupling across services.</span></span>

## <a name="example"></a><span data-ttu-id="c744b-143">示例</span><span class="sxs-lookup"><span data-stu-id="c744b-143">Example</span></span>

<span data-ttu-id="c744b-144">将 Nginx 用作 SSL 卸载设备，以下配置将终止入站 SSL 连接，并将连接分布到三个上游 HTTP 服务器之一。</span><span class="sxs-lookup"><span data-stu-id="c744b-144">Using Nginx as the SSL offload appliance, the following configuration terminates an inbound SSL connection and distributes the connection to one of three upstream HTTP servers.</span></span>

```console
upstream iis {
        server  10.3.0.10    max_fails=3    fail_timeout=15s;
        server  10.3.0.20    max_fails=3    fail_timeout=15s;
        server  10.3.0.30    max_fails=3    fail_timeout=15s;
}

server {
        listen 443;
        ssl on;
        ssl_certificate /etc/nginx/ssl/domain.cer;
        ssl_certificate_key /etc/nginx/ssl/domain.key;

        location / {
                set $targ iis;
                proxy_pass http://$targ;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header Host $host;
        }
}
```

## <a name="related-guidance"></a><span data-ttu-id="c744b-145">相关指南</span><span class="sxs-lookup"><span data-stu-id="c744b-145">Related guidance</span></span>

- [<span data-ttu-id="c744b-146">用于前端的后端模式</span><span class="sxs-lookup"><span data-stu-id="c744b-146">Backends for Frontends pattern</span></span>](./backends-for-frontends.md)
- [<span data-ttu-id="c744b-147">网关聚合模式</span><span class="sxs-lookup"><span data-stu-id="c744b-147">Gateway Aggregation pattern</span></span>](./gateway-aggregation.md)
- [<span data-ttu-id="c744b-148">网关路由模式</span><span class="sxs-lookup"><span data-stu-id="c744b-148">Gateway Routing pattern</span></span>](./gateway-routing.md)
