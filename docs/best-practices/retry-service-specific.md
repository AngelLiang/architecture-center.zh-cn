---
title: 重试服务指南
description: 设置重试机制的服务指南。
author: dragon119
ms.date: 07/13/2016
pnp.series.title: Best Practices
ms.openlocfilehash: f02843f179671da04bc2f09326b58075b432ba95
ms.sourcegitcommit: 85334ab0ccb072dac80de78aa82bcfa0f0044d3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35253071"
---
# <a name="retry-guidance-for-specific-services"></a><span data-ttu-id="7f3b6-103">特定服务的重试指南</span><span class="sxs-lookup"><span data-stu-id="7f3b6-103">Retry guidance for specific services</span></span>

<span data-ttu-id="7f3b6-104">大多数 Azure 服务和客户端 SDK 都包括重试机制。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-104">Most Azure services and client SDKs include a retry mechanism.</span></span> <span data-ttu-id="7f3b6-105">不过，这些重试机制各不相同，这是因为每个服务都有不同的特征和要求，这样一来，各个重试机制都会针对特定服务进行优化。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-105">However, these differ because each service has different characteristics and requirements, and so each retry mechanism is tuned to a specific service.</span></span> <span data-ttu-id="7f3b6-106">本指南汇总了大多数 Azure 服务的重试机制功能，并介绍了如何使用、适应或扩展相应服务的重试机制。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-106">This guide summarizes the retry mechanism features for the majority of Azure services, and includes information to help you use, adapt, or extend the retry mechanism for that service.</span></span>

<span data-ttu-id="7f3b6-107">有关如何处理临时故障、针对服务和资源重试连接和操作的一般指南，请参阅[重试指南](./transient-faults.md)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-107">For general guidance on handling transient faults, and retrying connections and operations against services and resources, see [Retry guidance](./transient-faults.md).</span></span>

<span data-ttu-id="7f3b6-108">下表总结了本指南中介绍的 Azure 服务重试功能。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-108">The following table summarizes the retry features for the Azure services described in this guidance.</span></span>

| <span data-ttu-id="7f3b6-109">**服务**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-109">**Service**</span></span> | <span data-ttu-id="7f3b6-110">**重试功能**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-110">**Retry capabilities**</span></span> | <span data-ttu-id="7f3b6-111">**策略配置**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-111">**Policy configuration**</span></span> | <span data-ttu-id="7f3b6-112">**范围**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-112">**Scope**</span></span> | <span data-ttu-id="7f3b6-113">**遥测功能**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-113">**Telemetry features**</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="7f3b6-114">**[Azure Active Directory](#azure-active-directory)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-114">**[Azure Active Directory](#azure-active-directory)**</span></span> |<span data-ttu-id="7f3b6-115">ADAL 库原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-115">Native in ADAL library</span></span> |<span data-ttu-id="7f3b6-116">嵌入到 ADAL 库</span><span class="sxs-lookup"><span data-stu-id="7f3b6-116">Embeded into ADAL library</span></span> |<span data-ttu-id="7f3b6-117">内部</span><span class="sxs-lookup"><span data-stu-id="7f3b6-117">Internal</span></span> |<span data-ttu-id="7f3b6-118">无</span><span class="sxs-lookup"><span data-stu-id="7f3b6-118">None</span></span> |
| <span data-ttu-id="7f3b6-119">**[Cosmos DB](#cosmos-db)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-119">**[Cosmos DB](#cosmos-db)**</span></span> |<span data-ttu-id="7f3b6-120">服务原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-120">Native in service</span></span> |<span data-ttu-id="7f3b6-121">不可配置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-121">Non-configurable</span></span> |<span data-ttu-id="7f3b6-122">全局</span><span class="sxs-lookup"><span data-stu-id="7f3b6-122">Global</span></span> |<span data-ttu-id="7f3b6-123">TraceSource</span><span class="sxs-lookup"><span data-stu-id="7f3b6-123">TraceSource</span></span> |
| <span data-ttu-id="7f3b6-124">**[事件中心](#azure-event-hubs)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-124">**[Event Hubs](#azure-event-hubs)**</span></span> |<span data-ttu-id="7f3b6-125">客户端原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-125">Native in client</span></span> |<span data-ttu-id="7f3b6-126">编程</span><span class="sxs-lookup"><span data-stu-id="7f3b6-126">Programmatic</span></span> |<span data-ttu-id="7f3b6-127">Client</span><span class="sxs-lookup"><span data-stu-id="7f3b6-127">Client</span></span> |<span data-ttu-id="7f3b6-128">无</span><span class="sxs-lookup"><span data-stu-id="7f3b6-128">None</span></span> |
| <span data-ttu-id="7f3b6-129">**[Redis 缓存](#azure-redis-cache)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-129">**[Redis Cache](#azure-redis-cache)**</span></span> |<span data-ttu-id="7f3b6-130">客户端原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-130">Native in client</span></span> |<span data-ttu-id="7f3b6-131">编程</span><span class="sxs-lookup"><span data-stu-id="7f3b6-131">Programmatic</span></span> |<span data-ttu-id="7f3b6-132">Client</span><span class="sxs-lookup"><span data-stu-id="7f3b6-132">Client</span></span> |<span data-ttu-id="7f3b6-133">TextWriter</span><span class="sxs-lookup"><span data-stu-id="7f3b6-133">TextWriter</span></span> |
| <span data-ttu-id="7f3b6-134">**[搜索](#azure-search)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-134">**[Search](#azure-search)**</span></span> |<span data-ttu-id="7f3b6-135">客户端原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-135">Native in client</span></span> |<span data-ttu-id="7f3b6-136">编程</span><span class="sxs-lookup"><span data-stu-id="7f3b6-136">Programmatic</span></span> |<span data-ttu-id="7f3b6-137">Client</span><span class="sxs-lookup"><span data-stu-id="7f3b6-137">Client</span></span> |<span data-ttu-id="7f3b6-138">ETW 或自定义</span><span class="sxs-lookup"><span data-stu-id="7f3b6-138">ETW or Custom</span></span> |
| <span data-ttu-id="7f3b6-139">**[服务总线](#service-bus)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-139">**[Service Bus](#service-bus)**</span></span> |<span data-ttu-id="7f3b6-140">客户端原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-140">Native in client</span></span> |<span data-ttu-id="7f3b6-141">编程</span><span class="sxs-lookup"><span data-stu-id="7f3b6-141">Programmatic</span></span> |<span data-ttu-id="7f3b6-142">命名空间管理器、消息工厂和客户端</span><span class="sxs-lookup"><span data-stu-id="7f3b6-142">Namespace Manager, Messaging Factory, and Client</span></span> |<span data-ttu-id="7f3b6-143">ETW</span><span class="sxs-lookup"><span data-stu-id="7f3b6-143">ETW</span></span> |
| <span data-ttu-id="7f3b6-144">**[Service Fabric](#service-fabric)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-144">**[Service Fabric](#service-fabric)**</span></span> |<span data-ttu-id="7f3b6-145">客户端原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-145">Native in client</span></span> |<span data-ttu-id="7f3b6-146">编程</span><span class="sxs-lookup"><span data-stu-id="7f3b6-146">Programmatic</span></span> |<span data-ttu-id="7f3b6-147">Client</span><span class="sxs-lookup"><span data-stu-id="7f3b6-147">Client</span></span> |<span data-ttu-id="7f3b6-148">无</span><span class="sxs-lookup"><span data-stu-id="7f3b6-148">None</span></span> | 
| <span data-ttu-id="7f3b6-149">**[使用 ADO.NET 的 SQL 数据库](#sql-database-using-adonet)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-149">**[SQL Database with ADO.NET](#sql-database-using-adonet)**</span></span> |[<span data-ttu-id="7f3b6-150">Polly</span><span class="sxs-lookup"><span data-stu-id="7f3b6-150">Polly</span></span>](#transient-fault-handling-with-polly) |<span data-ttu-id="7f3b6-151">声明性和编程</span><span class="sxs-lookup"><span data-stu-id="7f3b6-151">Declarative and programmatic</span></span> |<span data-ttu-id="7f3b6-152">各个语句或代码块</span><span class="sxs-lookup"><span data-stu-id="7f3b6-152">Single statements or blocks of code</span></span> |<span data-ttu-id="7f3b6-153">“自定义”</span><span class="sxs-lookup"><span data-stu-id="7f3b6-153">Custom</span></span> |
| <span data-ttu-id="7f3b6-154">**[使用 Entity Framework 的 SQL 数据库](#sql-database-using-entity-framework-6)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-154">**[SQL Database with Entity Framework](#sql-database-using-entity-framework-6)**</span></span> |<span data-ttu-id="7f3b6-155">客户端原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-155">Native in client</span></span> |<span data-ttu-id="7f3b6-156">编程</span><span class="sxs-lookup"><span data-stu-id="7f3b6-156">Programmatic</span></span> |<span data-ttu-id="7f3b6-157">每个应用域均为全局</span><span class="sxs-lookup"><span data-stu-id="7f3b6-157">Global per AppDomain</span></span> |<span data-ttu-id="7f3b6-158">无</span><span class="sxs-lookup"><span data-stu-id="7f3b6-158">None</span></span> |
| <span data-ttu-id="7f3b6-159">**[使用 Entity Framework Core 的 SQL 数据库](#sql-database-using-entity-framework-core)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-159">**[SQL Database with Entity Framework Core](#sql-database-using-entity-framework-core)**</span></span> |<span data-ttu-id="7f3b6-160">客户端原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-160">Native in client</span></span> |<span data-ttu-id="7f3b6-161">编程</span><span class="sxs-lookup"><span data-stu-id="7f3b6-161">Programmatic</span></span> |<span data-ttu-id="7f3b6-162">每个应用域均为全局</span><span class="sxs-lookup"><span data-stu-id="7f3b6-162">Global per AppDomain</span></span> |<span data-ttu-id="7f3b6-163">无</span><span class="sxs-lookup"><span data-stu-id="7f3b6-163">None</span></span> |
| <span data-ttu-id="7f3b6-164">**[存储](#azure-storage)**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-164">**[Storage](#azure-storage)**</span></span> |<span data-ttu-id="7f3b6-165">客户端原生</span><span class="sxs-lookup"><span data-stu-id="7f3b6-165">Native in client</span></span> |<span data-ttu-id="7f3b6-166">编程</span><span class="sxs-lookup"><span data-stu-id="7f3b6-166">Programmatic</span></span> |<span data-ttu-id="7f3b6-167">客户端   和各项操作</span><span class="sxs-lookup"><span data-stu-id="7f3b6-167">Client and individual operations</span></span> |<span data-ttu-id="7f3b6-168">TraceSource</span><span class="sxs-lookup"><span data-stu-id="7f3b6-168">TraceSource</span></span> |

> [!NOTE]
> <span data-ttu-id="7f3b6-169">对于大多数 Azure 内置重试机制，目前尚无方法针对不同类型的错误或异常应用不同的重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-169">For most of the Azure built-in retry mechanisms, there is currently no way apply a different retry policy for different types of error or exception.</span></span> <span data-ttu-id="7f3b6-170">应该配置可提供最佳平均性能和可用性的策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-170">You should configure a policy that provides the optimum average performance and availability.</span></span> <span data-ttu-id="7f3b6-171">微调策略的一种方法是分析日志文件，以确定发生的临时故障的类型。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-171">One way to fine-tune the policy is to analyze log files to determine the type of transient faults that are occurring.</span></span> 

## <a name="azure-active-directory"></a><span data-ttu-id="7f3b6-172">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="7f3b6-172">Azure Active Directory</span></span>
<span data-ttu-id="7f3b6-173">Azure Active Directory (Azure AD) 是一项全面的标识和访问管理云解决方案，集成了核心目录服务、高级标识监管、安全性和应用程序访问管理等各种功能。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-173">Azure Active Directory (Azure AD) is a comprehensive identity and access management cloud solution that combines core directory services, advanced identity governance, security, and application access management.</span></span> <span data-ttu-id="7f3b6-174">Microsoft Azure AD 还为开发人员提供了身份管理平台，以便他们可以根据集中的策略和规则，控制应用程序访问情况。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-174">Azure AD also offers developers an identity management platform to deliver access control to their applications, based on centralized policy and rules.</span></span>

> [!NOTE]
> <span data-ttu-id="7f3b6-175">有关托管服务标识终结点的重试指南，请参阅[如何使用 Azure VM 托管服务标识 (MSI) 进行令牌获取](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-175">For retry guidance on Managed Service Identity endpoints, see [How to use an Azure VM Managed Service Identity (MSI) for token acquisition](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-176">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-176">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-177">Active Directory 身份验证库 (ADAL) 提供适用于 Azure Active Directory 的内置重试机制。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-177">There is a built-in retry mechanism for Azure Active Directory in the Active Directory Authentication Library (ADAL).</span></span> <span data-ttu-id="7f3b6-178">为避免意外锁定，建议第三方库和应用程序代码**不要**重试失败的连接，而让 ADAL 处理重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-178">To avoid unexpected lockouts, we recommend that third party libraries and application code do **not** retry failed connections, but allow ADAL to handle retries.</span></span> 

### <a name="retry-usage-guidance"></a><span data-ttu-id="7f3b6-179">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="7f3b6-179">Retry usage guidance</span></span>
<span data-ttu-id="7f3b6-180">使用 Azure Active Directory 时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-180">Consider the following guidelines when using Azure Active Directory:</span></span>

* <span data-ttu-id="7f3b6-181">如有可能，请使用 ADAL 库和内置支持进行重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-181">When possible, use the ADAL library and the built-in support for retries.</span></span>
* <span data-ttu-id="7f3b6-182">在对 Azure Active Directory 使用 REST API 的情况下，如果结果代码为 429（太多请求）或 5xx 范围中的错误，请重试该操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-182">If you are using the REST API for Azure Active Directory, retry the operation if the result code is 429 (Too Many Requests) or an error in the 5xx range.</span></span> <span data-ttu-id="7f3b6-183">请勿针对其他任何错误重试操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-183">Do not retry for any other errors.</span></span>
* <span data-ttu-id="7f3b6-184">建议将指数回退策略用于 Azure Active Directory 的批处理方案。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-184">An exponential back-off policy is recommended for use in batch scenarios with Azure Active Directory.</span></span>

<span data-ttu-id="7f3b6-185">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-185">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="7f3b6-186">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-186">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="7f3b6-187">**上下文**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-187">**Context**</span></span> | <span data-ttu-id="7f3b6-188">**示例目标 E2E<br />最长延迟**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-188">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="7f3b6-189">**重试策略**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-189">**Retry strategy**</span></span> | <span data-ttu-id="7f3b6-190">**设置**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-190">**Settings**</span></span> | <span data-ttu-id="7f3b6-191">**值**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-191">**Values**</span></span> | <span data-ttu-id="7f3b6-192">**工作原理**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-192">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="7f3b6-193">交互式, UI,</span><span class="sxs-lookup"><span data-stu-id="7f3b6-193">Interactive, UI,</span></span><br /><span data-ttu-id="7f3b6-194">或 foreground</span><span class="sxs-lookup"><span data-stu-id="7f3b6-194">or foreground</span></span> |<span data-ttu-id="7f3b6-195">2 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-195">2 sec</span></span> |<span data-ttu-id="7f3b6-196">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="7f3b6-196">FixedInterval</span></span> |<span data-ttu-id="7f3b6-197">重试计数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-197">Retry count</span></span><br /><span data-ttu-id="7f3b6-198">重试间隔</span><span class="sxs-lookup"><span data-stu-id="7f3b6-198">Retry interval</span></span><br /><span data-ttu-id="7f3b6-199">首次快速重试</span><span class="sxs-lookup"><span data-stu-id="7f3b6-199">First fast retry</span></span> |<span data-ttu-id="7f3b6-200">3</span><span class="sxs-lookup"><span data-stu-id="7f3b6-200">3</span></span><br /><span data-ttu-id="7f3b6-201">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-201">500 ms</span></span><br /><span data-ttu-id="7f3b6-202">是</span><span class="sxs-lookup"><span data-stu-id="7f3b6-202">true</span></span> |<span data-ttu-id="7f3b6-203">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-203">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="7f3b6-204">第 2 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-204">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="7f3b6-205">第 3 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-205">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="7f3b6-206">背景或</span><span class="sxs-lookup"><span data-stu-id="7f3b6-206">Background or</span></span><br /><span data-ttu-id="7f3b6-207">批处理</span><span class="sxs-lookup"><span data-stu-id="7f3b6-207">batch</span></span> |<span data-ttu-id="7f3b6-208">60 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-208">60 sec</span></span> |<span data-ttu-id="7f3b6-209">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-209">ExponentialBackoff</span></span> |<span data-ttu-id="7f3b6-210">重试计数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-210">Retry count</span></span><br /><span data-ttu-id="7f3b6-211">最小回退</span><span class="sxs-lookup"><span data-stu-id="7f3b6-211">Min back-off</span></span><br /><span data-ttu-id="7f3b6-212">最大回退</span><span class="sxs-lookup"><span data-stu-id="7f3b6-212">Max back-off</span></span><br /><span data-ttu-id="7f3b6-213">增量回退</span><span class="sxs-lookup"><span data-stu-id="7f3b6-213">Delta back-off</span></span><br /><span data-ttu-id="7f3b6-214">首次快速重试</span><span class="sxs-lookup"><span data-stu-id="7f3b6-214">First fast retry</span></span> |<span data-ttu-id="7f3b6-215">5</span><span class="sxs-lookup"><span data-stu-id="7f3b6-215">5</span></span><br /><span data-ttu-id="7f3b6-216">0 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-216">0 sec</span></span><br /><span data-ttu-id="7f3b6-217">60 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-217">60 sec</span></span><br /><span data-ttu-id="7f3b6-218">2 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-218">2 sec</span></span><br /><span data-ttu-id="7f3b6-219">false</span><span class="sxs-lookup"><span data-stu-id="7f3b6-219">false</span></span> |<span data-ttu-id="7f3b6-220">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-220">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="7f3b6-221">第 2 次尝试 - 约延迟 2 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-221">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="7f3b6-222">第 3 次尝试 - 约延迟 6 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-222">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="7f3b6-223">第 4 次尝试 - 约延迟 14 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-223">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="7f3b6-224">第 5 次尝试 - 约延迟 30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-224">Attempt 5 - delay ~30 sec</span></span> |

### <a name="more-information"></a><span data-ttu-id="7f3b6-225">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f3b6-225">More information</span></span>
* <span data-ttu-id="7f3b6-226">[Azure Active Directory 身份验证库][adal]</span><span class="sxs-lookup"><span data-stu-id="7f3b6-226">[Azure Active Directory Authentication Libraries][adal]</span></span>

## <a name="cosmos-db"></a><span data-ttu-id="7f3b6-227">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="7f3b6-227">Cosmos DB</span></span>

<span data-ttu-id="7f3b6-228">Cosmos DB 是一种完全托管的多模型数据库，支持无架构 JSON 数据。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-228">Cosmos DB is a fully-managed multi-model database that supports schema-less JSON data.</span></span> <span data-ttu-id="7f3b6-229">它提供可配置的可靠性能、本机 JavaScript 事务处理，专为具有弹性延展能力的云构建而成。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-229">It offers configurable and reliable performance, native JavaScript transactional processing, and is built for the cloud with elastic scale.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-230">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-230">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-231">`DocumentClient` 类自动重试失败的尝试次数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-231">The `DocumentClient` class automatically retries failed attempts.</span></span> <span data-ttu-id="7f3b6-232">若要设置重试次数和最长等待时间，请配置 [ConnectionPolicy.RetryOptions]。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-232">To set the number of retries and the maximum wait time, configure [ConnectionPolicy.RetryOptions].</span></span> <span data-ttu-id="7f3b6-233">客户端引发的异常会超出重试策略，或不是暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-233">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>

<span data-ttu-id="7f3b6-234">如果 Cosmos DB 限制客户端，它会返回 HTTP 429 错误。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-234">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="7f3b6-235">请检查 `DocumentClientException` 中的状态代码。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-235">Check the status code in the `DocumentClientException`.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="7f3b6-236">策略配置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-236">Policy configuration</span></span>
<span data-ttu-id="7f3b6-237">下表显示了 `RetryOptions` 类的默认设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-237">The following table shows the default settings for the `RetryOptions` class.</span></span>

| <span data-ttu-id="7f3b6-238">设置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-238">Setting</span></span> | <span data-ttu-id="7f3b6-239">默认值</span><span class="sxs-lookup"><span data-stu-id="7f3b6-239">Default value</span></span> | <span data-ttu-id="7f3b6-240">说明</span><span class="sxs-lookup"><span data-stu-id="7f3b6-240">Description</span></span> |
| --- | --- | --- |
| <span data-ttu-id="7f3b6-241">MaxRetryAttemptsOnThrottledRequests</span><span class="sxs-lookup"><span data-stu-id="7f3b6-241">MaxRetryAttemptsOnThrottledRequests</span></span> |<span data-ttu-id="7f3b6-242">9</span><span class="sxs-lookup"><span data-stu-id="7f3b6-242">9</span></span> |<span data-ttu-id="7f3b6-243">因 Cosmos DB 对客户端应用速率限制而导致请求失败时的最大重试次数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-243">The maximum number of retries if the request fails because Cosmos DB applied rate limiting on the client.</span></span> |
| <span data-ttu-id="7f3b6-244">MaxRetryWaitTimeInSeconds</span><span class="sxs-lookup"><span data-stu-id="7f3b6-244">MaxRetryWaitTimeInSeconds</span></span> |<span data-ttu-id="7f3b6-245">30</span><span class="sxs-lookup"><span data-stu-id="7f3b6-245">30</span></span> |<span data-ttu-id="7f3b6-246">最大重试时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-246">The maximum retry time in seconds.</span></span> |

### <a name="example"></a><span data-ttu-id="7f3b6-247">示例</span><span class="sxs-lookup"><span data-stu-id="7f3b6-247">Example</span></span>
```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); ;
var options = client.ConnectionPolicy.RetryOptions;
options.MaxRetryAttemptsOnThrottledRequests = 5;
options.MaxRetryWaitTimeInSeconds = 15;
```

### <a name="telemetry"></a><span data-ttu-id="7f3b6-248">遥测</span><span class="sxs-lookup"><span data-stu-id="7f3b6-248">Telemetry</span></span>
<span data-ttu-id="7f3b6-249">重试尝试通过 .NET **TraceSource** 记录为未结构化的跟踪消息。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-249">Retry attempts are logged as unstructured trace messages through a .NET **TraceSource**.</span></span> <span data-ttu-id="7f3b6-250">必须配置 **TraceListener**，才能捕获事件并将这些事件写入合适的目标日志。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-250">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span>

<span data-ttu-id="7f3b6-251">例如，如果将以下内容添加到 App.config 文件，会在与可执行文件相同位置的文本文件中生成跟踪：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-251">For example, if you add the following to your App.config file, traces will be generated in a text file in the same location as the executable:</span></span>

```xml
<configuration>
  <system.diagnostics>
    <switches>
      <add name="SourceSwitch" value="Verbose"/>
    </switches>
    <sources>
      <source name="DocDBTrace" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" >
        <listeners>
          <add name="MyTextListener" type="System.Diagnostics.TextWriterTraceListener" traceOutputOptions="DateTime,ProcessId,ThreadId" initializeData="CosmosDBTrace.txt"></add>
        </listeners>
      </source>
    </sources>
  </system.diagnostics>
</configuration>
```

## <a name="event-hubs"></a><span data-ttu-id="7f3b6-252">事件中心</span><span class="sxs-lookup"><span data-stu-id="7f3b6-252">Event Hubs</span></span>

<span data-ttu-id="7f3b6-253">Azure 事件中心是超大规模的遥测引入服务，可收集、传输和存储数百万的事件。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-253">Azure Event Hubs is a hyper-scale telemetry ingestion service that collects, transforms, and stores millions of events.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-254">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-254">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-255">Azure 事件中心客户端库中的重试行为由 `EventHubClient` 类上的 `RetryPolicy` 属性控制。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-255">Retry behavior in the Azure Event Hubs Client Library is controlled by the `RetryPolicy` property on the `EventHubClient` class.</span></span> <span data-ttu-id="7f3b6-256">Azure 事件中心返回暂时性 `EventHubsException` 或 `OperationCanceledException` 时，默认策略会使用指数回退进行重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-256">The default policy retries with exponential backoff when Azure Event Hub returns a transient `EventHubsException` or an `OperationCanceledException`.</span></span>

### <a name="example"></a><span data-ttu-id="7f3b6-257">示例</span><span class="sxs-lookup"><span data-stu-id="7f3b6-257">Example</span></span>
```csharp
EventHubClient client = EventHubClient.CreateFromConnectionString("[event_hub_connection_string]");
client.RetryPolicy = RetryPolicy.Default;
```

### <a name="more-information"></a><span data-ttu-id="7f3b6-258">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f3b6-258">More information</span></span>
[<span data-ttu-id="7f3b6-259">Azure 事件中心的 .NET 标准客户端库</span><span class="sxs-lookup"><span data-stu-id="7f3b6-259"> .NET Standard client library for Azure Event Hubs</span></span>](https://github.com/Azure/azure-event-hubs-dotnet)

## <a name="azure-redis-cache"></a><span data-ttu-id="7f3b6-260">Azure Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="7f3b6-260">Azure Redis Cache</span></span>
<span data-ttu-id="7f3b6-261">Azure Redis 高速缓存是一项快速数据访问和低延迟高速缓存服务，基于常用的开放源代码 Redis 高速缓存。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-261">Azure Redis Cache is a fast data access and low latency cache service based on the popular open source Redis Cache.</span></span> <span data-ttu-id="7f3b6-262">它是由 Microsoft 管理的安全服务，可通过 Azure 中的任意应用程序进行访问。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-262">It is secure, managed by Microsoft, and is accessible from any application in Azure.</span></span>

<span data-ttu-id="7f3b6-263">本部分中的指南假设你使用 StackExchange.Redis 客户端访问高速缓存。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-263">The guidance in this section is based on using the StackExchange.Redis client to access the cache.</span></span> <span data-ttu-id="7f3b6-264">[Redis 网站](http://redis.io/clients)上列出了其他合适的客户端，这些客户端可能具有不同的重试机制。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-264">A list of other suitable clients can be found on the [Redis website](http://redis.io/clients), and these may have different retry mechanisms.</span></span>

<span data-ttu-id="7f3b6-265">请注意，StackExchange.Redis 客户端通过单个连接使用多路复用。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-265">Note that the StackExchange.Redis client uses multiplexing through a single connection.</span></span> <span data-ttu-id="7f3b6-266">建议的用法是，在应用程序启动时创建客户端实例，并对针对高速缓存执行的所有操作使用此实例。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-266">The recommended usage is to create an instance of the client at application startup and use this instance for all operations against the cache.</span></span> <span data-ttu-id="7f3b6-267">因此，高速缓存连接只建立一次，且本部分中的所有指南均与此启动连接（而不是访问高速缓存的每个操作）的重试策略相关。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-267">For this reason, the connection to the cache is made only once, and so all of the guidance in this section is related to the retry policy for this initial connection—and not for each operation that accesses the cache.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-268">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-268">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-269">StackExchange.Redis 客户端使用连接管理器类，此类通过一组选项进行配置，其中包括：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-269">The StackExchange.Redis client uses a connection manager class that is configured through a set of options, incuding:</span></span>

- <span data-ttu-id="7f3b6-270">**ConnectRetry**。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-270">**ConnectRetry**.</span></span> <span data-ttu-id="7f3b6-271">缓存连接失败的重试次数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-271">The number of times a failed connection to the cache will be retried.</span></span>
- <span data-ttu-id="7f3b6-272">**ReconnectRetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-272">**ReconnectRetryPolicy**.</span></span> <span data-ttu-id="7f3b6-273">要使用的重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-273">The retry strategy to use.</span></span>
- <span data-ttu-id="7f3b6-274">**ConnectTimeout**。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-274">**ConnectTimeout**.</span></span> <span data-ttu-id="7f3b6-275">以毫秒为单位的最长等待时间。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-275">The maximum waiting time in milliseconds.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="7f3b6-276">策略配置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-276">Policy configuration</span></span>
<span data-ttu-id="7f3b6-277">重试策略是以编程方式进行配置，方法为在连接到高速缓存前设置客户端的选项。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-277">Retry policies are configured programmatically by setting the options for the client before connecting to the cache.</span></span> <span data-ttu-id="7f3b6-278">为此，可以创建 **ConfigurationOptions** 类的实例，填充其属性，然后将它传递到 **Connect** 方法。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-278">This can be done by creating an instance of the **ConfigurationOptions** class, populating its properties, and passing it to the **Connect** method.</span></span>

<span data-ttu-id="7f3b6-279">内置类支持随机化重试间隔的线性（固定）延迟和指数回退。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-279">The built-in classes support linear (constant) delay and exponential backoff with randomized retry intervals.</span></span> <span data-ttu-id="7f3b6-280">还可以通过实现 **IReconnectRetryPolicy** 接口创建自定义重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-280">You can also create a custom retry policy by implementing the **IReconnectRetryPolicy** interface.</span></span>

<span data-ttu-id="7f3b6-281">下面的示例使用指数回退配置重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-281">The following example configures a retry strategy using exponential backoff.</span></span>

```csharp
var deltaBackOffInMilliseconds = TimeSpan.FromSeconds(5).Milliseconds;
var maxDeltaBackOffInMilliseconds = TimeSpan.FromSeconds(20).Milliseconds;
var options = new ConfigurationOptions
{
    EndPoints = {"localhost"},
    ConnectRetry = 3,
    ReconnectRetryPolicy = new ExponentialRetry(deltaBackOffInMilliseconds, maxDeltaBackOffInMilliseconds),
    ConnectTimeout = 2000
};
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="7f3b6-282">或者，可以将选项指定为字符串，然后将其传递到 **Connect** 方法。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-282">Alternatively, you can specify the options as a string, and pass this to the **Connect** method.</span></span> <span data-ttu-id="7f3b6-283">请注意，**ReconnectRetryPolicy** 属性不能这样设置，它只能通过代码设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-283">Note that the **ReconnectRetryPolicy** property cannot be set this way, only through code.</span></span>

```csharp
var options = "localhost,connectRetry=3,connectTimeout=2000";
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="7f3b6-284">也可以在连接到缓存时直接指定选项。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-284">You can also specify options directly when you connect to the cache.</span></span>

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

<span data-ttu-id="7f3b6-285">有关详细信息，请参阅 StackExchange.Redis 文档中的 [Stack Exchange Redis 配置](https://stackexchange.github.io/StackExchange.Redis/Configuration)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-285">For more information, see [Stack Exchange Redis Configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration) in the StackExchange.Redis documentation.</span></span>

<span data-ttu-id="7f3b6-286">下表显示了内置重试策略的默认设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-286">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="7f3b6-287">**上下文**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-287">**Context**</span></span> | <span data-ttu-id="7f3b6-288">**设置**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-288">**Setting**</span></span> | <span data-ttu-id="7f3b6-289">**默认值**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-289">**Default value**</span></span><br /><span data-ttu-id="7f3b6-290">(v 1.2.2)</span><span class="sxs-lookup"><span data-stu-id="7f3b6-290">(v 1.2.2)</span></span> | <span data-ttu-id="7f3b6-291">**含义**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-291">**Meaning**</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="7f3b6-292">配置选项</span><span class="sxs-lookup"><span data-stu-id="7f3b6-292">ConfigurationOptions</span></span> |<span data-ttu-id="7f3b6-293">ConnectRetry</span><span class="sxs-lookup"><span data-stu-id="7f3b6-293">ConnectRetry</span></span><br /><br /><span data-ttu-id="7f3b6-294">ConnectTimeout</span><span class="sxs-lookup"><span data-stu-id="7f3b6-294">ConnectTimeout</span></span><br /><br /><span data-ttu-id="7f3b6-295">SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="7f3b6-295">SyncTimeout</span></span><br /><br /><span data-ttu-id="7f3b6-296">ReconnectRetryPolicy</span><span class="sxs-lookup"><span data-stu-id="7f3b6-296">ReconnectRetryPolicy</span></span> |<span data-ttu-id="7f3b6-297">3</span><span class="sxs-lookup"><span data-stu-id="7f3b6-297">3</span></span><br /><br /><span data-ttu-id="7f3b6-298">最长 5000 毫秒，外加 SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="7f3b6-298">Maximum 5000 ms plus SyncTimeout</span></span><br /><span data-ttu-id="7f3b6-299">1000</span><span class="sxs-lookup"><span data-stu-id="7f3b6-299">1000</span></span><br /><br /><span data-ttu-id="7f3b6-300">LinearRetry 5000 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-300">LinearRetry 5000 ms</span></span> |<span data-ttu-id="7f3b6-301">初始连接操作期间重试连接的次数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-301">The number of times to repeat connect attempts during the initial connection operation.</span></span><br /><span data-ttu-id="7f3b6-302">连接操作的超时（以毫秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-302">Timeout (ms) for connect operations.</span></span> <span data-ttu-id="7f3b6-303">重试尝试之间没有延迟。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-303">Not a delay between retry attempts.</span></span><br /><span data-ttu-id="7f3b6-304">允许同步操作进行的时间（以毫秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-304">Time (ms) to allow for synchronous operations.</span></span><br /><br /><span data-ttu-id="7f3b6-305">每 5000 毫秒重试一次。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-305">Retry every 5000 ms.</span></span>|

> [!NOTE]
> <span data-ttu-id="7f3b6-306">对于同步操作，可将 `SyncTimeout` 添加到端到端延迟，但将其值设置过低可能会导致超时时间过长。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-306">For synchronous operations, `SyncTimeout` can add to the end-to-end latency, but setting the value too low can cause excessive timeouts.</span></span> <span data-ttu-id="7f3b6-307">请参阅[如何排查 Azure Redis 缓存问题][redis-cache-troubleshoot]。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-307">See [How to troubleshoot Azure Redis Cache][redis-cache-troubleshoot].</span></span> <span data-ttu-id="7f3b6-308">一般应避免使用同步操作，而改用异步操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-308">In general, avoid using synchronous operations, and use asynchronous operations instead.</span></span> <span data-ttu-id="7f3b6-309">有关详细信息，请参阅 [Pipelines and Multiplexers](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md)（管道和多路复用器）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-309">For more information see [Pipelines and Multiplexers](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).</span></span>
>
>

### <a name="retry-usage-guidance"></a><span data-ttu-id="7f3b6-310">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="7f3b6-310">Retry usage guidance</span></span>
<span data-ttu-id="7f3b6-311">使用 Azure Redis 高速缓存时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-311">Consider the following guidelines when using Azure Redis Cache:</span></span>

* <span data-ttu-id="7f3b6-312">StackExchange Redis 客户端管理自己的重试，但仅限在应用程序首次启动时建立的高速缓存连接。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-312">The StackExchange Redis client manages its own retries, but only when establishing a connection to the cache when the application first starts.</span></span> <span data-ttu-id="7f3b6-313">可以配置连接超时、重试尝试次数以及重试建立此连接间隔的时间，但重试策略不适用于针对缓存执行的操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-313">You can configure the connection timeout, the number of retry attempts, and the time between retries to establish this connection, but the retry policy does not apply to operations against the cache.</span></span>
* <span data-ttu-id="7f3b6-314">请考虑改为访问原始数据源进行回退，而不是使用大量重试尝试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-314">Instead of using a large number of retry attempts, consider falling back by accessing the original data source instead.</span></span>

### <a name="telemetry"></a><span data-ttu-id="7f3b6-315">遥测</span><span class="sxs-lookup"><span data-stu-id="7f3b6-315">Telemetry</span></span>
<span data-ttu-id="7f3b6-316">可以使用 **TextWriter** 收集连接（而不是其他操作）的相关信息。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-316">You can collect information about connections (but not other operations) using a **TextWriter**.</span></span>

```csharp
var writer = new StringWriter();
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="7f3b6-317">生成的输出示例如下所示。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-317">An example of the output this generates is shown below.</span></span>

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

### <a name="examples"></a><span data-ttu-id="7f3b6-318">示例</span><span class="sxs-lookup"><span data-stu-id="7f3b6-318">Examples</span></span>
<span data-ttu-id="7f3b6-319">下面的代码示例配置初始化 StackExchange.Redis 客户端时两次重试间的固定（线性）延迟。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-319">The following code example configures a constant (linear) delay between retries when initializing the StackExchange.Redis client.</span></span> <span data-ttu-id="7f3b6-320">此示例展示如何使用 **ConfigurationOptions** 实例设置配置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-320">This example shows how to set the configuration using a **ConfigurationOptions** instance.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
    class CacheRedisCodeSamples
    {
        public async static Task Samples()
        {
            var writer = new StringWriter();
            {
                try
                {
                    var retryTimeInMilliseconds = TimeSpan.FromSeconds(4).Milliseconds; // delay between retries

                    // Using object-based configuration.
                    var options = new ConfigurationOptions
                                        {
                                            EndPoints = { "localhost" },
                                            ConnectRetry = 3,
                                            ReconnectRetryPolicy = new LinearRetry(retryTimeInMilliseconds)
                                        };
                    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

                    // Store a reference to the multiplexer for use in the application.
                }
                catch
                {
                    Console.WriteLine(writer.ToString());
                    throw;
                }
            }
        }
    }
}
```

<span data-ttu-id="7f3b6-321">下一个示例通过将选项指定为字符串来设置配置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-321">The next example sets the configuration by specifying the options as a string.</span></span> <span data-ttu-id="7f3b6-322">连接超时是指等待连接缓存的时间上限，不是指两次重试尝试间的延迟。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-322">The connection timeout is the maximum period of time to wait for a connection to the cache, not the delay between retry attempts.</span></span> <span data-ttu-id="7f3b6-323">请注意，**ReconnectRetryPolicy** 属性只能通过代码设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-323">Note that the **ReconnectRetryPolicy** property can only be set by code.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
    class CacheRedisCodeSamples
    {
        public async static Task Samples()
        {
            var writer = new StringWriter();
            {
                try
                {
                    // Using string-based configuration.
                    var options = "localhost,connectRetry=3,connectTimeout=2000";
                    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

                    // Store a reference to the multiplexer for use in the application.
                }
                catch
                {
                    Console.WriteLine(writer.ToString());
                    throw;
                }
            }
        }
    }
}
```

<span data-ttu-id="7f3b6-324">有关更多示例，请参阅项目网站上的[配置](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-324">For more examples, see [Configuration](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) on the project website.</span></span>

### <a name="more-information"></a><span data-ttu-id="7f3b6-325">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f3b6-325">More information</span></span>
* [<span data-ttu-id="7f3b6-326">Redis 网站</span><span class="sxs-lookup"><span data-stu-id="7f3b6-326">Redis website</span></span>](http://redis.io/)

## <a name="azure-search"></a><span data-ttu-id="7f3b6-327">Azure 搜索</span><span class="sxs-lookup"><span data-stu-id="7f3b6-327">Azure Search</span></span>
<span data-ttu-id="7f3b6-328">Azure 搜索可用于向网站或应用程序添加功能强大且复杂的搜索功能、快速轻松地优化搜索结果，并构造大量经过微调的排名模型。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-328">Azure Search can be used to add powerful and sophisticated search capabilities to a website or application, quickly and easily tune search results, and construct rich and fine-tuned ranking models.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-329">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-329">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-330">[SearchServiceClient] 和 [SearchIndexClient] 类上的 `SetRetryPolicy` 方法控制 Azure 搜索 SDK 中的重试行为。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-330">Retry behavior in the Azure Search SDK is controlled by the `SetRetryPolicy` method on the [SearchServiceClient] and [SearchIndexClient] classes.</span></span> <span data-ttu-id="7f3b6-331">Azure 搜索返回 5xx 或 408（请求超时）响应时具有指数回退的默认策略重试次数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-331">The default policy retries with exponential backoff when Azure Search returns a 5xx or 408 (Request Timeout) response.</span></span>

### <a name="telemetry"></a><span data-ttu-id="7f3b6-332">遥测</span><span class="sxs-lookup"><span data-stu-id="7f3b6-332">Telemetry</span></span>
<span data-ttu-id="7f3b6-333">跟踪 ETW，或通过注册自定义提供程序来实现。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-333">Trace with ETW or by registering a custom trace provider.</span></span> <span data-ttu-id="7f3b6-334">有关详细信息，请参阅 [AutoRest 文档][autorest]。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-334">For more information, see the [AutoRest documentation][autorest].</span></span>

## <a name="service-bus"></a><span data-ttu-id="7f3b6-335">服务总线</span><span class="sxs-lookup"><span data-stu-id="7f3b6-335">Service Bus</span></span>
<span data-ttu-id="7f3b6-336">服务总线是云消息传送平台，可提供松散耦合的消息交换，同时提升应用程序各组件（无论是托管在云中还是在本地）的规模和复原能力。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-336">Service Bus is a cloud messaging platform that provides loosely coupled message exchange with improved scale and resiliency for components of an application, whether hosted in the cloud or on-premises.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-337">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-337">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-338">服务总线通过 [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) 基类的实现来实现重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-338">Service Bus implements retries using implementations of the [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) base class.</span></span> <span data-ttu-id="7f3b6-339">所有服务总线客户端均公开 **RetryPolicy** 属性，此属性可以设置为 **RetryPolicy** 基类的一个实现。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-339">All of the Service Bus clients expose a **RetryPolicy** property that can be set to one of the implementations of the **RetryPolicy** base class.</span></span> <span data-ttu-id="7f3b6-340">内置实现为：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-340">The built-in implementations are:</span></span>

* <span data-ttu-id="7f3b6-341">[RetryExponential 类](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-341">The [RetryExponential Class](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx).</span></span> <span data-ttu-id="7f3b6-342">这会公开用于控制回退间隔和重试计数的属性，以及用于限制操作完成总时间的 **TerminationTimeBuffer** 属性。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-342">This exposes properties that control the back-off interval, the retry count, and the **TerminationTimeBuffer** property that is used to limit the total time for the operation to complete.</span></span>
* <span data-ttu-id="7f3b6-343">[NoRetry 类](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-343">The [NoRetry Class](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx).</span></span> <span data-ttu-id="7f3b6-344">这适用于不需要在服务总线 API 一级进行重试的情况，如其他进程将重试作为批处理或多步操作的一部分进行管理的情况。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-344">This is used when retries at the Service Bus API level are not required, such as when retries are managed by another process as part of a batch or multiple step operation.</span></span>

<span data-ttu-id="7f3b6-345">服务总线操作可以返回一系列异常，如 [Appendix: Messaging Exceptions](http://msdn.microsoft.com/library/hh418082.aspx)（附录：消息传送异常）中所列。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-345">Service Bus actions can return a range of exceptions, as listed in [Appendix: Messaging Exceptions](http://msdn.microsoft.com/library/hh418082.aspx).</span></span> <span data-ttu-id="7f3b6-346">此列表提供了一些信息来指明重试操作是否合适。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-346">The list provides information about which if these indicate that retrying the operation is appropriate.</span></span> <span data-ttu-id="7f3b6-347">例如，[ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) 指明客户端应等待一段时间，并重试操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-347">For example, a [ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) indicates that the client should wait for a period of time, then retry the operation.</span></span> <span data-ttu-id="7f3b6-348">**ServerBusyException** 的出现还会导致服务总线切换到不同模式，这会令计算的重试延迟额外增加 10 秒。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-348">The occurrence of a **ServerBusyException** also causes Service Bus to switch to a different mode, in which an extra 10-second delay is added to the computed retry delays.</span></span> <span data-ttu-id="7f3b6-349">在一段很短的时间后，此模式会重置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-349">This mode is reset after a short period.</span></span>

<span data-ttu-id="7f3b6-350">从服务总线返回的异常会公开 **IsTransient** 属性，此属性指明客户端是否应重试操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-350">The exceptions returned from Service Bus expose the **IsTransient** property that indicates if the client should retry the operation.</span></span> <span data-ttu-id="7f3b6-351">内置的 **RetryExponential** 策略依赖于 **MessagingException** 类（所有服务总线异常的基类）中的 **IsTransient** 属性。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-351">The built-in **RetryExponential** policy relies on the **IsTransient** property in the **MessagingException** class, which is the base class for all Service Bus exceptions.</span></span> <span data-ttu-id="7f3b6-352">如果创建 **RetryPolicy** 基类的自定义实现，则可以结合使用异常类型和 **IsTransient** 属性来更精细地控制重试操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-352">If you create custom implementations of the **RetryPolicy** base class you could use a combination of the exception type and the **IsTransient** property to provide more fine-grained control over retry actions.</span></span> <span data-ttu-id="7f3b6-353">例如，可以检测 **QuotaExceededException**，并采取措施以在重试向其发送消息之前清空队列。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-353">For example, you could detect a **QuotaExceededException** and take action to drain the queue before retrying sending a message to it.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="7f3b6-354">策略配置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-354">Policy configuration</span></span>
<span data-ttu-id="7f3b6-355">重试策略以编程方式进行设置，可以设置为 **NamespaceManager** 和 **MessagingFactory** 的默认策略，也可以针对每个消息客户端进行单独设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-355">Retry policies are set programmatically, and can be set as a default policy for a **NamespaceManager** and for a **MessagingFactory**, or individually for each messaging client.</span></span> <span data-ttu-id="7f3b6-356">若要设置消息会话的默认重试策略 ，请设置 **NamespaceManager** 的 **RetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-356">To set the default retry policy for a messaging session you set the **RetryPolicy** of the **NamespaceManager**.</span></span>

```csharp
namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                                maxBackoff: TimeSpan.FromSeconds(30),
                                                                maxRetryCount: 3);
```

<span data-ttu-id="7f3b6-357">若要为通过消息工厂创建的所有客户端设置默认重试策略，请设置 **MessagingFactory** 的 **RetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-357">To set the default retry policy for all clients created from a messaging factory, you set the **RetryPolicy** of the **MessagingFactory**.</span></span>

```csharp
messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                    maxBackoff: TimeSpan.FromSeconds(30),
                                                    maxRetryCount: 3);
```

<span data-ttu-id="7f3b6-358">若要设置消息客户端的重试策略，或替代其默认策略，请使用相应策略类的实例设置其 **RetryPolicy** 属性：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-358">To set the retry policy for a messaging client, or to override its default policy, you set its **RetryPolicy** property using an instance of the required policy class:</span></span>

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                            maxBackoff: TimeSpan.FromSeconds(30),
                                            maxRetryCount: 3);
```

<span data-ttu-id="7f3b6-359">不能在各个操作级别设置重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-359">The retry policy cannot be set at the individual operation level.</span></span> <span data-ttu-id="7f3b6-360">它适用于消息客户端的所有操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-360">It applies to all operations for the messaging client.</span></span>
<span data-ttu-id="7f3b6-361">下表显示了内置重试策略的默认设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-361">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="7f3b6-362">设置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-362">Setting</span></span> | <span data-ttu-id="7f3b6-363">默认值</span><span class="sxs-lookup"><span data-stu-id="7f3b6-363">Default value</span></span> | <span data-ttu-id="7f3b6-364">含义</span><span class="sxs-lookup"><span data-stu-id="7f3b6-364">Meaning</span></span> |
|---------|---------------|---------|
| <span data-ttu-id="7f3b6-365">策略</span><span class="sxs-lookup"><span data-stu-id="7f3b6-365">Policy</span></span> | <span data-ttu-id="7f3b6-366">指数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-366">Exponential</span></span> | <span data-ttu-id="7f3b6-367">指数退让。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-367">Exponential back-off.</span></span> |
| <span data-ttu-id="7f3b6-368">MinimalBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-368">MinimalBackoff</span></span> | <span data-ttu-id="7f3b6-369">0</span><span class="sxs-lookup"><span data-stu-id="7f3b6-369">0</span></span> | <span data-ttu-id="7f3b6-370">最小退让间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-370">Minimum back-off interval.</span></span> <span data-ttu-id="7f3b6-371">此值将与通过 deltaBackoff 计算得出的重试间隔相加。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-371">This is added to the retry interval computed from deltaBackoff.</span></span> |
| <span data-ttu-id="7f3b6-372">MaximumBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-372">MaximumBackoff</span></span> | <span data-ttu-id="7f3b6-373">30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-373">30 seconds</span></span> | <span data-ttu-id="7f3b6-374">最大退让间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-374">Maximum back-off interval.</span></span> <span data-ttu-id="7f3b6-375">如果计算出的重试间隔大于 MaxBackoff，则使用 MaximumBackoff。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-375">MaximumBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> |
| <span data-ttu-id="7f3b6-376">DeltaBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-376">DeltaBackoff</span></span> | <span data-ttu-id="7f3b6-377">3 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-377">3 seconds</span></span> | <span data-ttu-id="7f3b6-378">不同重试之间的回退时间间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-378">Back-off interval between retries.</span></span> <span data-ttu-id="7f3b6-379">将针对后续的重试使用多个这样的时间跨度。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-379">Multiples of this timespan will be used for subsequent retry attempts.</span></span> |
| <span data-ttu-id="7f3b6-380">TimeBuffer</span><span class="sxs-lookup"><span data-stu-id="7f3b6-380">TimeBuffer</span></span> | <span data-ttu-id="7f3b6-381">5 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-381">5 seconds</span></span> | <span data-ttu-id="7f3b6-382">与重试关联的终止时间缓冲区。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-382">The termination time buffer associated with the retry.</span></span> <span data-ttu-id="7f3b6-383">如果剩余时间小于 TimeBuffer，将放弃重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-383">Retry attempts will be abandoned if the remaining time is less than TimeBuffer.</span></span> |
| <span data-ttu-id="7f3b6-384">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="7f3b6-384">MaxRetryCount</span></span> | <span data-ttu-id="7f3b6-385">10</span><span class="sxs-lookup"><span data-stu-id="7f3b6-385">10</span></span> | <span data-ttu-id="7f3b6-386">最大重试次数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-386">The maximum number of retries.</span></span> |
| <span data-ttu-id="7f3b6-387">ServerBusyBaseSleepTime</span><span class="sxs-lookup"><span data-stu-id="7f3b6-387">ServerBusyBaseSleepTime</span></span> | <span data-ttu-id="7f3b6-388">10 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-388">10 seconds</span></span> | <span data-ttu-id="7f3b6-389">如果遇到的最后一个异常为 **ServerBusyException**，则将此值与计算出的重试间隔相加。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-389">If the last exception encountered was **ServerBusyException**, this value will be added to the computed retry interval.</span></span> <span data-ttu-id="7f3b6-390">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-390">This value cannot be changed.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="7f3b6-391">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="7f3b6-391">Retry usage guidance</span></span>
<span data-ttu-id="7f3b6-392">使用服务总线时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-392">Consider the following guidelines when using Service Bus:</span></span>

* <span data-ttu-id="7f3b6-393">使用内置 **RetryExponential** 实现时，请勿将回退操作实现为响应服务器忙异常和自动切换到适合重试模式的策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-393">When using the built-in **RetryExponential** implementation, do not implement a fallback operation as the policy reacts to Server Busy exceptions and automatically switches to an appropriate retry mode.</span></span>
* <span data-ttu-id="7f3b6-394">服务总线支持配对的命名空间这一功能。如果主要命名空间中的队列失败，则此功能会实现自动故障转移到单独命名空间中的备份队列。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-394">Service Bus supports a feature called Paired Namespaces, which implements automatic failover to a backup queue in a separate namespace if the queue in the primary namespace fails.</span></span> <span data-ttu-id="7f3b6-395">在主要队列恢复后，来自辅助队列的消息可以发送回主要队列。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-395">Messages from the secondary queue can be sent back to the primary queue when it recovers.</span></span> <span data-ttu-id="7f3b6-396">此功能有助于解决临时故障。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-396">This feature helps to address transient failures.</span></span> <span data-ttu-id="7f3b6-397">有关详细信息，请参阅[异步消息传送模式和高可用性](http://msdn.microsoft.com/library/azure/dn292562.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-397">For more information, see [Asynchronous Messaging Patterns and High Availability](http://msdn.microsoft.com/library/azure/dn292562.aspx).</span></span>

<span data-ttu-id="7f3b6-398">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-398">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="7f3b6-399">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-399">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="7f3b6-400">上下文</span><span class="sxs-lookup"><span data-stu-id="7f3b6-400">Context</span></span> | <span data-ttu-id="7f3b6-401">示例最大延迟</span><span class="sxs-lookup"><span data-stu-id="7f3b6-401">Example maximum latency</span></span> | <span data-ttu-id="7f3b6-402">重试策略</span><span class="sxs-lookup"><span data-stu-id="7f3b6-402">Retry policy</span></span> | <span data-ttu-id="7f3b6-403">设置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-403">Settings</span></span> | <span data-ttu-id="7f3b6-404">工作原理</span><span class="sxs-lookup"><span data-stu-id="7f3b6-404">How it works</span></span> |
|---------|---------|---------|---------|---------|
| <span data-ttu-id="7f3b6-405">交互、UI 或前台</span><span class="sxs-lookup"><span data-stu-id="7f3b6-405">Interactive, UI, or foreground</span></span> | <span data-ttu-id="7f3b6-406">2 秒\*</span><span class="sxs-lookup"><span data-stu-id="7f3b6-406">2 seconds\*</span></span>  | <span data-ttu-id="7f3b6-407">指数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-407">Exponential</span></span> | <span data-ttu-id="7f3b6-408">MinimumBackoff = 0</span><span class="sxs-lookup"><span data-stu-id="7f3b6-408">MinimumBackoff = 0</span></span> <br/> <span data-ttu-id="7f3b6-409">MaximumBackoff = 30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-409">MaximumBackoff = 30 sec.</span></span> <br/> <span data-ttu-id="7f3b6-410">DeltaBackoff = 300 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-410">DeltaBackoff = 300 msec.</span></span> <br/> <span data-ttu-id="7f3b6-411">TimeBuffer = 300 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-411">TimeBuffer = 300 msec.</span></span> <br/> <span data-ttu-id="7f3b6-412">MaxRetryCount = 2</span><span class="sxs-lookup"><span data-stu-id="7f3b6-412">MaxRetryCount = 2</span></span> | <span data-ttu-id="7f3b6-413">第 1 次尝试：延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-413">Attempt 1: Delay 0 sec.</span></span> <br/> <span data-ttu-id="7f3b6-414">第 2 次尝试：延迟约 300 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-414">Attempt 2: Delay ~300 msec.</span></span> <br/> <span data-ttu-id="7f3b6-415">第 3 次尝试：延迟约 900 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-415">Attempt 3: Delay ~900 msec.</span></span> |
| <span data-ttu-id="7f3b6-416">后台或批处理</span><span class="sxs-lookup"><span data-stu-id="7f3b6-416">Background or batch</span></span> | <span data-ttu-id="7f3b6-417">30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-417">30 seconds</span></span> | <span data-ttu-id="7f3b6-418">指数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-418">Exponential</span></span> | <span data-ttu-id="7f3b6-419">MinimumBackoff = 1</span><span class="sxs-lookup"><span data-stu-id="7f3b6-419">MinimumBackoff = 1</span></span> <br/> <span data-ttu-id="7f3b6-420">MaximumBackoff = 30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-420">MaximumBackoff = 30 sec.</span></span> <br/> <span data-ttu-id="7f3b6-421">DeltaBackoff = 1.75 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-421">DeltaBackoff = 1.75 sec.</span></span> <br/> <span data-ttu-id="7f3b6-422">TimeBuffer = 5 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-422">TimeBuffer = 5 sec.</span></span> <br/> <span data-ttu-id="7f3b6-423">MaxRetryCount = 3</span><span class="sxs-lookup"><span data-stu-id="7f3b6-423">MaxRetryCount = 3</span></span> | <span data-ttu-id="7f3b6-424">第 1 次尝试：延迟约 1 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-424">Attempt 1: Delay ~1 sec.</span></span> <br/> <span data-ttu-id="7f3b6-425">第 2 次尝试：延迟约 3 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-425">Attempt 2: Delay ~3 sec.</span></span> <br/> <span data-ttu-id="7f3b6-426">第 3 次尝试：延迟约 6 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-426">Attempt 3: Delay ~6 msec.</span></span> <br/> <span data-ttu-id="7f3b6-427">第 4 次尝试：延迟约 13 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-427">Attempt 4: Delay ~13 msec.</span></span> |

<span data-ttu-id="7f3b6-428">\* 不包括收到“服务器忙”响应时增加的附加延迟。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-428">\* Not including additional delay that is added if a Server Busy response is received.</span></span>

### <a name="telemetry"></a><span data-ttu-id="7f3b6-429">遥测</span><span class="sxs-lookup"><span data-stu-id="7f3b6-429">Telemetry</span></span>
<span data-ttu-id="7f3b6-430">服务总线使用 **EventSource** 将重试记录为 ETW 事件。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-430">Service Bus logs retries as ETW events using an **EventSource**.</span></span> <span data-ttu-id="7f3b6-431">必须将 **EventListener** 附加到事件源，才能捕获事件并在性能查看器中查看这些事件，或将这些事件写入合适的目标日志。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-431">You must attach an **EventListener** to the event source to capture the events and view them in Performance Viewer, or write them to a suitable destination log.</span></span> <span data-ttu-id="7f3b6-432">为此，可以使用[语义式日志记录应用程序块](http://msdn.microsoft.com/library/dn775006.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-432">You could use the [Semantic Logging Application Block](http://msdn.microsoft.com/library/dn775006.aspx) to do this.</span></span> <span data-ttu-id="7f3b6-433">重试事件具有以下形式：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-433">The retry events are of the following form:</span></span>

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### <a name="examples"></a><span data-ttu-id="7f3b6-434">示例</span><span class="sxs-lookup"><span data-stu-id="7f3b6-434">Examples</span></span>
<span data-ttu-id="7f3b6-435">以下代码示例展示了如何为下列对象设置重试策略：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-435">The following code example shows how to set the retry policy for:</span></span>

* <span data-ttu-id="7f3b6-436">命名空间管理器：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-436">A namespace manager.</span></span> <span data-ttu-id="7f3b6-437">重试策略适用于此管理器上的所有操作，不能针对各个操作进行替代。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-437">The policy applies to all operations on that manager, and cannot be overridden for individual operations.</span></span>
* <span data-ttu-id="7f3b6-438">消息工厂：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-438">A messaging factory.</span></span> <span data-ttu-id="7f3b6-439">重试策略适用于通过此工厂创建的所有客户端，不能在创建各个客户端时进行替代。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-439">The policy applies to all clients created from that factory, and cannot be overridden when creating individual clients.</span></span>
* <span data-ttu-id="7f3b6-440">单个消息客户端：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-440">An individual messaging client.</span></span> <span data-ttu-id="7f3b6-441">在创建客户端后，可以为此客户端设置重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-441">After a client has been created, you can set the retry policy for that client.</span></span> <span data-ttu-id="7f3b6-442">重试策略适用于此客户端上的所有操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-442">The policy applies to all operations on that client.</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
    class ServiceBusCodeSamples
    {
        private const string connectionString =
            @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
                SharedAccessKeyName=RootManageSharedAccessKey;
                SharedAccessKey=C99..........Mk=";

        public async static Task Samples()
        {
            const string QueueName = "TestQueue";

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

            var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

            // The namespace manager will have a default exponential policy with 10 retry attempts
            // and a 3 second delay delta.
            // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
            // with an extra 10 sec added when receiving a ServiceBusyException.

            {
                // Set different values for the retry policy, used for all operations on the namespace manager.
                namespaceManager.Settings.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(0),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);

                // Policies cannot be specified on a per-operation basis.
                if (!await namespaceManager.QueueExistsAsync(QueueName))
                {
                    await namespaceManager.CreateQueueAsync(QueueName);
                }
            }

            var messagingFactory = MessagingFactory.Create(
                namespaceManager.Address, namespaceManager.Settings.TokenProvider);
            // The messaging factory will have a default exponential policy with 10 retry attempts
            // and a 3 second delay delta.
            // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
            // with an extra 10 sec added when receiving a ServiceBusyException.

            {
                // Set different values for the retry policy, used for clients created from it.
                messagingFactory.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(1),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);


                // Policies cannot be specified on a per-operation basis.
                var session = await messagingFactory.AcceptMessageSessionAsync();
            }

            {
                var client = messagingFactory.CreateQueueClient(QueueName);
                // The client inherits the policy from the factory that created it.


                // Set different values for the retry policy on the client.
                client.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(0.1),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);

                // Policies cannot be specified on a per-operation basis.
                var session = await client.AcceptMessageSessionAsync();
            }
        }
    }
}
```

### <a name="more-information"></a><span data-ttu-id="7f3b6-443">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f3b6-443">More information</span></span>
* [<span data-ttu-id="7f3b6-444">异步消息传送模式和高可用性</span><span class="sxs-lookup"><span data-stu-id="7f3b6-444">Asynchronous Messaging Patterns and High Availability</span></span>](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## <a name="service-fabric"></a><span data-ttu-id="7f3b6-445">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="7f3b6-445">Service Fabric</span></span>

<span data-ttu-id="7f3b6-446">在 Service Fabric 群集中分布可靠服务可防止出现本文描述的大部分潜在暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-446">Distributing reliable services in a Service Fabric cluster guards against most of the potential transient faults discussed in this article.</span></span> <span data-ttu-id="7f3b6-447">但是，某些暂时性错误仍有可能发生。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-447">Some transient faults are still possible, however.</span></span> <span data-ttu-id="7f3b6-448">例如，命名服务收到请求时可能正在进行路由更改，从而导致它引发异常。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-448">For example, the naming service might be in the middle of a routing change when it gets a request, causing it to throw an exception.</span></span> <span data-ttu-id="7f3b6-449">如果同一请求在 100 毫秒后发出，则有可能成功。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-449">If the same request comes 100 milliseconds later, it will probably succeed.</span></span>

<span data-ttu-id="7f3b6-450">Service Fabric 可在内部管理这种暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-450">Internally, Service Fabric manages this kind of transient fault.</span></span> <span data-ttu-id="7f3b6-451">你可以在设置服务时使用 `OperationRetrySettings` 类配置某些设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-451">You can configure some settings by using the `OperationRetrySettings` class while setting up your services.</span></span>  <span data-ttu-id="7f3b6-452">以下代码展示了一个示例。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-452">The following code shows an example.</span></span> <span data-ttu-id="7f3b6-453">在大多数情况下，不必执行此操作，直接使用默认设置即可。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-453">In most cases, this should not be necessary, and the default settings will be fine.</span></span>

```csharp
FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
{
    OperationTimeout = TimeSpan.FromSeconds(30)
};

var retrySettings = new OperationRetrySettings(TimeSpan.FromSeconds(15), TimeSpan.FromSeconds(1), 5);

var clientFactory = new FabricTransportServiceRemotingClientFactory(transportSettings);

var serviceProxyFactory = new ServiceProxyFactory((c) => clientFactory, retrySettings);

var client = serviceProxyFactory.CreateServiceProxy<ISomeService>(
    new Uri("fabric:/SomeApp/SomeStatefulReliableService"),
    new ServicePartitionKey(0));
```

### <a name="more-information"></a><span data-ttu-id="7f3b6-454">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f3b6-454">More information</span></span>

* [<span data-ttu-id="7f3b6-455">远程异常处理</span><span class="sxs-lookup"><span data-stu-id="7f3b6-455">Remote Exception Handling</span></span>](https://github.com/Microsoft/azure-docs/blob/master/articles/service-fabric/service-fabric-reliable-services-communication-remoting.md#remoting-exception-handling)

## <a name="sql-database-using-adonet"></a><span data-ttu-id="7f3b6-456">使用 ADO.NET 的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="7f3b6-456">SQL Database using ADO.NET</span></span>
<span data-ttu-id="7f3b6-457">SQL 数据库是一种托管的 SQL 数据库，具有各种大小，可作为标准（共享）和高级（非共享）服务提供。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-457">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-458">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-458">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-459">访问使用 ADO.NET 的 SQL 数据库时，其中没有内置重试支持。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-459">SQL Database has no built-in support for retries when accessed using ADO.NET.</span></span> <span data-ttu-id="7f3b6-460">不过，请求的返回代码可用于确定请求失败原因。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-460">However, the return codes from requests can be used to determine why a request failed.</span></span> <span data-ttu-id="7f3b6-461">有关 SQL 数据库限制的详细信息，请参阅 [Azure SQL 数据库资源限制](/azure/sql-database/sql-database-resource-limits)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-461">For more information about SQL Database throttling, see [Azure SQL Database resource limits](/azure/sql-database/sql-database-resource-limits).</span></span> <span data-ttu-id="7f3b6-462">有关相关错误代码的列表，请参阅 [SQL 数据库客户端应用程序的 SQL 错误代码](/azure/sql-database/sql-database-develop-error-messages)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-462">For a list of relevant error codes, see [SQL error codes for SQL Database client applications](/azure/sql-database/sql-database-develop-error-messages).</span></span>

<span data-ttu-id="7f3b6-463">可使用 Polly 库为 SQL 数据库实现重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-463">You can use the Polly library to implement retries for SQL Database.</span></span> <span data-ttu-id="7f3b6-464">请参阅[使用 Polly 处理暂时性错误](#transient-fault-handling-with-polly)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-464">See [Transient fault handling with Polly](#transient-fault-handling-with-polly).</span></span>

### <a name="retry-usage-guidance"></a><span data-ttu-id="7f3b6-465">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="7f3b6-465">Retry usage guidance</span></span>
<span data-ttu-id="7f3b6-466">访问使用 ADO.NET 的 SQL 数据库时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-466">Consider the following guidelines when accessing SQL Database using ADO.NET:</span></span>

* <span data-ttu-id="7f3b6-467">选择适当的服务选项（共享或高级）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-467">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="7f3b6-468">鉴于共享服务器的其他租户的使用情况，共享实例的连接延迟和限制可能比平常要长。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-468">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="7f3b6-469">如果需要更加可预测的性能和可靠的低延迟操作，请考虑选择高级选项。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-469">If more predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="7f3b6-470">请务必在适当的级别或作用域执行重试，以避免出现导致数据不一致的非幂等操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-470">Ensure that you perform retries at the appropriate level or scope to avoid non-idempotent operations causing inconsistency in the data.</span></span> <span data-ttu-id="7f3b6-471">理想情况下，所有操作都应是幂等操作，这样就可以重复执行，而不会导致不一致。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-471">Ideally, all operations should be idempotent so that they can be repeated without causing inconsistency.</span></span> <span data-ttu-id="7f3b6-472">如果并非如此，应在符合以下条件的级别或作用域执行重试：在一个操作失败时允许撤消所有相关更改；例如，从事务作用域内。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-472">Where this is not the case, the retry should be performed at a level or scope that allows all related changes to be undone if one operation fails; for example, from within a transactional scope.</span></span> <span data-ttu-id="7f3b6-473">有关详细信息，请参阅[云服务基础数据访问层 - 临时故障处理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-473">For more information, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).</span></span>
* <span data-ttu-id="7f3b6-474">不建议结合使用固定间隔策略和 Azure SQL 数据库（交互式方案除外，在此类方案中，仅有几次重试的间隔非常短）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-474">A fixed interval strategy is not recommended for use with Azure SQL Database except for interactive scenarios where there are only a few retries at very short intervals.</span></span> <span data-ttu-id="7f3b6-475">对于大多数方案，请考虑使用指数回退策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-475">Instead, consider using an exponential back-off strategy for the majority of scenarios.</span></span>
* <span data-ttu-id="7f3b6-476">定义连接时，为连接和命令超时选择合适的值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-476">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="7f3b6-477">当数据库忙碌时，超时太短可能会导致连接过早失败。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-477">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="7f3b6-478">超时太长则会导致在检测到失败的连接前等待太久，进而可能会妨碍重试逻辑正常运行。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-478">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="7f3b6-479">超时值是端到端延迟的组成部分；它可以有效地增加每次重试尝试的重试策略中指定的重试延迟。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-479">The value of the timeout is a component of the end-to-end latency; it is effectively added to the retry delay specified in the retry policy for every retry attempt.</span></span>
* <span data-ttu-id="7f3b6-480">重试一定次数后关闭连接（即使使用指数回退重试逻辑，也是如此），并在新连接上重试操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-480">Close the connection after a certain number of retries, even when using an exponential back off retry logic, and retry the operation on a new connection.</span></span> <span data-ttu-id="7f3b6-481">在同一个连接上多次重试相同的操作可能是导致连接问题出现的一个因素。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-481">Retrying the same operation multiple times on the same connection can be a factor that contributes to connection problems.</span></span> <span data-ttu-id="7f3b6-482">有关此技术的示例，请参阅[云服务基础数据访问层 - 临时故障处理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-482">For an example of this technique, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).</span></span>
* <span data-ttu-id="7f3b6-483">当连接池正在使用中（默认情况）时，可能会从池中选择相同的连接，即使在关闭并重新打开连接后，也可能会出现这样的情况。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-483">When connection pooling is in use (the default) there is a chance that the same connection will be chosen from the pool, even after closing and reopening a connection.</span></span> <span data-ttu-id="7f3b6-484">如果遇到这种情况，解决方法是调用 **SqlConnection** 类的 **ClearPool** 方法，将连接标记为不可重用。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-484">If this is the case, a technique to resolve it is to call the **ClearPool** method of the **SqlConnection** class to mark the connection as not reusable.</span></span> <span data-ttu-id="7f3b6-485">不过，仅在几次连接尝试均失败，且遇到与失败的连接相关的某类临时故障（如 SQL 超时（错误代码 -2））时，才能这样做。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-485">However, you should do this only after several connection attempts have failed, and only when encountering the specific class of transient failures such as SQL timeouts (error code -2) related to faulty connections.</span></span>
* <span data-ttu-id="7f3b6-486">如果数据访问代码使用作为 **TransactionScope** 实例启动的事务，则重试逻辑应重新打开连接，并启动新的事务作用域。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-486">If the data access code uses transactions initiated as **TransactionScope** instances, the retry logic should reopen the connection and initiate a new transaction scope.</span></span> <span data-ttu-id="7f3b6-487">因此，可重试代码块应包含事务的整个作用域。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-487">For this reason, the retryable code block should encompass the entire scope of the transaction.</span></span>

<span data-ttu-id="7f3b6-488">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-488">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="7f3b6-489">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-489">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="7f3b6-490">**上下文**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-490">**Context**</span></span> | <span data-ttu-id="7f3b6-491">**示例目标 E2E<br />最长延迟**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-491">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="7f3b6-492">**重试策略**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-492">**Retry strategy**</span></span> | <span data-ttu-id="7f3b6-493">**设置**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-493">**Settings**</span></span> | <span data-ttu-id="7f3b6-494">**值**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-494">**Values**</span></span> | <span data-ttu-id="7f3b6-495">**工作原理**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-495">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="7f3b6-496">交互式, UI,</span><span class="sxs-lookup"><span data-stu-id="7f3b6-496">Interactive, UI,</span></span><br /><span data-ttu-id="7f3b6-497">或 foreground</span><span class="sxs-lookup"><span data-stu-id="7f3b6-497">or foreground</span></span> |<span data-ttu-id="7f3b6-498">2 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-498">2 sec</span></span> |<span data-ttu-id="7f3b6-499">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="7f3b6-499">FixedInterval</span></span> |<span data-ttu-id="7f3b6-500">重试计数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-500">Retry count</span></span><br /><span data-ttu-id="7f3b6-501">重试间隔</span><span class="sxs-lookup"><span data-stu-id="7f3b6-501">Retry interval</span></span><br /><span data-ttu-id="7f3b6-502">首次快速重试</span><span class="sxs-lookup"><span data-stu-id="7f3b6-502">First fast retry</span></span> |<span data-ttu-id="7f3b6-503">3</span><span class="sxs-lookup"><span data-stu-id="7f3b6-503">3</span></span><br /><span data-ttu-id="7f3b6-504">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-504">500 ms</span></span><br /><span data-ttu-id="7f3b6-505">是</span><span class="sxs-lookup"><span data-stu-id="7f3b6-505">true</span></span> |<span data-ttu-id="7f3b6-506">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-506">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="7f3b6-507">第 2 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-507">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="7f3b6-508">第 3 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-508">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="7f3b6-509">背景</span><span class="sxs-lookup"><span data-stu-id="7f3b6-509">Background</span></span><br /><span data-ttu-id="7f3b6-510">或批处理</span><span class="sxs-lookup"><span data-stu-id="7f3b6-510">or batch</span></span> |<span data-ttu-id="7f3b6-511">30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-511">30 sec</span></span> |<span data-ttu-id="7f3b6-512">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-512">ExponentialBackoff</span></span> |<span data-ttu-id="7f3b6-513">重试计数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-513">Retry count</span></span><br /><span data-ttu-id="7f3b6-514">最小回退</span><span class="sxs-lookup"><span data-stu-id="7f3b6-514">Min back-off</span></span><br /><span data-ttu-id="7f3b6-515">最大回退</span><span class="sxs-lookup"><span data-stu-id="7f3b6-515">Max back-off</span></span><br /><span data-ttu-id="7f3b6-516">增量回退</span><span class="sxs-lookup"><span data-stu-id="7f3b6-516">Delta back-off</span></span><br /><span data-ttu-id="7f3b6-517">首次快速重试</span><span class="sxs-lookup"><span data-stu-id="7f3b6-517">First fast retry</span></span> |<span data-ttu-id="7f3b6-518">5</span><span class="sxs-lookup"><span data-stu-id="7f3b6-518">5</span></span><br /><span data-ttu-id="7f3b6-519">0 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-519">0 sec</span></span><br /><span data-ttu-id="7f3b6-520">60 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-520">60 sec</span></span><br /><span data-ttu-id="7f3b6-521">2 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-521">2 sec</span></span><br /><span data-ttu-id="7f3b6-522">false</span><span class="sxs-lookup"><span data-stu-id="7f3b6-522">false</span></span> |<span data-ttu-id="7f3b6-523">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-523">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="7f3b6-524">第 2 次尝试 - 约延迟 2 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-524">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="7f3b6-525">第 3 次尝试 - 约延迟 6 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-525">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="7f3b6-526">第 4 次尝试 - 约延迟 14 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-526">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="7f3b6-527">第 5 次尝试 - 约延迟 30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-527">Attempt 5 - delay ~30 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="7f3b6-528">端到端延迟目标假设采用服务连接的默认超时。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-528">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="7f3b6-529">如果指定更长的连接超时，则每次重试尝试都会将端到端延迟延长这一附加时间。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-529">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="7f3b6-530">示例</span><span class="sxs-lookup"><span data-stu-id="7f3b6-530">Examples</span></span>
<span data-ttu-id="7f3b6-531">本部分说明如何使用 Polly 通过在 `Policy` 类中配置的一组重试策略访问 Azure SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-531">This section shows how you can use Polly to access Azure SQL Database using a set of retry policies configured in the `Policy` class.</span></span>

<span data-ttu-id="7f3b6-532">以下代码展示了 `SqlCommand` 类上的扩展方法，该方法调用具有指数回退的 `ExecuteAsync`。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-532">The following code shows an extension method on the `SqlCommand` class that calls `ExecuteAsync` with exponential backoff.</span></span>

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command)
{
    GuardConnectionIsNotNull(command);

    var policy = Policy.Handle<Exception>().WaitAndRetryAsync(
        retryCount: 3, // Retry 3 times
        sleepDurationProvider: attempt => TimeSpan.FromMilliseconds(200 * Math.Pow(2, attempt - 1)), // Exponential backoff based on an initial 200ms delay.
        onRetry: (exception, attempt) => 
        {
            // Capture some info for logging/telemetry.  
            logger.LogWarn($"ExecuteReaderWithRetryAsync: Retry {attempt} due to {exception}.");
        });

    // Retry the following call according to the policy.
    await policy.ExecuteAsync<SqlDataReader>(async token =>
    {
        // This code is executed within the Policy 

        if (conn.State != System.Data.ConnectionState.Open) await conn.OpenAsync(token);
        return await command.ExecuteReaderAsync(System.Data.CommandBehavior.Default, token);

    }, cancellationToken);
}
```

<span data-ttu-id="7f3b6-533">可按以下方式使用此异步扩展方法。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-533">This asynchronous extension method can be used as follows.</span></span>

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync())
{
    // Do something with the values
}
```

### <a name="more-information"></a><span data-ttu-id="7f3b6-534">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f3b6-534">More information</span></span>
* [<span data-ttu-id="7f3b6-535">云服务基础数据访问层 - 临时故障处理</span><span class="sxs-lookup"><span data-stu-id="7f3b6-535">Cloud Service Fundamentals Data Access Layer – Transient Fault Handling</span></span>](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

<span data-ttu-id="7f3b6-536">有关如何充分利用 SQL 数据库的一般指南，请参阅 [Azure SQL 数据库性能和弹性指南](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-536">For general guidance on getting the most from SQL Database, see [Azure SQL Database Performance and Elasticity Guide](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx).</span></span>

## <a name="sql-database-using-entity-framework-6"></a><span data-ttu-id="7f3b6-537">使用 Entity Framework 6 的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="7f3b6-537">SQL Database using Entity Framework 6</span></span>
<span data-ttu-id="7f3b6-538">SQL 数据库是一种托管的 SQL 数据库，具有各种大小，可作为标准（共享）和高级（非共享）服务提供。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-538">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span> <span data-ttu-id="7f3b6-539">Entity Framework 是一种对象关系映射程序，可方便 .NET 开发人员使用域特定对象处理关系数据。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-539">Entity Framework is an object-relational mapper that enables .NET developers to work with relational data using domain-specific objects.</span></span> <span data-ttu-id="7f3b6-540">开发人员无需再像往常一样编写大部分数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-540">It eliminates the need for most of the data-access code that developers usually need to write.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-541">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-541">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-542">在通过名为[连接复原/重试逻辑](http://msdn.microsoft.com/data/dn456835.aspx)的机制访问使用 Entity Framework 6.0 及更高版本的 SQL 数据库时，提供重试支持。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-542">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher through a mechanism called [Connection Resiliency / Retry Logic](http://msdn.microsoft.com/data/dn456835.aspx).</span></span> <span data-ttu-id="7f3b6-543">重试机制的主要功能包括：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-543">The main features of the retry mechanism are:</span></span>

* <span data-ttu-id="7f3b6-544">主要抽象是 **IDbExecutionStrategy** 接口。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-544">The primary abstraction is the **IDbExecutionStrategy** interface.</span></span> <span data-ttu-id="7f3b6-545">此接口：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-545">This interface:</span></span>
  * <span data-ttu-id="7f3b6-546">定义同步和异步 **Execute**\* 方法。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-546">Defines synchronous and asynchronous **Execute**\* methods.</span></span>
  * <span data-ttu-id="7f3b6-547">定义可直接使用或可在数据库上下文中配置为默认策略的类（映射到提供程序名称或提供程序名称和服务器名称）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-547">Defines classes that can be used directly or can be configured on a database context as a default strategy, mapped to provider name, or mapped to a provider name and server name.</span></span> <span data-ttu-id="7f3b6-548">如果已在上下文中进行配置，则重试会在各个数据库操作级别发生，其中一些可能适用于给定的上下文操作。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-548">When configured on a context, retries occur at the level of individual database operations, of which there might be several for a given context operation.</span></span>
  * <span data-ttu-id="7f3b6-549">定义何时以及如何重试失败的连接。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-549">Defines when to retry a failed connection, and how.</span></span>
* <span data-ttu-id="7f3b6-550">它包括 **IDbExecutionStrategy** 接口的多个内置实现：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-550">It includes several built-in implementations of the **IDbExecutionStrategy** interface:</span></span>
  * <span data-ttu-id="7f3b6-551">默认 - 不重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-551">Default - no retrying.</span></span>
  * <span data-ttu-id="7f3b6-552">SQL 数据库默认 - 不重试（自动），但检查异常，并根据建议包装它们以使用 SQL 数据库策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-552">Default for SQL Database (automatic) - no retrying, but inspects exceptions and wraps them with suggestion to use the SQL Database strategy.</span></span>
  * <span data-ttu-id="7f3b6-553">SQL 数据库默认 - 指数（从基类继承），外加 SQL 数据库检测逻辑。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-553">Default for SQL Database - exponential (inherited from base class) plus SQL Database detection logic.</span></span>
* <span data-ttu-id="7f3b6-554">它实现了包含随机化的指数回退策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-554">It implements an exponential back-off strategy that includes randomization.</span></span>
* <span data-ttu-id="7f3b6-555">内置重试类有状态，但非线程安全。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-555">The built-in retry classes are stateful and are not thread safe.</span></span> <span data-ttu-id="7f3b6-556">不过，可以在当前操作完成后重用它们。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-556">However, they can be reused after the current operation is completed.</span></span>
* <span data-ttu-id="7f3b6-557">如果超出指定的重试计数，则结果会在新的异常中进行包装。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-557">If the specified retry count is exceeded, the results are wrapped in a new exception.</span></span> <span data-ttu-id="7f3b6-558">它不会加剧当前异常。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-558">It does not bubble up the current exception.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="7f3b6-559">策略配置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-559">Policy configuration</span></span>
<span data-ttu-id="7f3b6-560">在访问使用 Entity Framework 6.0 及更高版本的 SQL 数据库时，提供重试支持。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-560">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher.</span></span> <span data-ttu-id="7f3b6-561">重试策略是以编程方式进行配置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-561">Retry policies are configured programmatically.</span></span> <span data-ttu-id="7f3b6-562">不能逐个操作更改配置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-562">The configuration cannot be changed on a per-operation basis.</span></span>

<span data-ttu-id="7f3b6-563">在上下文中将策略配置为默认值时，需要指定一个用于根据需要新建策略的函数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-563">When configuring a strategy on the context as the default, you specify a function that creates a new strategy on demand.</span></span> <span data-ttu-id="7f3b6-564">以下代码展示了如何创建重试配置类来扩展 **DbConfiguration** 基类。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-564">The following code shows how you can create a retry configuration class that extends the **DbConfiguration** base class.</span></span>

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

<span data-ttu-id="7f3b6-565">然后，可以将此指定为应用程序启动时，所有使用 **DbConfiguration** 实例的 **SetConfiguration** 方法的操作的默认重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-565">You can then specify this as the default retry strategy for all operations using the **SetConfiguration** method of the **DbConfiguration** instance when the application starts.</span></span> <span data-ttu-id="7f3b6-566">默认情况下，EF 会自动发现和使用配置类。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-566">By default, EF will automatically discover and use the configuration class.</span></span>

```csharp
DbConfiguration.SetConfiguration(new BloggingContextConfiguration());
```

<span data-ttu-id="7f3b6-567">可以使用 **DbConfigurationType** 属性为上下文类添加批注，从而为上下文指定重试配置类。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-567">You can specify the retry configuration class for a context by annotating the context class with a **DbConfigurationType** attribute.</span></span> <span data-ttu-id="7f3b6-568">不过，如果只有一个配置类，则 EF 会使用它，而无需为上下文添加批注。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-568">However, if you have only one configuration class, EF will use it without the need to annotate the context.</span></span>

```csharp
[DbConfigurationType(typeof(BloggingContextConfiguration))]
public class BloggingContext : DbContext
```

<span data-ttu-id="7f3b6-569">如果需要对特定操作使用不同的重试策略，或对特定操作禁用重试，则可以创建配置类，以便能够在 **CallContext** 中设置标志，从而挂起或交换策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-569">If you need to use different retry strategies for specific operations, or disable retries for specific operations, you can create a configuration class that allows you to suspend or swap strategies by setting a flag in the **CallContext**.</span></span> <span data-ttu-id="7f3b6-570">配置类可以使用此标志切换策略，或禁用提供的策略并使用默认策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-570">The configuration class can use this flag to switch strategies, or disable the strategy you provide and use a default strategy.</span></span> <span data-ttu-id="7f3b6-571">有关详细信息，请参阅“重试执行策略限制（EF6 及更高版本）”页面中的[挂起执行策略](http://msdn.microsoft.com/dn307226#transactions_workarounds)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-571">For more information, see [Suspend Execution Strategy](http://msdn.microsoft.com/dn307226#transactions_workarounds) in the page Limitations with Retrying Execution Strategies (EF6 onwards).</span></span>

<span data-ttu-id="7f3b6-572">对各个操作使用特定重试策略的另一种方法是，创建相应策略类的实例，并通过参数提供所需的设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-572">Another technique for using specific retry strategies for individual operations is to create an instance of the required strategy class and supply the desired settings through parameters.</span></span> <span data-ttu-id="7f3b6-573">然后，需要调用它的 **ExecuteAsync** 方法。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-573">You then invoke its **ExecuteAsync** method.</span></span>

```csharp
var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
var blogs = await executionStrategy.ExecuteAsync(
    async () =>
    {
        using (var db = new BloggingContext("Blogs"))
        {
            // Acquire some values asynchronously and return them
        }
    },
    new CancellationToken()
);
```

<span data-ttu-id="7f3b6-574">使用 **DbConfiguration** 类的最简单方法是在与 **DbContext** 类相同的程序集中找到它。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-574">The simplest way to use a **DbConfiguration** class is to locate it in the same assembly as the **DbContext** class.</span></span> <span data-ttu-id="7f3b6-575">不过，如果需要在不同的方案（如使用不同的交互式重试策略和后台重试策略）中使用相同的上下文，则这样做并不适合。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-575">However, this is not appropriate when the same context is required in different scenarios, such as different interactive and background retry strategies.</span></span> <span data-ttu-id="7f3b6-576">如果不同的上下文在各个应用域中执行，则可以通过内置的支持在配置文件中指定配置类，也可以使用代码对它进行明确设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-576">If the different contexts execute in separate AppDomains, you can use the built-in support for specifying configuration classes in the configuration file or set it explicitly using code.</span></span> <span data-ttu-id="7f3b6-577">如果不同的上下文必须在同一应用域中执行，则需要使用自定义解决方案。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-577">If the different contexts must execute in the same AppDomain, a custom solution will be required.</span></span>

<span data-ttu-id="7f3b6-578">有关详细信息，请参阅[基于代码的配置（EF6 及更高版本）](http://msdn.microsoft.com/data/jj680699.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-578">For more information, see [Code-Based Configuration (EF6 onwards)](http://msdn.microsoft.com/data/jj680699.aspx).</span></span>

<span data-ttu-id="7f3b6-579">下表显示了使用 EF6 时的内置重试策略的默认设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-579">The following table shows the default settings for the built-in retry policy when using EF6.</span></span>

| <span data-ttu-id="7f3b6-580">设置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-580">Setting</span></span> | <span data-ttu-id="7f3b6-581">默认值</span><span class="sxs-lookup"><span data-stu-id="7f3b6-581">Default value</span></span> | <span data-ttu-id="7f3b6-582">含义</span><span class="sxs-lookup"><span data-stu-id="7f3b6-582">Meaning</span></span> |
|---------|---------------|---------|
| <span data-ttu-id="7f3b6-583">策略</span><span class="sxs-lookup"><span data-stu-id="7f3b6-583">Policy</span></span> | <span data-ttu-id="7f3b6-584">指数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-584">Exponential</span></span> | <span data-ttu-id="7f3b6-585">指数退让。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-585">Exponential back-off.</span></span> |
| <span data-ttu-id="7f3b6-586">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="7f3b6-586">MaxRetryCount</span></span> | <span data-ttu-id="7f3b6-587">5</span><span class="sxs-lookup"><span data-stu-id="7f3b6-587">5</span></span> | <span data-ttu-id="7f3b6-588">最大重试次数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-588">The maximum number of retries.</span></span> |
| <span data-ttu-id="7f3b6-589">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="7f3b6-589">MaxDelay</span></span> | <span data-ttu-id="7f3b6-590">30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-590">30 seconds</span></span> | <span data-ttu-id="7f3b6-591">重试之间的最大延迟。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-591">The maximum delay between retries.</span></span> <span data-ttu-id="7f3b6-592">此值不影响延迟序列的计算方式。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-592">This value does not affect how the series of delays are computed.</span></span> <span data-ttu-id="7f3b6-593">它只定义上限。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-593">It only defines an upper bound.</span></span> |
| <span data-ttu-id="7f3b6-594">DefaultCoefficient</span><span class="sxs-lookup"><span data-stu-id="7f3b6-594">DefaultCoefficient</span></span> | <span data-ttu-id="7f3b6-595">1 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-595">1 second</span></span> | <span data-ttu-id="7f3b6-596">指数退避计算的系数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-596">The coefficient for the exponential back-off computation.</span></span> <span data-ttu-id="7f3b6-597">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-597">This value cannot be changed.</span></span> |
| <span data-ttu-id="7f3b6-598">DefaultRandomFactor</span><span class="sxs-lookup"><span data-stu-id="7f3b6-598">DefaultRandomFactor</span></span> | <span data-ttu-id="7f3b6-599">1.1</span><span class="sxs-lookup"><span data-stu-id="7f3b6-599">1.1</span></span> | <span data-ttu-id="7f3b6-600">用于添加每个条目的随机延迟的乘数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-600">The multiplier used to add a random delay for each entry.</span></span> <span data-ttu-id="7f3b6-601">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-601">This value cannot be changed.</span></span> |
| <span data-ttu-id="7f3b6-602">DefaultExponentialBase</span><span class="sxs-lookup"><span data-stu-id="7f3b6-602">DefaultExponentialBase</span></span> | <span data-ttu-id="7f3b6-603">2</span><span class="sxs-lookup"><span data-stu-id="7f3b6-603">2</span></span> | <span data-ttu-id="7f3b6-604">用于计算下一次延迟的乘数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-604">The multiplier used to calculate the next delay.</span></span> <span data-ttu-id="7f3b6-605">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-605">This value cannot be changed.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="7f3b6-606">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="7f3b6-606">Retry usage guidance</span></span>
<span data-ttu-id="7f3b6-607">访问使用 EF6 的 SQL 数据库时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-607">Consider the following guidelines when accessing SQL Database using EF6:</span></span>

* <span data-ttu-id="7f3b6-608">选择适当的服务选项（共享或高级）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-608">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="7f3b6-609">鉴于共享服务器的其他租户的使用情况，共享实例的连接延迟和限制可能比平常要长。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-609">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="7f3b6-610">如果需要可预测的性能和可靠的低延迟操作，请考虑选择高级选项。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-610">If predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="7f3b6-611">不建议结合使用固定间隔策略和 Azure SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-611">A fixed interval strategy is not recommended for use with Azure SQL Database.</span></span> <span data-ttu-id="7f3b6-612">请改用指数回退策略，因为服务可能已过载，更长的延迟则可以让服务有更长时间进行恢复。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-612">Instead, use an exponential back-off strategy because the service may be overloaded, and longer delays allow more time for it to recover.</span></span>
* <span data-ttu-id="7f3b6-613">定义连接时，为连接和命令超时选择合适的值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-613">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="7f3b6-614">根据业务逻辑设计和全面测试来选择超时值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-614">Base the timeout on both your business logic design and through testing.</span></span> <span data-ttu-id="7f3b6-615">可能需要随着数据量或业务流程的变化，陆续修改此值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-615">You may need to modify this value over time as the volumes of data or the business processes change.</span></span> <span data-ttu-id="7f3b6-616">当数据库忙碌时，超时太短可能会导致连接过早失败。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-616">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="7f3b6-617">超时太长则会导致在检测到失败的连接前等待太久，进而可能会妨碍重试逻辑正常运行。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-617">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="7f3b6-618">超时值是端到端延迟的组成部分，尽管你无法轻松确定在保存上下文时会执行多少命令。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-618">The value of the timeout is a component of the end-to-end latency, although you cannot easily determine how many commands will execute when saving the context.</span></span> <span data-ttu-id="7f3b6-619">可以设置 **DbContext** 实例的 **CommandTimeout** 属性，以此来更改默认超时值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-619">You can change the default timeout by setting the **CommandTimeout** property of the **DbContext** instance.</span></span>
* <span data-ttu-id="7f3b6-620">Entity Framework 支持在配置文件中定义的重试配置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-620">Entity Framework supports retry configurations defined in configuration files.</span></span> <span data-ttu-id="7f3b6-621">不过，为了实现 Azure 上的最大灵活性，应考虑在应用程序内以编程方式创建配置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-621">However, for maximum flexibility on Azure you should consider creating the configuration programmatically within the application.</span></span> <span data-ttu-id="7f3b6-622">重试策略的特定参数（如重试次数和重试间隔）可以存储在服务配置文件中，并在运行时用于创建相应的策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-622">The specific parameters for the retry policies, such as the number of retries and the retry intervals, can be stored in the service configuration file and used at runtime to create the appropriate policies.</span></span> <span data-ttu-id="7f3b6-623">这样一来，无需重启应用程序，即可更改设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-623">This allows the settings to be changed without requiring the application to be restarted.</span></span>

<span data-ttu-id="7f3b6-624">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-624">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="7f3b6-625">无法指定重试尝试之间的延迟（固定值且以指数序列生成）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-625">You cannot specify the delay between retry attempts (it is fixed and generated as an exponential sequence).</span></span> <span data-ttu-id="7f3b6-626">只能指定最大值（如下所示）；除非创建自定义重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-626">You can specify only the maximum values, as shown here; unless you create a custom retry strategy.</span></span> <span data-ttu-id="7f3b6-627">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-627">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="7f3b6-628">**上下文**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-628">**Context**</span></span> | <span data-ttu-id="7f3b6-629">**示例目标 E2E<br />最长延迟**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-629">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="7f3b6-630">**重试策略**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-630">**Retry policy**</span></span> | <span data-ttu-id="7f3b6-631">**设置**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-631">**Settings**</span></span> | <span data-ttu-id="7f3b6-632">**值**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-632">**Values**</span></span> | <span data-ttu-id="7f3b6-633">**工作原理**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-633">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="7f3b6-634">交互式, UI,</span><span class="sxs-lookup"><span data-stu-id="7f3b6-634">Interactive, UI,</span></span><br /><span data-ttu-id="7f3b6-635">或 foreground</span><span class="sxs-lookup"><span data-stu-id="7f3b6-635">or foreground</span></span> |<span data-ttu-id="7f3b6-636">2 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-636">2 seconds</span></span> |<span data-ttu-id="7f3b6-637">指数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-637">Exponential</span></span> |<span data-ttu-id="7f3b6-638">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="7f3b6-638">MaxRetryCount</span></span><br /><span data-ttu-id="7f3b6-639">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="7f3b6-639">MaxDelay</span></span> |<span data-ttu-id="7f3b6-640">3</span><span class="sxs-lookup"><span data-stu-id="7f3b6-640">3</span></span><br /><span data-ttu-id="7f3b6-641">750 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-641">750 ms</span></span> |<span data-ttu-id="7f3b6-642">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-642">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="7f3b6-643">第 2 次尝试 - 延迟 750 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-643">Attempt 2 - delay 750 ms</span></span><br /><span data-ttu-id="7f3b6-644">第 3 次尝试 - 延迟 750 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-644">Attempt 3 – delay 750 ms</span></span> |
| <span data-ttu-id="7f3b6-645">背景</span><span class="sxs-lookup"><span data-stu-id="7f3b6-645">Background</span></span><br /> <span data-ttu-id="7f3b6-646">或批处理</span><span class="sxs-lookup"><span data-stu-id="7f3b6-646">or batch</span></span> |<span data-ttu-id="7f3b6-647">30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-647">30 seconds</span></span> |<span data-ttu-id="7f3b6-648">指数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-648">Exponential</span></span> |<span data-ttu-id="7f3b6-649">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="7f3b6-649">MaxRetryCount</span></span><br /><span data-ttu-id="7f3b6-650">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="7f3b6-650">MaxDelay</span></span> |<span data-ttu-id="7f3b6-651">5</span><span class="sxs-lookup"><span data-stu-id="7f3b6-651">5</span></span><br /><span data-ttu-id="7f3b6-652">12 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-652">12 seconds</span></span> |<span data-ttu-id="7f3b6-653">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-653">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="7f3b6-654">第 2 次尝试 - 约延迟 1 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-654">Attempt 2 - delay ~1 sec</span></span><br /><span data-ttu-id="7f3b6-655">第 3 次尝试 - 约延迟 3 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-655">Attempt 3 - delay ~3 sec</span></span><br /><span data-ttu-id="7f3b6-656">第 4 次尝试 - 约延迟 7 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-656">Attempt 4 - delay ~7 sec</span></span><br /><span data-ttu-id="7f3b6-657">第 5 次尝试 - 约延迟 12 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-657">Attempt 5 - delay 12 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="7f3b6-658">端到端延迟目标假设采用服务连接的默认超时。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-658">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="7f3b6-659">如果指定更长的连接超时，则每次重试尝试都会将端到端延迟延长这一附加时间。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-659">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="7f3b6-660">示例</span><span class="sxs-lookup"><span data-stu-id="7f3b6-660">Examples</span></span>
<span data-ttu-id="7f3b6-661">以下代码示例定义了使用 Entity Framework 的简单数据访问解决方案。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-661">The following code example defines a simple data access solution that uses Entity Framework.</span></span> <span data-ttu-id="7f3b6-662">它通过定义可扩展 **DbConfiguration** 的 **BlogConfiguration** 类的实例来指定重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-662">It sets a specific retry strategy by defining an instance of a class named **BlogConfiguration** that extends **DbConfiguration**.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
    public class BlogConfiguration : DbConfiguration
    {
        public BlogConfiguration()
        {
            // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
            // These values could be loaded from configuration rather than being hard-coded.
            this.SetExecutionStrategy(
                    "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
        }
    }

    // Specify the configuration type if more than one has been defined.
    // [DbConfigurationType(typeof(BlogConfiguration))]
    public class BloggingContext : DbContext
    {
        // Definition of content goes here.
    }

    class EF6CodeSamples
    {
        public async static Task Samples()
        {
            // Execution strategy configured by DbConfiguration subclass, discovered automatically or
            // or explicitly indicated through configuration or with an attribute. Default is no retries.
            using (var db = new BloggingContext("Blogs"))
            {
                // Add, edit, delete blog items here, then:
                await db.SaveChangesAsync();
            }
        }
    }
}
```

<span data-ttu-id="7f3b6-663">有关使用 Entity Framework 重试机制的更多示例，请参阅[连接复原/重试逻辑](http://msdn.microsoft.com/data/dn456835.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-663">More examples of using the Entity Framework retry mechanism can be found in [Connection Resiliency / Retry Logic](http://msdn.microsoft.com/data/dn456835.aspx).</span></span>

### <a name="more-information"></a><span data-ttu-id="7f3b6-664">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f3b6-664">More information</span></span>
* [<span data-ttu-id="7f3b6-665">Azure SQL 数据库性能和弹性指南</span><span class="sxs-lookup"><span data-stu-id="7f3b6-665">Azure SQL Database Performance and Elasticity Guide</span></span>](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## <a name="sql-database-using-entity-framework-core"></a><span data-ttu-id="7f3b6-666">使用 Entity Framework Core 的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="7f3b6-666">SQL Database using Entity Framework Core</span></span>
<span data-ttu-id="7f3b6-667">[Entity Framework Core](/ef/core/) 是一种对象关系映射程序，可方便 .NET Core 开发人员使用域特定对象处理数据。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-667">[Entity Framework Core](/ef/core/) is an object-relational mapper that enables .NET Core developers to work with data using domain-specific objects.</span></span> <span data-ttu-id="7f3b6-668">开发人员无需再像往常一样编写大部分数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-668">It eliminates the need for most of the data-access code that developers usually need to write.</span></span> <span data-ttu-id="7f3b6-669">此版 Entity Framework 从头开始编写，不自动从 EF6.x 继承所有功能。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-669">This version of Entity Framework was written from the ground up, and doesn't automatically inherit all the features from EF6.x.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-670">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-670">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-671">在通过名为[连接复原能力](/ef/core/miscellaneous/connection-resiliency)的机制访问使用 Entity Framework Core 的 SQL 数据库时，提供重试支持。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-671">Retry support is provided when accessing SQL Database using Entity Framework Core through a mechanism called [Connection Resiliency](/ef/core/miscellaneous/connection-resiliency).</span></span> <span data-ttu-id="7f3b6-672">连接复原能力在 EF Core 1.1.0 中引入。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-672">Connection resiliency was introduced in EF Core 1.1.0.</span></span>

<span data-ttu-id="7f3b6-673">主抽象是 `IExecutionStrategy` 接口。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-673">The primary abstraction is the `IExecutionStrategy` interface.</span></span> <span data-ttu-id="7f3b6-674">SQL Server（包括 SQL Azure）的执行策略可识别能够重试的异常类型，并为最大重试次数、两次重试间的延迟等提供合理的默认值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-674">The execution strategy for SQL Server, including SQL Azure, is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, and so on.</span></span>

### <a name="examples"></a><span data-ttu-id="7f3b6-675">示例</span><span class="sxs-lookup"><span data-stu-id="7f3b6-675">Examples</span></span>

<span data-ttu-id="7f3b6-676">以下代码在配置 DbContext 对象（表示与数据库的会话）时实现自动重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-676">The following code enables automatic retries when configuring the DbContext object, which represents a session with the database.</span></span> 

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(
            @"Server=(localdb)\mssqllocaldb;Database=EFMiscellanous.ConnectionResiliency;Trusted_Connection=True;",
            options => options.EnableRetryOnFailure());
}
```

<span data-ttu-id="7f3b6-677">以下代码展示如何使用执行策略在自动重试机制下执行事务。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-677">The following code shows how to execute a transaction with automatic retries, by using an execution strategy.</span></span> <span data-ttu-id="7f3b6-678">该事务在委托中定义。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-678">The transaction is defined in a delegate.</span></span> <span data-ttu-id="7f3b6-679">如果出现暂时性失败，执行策略将再次调用委托。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-679">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

```csharp
using (var db = new BloggingContext())
{
    var strategy = db.Database.CreateExecutionStrategy();

    strategy.Execute(() =>
    {
        using (var transaction = db.Database.BeginTransaction())
        {
            db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
            db.SaveChanges();

            db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/visualstudio" });
            db.SaveChanges();

            transaction.Commit();
        }
    });
}
```

## <a name="azure-storage"></a><span data-ttu-id="7f3b6-680">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="7f3b6-680">Azure Storage</span></span>
<span data-ttu-id="7f3b6-681">Azure 存储服务包括表和 blob 存储、文件以及存储队列。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-681">Azure storage services include table and blob storage, files, and storage queues.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="7f3b6-682">重试机制</span><span class="sxs-lookup"><span data-stu-id="7f3b6-682">Retry mechanism</span></span>
<span data-ttu-id="7f3b6-683">重试在单独的 REST 操作一级发生，是客户端 API 实现的主要组成部分。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-683">Retries occur at the individual REST operation level and are an integral part of the client API implementation.</span></span> <span data-ttu-id="7f3b6-684">客户端存储 SDK 使用可实现 [IExtendedRetryPolicy Interface](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx)（IExtendedRetryPolicy 接口）的类。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-684">The client storage SDK uses classes that implement the [IExtendedRetryPolicy Interface](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).</span></span>

<span data-ttu-id="7f3b6-685">接口的实现各不相同。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-685">There are different implementations of the interface.</span></span> <span data-ttu-id="7f3b6-686">存储客户端可以从专门用于访问表、blob 和队列的策略中进行选择。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-686">Storage clients can choose from policies specifically designed for accessing tables, blobs, and queues.</span></span> <span data-ttu-id="7f3b6-687">每个实现使用不同的重试策略，此策略基本上定义了重试间隔和其他详细信息。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-687">Each implementation uses a different retry strategy that essentially defines the retry interval and other details.</span></span>

<span data-ttu-id="7f3b6-688">内置类支持线性（固定延迟）和指数随机化重试间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-688">The built-in classes provide support for linear (constant delay) and exponential with randomization retry intervals.</span></span> <span data-ttu-id="7f3b6-689">当其他进程在较高级别处理重试时，还有不重试策略可供使用。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-689">There is also a no retry policy for use when another process is handling retries at a higher level.</span></span> <span data-ttu-id="7f3b6-690">不过，如果具有内置类未规定的特定要求，则可以实现自己的重试类。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-690">However, you can implement your own retry classes if you have specific requirements not provided by the built-in classes.</span></span>

<span data-ttu-id="7f3b6-691">如果使用的是读取访问异地冗余存储 (RA-GRS)，且请求的结果是可重试错误，则备用重试会在主要和辅助存储服务位置之间切换。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-691">Alternate retries switch between primary and secondary storage service location if you are using read access geo-redundant storage (RA-GRS) and the result of the request is a retryable error.</span></span> <span data-ttu-id="7f3b6-692">有关详细信息，请参阅 [Azure 存储冗余选项](http://msdn.microsoft.com/library/azure/dn727290.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-692">See [Azure Storage Redundancy Options](http://msdn.microsoft.com/library/azure/dn727290.aspx) for more information.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="7f3b6-693">策略配置</span><span class="sxs-lookup"><span data-stu-id="7f3b6-693">Policy configuration</span></span>
<span data-ttu-id="7f3b6-694">重试策略是以编程方式进行配置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-694">Retry policies are configured programmatically.</span></span> <span data-ttu-id="7f3b6-695">典型的过程是创建并填充 **TableRequestOptions**、**BlobRequestOptions**、**FileRequestOptions** 或 **QueueRequestOptions** 实例。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-695">A typical procedure is to create and populate a **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions**, or **QueueRequestOptions** instance.</span></span>

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. 
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

<span data-ttu-id="7f3b6-696">然后，可以在客户端上设置请求选项实例，客户端的所有操作都会使用指定的请求选项。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-696">The request options instance can then be set on the client, and all operations with the client will use the specified request options.</span></span>

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

<span data-ttu-id="7f3b6-697">可以将填充的请求选项类实例作为参数传递到操作方法，以此来替代客户端请求选项。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-697">You can override the client request options by passing a populated instance of the request options class as a parameter to operation methods.</span></span>

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

<span data-ttu-id="7f3b6-698">使用 **OperationContext** 实例可以指定在发生重试时以及在操作完成时要执行的代码。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-698">You use an **OperationContext** instance to specify the code to execute when a retry occurs and when an operation has completed.</span></span> <span data-ttu-id="7f3b6-699">此代码可以收集供日志和遥测使用的操作的相关信息。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-699">This code can collect information about the operation for use in logs and telemetry.</span></span>

```csharp
// Set up notifications for an operation
var context = new OperationContext();
context.ClientRequestID = "some request id";
context.Retrying += (sender, args) =>
{
    /* Collect retry information */
};
context.RequestCompleted += (sender, args) =>
{
    /* Collect operation completion information */
};
var stats = await client.GetServiceStatsAsync(null, context);
```

<span data-ttu-id="7f3b6-700">除了指明故障是否适合重试，扩展的重试策略还会返回 **RetryContext** 对象，用于指明重试次数、上次请求结果、下次重试是在主要位置还是在辅助位置上发生（有关详细信息，请参阅下表）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-700">In addition to indicating whether a failure is suitable for retry, the extended retry policies return a **RetryContext** object that indicates the number of retries, the results of the last request, whether the next retry will happen in the primary or secondary location (see table below for details).</span></span> <span data-ttu-id="7f3b6-701">**RetryContext** 对象的属性可用于确定是否以及何时尝试进行重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-701">The properties of the **RetryContext** object can be used to decide if and when to attempt a retry.</span></span> <span data-ttu-id="7f3b6-702">如需了解更多详情，请参阅 [IExtendedRetryPolicy.Evaluate 方法](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-702">For more details, see [IExtendedRetryPolicy.Evaluate Method](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).</span></span>

<span data-ttu-id="7f3b6-703">下表显示了内置重试策略的默认设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-703">The following tables show the default settings for the built-in retry policies.</span></span>

<span data-ttu-id="7f3b6-704">**请求选项**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-704">**Request options**</span></span>

| <span data-ttu-id="7f3b6-705">**设置**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-705">**Setting**</span></span> | <span data-ttu-id="7f3b6-706">**默认值**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-706">**Default value**</span></span> | <span data-ttu-id="7f3b6-707">**含义**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-707">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="7f3b6-708">MaximumExecutionTime</span><span class="sxs-lookup"><span data-stu-id="7f3b6-708">MaximumExecutionTime</span></span> | <span data-ttu-id="7f3b6-709">无</span><span class="sxs-lookup"><span data-stu-id="7f3b6-709">None</span></span> | <span data-ttu-id="7f3b6-710">请求的最长执行时间，包括所有可能的重试尝试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-710">Maximum execution time for the request, including all potential retry attempts.</span></span> <span data-ttu-id="7f3b6-711">如果未指定，则允许请求花费的时间没有限制。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-711">If it is not specified, then the amount of time that a request is permitted to take is unlimited.</span></span> <span data-ttu-id="7f3b6-712">换而言之，请求可能会挂起。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-712">In other words, the request might hang.</span></span> |
| <span data-ttu-id="7f3b6-713">ServerTimeOut</span><span class="sxs-lookup"><span data-stu-id="7f3b6-713">ServerTimeout</span></span> | <span data-ttu-id="7f3b6-714">无</span><span class="sxs-lookup"><span data-stu-id="7f3b6-714">None</span></span> | <span data-ttu-id="7f3b6-715">请求的服务器超时间隔（值四舍五入到秒）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-715">Server timeout interval for the request (value is rounded to seconds).</span></span> <span data-ttu-id="7f3b6-716">如果未指定，则会使用所有服务器请求的默认值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-716">If not specified, it will use the default value for all requests to the server.</span></span> <span data-ttu-id="7f3b6-717">通常情况下，最好忽略此设置，以便使用服务器默认值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-717">Usually, the best option is to omit this setting so that the server default is used.</span></span> | 
| <span data-ttu-id="7f3b6-718">LocationMode</span><span class="sxs-lookup"><span data-stu-id="7f3b6-718">LocationMode</span></span> | <span data-ttu-id="7f3b6-719">无</span><span class="sxs-lookup"><span data-stu-id="7f3b6-719">None</span></span> | <span data-ttu-id="7f3b6-720">如果创建的存储帐户设置了读取访问异地冗余存储 (RA-GRS) 复制选项，则可以使用位置模式来指明哪个位置应接收请求。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-720">If the storage account is created with the Read access geo-redundant storage (RA-GRS) replication option, you can use the location mode to indicate which location should receive the request.</span></span> <span data-ttu-id="7f3b6-721">例如，如果指定的是 **PrimaryThenSecondary**，则请求总是会先发送到主要位置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-721">For example, if **PrimaryThenSecondary** is specified, requests are always sent to the primary location first.</span></span> <span data-ttu-id="7f3b6-722">如果某个请求失败，它将发送到辅助位置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-722">If a request fails, it is sent to the secondary location.</span></span> |
| <span data-ttu-id="7f3b6-723">RetryPolicy</span><span class="sxs-lookup"><span data-stu-id="7f3b6-723">RetryPolicy</span></span> | <span data-ttu-id="7f3b6-724">ExponentialPolicy</span><span class="sxs-lookup"><span data-stu-id="7f3b6-724">ExponentialPolicy</span></span> | <span data-ttu-id="7f3b6-725">有关每个选项的详细信息，请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-725">See below for details of each option.</span></span> |

<span data-ttu-id="7f3b6-726">**指数策略**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-726">**Exponential policy**</span></span> 

| <span data-ttu-id="7f3b6-727">**设置**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-727">**Setting**</span></span> | <span data-ttu-id="7f3b6-728">**默认值**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-728">**Default value**</span></span> | <span data-ttu-id="7f3b6-729">**含义**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-729">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="7f3b6-730">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="7f3b6-730">maxAttempt</span></span> | <span data-ttu-id="7f3b6-731">3</span><span class="sxs-lookup"><span data-stu-id="7f3b6-731">3</span></span> | <span data-ttu-id="7f3b6-732">重试的次数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-732">Number of retry attempts.</span></span> |
| <span data-ttu-id="7f3b6-733">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-733">deltaBackoff</span></span> | <span data-ttu-id="7f3b6-734">4 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-734">4 seconds</span></span> | <span data-ttu-id="7f3b6-735">不同重试之间的回退时间间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-735">Back-off interval between retries.</span></span> <span data-ttu-id="7f3b6-736">将针对后续的重试使用多个这样的时间跨度（包括随机元素）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-736">Multiples of this timespan, including a random element, will be used for subsequent retry attempts.</span></span> |
| <span data-ttu-id="7f3b6-737">MinBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-737">MinBackoff</span></span> | <span data-ttu-id="7f3b6-738">3 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-738">3 seconds</span></span> | <span data-ttu-id="7f3b6-739">添加到从 deltaBackoff 计算的所有重试时间间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-739">Added to all retry intervals computed from deltaBackoff.</span></span> <span data-ttu-id="7f3b6-740">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-740">This value cannot be changed.</span></span>
| <span data-ttu-id="7f3b6-741">MaxBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-741">MaxBackoff</span></span> | <span data-ttu-id="7f3b6-742">120 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-742">120 seconds</span></span> | <span data-ttu-id="7f3b6-743">如果计得的重试时间间隔大于 MaxBackoff，则使用 MaxBackoff。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-743">MaxBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> <span data-ttu-id="7f3b6-744">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-744">This value cannot be changed.</span></span> |

<span data-ttu-id="7f3b6-745">**线性策略**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-745">**Linear policy**</span></span>

| <span data-ttu-id="7f3b6-746">**设置**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-746">**Setting**</span></span> | <span data-ttu-id="7f3b6-747">**默认值**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-747">**Default value**</span></span> | <span data-ttu-id="7f3b6-748">**含义**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-748">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="7f3b6-749">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="7f3b6-749">maxAttempt</span></span> | <span data-ttu-id="7f3b6-750">3</span><span class="sxs-lookup"><span data-stu-id="7f3b6-750">3</span></span> | <span data-ttu-id="7f3b6-751">重试的次数。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-751">Number of retry attempts.</span></span> |
| <span data-ttu-id="7f3b6-752">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-752">deltaBackoff</span></span> | <span data-ttu-id="7f3b6-753">30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-753">30 seconds</span></span> | <span data-ttu-id="7f3b6-754">不同重试之间的回退时间间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-754">Back-off interval between retries.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="7f3b6-755">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="7f3b6-755">Retry usage guidance</span></span>
<span data-ttu-id="7f3b6-756">在使用存储客户端 API 访问 Azure 存储服务时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-756">Consider the following guidelines when accessing Azure storage services using the storage client API:</span></span>

* <span data-ttu-id="7f3b6-757">使用 Microsoft.WindowsAzure.Storage.RetryPolicies 命名空间中符合要求的内置重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-757">Use the built-in retry policies from the Microsoft.WindowsAzure.Storage.RetryPolicies namespace where they are appropriate for your requirements.</span></span> <span data-ttu-id="7f3b6-758">在大多数情况下，这些策略就够用了。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-758">In most cases, these policies will be sufficient.</span></span>
* <span data-ttu-id="7f3b6-759">对批处理操作、后台任务或非交互式方案使用 **ExponentialRetry** 策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-759">Use the **ExponentialRetry** policy in batch operations, background tasks, or non-interactive scenarios.</span></span> <span data-ttu-id="7f3b6-760">在这种情况下，服务通常可以有更多的恢复时间。结果就是，提高了操作最终成功的可能性。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-760">In these scenarios, you can typically allow more time for the service to recover—with a consequently increased chance of the operation eventually succeeding.</span></span>
* <span data-ttu-id="7f3b6-761">考虑指定 **RequestOptions** 参数的 **MaximumExecutionTime** 属性，以限制总执行时间；但在选择超时值时，请将操作的类型和大小考虑进去。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-761">Consider specifying the **MaximumExecutionTime** property of the **RequestOptions** parameter to limit the total execution time, but take into account the type and size of the operation when choosing a timeout value.</span></span>
* <span data-ttu-id="7f3b6-762">如果需要实现自定义重试，请避免创建存储客户端类的包装器。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-762">If you need to implement a custom retry, avoid creating wrappers around the storage client classes.</span></span> <span data-ttu-id="7f3b6-763">应使用这些功能通过 **IExtendedRetryPolicy** 接口扩展现有策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-763">Instead, use the capabilities to extend the existing policies through the **IExtendedRetryPolicy** interface.</span></span>
* <span data-ttu-id="7f3b6-764">如果使用的是读取访问异地冗余存储 (RA-GRS)，则可以使用 **LocationMode** 指定在主要访问失败时重试尝试会访问存储空间的只读辅助副本。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-764">If you are using read access geo-redundant storage (RA-GRS) you can use the **LocationMode** to specify that retry attempts will access the secondary read-only copy of the store should the primary access fail.</span></span> <span data-ttu-id="7f3b6-765">不过，使用此选项时，必须确保在尚未完成从主要存储空间进行复制的情况下，应用程序可以成功使用可能已过时的数据。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-765">However, when using this option you must ensure that your application can work successfully with data that may be stale if the replication from the primary store has not yet completed.</span></span>

<span data-ttu-id="7f3b6-766">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-766">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="7f3b6-767">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-767">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>  

| <span data-ttu-id="7f3b6-768">**上下文**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-768">**Context**</span></span> | <span data-ttu-id="7f3b6-769">**示例目标 E2E<br />最长延迟**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-769">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="7f3b6-770">**重试策略**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-770">**Retry policy**</span></span> | <span data-ttu-id="7f3b6-771">**设置**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-771">**Settings**</span></span> | <span data-ttu-id="7f3b6-772">**值**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-772">**Values**</span></span> | <span data-ttu-id="7f3b6-773">**工作原理**</span><span class="sxs-lookup"><span data-stu-id="7f3b6-773">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="7f3b6-774">交互式, UI,</span><span class="sxs-lookup"><span data-stu-id="7f3b6-774">Interactive, UI,</span></span><br /><span data-ttu-id="7f3b6-775">或 foreground</span><span class="sxs-lookup"><span data-stu-id="7f3b6-775">or foreground</span></span> |<span data-ttu-id="7f3b6-776">2 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-776">2 seconds</span></span> |<span data-ttu-id="7f3b6-777">线性</span><span class="sxs-lookup"><span data-stu-id="7f3b6-777">Linear</span></span> |<span data-ttu-id="7f3b6-778">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="7f3b6-778">maxAttempt</span></span><br /><span data-ttu-id="7f3b6-779">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-779">deltaBackoff</span></span> |<span data-ttu-id="7f3b6-780">3</span><span class="sxs-lookup"><span data-stu-id="7f3b6-780">3</span></span><br /><span data-ttu-id="7f3b6-781">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-781">500 ms</span></span> |<span data-ttu-id="7f3b6-782">第 1 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-782">Attempt 1 - delay 500 ms</span></span><br /><span data-ttu-id="7f3b6-783">第 2 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-783">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="7f3b6-784">第 3 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-784">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="7f3b6-785">背景</span><span class="sxs-lookup"><span data-stu-id="7f3b6-785">Background</span></span><br /><span data-ttu-id="7f3b6-786">或批处理</span><span class="sxs-lookup"><span data-stu-id="7f3b6-786">or batch</span></span> |<span data-ttu-id="7f3b6-787">30 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-787">30 seconds</span></span> |<span data-ttu-id="7f3b6-788">指数</span><span class="sxs-lookup"><span data-stu-id="7f3b6-788">Exponential</span></span> |<span data-ttu-id="7f3b6-789">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="7f3b6-789">maxAttempt</span></span><br /><span data-ttu-id="7f3b6-790">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="7f3b6-790">deltaBackoff</span></span> |<span data-ttu-id="7f3b6-791">5</span><span class="sxs-lookup"><span data-stu-id="7f3b6-791">5</span></span><br /><span data-ttu-id="7f3b6-792">4 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-792">4 seconds</span></span> |<span data-ttu-id="7f3b6-793">第 1 次尝试 - 约延迟 3 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-793">Attempt 1 - delay ~3 sec</span></span><br /><span data-ttu-id="7f3b6-794">第 2 次尝试 - 约延迟 7 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-794">Attempt 2 - delay ~7 sec</span></span><br /><span data-ttu-id="7f3b6-795">第 3 次尝试 - 约延迟 15 秒</span><span class="sxs-lookup"><span data-stu-id="7f3b6-795">Attempt 3 - delay ~15 sec</span></span> |

### <a name="telemetry"></a><span data-ttu-id="7f3b6-796">遥测</span><span class="sxs-lookup"><span data-stu-id="7f3b6-796">Telemetry</span></span>
<span data-ttu-id="7f3b6-797">重试尝试记录到 **TraceSource** 中。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-797">Retry attempts are logged to a **TraceSource**.</span></span> <span data-ttu-id="7f3b6-798">必须配置 **TraceListener**，才能捕获事件并将这些事件写入合适的目标日志。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-798">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span> <span data-ttu-id="7f3b6-799">可以使用 **TextWriterTraceListener** 或 **XmlWriterTraceListener** 将数据写入日志文件、使用 **EventLogTraceListener** 将数据写入 Windows 事件日志，或使用 **EventProviderTraceListener** 将跟踪数据写入 ETW 子系统。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-799">You can use the **TextWriterTraceListener** or **XmlWriterTraceListener** to write the data to a log file, the **EventLogTraceListener** to write to the Windows Event Log, or the **EventProviderTraceListener** to write trace data to the ETW subsystem.</span></span> <span data-ttu-id="7f3b6-800">此外，还可以配置缓冲区的自动刷新和即将记录的事件详细信息（例如，错误、警告、信息和详细内容）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-800">You can also configure auto-flushing of the buffer, and the verbosity of events that will be logged (for example, Error, Warning, Informational, and Verbose).</span></span> <span data-ttu-id="7f3b6-801">有关详细信息，请参阅 [Client-side Logging with the .NET Storage Client Library](http://msdn.microsoft.com/library/azure/dn782839.aspx)（使用 .NET 存储客户端库的客户端日志记录）。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-801">For more information, see [Client-side Logging with the .NET Storage Client Library](http://msdn.microsoft.com/library/azure/dn782839.aspx).</span></span>

<span data-ttu-id="7f3b6-802">操作可以接收 **OperationContext** 实例，这会公开可用于附加自定义遥测逻辑的 **Retrying** 事件。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-802">Operations can receive an **OperationContext** instance, which exposes a **Retrying** event that can be used to attach custom telemetry logic.</span></span> <span data-ttu-id="7f3b6-803">有关详细信息，请参阅 [OperationContext.Retrying 事件](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-803">For more information, see [OperationContext.Retrying Event](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).</span></span>

### <a name="examples"></a><span data-ttu-id="7f3b6-804">示例</span><span class="sxs-lookup"><span data-stu-id="7f3b6-804">Examples</span></span>
<span data-ttu-id="7f3b6-805">以下代码示例展示了如何创建两个具有不同重试设置的 **TableRequestOptions** 实例；一个用于交互式请求，另一个用于后台请求。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-805">The following code example shows how to create two **TableRequestOptions** instances with different retry settings; one for interactive requests and one for background requests.</span></span> <span data-ttu-id="7f3b6-806">然后，此示例在客户端上设置这两个重试策略，以便它们针对所有请求进行应用。此示例还会对特定请求设置交互式策略，以便替代应用于客户端的默认设置。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-806">The example then sets these two retry policies on the client so that they apply for all requests, and also sets the interactive strategy on a specific request so that it overrides the default settings applied to the client.</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case. 
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

### <a name="more-information"></a><span data-ttu-id="7f3b6-807">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f3b6-807">More information</span></span>
* [<span data-ttu-id="7f3b6-808">Azure 存储客户端库重试策略建议</span><span class="sxs-lookup"><span data-stu-id="7f3b6-808">Azure Storage Client Library Retry Policy Recommendations</span></span>](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
* [<span data-ttu-id="7f3b6-809">存储客户端库 2.0 - 实现重试策略</span><span class="sxs-lookup"><span data-stu-id="7f3b6-809">Storage Client Library 2.0 – Implementing Retry Policies</span></span>](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## <a name="general-rest-and-retry-guidelines"></a><span data-ttu-id="7f3b6-810">常规 REST 和重试指南</span><span class="sxs-lookup"><span data-stu-id="7f3b6-810">General REST and retry guidelines</span></span>
<span data-ttu-id="7f3b6-811">访问 Azure 或第三方服务时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-811">Consider the following when accessing Azure or third party services:</span></span>

* <span data-ttu-id="7f3b6-812">使用管理重试的系统化方法（可能作为可重用代码），以便跨所有客户端和解决方案应用一致的方法。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-812">Use a systematic approach to managing retries, perhaps as reusable code, so that you can apply a consistent methodology across all clients and all solutions.</span></span>
* <span data-ttu-id="7f3b6-813">如果目标服务或客户端没有内置的重试机制，请考虑使用重试框架（如临时故障处理应用程序块）来管理重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-813">Consider using a retry framework such as the Transient Fault Handling Application Block to manage retries if the target service or client has no built-in retry mechanism.</span></span> <span data-ttu-id="7f3b6-814">这会帮助你实现一致的重试行为，并可能会为目标服务提供合适的默认重试策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-814">This will help you implement a consistent retry behavior, and it may provide a suitable default retry strategy for the target service.</span></span> <span data-ttu-id="7f3b6-815">不过，可能需要为具有非标准行为的服务创建自定义重试代码，无需根据异常来指明临时故障；或者，可能需要使用 **Retry-Response** 答复来管理重试行为。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-815">However, you may need to create custom retry code for services that have non-standard behavior, that do not rely on exceptions to indicate transient failures, or if you want to use a **Retry-Response** reply to manage retry behavior.</span></span>
* <span data-ttu-id="7f3b6-816">临时检测逻辑视用于调用 REST 调用的实际客户端 API 而定。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-816">The transient detection logic will depend on the actual client API you use to invoke the REST calls.</span></span> <span data-ttu-id="7f3b6-817">某些客户端（如较新的 **HttpClient** 类）不会引发包含失败 HTTP 状态代码的已完成请求的异常。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-817">Some clients, such as the newer **HttpClient** class, will not throw exceptions for completed requests with a non-success HTTP status code.</span></span> <span data-ttu-id="7f3b6-818">这可以提升性能，但会阻止使用临时故障处理应用程序块。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-818">This improves performance but prevents the use of the Transient Fault Handling Application Block.</span></span> <span data-ttu-id="7f3b6-819">在这种情况下，可以使用针对失败 HTTP 状态代码引发异常的代码，包装 REST API 调用，并可以由临时故障处理应用程序块进行处理。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-819">In this case you could wrap the call to the REST API with code that produces exceptions for non-success HTTP status codes, which can then be processed by the block.</span></span> <span data-ttu-id="7f3b6-820">或者，可以使用另一种机制来驱动重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-820">Alternatively, you can use a different mechanism to drive the retries.</span></span>
* <span data-ttu-id="7f3b6-821">从服务返回的 HTTP 状态代码可以帮助指明故障是否是临时故障。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-821">The HTTP status code returned from the service can help to indicate whether the failure is transient.</span></span> <span data-ttu-id="7f3b6-822">可能需要检查客户端生成的异常或重试框架，以访问状态代码或确定对等的异常类型。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-822">You may need to examine the exceptions generated by a client or the retry framework to access the status code or to determine the equivalent exception type.</span></span> <span data-ttu-id="7f3b6-823">以下 HTTP 代码通常可指明重试是否合适：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-823">The following HTTP codes typically indicate that a retry is appropriate:</span></span>
  * <span data-ttu-id="7f3b6-824">408 请求超时</span><span class="sxs-lookup"><span data-stu-id="7f3b6-824">408 Request Timeout</span></span>
  * <span data-ttu-id="7f3b6-825">429 请求过多</span><span class="sxs-lookup"><span data-stu-id="7f3b6-825">429 Too Many Requests</span></span>
  * <span data-ttu-id="7f3b6-826">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="7f3b6-826">500 Internal Server Error</span></span>
  * <span data-ttu-id="7f3b6-827">502 错误的网关</span><span class="sxs-lookup"><span data-stu-id="7f3b6-827">502 Bad Gateway</span></span>
  * <span data-ttu-id="7f3b6-828">503 服务不可用</span><span class="sxs-lookup"><span data-stu-id="7f3b6-828">503 Service Unavailable</span></span>
  * <span data-ttu-id="7f3b6-829">504 网关超时</span><span class="sxs-lookup"><span data-stu-id="7f3b6-829">504 Gateway Timeout</span></span>
* <span data-ttu-id="7f3b6-830">如果根据异常构建重试逻辑，则以下代码通常可指明出现了无法建立连接的临时故障：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-830">If you base your retry logic on exceptions, the following typically indicate a transient failure where no connection could be established:</span></span>
  * <span data-ttu-id="7f3b6-831">WebExceptionStatus.ConnectionClosed</span><span class="sxs-lookup"><span data-stu-id="7f3b6-831">WebExceptionStatus.ConnectionClosed</span></span>
  * <span data-ttu-id="7f3b6-832">WebExceptionStatus.ConnectFailure</span><span class="sxs-lookup"><span data-stu-id="7f3b6-832">WebExceptionStatus.ConnectFailure</span></span>
  * <span data-ttu-id="7f3b6-833">WebExceptionStatus.Timeout</span><span class="sxs-lookup"><span data-stu-id="7f3b6-833">WebExceptionStatus.Timeout</span></span>
  * <span data-ttu-id="7f3b6-834">WebExceptionStatus.RequestCanceled</span><span class="sxs-lookup"><span data-stu-id="7f3b6-834">WebExceptionStatus.RequestCanceled</span></span>
* <span data-ttu-id="7f3b6-835">如果服务处于不可用状态，则服务可以指明在 **Retry-After** 响应头或其他自定义头中进行重试前的相应延迟。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-835">In the case of a service unavailable status, the service might indicate the appropriate delay before retrying in the **Retry-After** response header or a different custom header.</span></span> <span data-ttu-id="7f3b6-836">服务还可以发送其他信息，既能以自定义头的形式，也能嵌入响应内容中。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-836">Services might also send additional information as custom headers, or embedded in the content of the response.</span></span> <span data-ttu-id="7f3b6-837">临时故障处理应用程序块不能使用标准头或任何自定义“retry-after”头。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-837">The Transient Fault Handling Application Block cannot use the standard or any custom “retry-after” headers.</span></span>
* <span data-ttu-id="7f3b6-838">请勿针对表示客户端错误的状态代码（4xx 范围内的错误）进行重试，“408 请求超时”除外。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-838">Do not retry for status codes representing client errors (errors in the 4xx range) except for a 408 Request Timeout.</span></span>
* <span data-ttu-id="7f3b6-839">在各种条件（如不同的网络状态和系统负载）下全面测试重试策略和机制。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-839">Thoroughly test your retry strategies and mechanisms under a range of conditions, such as different network states and varying system loadings.</span></span>

### <a name="retry-strategies"></a><span data-ttu-id="7f3b6-840">重试策略</span><span class="sxs-lookup"><span data-stu-id="7f3b6-840">Retry strategies</span></span>
<span data-ttu-id="7f3b6-841">以下是典型的重试策略间隔类型：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-841">The following are the typical types of retry strategy intervals:</span></span>

* <span data-ttu-id="7f3b6-842">**指数**。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-842">**Exponential**.</span></span> <span data-ttu-id="7f3b6-843">此重试策略执行指定次数的重试，并使用随机指数回退方法来确定重试间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-843">A retry policy that performs a specified number of retries, using a randomized exponential back off approach to determine the interval between retries.</span></span> <span data-ttu-id="7f3b6-844">例如：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-844">For example:</span></span>

    ```csharp
    var random = new Random();

    var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
                random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
                (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
    var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
                    this.maxBackoff.TotalMilliseconds);
    retryInterval = TimeSpan.FromMilliseconds(interval);
    ```

* <span data-ttu-id="7f3b6-845">**增量**。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-845">**Incremental**.</span></span> <span data-ttu-id="7f3b6-846">此重试策略具有指定的重试尝试次数以及增量的重试间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-846">A retry strategy with a specified number of retry attempts and an incremental time interval between retries.</span></span> <span data-ttu-id="7f3b6-847">例如：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-847">For example:</span></span>

    ```csharp
    retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
                    (this.increment.TotalMilliseconds * currentRetryCount));
    ```

* <span data-ttu-id="7f3b6-848">**线性重试**。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-848">**LinearRetry**.</span></span> <span data-ttu-id="7f3b6-849">此重试策略执行指定次数的重试，并使用指定的固定重试间隔。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-849">A retry policy that performs a specified number of retries, using a specified fixed time interval between retries.</span></span> <span data-ttu-id="7f3b6-850">例如：</span><span class="sxs-lookup"><span data-stu-id="7f3b6-850">For example:</span></span>

    ```csharp
    retryInterval = this.deltaBackoff;
    ```

### <a name="transient-fault-handling-with-polly"></a><span data-ttu-id="7f3b6-851">使用 Polly 处理暂时性错误</span><span class="sxs-lookup"><span data-stu-id="7f3b6-851">Transient fault handling with Polly</span></span>
<span data-ttu-id="7f3b6-852">[Polly][polly] 是一个库，用于以编程方式处理重试和[断路器][circuit-breaker]策略。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-852">[Polly][polly] is a library to programatically handle retries and [circuit breaker][circuit-breaker] strategies.</span></span> <span data-ttu-id="7f3b6-853">Polly 项目隶属于 [.NET Foundation][dotnet-foundation]。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-853">The Polly project is a member of the [.NET Foundation][dotnet-foundation].</span></span> <span data-ttu-id="7f3b6-854">对于客户端不对重试提供本机支持的服务，Polly 是一种有效的替代方法，它让用户无需编写可能难以正确实现的自定义重试代码。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-854">For services where the client does not natively support retries, Polly is a valid alternative and avoids the need to write custom retry code, which can be hard to implement correctly.</span></span> <span data-ttu-id="7f3b6-855">Polly 还提供一种在出现错误时对其进行跟踪的方法，以便用户记录重试。</span><span class="sxs-lookup"><span data-stu-id="7f3b6-855">Polly also provides a way to trace errors when they occur, so that you can log retries.</span></span>


<!-- links -->

[adal]: /azure/active-directory/develop/active-directory-authentication-libraries
[autorest]: https://github.com/Azure/autorest/tree/master/docs
[circuit-breaker]: ../patterns/circuit-breaker.md
[ConnectionPolicy.RetryOptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.retryoptions.aspx
[dotnet-foundation]: https://dotnetfoundation.org/
[polly]: http://www.thepollyproject.org
[redis-cache-troubleshoot]: /azure/redis-cache/cache-how-to-troubleshoot
[SearchIndexClient]: https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx
[SearchServiceClient]: https://msdn.microsoft.com/library/microsoft.azure.search.searchserviceclient.aspx


### <a name="more-information"></a><span data-ttu-id="7f3b6-859">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f3b6-859">More information</span></span>
* [<span data-ttu-id="7f3b6-860">连接复原</span><span class="sxs-lookup"><span data-stu-id="7f3b6-860">Connection Resiliency</span></span>](/ef/core/miscellaneous/connection-resiliency)
* [<span data-ttu-id="7f3b6-861">数据点 - EF Core 1.1</span><span class="sxs-lookup"><span data-stu-id="7f3b6-861">Data Points - EF Core 1.1</span></span>](https://msdn.microsoft.com/magazine/mt745093.aspx)


