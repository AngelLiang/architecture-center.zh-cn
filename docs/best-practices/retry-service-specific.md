---
title: 重试服务指南
description: 设置重试机制的服务指南。
author: dragon119
ms.date: 07/13/2016
pnp.series.title: Best Practices
ms.openlocfilehash: 790c933458717f2cb4cde0741b1d22f6ae89cc39
ms.sourcegitcommit: 8ec48a0e2c080c9e2e0abbfdbc463622b28de2f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2018
ms.locfileid: "43016106"
---
# <a name="retry-guidance-for-specific-services"></a><span data-ttu-id="45935-103">特定服务的重试指南</span><span class="sxs-lookup"><span data-stu-id="45935-103">Retry guidance for specific services</span></span>

<span data-ttu-id="45935-104">大多数 Azure 服务和客户端 SDK 都包括重试机制。</span><span class="sxs-lookup"><span data-stu-id="45935-104">Most Azure services and client SDKs include a retry mechanism.</span></span> <span data-ttu-id="45935-105">不过，这些重试机制各不相同，这是因为每个服务都有不同的特征和要求，这样一来，各个重试机制都会针对特定服务进行优化。</span><span class="sxs-lookup"><span data-stu-id="45935-105">However, these differ because each service has different characteristics and requirements, and so each retry mechanism is tuned to a specific service.</span></span> <span data-ttu-id="45935-106">本指南汇总了大多数 Azure 服务的重试机制功能，并介绍了如何使用、适应或扩展相应服务的重试机制。</span><span class="sxs-lookup"><span data-stu-id="45935-106">This guide summarizes the retry mechanism features for the majority of Azure services, and includes information to help you use, adapt, or extend the retry mechanism for that service.</span></span>

<span data-ttu-id="45935-107">有关如何处理临时故障、针对服务和资源重试连接和操作的一般指南，请参阅[重试指南](./transient-faults.md)。</span><span class="sxs-lookup"><span data-stu-id="45935-107">For general guidance on handling transient faults, and retrying connections and operations against services and resources, see [Retry guidance](./transient-faults.md).</span></span>

<span data-ttu-id="45935-108">下表总结了本指南中介绍的 Azure 服务重试功能。</span><span class="sxs-lookup"><span data-stu-id="45935-108">The following table summarizes the retry features for the Azure services described in this guidance.</span></span>

| <span data-ttu-id="45935-109">**服务**</span><span class="sxs-lookup"><span data-stu-id="45935-109">**Service**</span></span> | <span data-ttu-id="45935-110">**重试功能**</span><span class="sxs-lookup"><span data-stu-id="45935-110">**Retry capabilities**</span></span> | <span data-ttu-id="45935-111">**策略配置**</span><span class="sxs-lookup"><span data-stu-id="45935-111">**Policy configuration**</span></span> | <span data-ttu-id="45935-112">**范围**</span><span class="sxs-lookup"><span data-stu-id="45935-112">**Scope**</span></span> | <span data-ttu-id="45935-113">**遥测功能**</span><span class="sxs-lookup"><span data-stu-id="45935-113">**Telemetry features**</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="45935-114">**[Azure Active Directory](#azure-active-directory)**</span><span class="sxs-lookup"><span data-stu-id="45935-114">**[Azure Active Directory](#azure-active-directory)**</span></span> |<span data-ttu-id="45935-115">ADAL 库原生</span><span class="sxs-lookup"><span data-stu-id="45935-115">Native in ADAL library</span></span> |<span data-ttu-id="45935-116">嵌入到 ADAL 库</span><span class="sxs-lookup"><span data-stu-id="45935-116">Embeded into ADAL library</span></span> |<span data-ttu-id="45935-117">内部</span><span class="sxs-lookup"><span data-stu-id="45935-117">Internal</span></span> |<span data-ttu-id="45935-118">无</span><span class="sxs-lookup"><span data-stu-id="45935-118">None</span></span> |
| <span data-ttu-id="45935-119">**[Cosmos DB](#cosmos-db)**</span><span class="sxs-lookup"><span data-stu-id="45935-119">**[Cosmos DB](#cosmos-db)**</span></span> |<span data-ttu-id="45935-120">服务原生</span><span class="sxs-lookup"><span data-stu-id="45935-120">Native in service</span></span> |<span data-ttu-id="45935-121">不可配置</span><span class="sxs-lookup"><span data-stu-id="45935-121">Non-configurable</span></span> |<span data-ttu-id="45935-122">全局</span><span class="sxs-lookup"><span data-stu-id="45935-122">Global</span></span> |<span data-ttu-id="45935-123">TraceSource</span><span class="sxs-lookup"><span data-stu-id="45935-123">TraceSource</span></span> |
| <span data-ttu-id="45935-124">**Data Lake Store**</span><span class="sxs-lookup"><span data-stu-id="45935-124">**Data Lake Store**</span></span> |<span data-ttu-id="45935-125">客户端原生</span><span class="sxs-lookup"><span data-stu-id="45935-125">Native in client</span></span> |<span data-ttu-id="45935-126">不可配置</span><span class="sxs-lookup"><span data-stu-id="45935-126">Non-configurable</span></span> |<span data-ttu-id="45935-127">各项操作</span><span class="sxs-lookup"><span data-stu-id="45935-127">Individual operations</span></span> |<span data-ttu-id="45935-128">无</span><span class="sxs-lookup"><span data-stu-id="45935-128">None</span></span> |
| <span data-ttu-id="45935-129">**[事件中心](#event-hubs)**</span><span class="sxs-lookup"><span data-stu-id="45935-129">**[Event Hubs](#event-hubs)**</span></span> |<span data-ttu-id="45935-130">客户端原生</span><span class="sxs-lookup"><span data-stu-id="45935-130">Native in client</span></span> |<span data-ttu-id="45935-131">编程</span><span class="sxs-lookup"><span data-stu-id="45935-131">Programmatic</span></span> |<span data-ttu-id="45935-132">Client</span><span class="sxs-lookup"><span data-stu-id="45935-132">Client</span></span> |<span data-ttu-id="45935-133">无</span><span class="sxs-lookup"><span data-stu-id="45935-133">None</span></span> |
| <span data-ttu-id="45935-134">**[IoT 中心](#iot-hub)**</span><span class="sxs-lookup"><span data-stu-id="45935-134">**[IoT Hub](#iot-hub)**</span></span> |<span data-ttu-id="45935-135">客户端 SDK 原生</span><span class="sxs-lookup"><span data-stu-id="45935-135">Native in client SDK</span></span> |<span data-ttu-id="45935-136">编程</span><span class="sxs-lookup"><span data-stu-id="45935-136">Programmatic</span></span> |<span data-ttu-id="45935-137">Client</span><span class="sxs-lookup"><span data-stu-id="45935-137">Client</span></span> |<span data-ttu-id="45935-138">无</span><span class="sxs-lookup"><span data-stu-id="45935-138">None</span></span> |
| <span data-ttu-id="45935-139">**[Redis 缓存](#azure-redis-cache)**</span><span class="sxs-lookup"><span data-stu-id="45935-139">**[Redis Cache](#azure-redis-cache)**</span></span> |<span data-ttu-id="45935-140">客户端原生</span><span class="sxs-lookup"><span data-stu-id="45935-140">Native in client</span></span> |<span data-ttu-id="45935-141">编程</span><span class="sxs-lookup"><span data-stu-id="45935-141">Programmatic</span></span> |<span data-ttu-id="45935-142">Client</span><span class="sxs-lookup"><span data-stu-id="45935-142">Client</span></span> |<span data-ttu-id="45935-143">TextWriter</span><span class="sxs-lookup"><span data-stu-id="45935-143">TextWriter</span></span> |
| <span data-ttu-id="45935-144">**[搜索](#azure-search)**</span><span class="sxs-lookup"><span data-stu-id="45935-144">**[Search](#azure-search)**</span></span> |<span data-ttu-id="45935-145">客户端原生</span><span class="sxs-lookup"><span data-stu-id="45935-145">Native in client</span></span> |<span data-ttu-id="45935-146">编程</span><span class="sxs-lookup"><span data-stu-id="45935-146">Programmatic</span></span> |<span data-ttu-id="45935-147">Client</span><span class="sxs-lookup"><span data-stu-id="45935-147">Client</span></span> |<span data-ttu-id="45935-148">ETW 或自定义</span><span class="sxs-lookup"><span data-stu-id="45935-148">ETW or Custom</span></span> |
| <span data-ttu-id="45935-149">**[服务总线](#service-bus)**</span><span class="sxs-lookup"><span data-stu-id="45935-149">**[Service Bus](#service-bus)**</span></span> |<span data-ttu-id="45935-150">客户端原生</span><span class="sxs-lookup"><span data-stu-id="45935-150">Native in client</span></span> |<span data-ttu-id="45935-151">编程</span><span class="sxs-lookup"><span data-stu-id="45935-151">Programmatic</span></span> |<span data-ttu-id="45935-152">命名空间管理器、消息工厂和客户端</span><span class="sxs-lookup"><span data-stu-id="45935-152">Namespace Manager, Messaging Factory, and Client</span></span> |<span data-ttu-id="45935-153">ETW</span><span class="sxs-lookup"><span data-stu-id="45935-153">ETW</span></span> |
| <span data-ttu-id="45935-154">**[Service Fabric](#service-fabric)**</span><span class="sxs-lookup"><span data-stu-id="45935-154">**[Service Fabric](#service-fabric)**</span></span> |<span data-ttu-id="45935-155">客户端原生</span><span class="sxs-lookup"><span data-stu-id="45935-155">Native in client</span></span> |<span data-ttu-id="45935-156">编程</span><span class="sxs-lookup"><span data-stu-id="45935-156">Programmatic</span></span> |<span data-ttu-id="45935-157">Client</span><span class="sxs-lookup"><span data-stu-id="45935-157">Client</span></span> |<span data-ttu-id="45935-158">无</span><span class="sxs-lookup"><span data-stu-id="45935-158">None</span></span> | 
| <span data-ttu-id="45935-159">**[使用 ADO.NET 的 SQL 数据库](#sql-database-using-adonet)**</span><span class="sxs-lookup"><span data-stu-id="45935-159">**[SQL Database with ADO.NET](#sql-database-using-adonet)**</span></span> |[<span data-ttu-id="45935-160">Polly</span><span class="sxs-lookup"><span data-stu-id="45935-160">Polly</span></span>](#transient-fault-handling-with-polly) |<span data-ttu-id="45935-161">声明性和编程</span><span class="sxs-lookup"><span data-stu-id="45935-161">Declarative and programmatic</span></span> |<span data-ttu-id="45935-162">各个语句或代码块</span><span class="sxs-lookup"><span data-stu-id="45935-162">Single statements or blocks of code</span></span> |<span data-ttu-id="45935-163">“自定义”</span><span class="sxs-lookup"><span data-stu-id="45935-163">Custom</span></span> |
| <span data-ttu-id="45935-164">**[使用 Entity Framework 的 SQL 数据库](#sql-database-using-entity-framework-6)**</span><span class="sxs-lookup"><span data-stu-id="45935-164">**[SQL Database with Entity Framework](#sql-database-using-entity-framework-6)**</span></span> |<span data-ttu-id="45935-165">客户端原生</span><span class="sxs-lookup"><span data-stu-id="45935-165">Native in client</span></span> |<span data-ttu-id="45935-166">编程</span><span class="sxs-lookup"><span data-stu-id="45935-166">Programmatic</span></span> |<span data-ttu-id="45935-167">每个应用域均为全局</span><span class="sxs-lookup"><span data-stu-id="45935-167">Global per AppDomain</span></span> |<span data-ttu-id="45935-168">无</span><span class="sxs-lookup"><span data-stu-id="45935-168">None</span></span> |
| <span data-ttu-id="45935-169">**[使用 Entity Framework Core 的 SQL 数据库](#sql-database-using-entity-framework-core)**</span><span class="sxs-lookup"><span data-stu-id="45935-169">**[SQL Database with Entity Framework Core](#sql-database-using-entity-framework-core)**</span></span> |<span data-ttu-id="45935-170">客户端原生</span><span class="sxs-lookup"><span data-stu-id="45935-170">Native in client</span></span> |<span data-ttu-id="45935-171">编程</span><span class="sxs-lookup"><span data-stu-id="45935-171">Programmatic</span></span> |<span data-ttu-id="45935-172">每个应用域均为全局</span><span class="sxs-lookup"><span data-stu-id="45935-172">Global per AppDomain</span></span> |<span data-ttu-id="45935-173">无</span><span class="sxs-lookup"><span data-stu-id="45935-173">None</span></span> |
| <span data-ttu-id="45935-174">**[存储](#azure-storage)**</span><span class="sxs-lookup"><span data-stu-id="45935-174">**[Storage](#azure-storage)**</span></span> |<span data-ttu-id="45935-175">客户端原生</span><span class="sxs-lookup"><span data-stu-id="45935-175">Native in client</span></span> |<span data-ttu-id="45935-176">编程</span><span class="sxs-lookup"><span data-stu-id="45935-176">Programmatic</span></span> |<span data-ttu-id="45935-177">客户端   和各项操作</span><span class="sxs-lookup"><span data-stu-id="45935-177">Client and individual operations</span></span> |<span data-ttu-id="45935-178">TraceSource</span><span class="sxs-lookup"><span data-stu-id="45935-178">TraceSource</span></span> |

> [!NOTE]
> <span data-ttu-id="45935-179">对于大多数 Azure 内置重试机制，目前尚无方法针对不同类型的错误或异常应用不同的重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-179">For most of the Azure built-in retry mechanisms, there is currently no way apply a different retry policy for different types of error or exception.</span></span> <span data-ttu-id="45935-180">应该配置可提供最佳平均性能和可用性的策略。</span><span class="sxs-lookup"><span data-stu-id="45935-180">You should configure a policy that provides the optimum average performance and availability.</span></span> <span data-ttu-id="45935-181">微调策略的一种方法是分析日志文件，以确定发生的临时故障的类型。</span><span class="sxs-lookup"><span data-stu-id="45935-181">One way to fine-tune the policy is to analyze log files to determine the type of transient faults that are occurring.</span></span> 

## <a name="azure-active-directory"></a><span data-ttu-id="45935-182">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="45935-182">Azure Active Directory</span></span>
<span data-ttu-id="45935-183">Azure Active Directory (Azure AD) 是一项全面的标识和访问管理云解决方案，集成了核心目录服务、高级标识监管、安全性和应用程序访问管理等各种功能。</span><span class="sxs-lookup"><span data-stu-id="45935-183">Azure Active Directory (Azure AD) is a comprehensive identity and access management cloud solution that combines core directory services, advanced identity governance, security, and application access management.</span></span> <span data-ttu-id="45935-184">Microsoft Azure AD 还为开发人员提供了身份管理平台，以便他们可以根据集中的策略和规则，控制应用程序访问情况。</span><span class="sxs-lookup"><span data-stu-id="45935-184">Azure AD also offers developers an identity management platform to deliver access control to their applications, based on centralized policy and rules.</span></span>

> [!NOTE]
> <span data-ttu-id="45935-185">有关托管服务标识终结点的重试指南，请参阅[如何使用 Azure VM 托管服务标识 (MSI) 进行令牌获取](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)。</span><span class="sxs-lookup"><span data-stu-id="45935-185">For retry guidance on Managed Service Identity endpoints, see [How to use an Azure VM Managed Service Identity (MSI) for token acquisition](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-186">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-186">Retry mechanism</span></span>
<span data-ttu-id="45935-187">Active Directory 身份验证库 (ADAL) 提供适用于 Azure Active Directory 的内置重试机制。</span><span class="sxs-lookup"><span data-stu-id="45935-187">There is a built-in retry mechanism for Azure Active Directory in the Active Directory Authentication Library (ADAL).</span></span> <span data-ttu-id="45935-188">为避免意外锁定，建议第三方库和应用程序代码**不要**重试失败的连接，而让 ADAL 处理重试。</span><span class="sxs-lookup"><span data-stu-id="45935-188">To avoid unexpected lockouts, we recommend that third party libraries and application code do **not** retry failed connections, but allow ADAL to handle retries.</span></span> 

### <a name="retry-usage-guidance"></a><span data-ttu-id="45935-189">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="45935-189">Retry usage guidance</span></span>
<span data-ttu-id="45935-190">使用 Azure Active Directory 时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="45935-190">Consider the following guidelines when using Azure Active Directory:</span></span>

* <span data-ttu-id="45935-191">如有可能，请使用 ADAL 库和内置支持进行重试。</span><span class="sxs-lookup"><span data-stu-id="45935-191">When possible, use the ADAL library and the built-in support for retries.</span></span>
* <span data-ttu-id="45935-192">在对 Azure Active Directory 使用 REST API 的情况下，如果结果代码为 429（太多请求）或 5xx 范围中的错误，请重试该操作。</span><span class="sxs-lookup"><span data-stu-id="45935-192">If you are using the REST API for Azure Active Directory, retry the operation if the result code is 429 (Too Many Requests) or an error in the 5xx range.</span></span> <span data-ttu-id="45935-193">请勿针对其他任何错误重试操作。</span><span class="sxs-lookup"><span data-stu-id="45935-193">Do not retry for any other errors.</span></span>
* <span data-ttu-id="45935-194">建议将指数回退策略用于 Azure Active Directory 的批处理方案。</span><span class="sxs-lookup"><span data-stu-id="45935-194">An exponential back-off policy is recommended for use in batch scenarios with Azure Active Directory.</span></span>

<span data-ttu-id="45935-195">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="45935-195">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="45935-196">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="45935-196">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="45935-197">**上下文**</span><span class="sxs-lookup"><span data-stu-id="45935-197">**Context**</span></span> | <span data-ttu-id="45935-198">**示例目标 E2E<br />最长延迟**</span><span class="sxs-lookup"><span data-stu-id="45935-198">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="45935-199">**重试策略**</span><span class="sxs-lookup"><span data-stu-id="45935-199">**Retry strategy**</span></span> | <span data-ttu-id="45935-200">**设置**</span><span class="sxs-lookup"><span data-stu-id="45935-200">**Settings**</span></span> | <span data-ttu-id="45935-201">**值**</span><span class="sxs-lookup"><span data-stu-id="45935-201">**Values**</span></span> | <span data-ttu-id="45935-202">**工作原理**</span><span class="sxs-lookup"><span data-stu-id="45935-202">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="45935-203">交互式, UI,</span><span class="sxs-lookup"><span data-stu-id="45935-203">Interactive, UI,</span></span><br /><span data-ttu-id="45935-204">或 foreground</span><span class="sxs-lookup"><span data-stu-id="45935-204">or foreground</span></span> |<span data-ttu-id="45935-205">2 秒</span><span class="sxs-lookup"><span data-stu-id="45935-205">2 sec</span></span> |<span data-ttu-id="45935-206">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="45935-206">FixedInterval</span></span> |<span data-ttu-id="45935-207">重试计数</span><span class="sxs-lookup"><span data-stu-id="45935-207">Retry count</span></span><br /><span data-ttu-id="45935-208">重试间隔</span><span class="sxs-lookup"><span data-stu-id="45935-208">Retry interval</span></span><br /><span data-ttu-id="45935-209">首次快速重试</span><span class="sxs-lookup"><span data-stu-id="45935-209">First fast retry</span></span> |<span data-ttu-id="45935-210">3</span><span class="sxs-lookup"><span data-stu-id="45935-210">3</span></span><br /><span data-ttu-id="45935-211">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-211">500 ms</span></span><br /><span data-ttu-id="45935-212">是</span><span class="sxs-lookup"><span data-stu-id="45935-212">true</span></span> |<span data-ttu-id="45935-213">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="45935-213">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="45935-214">第 2 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-214">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="45935-215">第 3 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-215">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="45935-216">背景或</span><span class="sxs-lookup"><span data-stu-id="45935-216">Background or</span></span><br /><span data-ttu-id="45935-217">批处理</span><span class="sxs-lookup"><span data-stu-id="45935-217">batch</span></span> |<span data-ttu-id="45935-218">60 秒</span><span class="sxs-lookup"><span data-stu-id="45935-218">60 sec</span></span> |<span data-ttu-id="45935-219">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-219">ExponentialBackoff</span></span> |<span data-ttu-id="45935-220">重试计数</span><span class="sxs-lookup"><span data-stu-id="45935-220">Retry count</span></span><br /><span data-ttu-id="45935-221">最小回退</span><span class="sxs-lookup"><span data-stu-id="45935-221">Min back-off</span></span><br /><span data-ttu-id="45935-222">最大回退</span><span class="sxs-lookup"><span data-stu-id="45935-222">Max back-off</span></span><br /><span data-ttu-id="45935-223">增量回退</span><span class="sxs-lookup"><span data-stu-id="45935-223">Delta back-off</span></span><br /><span data-ttu-id="45935-224">首次快速重试</span><span class="sxs-lookup"><span data-stu-id="45935-224">First fast retry</span></span> |<span data-ttu-id="45935-225">5</span><span class="sxs-lookup"><span data-stu-id="45935-225">5</span></span><br /><span data-ttu-id="45935-226">0 秒</span><span class="sxs-lookup"><span data-stu-id="45935-226">0 sec</span></span><br /><span data-ttu-id="45935-227">60 秒</span><span class="sxs-lookup"><span data-stu-id="45935-227">60 sec</span></span><br /><span data-ttu-id="45935-228">2 秒</span><span class="sxs-lookup"><span data-stu-id="45935-228">2 sec</span></span><br /><span data-ttu-id="45935-229">false</span><span class="sxs-lookup"><span data-stu-id="45935-229">false</span></span> |<span data-ttu-id="45935-230">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="45935-230">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="45935-231">第 2 次尝试 - 约延迟 2 秒</span><span class="sxs-lookup"><span data-stu-id="45935-231">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="45935-232">第 3 次尝试 - 约延迟 6 秒</span><span class="sxs-lookup"><span data-stu-id="45935-232">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="45935-233">第 4 次尝试 - 约延迟 14 秒</span><span class="sxs-lookup"><span data-stu-id="45935-233">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="45935-234">第 5 次尝试 - 约延迟 30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-234">Attempt 5 - delay ~30 sec</span></span> |

### <a name="more-information"></a><span data-ttu-id="45935-235">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-235">More information</span></span>
* <span data-ttu-id="45935-236">[Azure Active Directory 身份验证库][adal]</span><span class="sxs-lookup"><span data-stu-id="45935-236">[Azure Active Directory Authentication Libraries][adal]</span></span>

## <a name="cosmos-db"></a><span data-ttu-id="45935-237">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="45935-237">Cosmos DB</span></span>

<span data-ttu-id="45935-238">Cosmos DB 是一种完全托管的多模型数据库，支持无架构 JSON 数据。</span><span class="sxs-lookup"><span data-stu-id="45935-238">Cosmos DB is a fully-managed multi-model database that supports schema-less JSON data.</span></span> <span data-ttu-id="45935-239">它提供可配置的可靠性能、本机 JavaScript 事务处理，专为具有弹性延展能力的云构建而成。</span><span class="sxs-lookup"><span data-stu-id="45935-239">It offers configurable and reliable performance, native JavaScript transactional processing, and is built for the cloud with elastic scale.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-240">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-240">Retry mechanism</span></span>
<span data-ttu-id="45935-241">`DocumentClient` 类自动重试失败的尝试次数。</span><span class="sxs-lookup"><span data-stu-id="45935-241">The `DocumentClient` class automatically retries failed attempts.</span></span> <span data-ttu-id="45935-242">若要设置重试次数和最长等待时间，请配置 [ConnectionPolicy.RetryOptions]。</span><span class="sxs-lookup"><span data-stu-id="45935-242">To set the number of retries and the maximum wait time, configure [ConnectionPolicy.RetryOptions].</span></span> <span data-ttu-id="45935-243">客户端引发的异常会超出重试策略，或不是暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="45935-243">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>

<span data-ttu-id="45935-244">如果 Cosmos DB 限制客户端，它会返回 HTTP 429 错误。</span><span class="sxs-lookup"><span data-stu-id="45935-244">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="45935-245">请检查 `DocumentClientException` 中的状态代码。</span><span class="sxs-lookup"><span data-stu-id="45935-245">Check the status code in the `DocumentClientException`.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="45935-246">策略配置</span><span class="sxs-lookup"><span data-stu-id="45935-246">Policy configuration</span></span>
<span data-ttu-id="45935-247">下表显示了 `RetryOptions` 类的默认设置。</span><span class="sxs-lookup"><span data-stu-id="45935-247">The following table shows the default settings for the `RetryOptions` class.</span></span>

| <span data-ttu-id="45935-248">设置</span><span class="sxs-lookup"><span data-stu-id="45935-248">Setting</span></span> | <span data-ttu-id="45935-249">默认值</span><span class="sxs-lookup"><span data-stu-id="45935-249">Default value</span></span> | <span data-ttu-id="45935-250">Description</span><span class="sxs-lookup"><span data-stu-id="45935-250">Description</span></span> |
| --- | --- | --- |
| <span data-ttu-id="45935-251">MaxRetryAttemptsOnThrottledRequests</span><span class="sxs-lookup"><span data-stu-id="45935-251">MaxRetryAttemptsOnThrottledRequests</span></span> |<span data-ttu-id="45935-252">9</span><span class="sxs-lookup"><span data-stu-id="45935-252">9</span></span> |<span data-ttu-id="45935-253">因 Cosmos DB 对客户端应用速率限制而导致请求失败时的最大重试次数。</span><span class="sxs-lookup"><span data-stu-id="45935-253">The maximum number of retries if the request fails because Cosmos DB applied rate limiting on the client.</span></span> |
| <span data-ttu-id="45935-254">MaxRetryWaitTimeInSeconds</span><span class="sxs-lookup"><span data-stu-id="45935-254">MaxRetryWaitTimeInSeconds</span></span> |<span data-ttu-id="45935-255">30</span><span class="sxs-lookup"><span data-stu-id="45935-255">30</span></span> |<span data-ttu-id="45935-256">最大重试时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="45935-256">The maximum retry time in seconds.</span></span> |

### <a name="example"></a><span data-ttu-id="45935-257">示例</span><span class="sxs-lookup"><span data-stu-id="45935-257">Example</span></span>
```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); ;
var options = client.ConnectionPolicy.RetryOptions;
options.MaxRetryAttemptsOnThrottledRequests = 5;
options.MaxRetryWaitTimeInSeconds = 15;
```

### <a name="telemetry"></a><span data-ttu-id="45935-258">遥测</span><span class="sxs-lookup"><span data-stu-id="45935-258">Telemetry</span></span>
<span data-ttu-id="45935-259">重试尝试通过 .NET **TraceSource** 记录为未结构化的跟踪消息。</span><span class="sxs-lookup"><span data-stu-id="45935-259">Retry attempts are logged as unstructured trace messages through a .NET **TraceSource**.</span></span> <span data-ttu-id="45935-260">必须配置 **TraceListener**，才能捕获事件并将这些事件写入合适的目标日志。</span><span class="sxs-lookup"><span data-stu-id="45935-260">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span>

<span data-ttu-id="45935-261">例如，如果将以下内容添加到 App.config 文件，会在与可执行文件相同位置的文本文件中生成跟踪：</span><span class="sxs-lookup"><span data-stu-id="45935-261">For example, if you add the following to your App.config file, traces will be generated in a text file in the same location as the executable:</span></span>

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

## <a name="event-hubs"></a><span data-ttu-id="45935-262">事件中心</span><span class="sxs-lookup"><span data-stu-id="45935-262">Event Hubs</span></span>

<span data-ttu-id="45935-263">Azure 事件中心是超大规模的遥测引入服务，可收集、传输和存储数百万的事件。</span><span class="sxs-lookup"><span data-stu-id="45935-263">Azure Event Hubs is a hyper-scale telemetry ingestion service that collects, transforms, and stores millions of events.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-264">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-264">Retry mechanism</span></span>
<span data-ttu-id="45935-265">Azure 事件中心客户端库中的重试行为由 `EventHubClient` 类上的 `RetryPolicy` 属性控制。</span><span class="sxs-lookup"><span data-stu-id="45935-265">Retry behavior in the Azure Event Hubs Client Library is controlled by the `RetryPolicy` property on the `EventHubClient` class.</span></span> <span data-ttu-id="45935-266">Azure 事件中心返回暂时性 `EventHubsException` 或 `OperationCanceledException` 时，默认策略会使用指数回退进行重试。</span><span class="sxs-lookup"><span data-stu-id="45935-266">The default policy retries with exponential backoff when Azure Event Hub returns a transient `EventHubsException` or an `OperationCanceledException`.</span></span>

### <a name="example"></a><span data-ttu-id="45935-267">示例</span><span class="sxs-lookup"><span data-stu-id="45935-267">Example</span></span>
```csharp
EventHubClient client = EventHubClient.CreateFromConnectionString("[event_hub_connection_string]");
client.RetryPolicy = RetryPolicy.Default;
```

### <a name="more-information"></a><span data-ttu-id="45935-268">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-268">More information</span></span>
[<span data-ttu-id="45935-269">Azure 事件中心的 .NET 标准客户端库</span><span class="sxs-lookup"><span data-stu-id="45935-269"> .NET Standard client library for Azure Event Hubs</span></span>](https://github.com/Azure/azure-event-hubs-dotnet)

## <a name="iot-hub"></a><span data-ttu-id="45935-270">IoT 中心</span><span class="sxs-lookup"><span data-stu-id="45935-270">IoT Hub</span></span>

<span data-ttu-id="45935-271">Azure IoT 中心是一项服务，用于连接、监视和管理那些开发物联网 (IoT) 应用程序的设备。</span><span class="sxs-lookup"><span data-stu-id="45935-271">Azure IoT Hub is a service for connecting, monitoring, and managing devices to develop Internet of Things (IoT) applications.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-272">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-272">Retry mechanism</span></span>

<span data-ttu-id="45935-273">Azure IoT 设备 SDK 可以检测网络、协议或应用程序中的错误。</span><span class="sxs-lookup"><span data-stu-id="45935-273">The Azure IoT device SDK can detect errors in the network, protocol, or application.</span></span> <span data-ttu-id="45935-274">此 SDK 根据错误类型来检查是否需执行重试。</span><span class="sxs-lookup"><span data-stu-id="45935-274">Based on the error type, the SDK checks whether a retry needs to be performed.</span></span> <span data-ttu-id="45935-275">如果错误是可恢复的，SDK 就会开始使用配置的重试策略进行重试。</span><span class="sxs-lookup"><span data-stu-id="45935-275">If the error is *recoverable*, the SDK begins to retry using the configured retry policy.</span></span>

<span data-ttu-id="45935-276">默认的重试策略为“带随机抖动的指数回退”，但这是可以配置的。</span><span class="sxs-lookup"><span data-stu-id="45935-276">The default retry policy is *exponential back-off with random jitter*, but it can be configured.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="45935-277">策略配置</span><span class="sxs-lookup"><span data-stu-id="45935-277">Policy configuration</span></span>

<span data-ttu-id="45935-278">策略配置因语言而异。</span><span class="sxs-lookup"><span data-stu-id="45935-278">Policy configuration differs by language.</span></span> <span data-ttu-id="45935-279">如需更多详细信息，请参阅 [IoT 中心重试策略配置](/azure/iot-hub/iot-hub-reliability-features-in-sdks#retry-policy-apis)。</span><span class="sxs-lookup"><span data-stu-id="45935-279">For more details, see [IoT Hub retry policy configuration](/azure/iot-hub/iot-hub-reliability-features-in-sdks#retry-policy-apis).</span></span>

### <a name="more-information"></a><span data-ttu-id="45935-280">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-280">More information</span></span>

* [<span data-ttu-id="45935-281">IoT 中心重试策略</span><span class="sxs-lookup"><span data-stu-id="45935-281">IoT Hub retry policy</span></span>](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
* [<span data-ttu-id="45935-282">排查 IoT 中心设备断开连接的问题</span><span class="sxs-lookup"><span data-stu-id="45935-282">Troubleshoot IoT Hub device disconnection</span></span>](/azure/iot-hub/iot-hub-troubleshoot-connectivity)

## <a name="azure-redis-cache"></a><span data-ttu-id="45935-283">Azure Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="45935-283">Azure Redis Cache</span></span>
<span data-ttu-id="45935-284">Azure Redis 高速缓存是一项快速数据访问和低延迟高速缓存服务，基于常用的开放源代码 Redis 高速缓存。</span><span class="sxs-lookup"><span data-stu-id="45935-284">Azure Redis Cache is a fast data access and low latency cache service based on the popular open source Redis Cache.</span></span> <span data-ttu-id="45935-285">它是由 Microsoft 管理的安全服务，可通过 Azure 中的任意应用程序进行访问。</span><span class="sxs-lookup"><span data-stu-id="45935-285">It is secure, managed by Microsoft, and is accessible from any application in Azure.</span></span>

<span data-ttu-id="45935-286">本部分中的指南假设你使用 StackExchange.Redis 客户端访问高速缓存。</span><span class="sxs-lookup"><span data-stu-id="45935-286">The guidance in this section is based on using the StackExchange.Redis client to access the cache.</span></span> <span data-ttu-id="45935-287">[Redis 网站](http://redis.io/clients)上列出了其他合适的客户端，这些客户端可能具有不同的重试机制。</span><span class="sxs-lookup"><span data-stu-id="45935-287">A list of other suitable clients can be found on the [Redis website](http://redis.io/clients), and these may have different retry mechanisms.</span></span>

<span data-ttu-id="45935-288">请注意，StackExchange.Redis 客户端通过单个连接使用多路复用。</span><span class="sxs-lookup"><span data-stu-id="45935-288">Note that the StackExchange.Redis client uses multiplexing through a single connection.</span></span> <span data-ttu-id="45935-289">建议的用法是，在应用程序启动时创建客户端实例，并对针对高速缓存执行的所有操作使用此实例。</span><span class="sxs-lookup"><span data-stu-id="45935-289">The recommended usage is to create an instance of the client at application startup and use this instance for all operations against the cache.</span></span> <span data-ttu-id="45935-290">因此，高速缓存连接只建立一次，且本部分中的所有指南均与此启动连接（而不是访问高速缓存的每个操作）的重试策略相关。</span><span class="sxs-lookup"><span data-stu-id="45935-290">For this reason, the connection to the cache is made only once, and so all of the guidance in this section is related to the retry policy for this initial connection—and not for each operation that accesses the cache.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-291">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-291">Retry mechanism</span></span>
<span data-ttu-id="45935-292">StackExchange.Redis 客户端使用连接管理器类，此类通过一组选项进行配置，其中包括：</span><span class="sxs-lookup"><span data-stu-id="45935-292">The StackExchange.Redis client uses a connection manager class that is configured through a set of options, including:</span></span>

- <span data-ttu-id="45935-293">**ConnectRetry**。</span><span class="sxs-lookup"><span data-stu-id="45935-293">**ConnectRetry**.</span></span> <span data-ttu-id="45935-294">缓存连接失败的重试次数。</span><span class="sxs-lookup"><span data-stu-id="45935-294">The number of times a failed connection to the cache will be retried.</span></span>
- <span data-ttu-id="45935-295">**ReconnectRetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="45935-295">**ReconnectRetryPolicy**.</span></span> <span data-ttu-id="45935-296">要使用的重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-296">The retry strategy to use.</span></span>
- <span data-ttu-id="45935-297">**ConnectTimeout**。</span><span class="sxs-lookup"><span data-stu-id="45935-297">**ConnectTimeout**.</span></span> <span data-ttu-id="45935-298">以毫秒为单位的最长等待时间。</span><span class="sxs-lookup"><span data-stu-id="45935-298">The maximum waiting time in milliseconds.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="45935-299">策略配置</span><span class="sxs-lookup"><span data-stu-id="45935-299">Policy configuration</span></span>
<span data-ttu-id="45935-300">重试策略是以编程方式进行配置，方法为在连接到高速缓存前设置客户端的选项。</span><span class="sxs-lookup"><span data-stu-id="45935-300">Retry policies are configured programmatically by setting the options for the client before connecting to the cache.</span></span> <span data-ttu-id="45935-301">为此，可以创建 **ConfigurationOptions** 类的实例，填充其属性，然后将它传递到 **Connect** 方法。</span><span class="sxs-lookup"><span data-stu-id="45935-301">This can be done by creating an instance of the **ConfigurationOptions** class, populating its properties, and passing it to the **Connect** method.</span></span>

<span data-ttu-id="45935-302">内置类支持随机化重试间隔的线性（固定）延迟和指数回退。</span><span class="sxs-lookup"><span data-stu-id="45935-302">The built-in classes support linear (constant) delay and exponential backoff with randomized retry intervals.</span></span> <span data-ttu-id="45935-303">还可以通过实现 **IReconnectRetryPolicy** 接口创建自定义重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-303">You can also create a custom retry policy by implementing the **IReconnectRetryPolicy** interface.</span></span>

<span data-ttu-id="45935-304">下面的示例使用指数回退配置重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-304">The following example configures a retry strategy using exponential backoff.</span></span>

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

<span data-ttu-id="45935-305">或者，可以将选项指定为字符串，然后将其传递到 **Connect** 方法。</span><span class="sxs-lookup"><span data-stu-id="45935-305">Alternatively, you can specify the options as a string, and pass this to the **Connect** method.</span></span> <span data-ttu-id="45935-306">请注意，**ReconnectRetryPolicy** 属性不能这样设置，它只能通过代码设置。</span><span class="sxs-lookup"><span data-stu-id="45935-306">Note that the **ReconnectRetryPolicy** property cannot be set this way, only through code.</span></span>

```csharp
var options = "localhost,connectRetry=3,connectTimeout=2000";
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="45935-307">也可以在连接到缓存时直接指定选项。</span><span class="sxs-lookup"><span data-stu-id="45935-307">You can also specify options directly when you connect to the cache.</span></span>

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

<span data-ttu-id="45935-308">有关详细信息，请参阅 StackExchange.Redis 文档中的 [Stack Exchange Redis 配置](https://stackexchange.github.io/StackExchange.Redis/Configuration)。</span><span class="sxs-lookup"><span data-stu-id="45935-308">For more information, see [Stack Exchange Redis Configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration) in the StackExchange.Redis documentation.</span></span>

<span data-ttu-id="45935-309">下表显示了内置重试策略的默认设置。</span><span class="sxs-lookup"><span data-stu-id="45935-309">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="45935-310">**上下文**</span><span class="sxs-lookup"><span data-stu-id="45935-310">**Context**</span></span> | <span data-ttu-id="45935-311">**设置**</span><span class="sxs-lookup"><span data-stu-id="45935-311">**Setting**</span></span> | <span data-ttu-id="45935-312">**默认值**</span><span class="sxs-lookup"><span data-stu-id="45935-312">**Default value**</span></span><br /><span data-ttu-id="45935-313">(v 1.2.2)</span><span class="sxs-lookup"><span data-stu-id="45935-313">(v 1.2.2)</span></span> | <span data-ttu-id="45935-314">**含义**</span><span class="sxs-lookup"><span data-stu-id="45935-314">**Meaning**</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="45935-315">配置选项</span><span class="sxs-lookup"><span data-stu-id="45935-315">ConfigurationOptions</span></span> |<span data-ttu-id="45935-316">ConnectRetry</span><span class="sxs-lookup"><span data-stu-id="45935-316">ConnectRetry</span></span><br /><br /><span data-ttu-id="45935-317">ConnectTimeout</span><span class="sxs-lookup"><span data-stu-id="45935-317">ConnectTimeout</span></span><br /><br /><span data-ttu-id="45935-318">SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="45935-318">SyncTimeout</span></span><br /><br /><span data-ttu-id="45935-319">ReconnectRetryPolicy</span><span class="sxs-lookup"><span data-stu-id="45935-319">ReconnectRetryPolicy</span></span> |<span data-ttu-id="45935-320">3</span><span class="sxs-lookup"><span data-stu-id="45935-320">3</span></span><br /><br /><span data-ttu-id="45935-321">最长 5000 毫秒，外加 SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="45935-321">Maximum 5000 ms plus SyncTimeout</span></span><br /><span data-ttu-id="45935-322">1000</span><span class="sxs-lookup"><span data-stu-id="45935-322">1000</span></span><br /><br /><span data-ttu-id="45935-323">LinearRetry 5000 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-323">LinearRetry 5000 ms</span></span> |<span data-ttu-id="45935-324">初始连接操作期间重试连接的次数。</span><span class="sxs-lookup"><span data-stu-id="45935-324">The number of times to repeat connect attempts during the initial connection operation.</span></span><br /><span data-ttu-id="45935-325">连接操作的超时（以毫秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="45935-325">Timeout (ms) for connect operations.</span></span> <span data-ttu-id="45935-326">重试尝试之间没有延迟。</span><span class="sxs-lookup"><span data-stu-id="45935-326">Not a delay between retry attempts.</span></span><br /><span data-ttu-id="45935-327">允许同步操作进行的时间（以毫秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="45935-327">Time (ms) to allow for synchronous operations.</span></span><br /><br /><span data-ttu-id="45935-328">每 5000 毫秒重试一次。</span><span class="sxs-lookup"><span data-stu-id="45935-328">Retry every 5000 ms.</span></span>|

> [!NOTE]
> <span data-ttu-id="45935-329">对于同步操作，可将 `SyncTimeout` 添加到端到端延迟，但将其值设置过低可能会导致超时时间过长。</span><span class="sxs-lookup"><span data-stu-id="45935-329">For synchronous operations, `SyncTimeout` can add to the end-to-end latency, but setting the value too low can cause excessive timeouts.</span></span> <span data-ttu-id="45935-330">请参阅[如何排查 Azure Redis 缓存问题][redis-cache-troubleshoot]。</span><span class="sxs-lookup"><span data-stu-id="45935-330">See [How to troubleshoot Azure Redis Cache][redis-cache-troubleshoot].</span></span> <span data-ttu-id="45935-331">一般应避免使用同步操作，而改用异步操作。</span><span class="sxs-lookup"><span data-stu-id="45935-331">In general, avoid using synchronous operations, and use asynchronous operations instead.</span></span> <span data-ttu-id="45935-332">有关详细信息，请参阅 [Pipelines and Multiplexers](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md)（管道和多路复用器）。</span><span class="sxs-lookup"><span data-stu-id="45935-332">For more information see [Pipelines and Multiplexers](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).</span></span>
>
>

### <a name="retry-usage-guidance"></a><span data-ttu-id="45935-333">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="45935-333">Retry usage guidance</span></span>
<span data-ttu-id="45935-334">使用 Azure Redis 高速缓存时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="45935-334">Consider the following guidelines when using Azure Redis Cache:</span></span>

* <span data-ttu-id="45935-335">StackExchange Redis 客户端管理自己的重试，但仅限在应用程序首次启动时建立的高速缓存连接。</span><span class="sxs-lookup"><span data-stu-id="45935-335">The StackExchange Redis client manages its own retries, but only when establishing a connection to the cache when the application first starts.</span></span> <span data-ttu-id="45935-336">可以配置连接超时、重试尝试次数以及重试建立此连接间隔的时间，但重试策略不适用于针对缓存执行的操作。</span><span class="sxs-lookup"><span data-stu-id="45935-336">You can configure the connection timeout, the number of retry attempts, and the time between retries to establish this connection, but the retry policy does not apply to operations against the cache.</span></span>
* <span data-ttu-id="45935-337">请考虑改为访问原始数据源进行回退，而不是使用大量重试尝试。</span><span class="sxs-lookup"><span data-stu-id="45935-337">Instead of using a large number of retry attempts, consider falling back by accessing the original data source instead.</span></span>

### <a name="telemetry"></a><span data-ttu-id="45935-338">遥测</span><span class="sxs-lookup"><span data-stu-id="45935-338">Telemetry</span></span>
<span data-ttu-id="45935-339">可以使用 **TextWriter** 收集连接（而不是其他操作）的相关信息。</span><span class="sxs-lookup"><span data-stu-id="45935-339">You can collect information about connections (but not other operations) using a **TextWriter**.</span></span>

```csharp
var writer = new StringWriter();
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="45935-340">生成的输出示例如下所示。</span><span class="sxs-lookup"><span data-stu-id="45935-340">An example of the output this generates is shown below.</span></span>

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

### <a name="examples"></a><span data-ttu-id="45935-341">示例</span><span class="sxs-lookup"><span data-stu-id="45935-341">Examples</span></span>
<span data-ttu-id="45935-342">下面的代码示例配置初始化 StackExchange.Redis 客户端时两次重试间的固定（线性）延迟。</span><span class="sxs-lookup"><span data-stu-id="45935-342">The following code example configures a constant (linear) delay between retries when initializing the StackExchange.Redis client.</span></span> <span data-ttu-id="45935-343">此示例展示如何使用 **ConfigurationOptions** 实例设置配置。</span><span class="sxs-lookup"><span data-stu-id="45935-343">This example shows how to set the configuration using a **ConfigurationOptions** instance.</span></span>

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

<span data-ttu-id="45935-344">下一个示例通过将选项指定为字符串来设置配置。</span><span class="sxs-lookup"><span data-stu-id="45935-344">The next example sets the configuration by specifying the options as a string.</span></span> <span data-ttu-id="45935-345">连接超时是指等待连接缓存的时间上限，不是指两次重试尝试间的延迟。</span><span class="sxs-lookup"><span data-stu-id="45935-345">The connection timeout is the maximum period of time to wait for a connection to the cache, not the delay between retry attempts.</span></span> <span data-ttu-id="45935-346">请注意，**ReconnectRetryPolicy** 属性只能通过代码设置。</span><span class="sxs-lookup"><span data-stu-id="45935-346">Note that the **ReconnectRetryPolicy** property can only be set by code.</span></span>

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

<span data-ttu-id="45935-347">有关更多示例，请参阅项目网站上的[配置](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration)。</span><span class="sxs-lookup"><span data-stu-id="45935-347">For more examples, see [Configuration](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) on the project website.</span></span>

### <a name="more-information"></a><span data-ttu-id="45935-348">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-348">More information</span></span>
* [<span data-ttu-id="45935-349">Redis 网站</span><span class="sxs-lookup"><span data-stu-id="45935-349">Redis website</span></span>](http://redis.io/)

## <a name="azure-search"></a><span data-ttu-id="45935-350">Azure 搜索</span><span class="sxs-lookup"><span data-stu-id="45935-350">Azure Search</span></span>
<span data-ttu-id="45935-351">Azure 搜索可用于向网站或应用程序添加功能强大且复杂的搜索功能、快速轻松地优化搜索结果，并构造大量经过微调的排名模型。</span><span class="sxs-lookup"><span data-stu-id="45935-351">Azure Search can be used to add powerful and sophisticated search capabilities to a website or application, quickly and easily tune search results, and construct rich and fine-tuned ranking models.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-352">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-352">Retry mechanism</span></span>
<span data-ttu-id="45935-353">[SearchServiceClient] 和 [SearchIndexClient] 类上的 `SetRetryPolicy` 方法控制 Azure 搜索 SDK 中的重试行为。</span><span class="sxs-lookup"><span data-stu-id="45935-353">Retry behavior in the Azure Search SDK is controlled by the `SetRetryPolicy` method on the [SearchServiceClient] and [SearchIndexClient] classes.</span></span> <span data-ttu-id="45935-354">Azure 搜索返回 5xx 或 408（请求超时）响应时具有指数回退的默认策略重试次数。</span><span class="sxs-lookup"><span data-stu-id="45935-354">The default policy retries with exponential backoff when Azure Search returns a 5xx or 408 (Request Timeout) response.</span></span>

### <a name="telemetry"></a><span data-ttu-id="45935-355">遥测</span><span class="sxs-lookup"><span data-stu-id="45935-355">Telemetry</span></span>
<span data-ttu-id="45935-356">跟踪 ETW，或通过注册自定义提供程序来实现。</span><span class="sxs-lookup"><span data-stu-id="45935-356">Trace with ETW or by registering a custom trace provider.</span></span> <span data-ttu-id="45935-357">有关详细信息，请参阅 [AutoRest 文档][autorest]。</span><span class="sxs-lookup"><span data-stu-id="45935-357">For more information, see the [AutoRest documentation][autorest].</span></span>

## <a name="service-bus"></a><span data-ttu-id="45935-358">服务总线</span><span class="sxs-lookup"><span data-stu-id="45935-358">Service Bus</span></span>
<span data-ttu-id="45935-359">服务总线是云消息传送平台，可提供松散耦合的消息交换，同时提升应用程序各组件（无论是托管在云中还是在本地）的规模和复原能力。</span><span class="sxs-lookup"><span data-stu-id="45935-359">Service Bus is a cloud messaging platform that provides loosely coupled message exchange with improved scale and resiliency for components of an application, whether hosted in the cloud or on-premises.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-360">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-360">Retry mechanism</span></span>
<span data-ttu-id="45935-361">服务总线通过 [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) 基类的实现来实现重试。</span><span class="sxs-lookup"><span data-stu-id="45935-361">Service Bus implements retries using implementations of the [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) base class.</span></span> <span data-ttu-id="45935-362">所有服务总线客户端均公开 **RetryPolicy** 属性，此属性可以设置为 **RetryPolicy** 基类的一个实现。</span><span class="sxs-lookup"><span data-stu-id="45935-362">All of the Service Bus clients expose a **RetryPolicy** property that can be set to one of the implementations of the **RetryPolicy** base class.</span></span> <span data-ttu-id="45935-363">内置实现为：</span><span class="sxs-lookup"><span data-stu-id="45935-363">The built-in implementations are:</span></span>

* <span data-ttu-id="45935-364">[RetryExponential 类](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-364">The [RetryExponential Class](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx).</span></span> <span data-ttu-id="45935-365">这会公开用于控制回退间隔和重试计数的属性，以及用于限制操作完成总时间的 **TerminationTimeBuffer** 属性。</span><span class="sxs-lookup"><span data-stu-id="45935-365">This exposes properties that control the back-off interval, the retry count, and the **TerminationTimeBuffer** property that is used to limit the total time for the operation to complete.</span></span>
* <span data-ttu-id="45935-366">[NoRetry 类](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-366">The [NoRetry Class](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx).</span></span> <span data-ttu-id="45935-367">这适用于不需要在服务总线 API 一级进行重试的情况，如其他进程将重试作为批处理或多步操作的一部分进行管理的情况。</span><span class="sxs-lookup"><span data-stu-id="45935-367">This is used when retries at the Service Bus API level are not required, such as when retries are managed by another process as part of a batch or multiple step operation.</span></span>

<span data-ttu-id="45935-368">服务总线操作可能返回一系列异常，如[服务总线消息传送异常](/azure/service-bus-messaging/service-bus-messaging-exceptions)中所列。</span><span class="sxs-lookup"><span data-stu-id="45935-368">Service Bus actions can return a range of exceptions, as listed in [Service Bus messaging exceptions](/azure/service-bus-messaging/service-bus-messaging-exceptions).</span></span> <span data-ttu-id="45935-369">此列表提供了一些信息来指明重试操作是否合适。</span><span class="sxs-lookup"><span data-stu-id="45935-369">The list provides information about which if these indicate that retrying the operation is appropriate.</span></span> <span data-ttu-id="45935-370">例如，**ServerBusyException** 指明客户端应等待一段时间，并重试操作。</span><span class="sxs-lookup"><span data-stu-id="45935-370">For example, a **ServerBusyException** indicates that the client should wait for a period of time, then retry the operation.</span></span> <span data-ttu-id="45935-371">**ServerBusyException** 的出现还会导致服务总线切换到不同模式，这会令计算的重试延迟额外增加 10 秒。</span><span class="sxs-lookup"><span data-stu-id="45935-371">The occurrence of a **ServerBusyException** also causes Service Bus to switch to a different mode, in which an extra 10-second delay is added to the computed retry delays.</span></span> <span data-ttu-id="45935-372">在一段很短的时间后，此模式会重置。</span><span class="sxs-lookup"><span data-stu-id="45935-372">This mode is reset after a short period.</span></span>

<span data-ttu-id="45935-373">从服务总线返回的异常会公开 **IsTransient** 属性，此属性指明客户端是否应重试操作。</span><span class="sxs-lookup"><span data-stu-id="45935-373">The exceptions returned from Service Bus expose the **IsTransient** property that indicates if the client should retry the operation.</span></span> <span data-ttu-id="45935-374">内置的 **RetryExponential** 策略依赖于 **MessagingException** 类（所有服务总线异常的基类）中的 **IsTransient** 属性。</span><span class="sxs-lookup"><span data-stu-id="45935-374">The built-in **RetryExponential** policy relies on the **IsTransient** property in the **MessagingException** class, which is the base class for all Service Bus exceptions.</span></span> <span data-ttu-id="45935-375">如果创建 **RetryPolicy** 基类的自定义实现，则可以结合使用异常类型和 **IsTransient** 属性来更精细地控制重试操作。</span><span class="sxs-lookup"><span data-stu-id="45935-375">If you create custom implementations of the **RetryPolicy** base class you could use a combination of the exception type and the **IsTransient** property to provide more fine-grained control over retry actions.</span></span> <span data-ttu-id="45935-376">例如，可以检测 **QuotaExceededException**，并采取措施以在重试向其发送消息之前清空队列。</span><span class="sxs-lookup"><span data-stu-id="45935-376">For example, you could detect a **QuotaExceededException** and take action to drain the queue before retrying sending a message to it.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="45935-377">策略配置</span><span class="sxs-lookup"><span data-stu-id="45935-377">Policy configuration</span></span>
<span data-ttu-id="45935-378">重试策略以编程方式进行设置，可以设置为 **NamespaceManager** 和 **MessagingFactory** 的默认策略，也可以针对每个消息客户端进行单独设置。</span><span class="sxs-lookup"><span data-stu-id="45935-378">Retry policies are set programmatically, and can be set as a default policy for a **NamespaceManager** and for a **MessagingFactory**, or individually for each messaging client.</span></span> <span data-ttu-id="45935-379">若要设置消息会话的默认重试策略 ，请设置 **NamespaceManager** 的 **RetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="45935-379">To set the default retry policy for a messaging session you set the **RetryPolicy** of the **NamespaceManager**.</span></span>

```csharp
namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                                maxBackoff: TimeSpan.FromSeconds(30),
                                                                maxRetryCount: 3);
```

<span data-ttu-id="45935-380">若要为通过消息工厂创建的所有客户端设置默认重试策略，请设置 **MessagingFactory** 的 **RetryPolicy**。</span><span class="sxs-lookup"><span data-stu-id="45935-380">To set the default retry policy for all clients created from a messaging factory, you set the **RetryPolicy** of the **MessagingFactory**.</span></span>

```csharp
messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                    maxBackoff: TimeSpan.FromSeconds(30),
                                                    maxRetryCount: 3);
```

<span data-ttu-id="45935-381">若要设置消息客户端的重试策略，或替代其默认策略，请使用相应策略类的实例设置其 **RetryPolicy** 属性：</span><span class="sxs-lookup"><span data-stu-id="45935-381">To set the retry policy for a messaging client, or to override its default policy, you set its **RetryPolicy** property using an instance of the required policy class:</span></span>

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                            maxBackoff: TimeSpan.FromSeconds(30),
                                            maxRetryCount: 3);
```

<span data-ttu-id="45935-382">不能在各个操作级别设置重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-382">The retry policy cannot be set at the individual operation level.</span></span> <span data-ttu-id="45935-383">它适用于消息客户端的所有操作。</span><span class="sxs-lookup"><span data-stu-id="45935-383">It applies to all operations for the messaging client.</span></span>
<span data-ttu-id="45935-384">下表显示了内置重试策略的默认设置。</span><span class="sxs-lookup"><span data-stu-id="45935-384">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="45935-385">设置</span><span class="sxs-lookup"><span data-stu-id="45935-385">Setting</span></span> | <span data-ttu-id="45935-386">默认值</span><span class="sxs-lookup"><span data-stu-id="45935-386">Default value</span></span> | <span data-ttu-id="45935-387">含义</span><span class="sxs-lookup"><span data-stu-id="45935-387">Meaning</span></span> |
|---------|---------------|---------|
| <span data-ttu-id="45935-388">策略</span><span class="sxs-lookup"><span data-stu-id="45935-388">Policy</span></span> | <span data-ttu-id="45935-389">指数</span><span class="sxs-lookup"><span data-stu-id="45935-389">Exponential</span></span> | <span data-ttu-id="45935-390">指数退让。</span><span class="sxs-lookup"><span data-stu-id="45935-390">Exponential back-off.</span></span> |
| <span data-ttu-id="45935-391">MinimalBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-391">MinimalBackoff</span></span> | <span data-ttu-id="45935-392">0</span><span class="sxs-lookup"><span data-stu-id="45935-392">0</span></span> | <span data-ttu-id="45935-393">最小退让间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-393">Minimum back-off interval.</span></span> <span data-ttu-id="45935-394">此值将与通过 deltaBackoff 计算得出的重试间隔相加。</span><span class="sxs-lookup"><span data-stu-id="45935-394">This is added to the retry interval computed from deltaBackoff.</span></span> |
| <span data-ttu-id="45935-395">MaximumBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-395">MaximumBackoff</span></span> | <span data-ttu-id="45935-396">30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-396">30 seconds</span></span> | <span data-ttu-id="45935-397">最大退让间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-397">Maximum back-off interval.</span></span> <span data-ttu-id="45935-398">如果计算出的重试间隔大于 MaxBackoff，则使用 MaximumBackoff。</span><span class="sxs-lookup"><span data-stu-id="45935-398">MaximumBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> |
| <span data-ttu-id="45935-399">DeltaBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-399">DeltaBackoff</span></span> | <span data-ttu-id="45935-400">3 秒</span><span class="sxs-lookup"><span data-stu-id="45935-400">3 seconds</span></span> | <span data-ttu-id="45935-401">不同重试之间的回退时间间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-401">Back-off interval between retries.</span></span> <span data-ttu-id="45935-402">将针对后续的重试使用多个这样的时间跨度。</span><span class="sxs-lookup"><span data-stu-id="45935-402">Multiples of this timespan will be used for subsequent retry attempts.</span></span> |
| <span data-ttu-id="45935-403">TimeBuffer</span><span class="sxs-lookup"><span data-stu-id="45935-403">TimeBuffer</span></span> | <span data-ttu-id="45935-404">5 秒</span><span class="sxs-lookup"><span data-stu-id="45935-404">5 seconds</span></span> | <span data-ttu-id="45935-405">与重试关联的终止时间缓冲区。</span><span class="sxs-lookup"><span data-stu-id="45935-405">The termination time buffer associated with the retry.</span></span> <span data-ttu-id="45935-406">如果剩余时间小于 TimeBuffer，将放弃重试。</span><span class="sxs-lookup"><span data-stu-id="45935-406">Retry attempts will be abandoned if the remaining time is less than TimeBuffer.</span></span> |
| <span data-ttu-id="45935-407">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="45935-407">MaxRetryCount</span></span> | <span data-ttu-id="45935-408">10</span><span class="sxs-lookup"><span data-stu-id="45935-408">10</span></span> | <span data-ttu-id="45935-409">最大重试次数。</span><span class="sxs-lookup"><span data-stu-id="45935-409">The maximum number of retries.</span></span> |
| <span data-ttu-id="45935-410">ServerBusyBaseSleepTime</span><span class="sxs-lookup"><span data-stu-id="45935-410">ServerBusyBaseSleepTime</span></span> | <span data-ttu-id="45935-411">10 秒</span><span class="sxs-lookup"><span data-stu-id="45935-411">10 seconds</span></span> | <span data-ttu-id="45935-412">如果遇到的最后一个异常为 **ServerBusyException**，则将此值与计算出的重试间隔相加。</span><span class="sxs-lookup"><span data-stu-id="45935-412">If the last exception encountered was **ServerBusyException**, this value will be added to the computed retry interval.</span></span> <span data-ttu-id="45935-413">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="45935-413">This value cannot be changed.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="45935-414">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="45935-414">Retry usage guidance</span></span>
<span data-ttu-id="45935-415">使用服务总线时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="45935-415">Consider the following guidelines when using Service Bus:</span></span>

* <span data-ttu-id="45935-416">使用内置 **RetryExponential** 实现时，请勿将回退操作实现为响应服务器忙异常和自动切换到适合重试模式的策略。</span><span class="sxs-lookup"><span data-stu-id="45935-416">When using the built-in **RetryExponential** implementation, do not implement a fallback operation as the policy reacts to Server Busy exceptions and automatically switches to an appropriate retry mode.</span></span>
* <span data-ttu-id="45935-417">服务总线支持配对的命名空间这一功能。如果主要命名空间中的队列失败，则此功能会实现自动故障转移到单独命名空间中的备份队列。</span><span class="sxs-lookup"><span data-stu-id="45935-417">Service Bus supports a feature called Paired Namespaces, which implements automatic failover to a backup queue in a separate namespace if the queue in the primary namespace fails.</span></span> <span data-ttu-id="45935-418">在主要队列恢复后，来自辅助队列的消息可以发送回主要队列。</span><span class="sxs-lookup"><span data-stu-id="45935-418">Messages from the secondary queue can be sent back to the primary queue when it recovers.</span></span> <span data-ttu-id="45935-419">此功能有助于解决临时故障。</span><span class="sxs-lookup"><span data-stu-id="45935-419">This feature helps to address transient failures.</span></span> <span data-ttu-id="45935-420">有关详细信息，请参阅[异步消息传送模式和高可用性](http://msdn.microsoft.com/library/azure/dn292562.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-420">For more information, see [Asynchronous Messaging Patterns and High Availability](http://msdn.microsoft.com/library/azure/dn292562.aspx).</span></span>

<span data-ttu-id="45935-421">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="45935-421">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="45935-422">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="45935-422">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="45935-423">上下文</span><span class="sxs-lookup"><span data-stu-id="45935-423">Context</span></span> | <span data-ttu-id="45935-424">示例最大延迟</span><span class="sxs-lookup"><span data-stu-id="45935-424">Example maximum latency</span></span> | <span data-ttu-id="45935-425">重试策略</span><span class="sxs-lookup"><span data-stu-id="45935-425">Retry policy</span></span> | <span data-ttu-id="45935-426">设置</span><span class="sxs-lookup"><span data-stu-id="45935-426">Settings</span></span> | <span data-ttu-id="45935-427">工作原理</span><span class="sxs-lookup"><span data-stu-id="45935-427">How it works</span></span> |
|---------|---------|---------|---------|---------|
| <span data-ttu-id="45935-428">交互、UI 或前台</span><span class="sxs-lookup"><span data-stu-id="45935-428">Interactive, UI, or foreground</span></span> | <span data-ttu-id="45935-429">2 秒\*</span><span class="sxs-lookup"><span data-stu-id="45935-429">2 seconds\*</span></span>  | <span data-ttu-id="45935-430">指数</span><span class="sxs-lookup"><span data-stu-id="45935-430">Exponential</span></span> | <span data-ttu-id="45935-431">MinimumBackoff = 0</span><span class="sxs-lookup"><span data-stu-id="45935-431">MinimumBackoff = 0</span></span> <br/> <span data-ttu-id="45935-432">MaximumBackoff = 30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-432">MaximumBackoff = 30 sec.</span></span> <br/> <span data-ttu-id="45935-433">DeltaBackoff = 300 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-433">DeltaBackoff = 300 msec.</span></span> <br/> <span data-ttu-id="45935-434">TimeBuffer = 300 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-434">TimeBuffer = 300 msec.</span></span> <br/> <span data-ttu-id="45935-435">MaxRetryCount = 2</span><span class="sxs-lookup"><span data-stu-id="45935-435">MaxRetryCount = 2</span></span> | <span data-ttu-id="45935-436">第 1 次尝试：延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="45935-436">Attempt 1: Delay 0 sec.</span></span> <br/> <span data-ttu-id="45935-437">第 2 次尝试：延迟约 300 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-437">Attempt 2: Delay ~300 msec.</span></span> <br/> <span data-ttu-id="45935-438">第 3 次尝试：延迟约 900 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-438">Attempt 3: Delay ~900 msec.</span></span> |
| <span data-ttu-id="45935-439">后台或批处理</span><span class="sxs-lookup"><span data-stu-id="45935-439">Background or batch</span></span> | <span data-ttu-id="45935-440">30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-440">30 seconds</span></span> | <span data-ttu-id="45935-441">指数</span><span class="sxs-lookup"><span data-stu-id="45935-441">Exponential</span></span> | <span data-ttu-id="45935-442">MinimumBackoff = 1</span><span class="sxs-lookup"><span data-stu-id="45935-442">MinimumBackoff = 1</span></span> <br/> <span data-ttu-id="45935-443">MaximumBackoff = 30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-443">MaximumBackoff = 30 sec.</span></span> <br/> <span data-ttu-id="45935-444">DeltaBackoff = 1.75 秒</span><span class="sxs-lookup"><span data-stu-id="45935-444">DeltaBackoff = 1.75 sec.</span></span> <br/> <span data-ttu-id="45935-445">TimeBuffer = 5 秒</span><span class="sxs-lookup"><span data-stu-id="45935-445">TimeBuffer = 5 sec.</span></span> <br/> <span data-ttu-id="45935-446">MaxRetryCount = 3</span><span class="sxs-lookup"><span data-stu-id="45935-446">MaxRetryCount = 3</span></span> | <span data-ttu-id="45935-447">第 1 次尝试：延迟约 1 秒</span><span class="sxs-lookup"><span data-stu-id="45935-447">Attempt 1: Delay ~1 sec.</span></span> <br/> <span data-ttu-id="45935-448">第 2 次尝试：延迟约 3 秒</span><span class="sxs-lookup"><span data-stu-id="45935-448">Attempt 2: Delay ~3 sec.</span></span> <br/> <span data-ttu-id="45935-449">第 3 次尝试：延迟约 6 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-449">Attempt 3: Delay ~6 msec.</span></span> <br/> <span data-ttu-id="45935-450">第 4 次尝试：延迟约 13 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-450">Attempt 4: Delay ~13 msec.</span></span> |

<span data-ttu-id="45935-451">\* 不包括收到“服务器忙”响应时增加的附加延迟。</span><span class="sxs-lookup"><span data-stu-id="45935-451">\* Not including additional delay that is added if a Server Busy response is received.</span></span>

### <a name="telemetry"></a><span data-ttu-id="45935-452">遥测</span><span class="sxs-lookup"><span data-stu-id="45935-452">Telemetry</span></span>
<span data-ttu-id="45935-453">服务总线使用 **EventSource** 将重试记录为 ETW 事件。</span><span class="sxs-lookup"><span data-stu-id="45935-453">Service Bus logs retries as ETW events using an **EventSource**.</span></span> <span data-ttu-id="45935-454">必须将 **EventListener** 附加到事件源，才能捕获事件并在性能查看器中查看这些事件，或将这些事件写入合适的目标日志。</span><span class="sxs-lookup"><span data-stu-id="45935-454">You must attach an **EventListener** to the event source to capture the events and view them in Performance Viewer, or write them to a suitable destination log.</span></span> <span data-ttu-id="45935-455">重试事件具有以下形式：</span><span class="sxs-lookup"><span data-stu-id="45935-455">The retry events are of the following form:</span></span>

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

### <a name="examples"></a><span data-ttu-id="45935-456">示例</span><span class="sxs-lookup"><span data-stu-id="45935-456">Examples</span></span>
<span data-ttu-id="45935-457">以下代码示例展示了如何为下列对象设置重试策略：</span><span class="sxs-lookup"><span data-stu-id="45935-457">The following code example shows how to set the retry policy for:</span></span>

* <span data-ttu-id="45935-458">命名空间管理器：</span><span class="sxs-lookup"><span data-stu-id="45935-458">A namespace manager.</span></span> <span data-ttu-id="45935-459">重试策略适用于此管理器上的所有操作，不能针对各个操作进行替代。</span><span class="sxs-lookup"><span data-stu-id="45935-459">The policy applies to all operations on that manager, and cannot be overridden for individual operations.</span></span>
* <span data-ttu-id="45935-460">消息工厂：</span><span class="sxs-lookup"><span data-stu-id="45935-460">A messaging factory.</span></span> <span data-ttu-id="45935-461">重试策略适用于通过此工厂创建的所有客户端，不能在创建各个客户端时进行替代。</span><span class="sxs-lookup"><span data-stu-id="45935-461">The policy applies to all clients created from that factory, and cannot be overridden when creating individual clients.</span></span>
* <span data-ttu-id="45935-462">单个消息客户端：</span><span class="sxs-lookup"><span data-stu-id="45935-462">An individual messaging client.</span></span> <span data-ttu-id="45935-463">在创建客户端后，可以为此客户端设置重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-463">After a client has been created, you can set the retry policy for that client.</span></span> <span data-ttu-id="45935-464">重试策略适用于此客户端上的所有操作。</span><span class="sxs-lookup"><span data-stu-id="45935-464">The policy applies to all operations on that client.</span></span>

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

### <a name="more-information"></a><span data-ttu-id="45935-465">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-465">More information</span></span>
* [<span data-ttu-id="45935-466">异步消息传送模式和高可用性</span><span class="sxs-lookup"><span data-stu-id="45935-466">Asynchronous Messaging Patterns and High Availability</span></span>](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## <a name="service-fabric"></a><span data-ttu-id="45935-467">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="45935-467">Service Fabric</span></span>

<span data-ttu-id="45935-468">在 Service Fabric 群集中分布可靠服务可防止出现本文描述的大部分潜在暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="45935-468">Distributing reliable services in a Service Fabric cluster guards against most of the potential transient faults discussed in this article.</span></span> <span data-ttu-id="45935-469">但是，某些暂时性错误仍有可能发生。</span><span class="sxs-lookup"><span data-stu-id="45935-469">Some transient faults are still possible, however.</span></span> <span data-ttu-id="45935-470">例如，命名服务收到请求时可能正在进行路由更改，从而导致它引发异常。</span><span class="sxs-lookup"><span data-stu-id="45935-470">For example, the naming service might be in the middle of a routing change when it gets a request, causing it to throw an exception.</span></span> <span data-ttu-id="45935-471">如果同一请求在 100 毫秒后发出，则有可能成功。</span><span class="sxs-lookup"><span data-stu-id="45935-471">If the same request comes 100 milliseconds later, it will probably succeed.</span></span>

<span data-ttu-id="45935-472">Service Fabric 可在内部管理这种暂时性错误。</span><span class="sxs-lookup"><span data-stu-id="45935-472">Internally, Service Fabric manages this kind of transient fault.</span></span> <span data-ttu-id="45935-473">你可以在设置服务时使用 `OperationRetrySettings` 类配置某些设置。</span><span class="sxs-lookup"><span data-stu-id="45935-473">You can configure some settings by using the `OperationRetrySettings` class while setting up your services.</span></span>  <span data-ttu-id="45935-474">以下代码展示了一个示例。</span><span class="sxs-lookup"><span data-stu-id="45935-474">The following code shows an example.</span></span> <span data-ttu-id="45935-475">在大多数情况下，不必执行此操作，直接使用默认设置即可。</span><span class="sxs-lookup"><span data-stu-id="45935-475">In most cases, this should not be necessary, and the default settings will be fine.</span></span>

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

### <a name="more-information"></a><span data-ttu-id="45935-476">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-476">More information</span></span>

* [<span data-ttu-id="45935-477">远程异常处理</span><span class="sxs-lookup"><span data-stu-id="45935-477">Remote Exception Handling</span></span>](https://github.com/Microsoft/azure-docs/blob/master/articles/service-fabric/service-fabric-reliable-services-communication-remoting.md#remoting-exception-handling)

## <a name="sql-database-using-adonet"></a><span data-ttu-id="45935-478">使用 ADO.NET 的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="45935-478">SQL Database using ADO.NET</span></span>
<span data-ttu-id="45935-479">SQL 数据库是一种托管的 SQL 数据库，具有各种大小，可作为标准（共享）和高级（非共享）服务提供。</span><span class="sxs-lookup"><span data-stu-id="45935-479">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-480">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-480">Retry mechanism</span></span>
<span data-ttu-id="45935-481">访问使用 ADO.NET 的 SQL 数据库时，其中没有内置重试支持。</span><span class="sxs-lookup"><span data-stu-id="45935-481">SQL Database has no built-in support for retries when accessed using ADO.NET.</span></span> <span data-ttu-id="45935-482">不过，请求的返回代码可用于确定请求失败原因。</span><span class="sxs-lookup"><span data-stu-id="45935-482">However, the return codes from requests can be used to determine why a request failed.</span></span> <span data-ttu-id="45935-483">有关 SQL 数据库限制的详细信息，请参阅 [Azure SQL 数据库资源限制](/azure/sql-database/sql-database-resource-limits)。</span><span class="sxs-lookup"><span data-stu-id="45935-483">For more information about SQL Database throttling, see [Azure SQL Database resource limits](/azure/sql-database/sql-database-resource-limits).</span></span> <span data-ttu-id="45935-484">有关相关错误代码的列表，请参阅 [SQL 数据库客户端应用程序的 SQL 错误代码](/azure/sql-database/sql-database-develop-error-messages)。</span><span class="sxs-lookup"><span data-stu-id="45935-484">For a list of relevant error codes, see [SQL error codes for SQL Database client applications](/azure/sql-database/sql-database-develop-error-messages).</span></span>

<span data-ttu-id="45935-485">可使用 Polly 库为 SQL 数据库实现重试。</span><span class="sxs-lookup"><span data-stu-id="45935-485">You can use the Polly library to implement retries for SQL Database.</span></span> <span data-ttu-id="45935-486">请参阅[使用 Polly 处理暂时性错误](#transient-fault-handling-with-polly)。</span><span class="sxs-lookup"><span data-stu-id="45935-486">See [Transient fault handling with Polly](#transient-fault-handling-with-polly).</span></span>

### <a name="retry-usage-guidance"></a><span data-ttu-id="45935-487">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="45935-487">Retry usage guidance</span></span>
<span data-ttu-id="45935-488">访问使用 ADO.NET 的 SQL 数据库时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="45935-488">Consider the following guidelines when accessing SQL Database using ADO.NET:</span></span>

* <span data-ttu-id="45935-489">选择适当的服务选项（共享或高级）。</span><span class="sxs-lookup"><span data-stu-id="45935-489">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="45935-490">鉴于共享服务器的其他租户的使用情况，共享实例的连接延迟和限制可能比平常要长。</span><span class="sxs-lookup"><span data-stu-id="45935-490">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="45935-491">如果需要更加可预测的性能和可靠的低延迟操作，请考虑选择高级选项。</span><span class="sxs-lookup"><span data-stu-id="45935-491">If more predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="45935-492">请务必在适当的级别或作用域执行重试，以避免出现导致数据不一致的非幂等操作。</span><span class="sxs-lookup"><span data-stu-id="45935-492">Ensure that you perform retries at the appropriate level or scope to avoid non-idempotent operations causing inconsistency in the data.</span></span> <span data-ttu-id="45935-493">理想情况下，所有操作都应是幂等操作，这样就可以重复执行，而不会导致不一致。</span><span class="sxs-lookup"><span data-stu-id="45935-493">Ideally, all operations should be idempotent so that they can be repeated without causing inconsistency.</span></span> <span data-ttu-id="45935-494">如果并非如此，应在符合以下条件的级别或作用域执行重试：在一个操作失败时允许撤消所有相关更改；例如，从事务作用域内。</span><span class="sxs-lookup"><span data-stu-id="45935-494">Where this is not the case, the retry should be performed at a level or scope that allows all related changes to be undone if one operation fails; for example, from within a transactional scope.</span></span> <span data-ttu-id="45935-495">有关详细信息，请参阅[云服务基础数据访问层 - 临时故障处理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee)。</span><span class="sxs-lookup"><span data-stu-id="45935-495">For more information, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).</span></span>
* <span data-ttu-id="45935-496">不建议结合使用固定间隔策略和 Azure SQL 数据库（交互式方案除外，在此类方案中，仅有几次重试的间隔非常短）。</span><span class="sxs-lookup"><span data-stu-id="45935-496">A fixed interval strategy is not recommended for use with Azure SQL Database except for interactive scenarios where there are only a few retries at very short intervals.</span></span> <span data-ttu-id="45935-497">对于大多数方案，请考虑使用指数回退策略。</span><span class="sxs-lookup"><span data-stu-id="45935-497">Instead, consider using an exponential back-off strategy for the majority of scenarios.</span></span>
* <span data-ttu-id="45935-498">定义连接时，为连接和命令超时选择合适的值。</span><span class="sxs-lookup"><span data-stu-id="45935-498">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="45935-499">当数据库忙碌时，超时太短可能会导致连接过早失败。</span><span class="sxs-lookup"><span data-stu-id="45935-499">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="45935-500">超时太长则会导致在检测到失败的连接前等待太久，进而可能会妨碍重试逻辑正常运行。</span><span class="sxs-lookup"><span data-stu-id="45935-500">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="45935-501">超时值是端到端延迟的组成部分；它可以有效地增加每次重试尝试的重试策略中指定的重试延迟。</span><span class="sxs-lookup"><span data-stu-id="45935-501">The value of the timeout is a component of the end-to-end latency; it is effectively added to the retry delay specified in the retry policy for every retry attempt.</span></span>
* <span data-ttu-id="45935-502">重试一定次数后关闭连接（即使使用指数回退重试逻辑，也是如此），并在新连接上重试操作。</span><span class="sxs-lookup"><span data-stu-id="45935-502">Close the connection after a certain number of retries, even when using an exponential back off retry logic, and retry the operation on a new connection.</span></span> <span data-ttu-id="45935-503">在同一个连接上多次重试相同的操作可能是导致连接问题出现的一个因素。</span><span class="sxs-lookup"><span data-stu-id="45935-503">Retrying the same operation multiple times on the same connection can be a factor that contributes to connection problems.</span></span> <span data-ttu-id="45935-504">有关此技术的示例，请参阅[云服务基础数据访问层 - 临时故障处理](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-504">For an example of this technique, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).</span></span>
* <span data-ttu-id="45935-505">当连接池正在使用中（默认情况）时，可能会从池中选择相同的连接，即使在关闭并重新打开连接后，也可能会出现这样的情况。</span><span class="sxs-lookup"><span data-stu-id="45935-505">When connection pooling is in use (the default) there is a chance that the same connection will be chosen from the pool, even after closing and reopening a connection.</span></span> <span data-ttu-id="45935-506">如果遇到这种情况，解决方法是调用 **SqlConnection** 类的 **ClearPool** 方法，将连接标记为不可重用。</span><span class="sxs-lookup"><span data-stu-id="45935-506">If this is the case, a technique to resolve it is to call the **ClearPool** method of the **SqlConnection** class to mark the connection as not reusable.</span></span> <span data-ttu-id="45935-507">不过，仅在几次连接尝试均失败，且遇到与失败的连接相关的某类临时故障（如 SQL 超时（错误代码 -2））时，才能这样做。</span><span class="sxs-lookup"><span data-stu-id="45935-507">However, you should do this only after several connection attempts have failed, and only when encountering the specific class of transient failures such as SQL timeouts (error code -2) related to faulty connections.</span></span>
* <span data-ttu-id="45935-508">如果数据访问代码使用作为 **TransactionScope** 实例启动的事务，则重试逻辑应重新打开连接，并启动新的事务作用域。</span><span class="sxs-lookup"><span data-stu-id="45935-508">If the data access code uses transactions initiated as **TransactionScope** instances, the retry logic should reopen the connection and initiate a new transaction scope.</span></span> <span data-ttu-id="45935-509">因此，可重试代码块应包含事务的整个作用域。</span><span class="sxs-lookup"><span data-stu-id="45935-509">For this reason, the retryable code block should encompass the entire scope of the transaction.</span></span>

<span data-ttu-id="45935-510">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="45935-510">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="45935-511">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="45935-511">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="45935-512">**上下文**</span><span class="sxs-lookup"><span data-stu-id="45935-512">**Context**</span></span> | <span data-ttu-id="45935-513">**示例目标 E2E<br />最长延迟**</span><span class="sxs-lookup"><span data-stu-id="45935-513">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="45935-514">**重试策略**</span><span class="sxs-lookup"><span data-stu-id="45935-514">**Retry strategy**</span></span> | <span data-ttu-id="45935-515">**设置**</span><span class="sxs-lookup"><span data-stu-id="45935-515">**Settings**</span></span> | <span data-ttu-id="45935-516">**值**</span><span class="sxs-lookup"><span data-stu-id="45935-516">**Values**</span></span> | <span data-ttu-id="45935-517">**工作原理**</span><span class="sxs-lookup"><span data-stu-id="45935-517">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="45935-518">交互式, UI,</span><span class="sxs-lookup"><span data-stu-id="45935-518">Interactive, UI,</span></span><br /><span data-ttu-id="45935-519">或 foreground</span><span class="sxs-lookup"><span data-stu-id="45935-519">or foreground</span></span> |<span data-ttu-id="45935-520">2 秒</span><span class="sxs-lookup"><span data-stu-id="45935-520">2 sec</span></span> |<span data-ttu-id="45935-521">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="45935-521">FixedInterval</span></span> |<span data-ttu-id="45935-522">重试计数</span><span class="sxs-lookup"><span data-stu-id="45935-522">Retry count</span></span><br /><span data-ttu-id="45935-523">重试间隔</span><span class="sxs-lookup"><span data-stu-id="45935-523">Retry interval</span></span><br /><span data-ttu-id="45935-524">首次快速重试</span><span class="sxs-lookup"><span data-stu-id="45935-524">First fast retry</span></span> |<span data-ttu-id="45935-525">3</span><span class="sxs-lookup"><span data-stu-id="45935-525">3</span></span><br /><span data-ttu-id="45935-526">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-526">500 ms</span></span><br /><span data-ttu-id="45935-527">是</span><span class="sxs-lookup"><span data-stu-id="45935-527">true</span></span> |<span data-ttu-id="45935-528">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="45935-528">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="45935-529">第 2 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-529">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="45935-530">第 3 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-530">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="45935-531">背景</span><span class="sxs-lookup"><span data-stu-id="45935-531">Background</span></span><br /><span data-ttu-id="45935-532">或批处理</span><span class="sxs-lookup"><span data-stu-id="45935-532">or batch</span></span> |<span data-ttu-id="45935-533">30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-533">30 sec</span></span> |<span data-ttu-id="45935-534">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-534">ExponentialBackoff</span></span> |<span data-ttu-id="45935-535">重试计数</span><span class="sxs-lookup"><span data-stu-id="45935-535">Retry count</span></span><br /><span data-ttu-id="45935-536">最小回退</span><span class="sxs-lookup"><span data-stu-id="45935-536">Min back-off</span></span><br /><span data-ttu-id="45935-537">最大回退</span><span class="sxs-lookup"><span data-stu-id="45935-537">Max back-off</span></span><br /><span data-ttu-id="45935-538">增量回退</span><span class="sxs-lookup"><span data-stu-id="45935-538">Delta back-off</span></span><br /><span data-ttu-id="45935-539">首次快速重试</span><span class="sxs-lookup"><span data-stu-id="45935-539">First fast retry</span></span> |<span data-ttu-id="45935-540">5</span><span class="sxs-lookup"><span data-stu-id="45935-540">5</span></span><br /><span data-ttu-id="45935-541">0 秒</span><span class="sxs-lookup"><span data-stu-id="45935-541">0 sec</span></span><br /><span data-ttu-id="45935-542">60 秒</span><span class="sxs-lookup"><span data-stu-id="45935-542">60 sec</span></span><br /><span data-ttu-id="45935-543">2 秒</span><span class="sxs-lookup"><span data-stu-id="45935-543">2 sec</span></span><br /><span data-ttu-id="45935-544">false</span><span class="sxs-lookup"><span data-stu-id="45935-544">false</span></span> |<span data-ttu-id="45935-545">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="45935-545">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="45935-546">第 2 次尝试 - 约延迟 2 秒</span><span class="sxs-lookup"><span data-stu-id="45935-546">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="45935-547">第 3 次尝试 - 约延迟 6 秒</span><span class="sxs-lookup"><span data-stu-id="45935-547">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="45935-548">第 4 次尝试 - 约延迟 14 秒</span><span class="sxs-lookup"><span data-stu-id="45935-548">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="45935-549">第 5 次尝试 - 约延迟 30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-549">Attempt 5 - delay ~30 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="45935-550">端到端延迟目标假设采用服务连接的默认超时。</span><span class="sxs-lookup"><span data-stu-id="45935-550">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="45935-551">如果指定更长的连接超时，则每次重试尝试都会将端到端延迟延长这一附加时间。</span><span class="sxs-lookup"><span data-stu-id="45935-551">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="45935-552">示例</span><span class="sxs-lookup"><span data-stu-id="45935-552">Examples</span></span>
<span data-ttu-id="45935-553">本部分说明如何使用 Polly 通过在 `Policy` 类中配置的一组重试策略访问 Azure SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="45935-553">This section shows how you can use Polly to access Azure SQL Database using a set of retry policies configured in the `Policy` class.</span></span>

<span data-ttu-id="45935-554">以下代码展示了 `SqlCommand` 类上的扩展方法，该方法调用具有指数回退的 `ExecuteAsync`。</span><span class="sxs-lookup"><span data-stu-id="45935-554">The following code shows an extension method on the `SqlCommand` class that calls `ExecuteAsync` with exponential backoff.</span></span>

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

<span data-ttu-id="45935-555">可按以下方式使用此异步扩展方法。</span><span class="sxs-lookup"><span data-stu-id="45935-555">This asynchronous extension method can be used as follows.</span></span>

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync())
{
    // Do something with the values
}
```

### <a name="more-information"></a><span data-ttu-id="45935-556">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-556">More information</span></span>
* [<span data-ttu-id="45935-557">云服务基础数据访问层 - 临时故障处理</span><span class="sxs-lookup"><span data-stu-id="45935-557">Cloud Service Fundamentals Data Access Layer – Transient Fault Handling</span></span>](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

<span data-ttu-id="45935-558">有关如何充分利用 SQL 数据库的一般指南，请参阅 [Azure SQL 数据库性能和弹性指南](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-558">For general guidance on getting the most from SQL Database, see [Azure SQL Database Performance and Elasticity Guide](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx).</span></span>

## <a name="sql-database-using-entity-framework-6"></a><span data-ttu-id="45935-559">使用 Entity Framework 6 的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="45935-559">SQL Database using Entity Framework 6</span></span>
<span data-ttu-id="45935-560">SQL 数据库是一种托管的 SQL 数据库，具有各种大小，可作为标准（共享）和高级（非共享）服务提供。</span><span class="sxs-lookup"><span data-stu-id="45935-560">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span> <span data-ttu-id="45935-561">Entity Framework 是一种对象关系映射程序，可方便 .NET 开发人员使用域特定对象处理关系数据。</span><span class="sxs-lookup"><span data-stu-id="45935-561">Entity Framework is an object-relational mapper that enables .NET developers to work with relational data using domain-specific objects.</span></span> <span data-ttu-id="45935-562">开发人员无需再像往常一样编写大部分数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="45935-562">It eliminates the need for most of the data-access code that developers usually need to write.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-563">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-563">Retry mechanism</span></span>
<span data-ttu-id="45935-564">在通过名为[连接复原/重试逻辑](http://msdn.microsoft.com/data/dn456835.aspx)的机制访问使用 Entity Framework 6.0 及更高版本的 SQL 数据库时，提供重试支持。</span><span class="sxs-lookup"><span data-stu-id="45935-564">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher through a mechanism called [Connection Resiliency / Retry Logic](http://msdn.microsoft.com/data/dn456835.aspx).</span></span> <span data-ttu-id="45935-565">重试机制的主要功能包括：</span><span class="sxs-lookup"><span data-stu-id="45935-565">The main features of the retry mechanism are:</span></span>

* <span data-ttu-id="45935-566">主要抽象是 **IDbExecutionStrategy** 接口。</span><span class="sxs-lookup"><span data-stu-id="45935-566">The primary abstraction is the **IDbExecutionStrategy** interface.</span></span> <span data-ttu-id="45935-567">此接口：</span><span class="sxs-lookup"><span data-stu-id="45935-567">This interface:</span></span>
  * <span data-ttu-id="45935-568">定义同步和异步 **Execute**\* 方法。</span><span class="sxs-lookup"><span data-stu-id="45935-568">Defines synchronous and asynchronous **Execute**\* methods.</span></span>
  * <span data-ttu-id="45935-569">定义可直接使用或可在数据库上下文中配置为默认策略的类（映射到提供程序名称或提供程序名称和服务器名称）。</span><span class="sxs-lookup"><span data-stu-id="45935-569">Defines classes that can be used directly or can be configured on a database context as a default strategy, mapped to provider name, or mapped to a provider name and server name.</span></span> <span data-ttu-id="45935-570">如果已在上下文中进行配置，则重试会在各个数据库操作级别发生，其中一些可能适用于给定的上下文操作。</span><span class="sxs-lookup"><span data-stu-id="45935-570">When configured on a context, retries occur at the level of individual database operations, of which there might be several for a given context operation.</span></span>
  * <span data-ttu-id="45935-571">定义何时以及如何重试失败的连接。</span><span class="sxs-lookup"><span data-stu-id="45935-571">Defines when to retry a failed connection, and how.</span></span>
* <span data-ttu-id="45935-572">它包括 **IDbExecutionStrategy** 接口的多个内置实现：</span><span class="sxs-lookup"><span data-stu-id="45935-572">It includes several built-in implementations of the **IDbExecutionStrategy** interface:</span></span>
  * <span data-ttu-id="45935-573">默认 - 不重试。</span><span class="sxs-lookup"><span data-stu-id="45935-573">Default - no retrying.</span></span>
  * <span data-ttu-id="45935-574">SQL 数据库默认 - 不重试（自动），但检查异常，并根据建议包装它们以使用 SQL 数据库策略。</span><span class="sxs-lookup"><span data-stu-id="45935-574">Default for SQL Database (automatic) - no retrying, but inspects exceptions and wraps them with suggestion to use the SQL Database strategy.</span></span>
  * <span data-ttu-id="45935-575">SQL 数据库默认 - 指数（从基类继承），外加 SQL 数据库检测逻辑。</span><span class="sxs-lookup"><span data-stu-id="45935-575">Default for SQL Database - exponential (inherited from base class) plus SQL Database detection logic.</span></span>
* <span data-ttu-id="45935-576">它实现了包含随机化的指数回退策略。</span><span class="sxs-lookup"><span data-stu-id="45935-576">It implements an exponential back-off strategy that includes randomization.</span></span>
* <span data-ttu-id="45935-577">内置重试类有状态，但非线程安全。</span><span class="sxs-lookup"><span data-stu-id="45935-577">The built-in retry classes are stateful and are not thread safe.</span></span> <span data-ttu-id="45935-578">不过，可以在当前操作完成后重用它们。</span><span class="sxs-lookup"><span data-stu-id="45935-578">However, they can be reused after the current operation is completed.</span></span>
* <span data-ttu-id="45935-579">如果超出指定的重试计数，则结果会在新的异常中进行包装。</span><span class="sxs-lookup"><span data-stu-id="45935-579">If the specified retry count is exceeded, the results are wrapped in a new exception.</span></span> <span data-ttu-id="45935-580">它不会加剧当前异常。</span><span class="sxs-lookup"><span data-stu-id="45935-580">It does not bubble up the current exception.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="45935-581">策略配置</span><span class="sxs-lookup"><span data-stu-id="45935-581">Policy configuration</span></span>
<span data-ttu-id="45935-582">在访问使用 Entity Framework 6.0 及更高版本的 SQL 数据库时，提供重试支持。</span><span class="sxs-lookup"><span data-stu-id="45935-582">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher.</span></span> <span data-ttu-id="45935-583">重试策略是以编程方式进行配置。</span><span class="sxs-lookup"><span data-stu-id="45935-583">Retry policies are configured programmatically.</span></span> <span data-ttu-id="45935-584">不能逐个操作更改配置。</span><span class="sxs-lookup"><span data-stu-id="45935-584">The configuration cannot be changed on a per-operation basis.</span></span>

<span data-ttu-id="45935-585">在上下文中将策略配置为默认值时，需要指定一个用于根据需要新建策略的函数。</span><span class="sxs-lookup"><span data-stu-id="45935-585">When configuring a strategy on the context as the default, you specify a function that creates a new strategy on demand.</span></span> <span data-ttu-id="45935-586">以下代码展示了如何创建重试配置类来扩展 **DbConfiguration** 基类。</span><span class="sxs-lookup"><span data-stu-id="45935-586">The following code shows how you can create a retry configuration class that extends the **DbConfiguration** base class.</span></span>

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

<span data-ttu-id="45935-587">然后，可以将此指定为应用程序启动时，所有使用 **DbConfiguration** 实例的 **SetConfiguration** 方法的操作的默认重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-587">You can then specify this as the default retry strategy for all operations using the **SetConfiguration** method of the **DbConfiguration** instance when the application starts.</span></span> <span data-ttu-id="45935-588">默认情况下，EF 会自动发现和使用配置类。</span><span class="sxs-lookup"><span data-stu-id="45935-588">By default, EF will automatically discover and use the configuration class.</span></span>

```csharp
DbConfiguration.SetConfiguration(new BloggingContextConfiguration());
```

<span data-ttu-id="45935-589">可以使用 **DbConfigurationType** 属性为上下文类添加批注，从而为上下文指定重试配置类。</span><span class="sxs-lookup"><span data-stu-id="45935-589">You can specify the retry configuration class for a context by annotating the context class with a **DbConfigurationType** attribute.</span></span> <span data-ttu-id="45935-590">不过，如果只有一个配置类，则 EF 会使用它，而无需为上下文添加批注。</span><span class="sxs-lookup"><span data-stu-id="45935-590">However, if you have only one configuration class, EF will use it without the need to annotate the context.</span></span>

```csharp
[DbConfigurationType(typeof(BloggingContextConfiguration))]
public class BloggingContext : DbContext
```

<span data-ttu-id="45935-591">如果需要对特定操作使用不同的重试策略，或对特定操作禁用重试，则可以创建配置类，以便能够在 **CallContext** 中设置标志，从而挂起或交换策略。</span><span class="sxs-lookup"><span data-stu-id="45935-591">If you need to use different retry strategies for specific operations, or disable retries for specific operations, you can create a configuration class that allows you to suspend or swap strategies by setting a flag in the **CallContext**.</span></span> <span data-ttu-id="45935-592">配置类可以使用此标志切换策略，或禁用提供的策略并使用默认策略。</span><span class="sxs-lookup"><span data-stu-id="45935-592">The configuration class can use this flag to switch strategies, or disable the strategy you provide and use a default strategy.</span></span> <span data-ttu-id="45935-593">有关详细信息，请参阅“重试执行策略限制（EF6 及更高版本）”页面中的[挂起执行策略](http://msdn.microsoft.com/dn307226#transactions_workarounds)。</span><span class="sxs-lookup"><span data-stu-id="45935-593">For more information, see [Suspend Execution Strategy](http://msdn.microsoft.com/dn307226#transactions_workarounds) in the page Limitations with Retrying Execution Strategies (EF6 onwards).</span></span>

<span data-ttu-id="45935-594">对各个操作使用特定重试策略的另一种方法是，创建相应策略类的实例，并通过参数提供所需的设置。</span><span class="sxs-lookup"><span data-stu-id="45935-594">Another technique for using specific retry strategies for individual operations is to create an instance of the required strategy class and supply the desired settings through parameters.</span></span> <span data-ttu-id="45935-595">然后，需要调用它的 **ExecuteAsync** 方法。</span><span class="sxs-lookup"><span data-stu-id="45935-595">You then invoke its **ExecuteAsync** method.</span></span>

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

<span data-ttu-id="45935-596">使用 **DbConfiguration** 类的最简单方法是在与 **DbContext** 类相同的程序集中找到它。</span><span class="sxs-lookup"><span data-stu-id="45935-596">The simplest way to use a **DbConfiguration** class is to locate it in the same assembly as the **DbContext** class.</span></span> <span data-ttu-id="45935-597">不过，如果需要在不同的方案（如使用不同的交互式重试策略和后台重试策略）中使用相同的上下文，则这样做并不适合。</span><span class="sxs-lookup"><span data-stu-id="45935-597">However, this is not appropriate when the same context is required in different scenarios, such as different interactive and background retry strategies.</span></span> <span data-ttu-id="45935-598">如果不同的上下文在各个应用域中执行，则可以通过内置的支持在配置文件中指定配置类，也可以使用代码对它进行明确设置。</span><span class="sxs-lookup"><span data-stu-id="45935-598">If the different contexts execute in separate AppDomains, you can use the built-in support for specifying configuration classes in the configuration file or set it explicitly using code.</span></span> <span data-ttu-id="45935-599">如果不同的上下文必须在同一应用域中执行，则需要使用自定义解决方案。</span><span class="sxs-lookup"><span data-stu-id="45935-599">If the different contexts must execute in the same AppDomain, a custom solution will be required.</span></span>

<span data-ttu-id="45935-600">有关详细信息，请参阅[基于代码的配置（EF6 及更高版本）](http://msdn.microsoft.com/data/jj680699.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-600">For more information, see [Code-Based Configuration (EF6 onwards)](http://msdn.microsoft.com/data/jj680699.aspx).</span></span>

<span data-ttu-id="45935-601">下表显示了使用 EF6 时的内置重试策略的默认设置。</span><span class="sxs-lookup"><span data-stu-id="45935-601">The following table shows the default settings for the built-in retry policy when using EF6.</span></span>

| <span data-ttu-id="45935-602">设置</span><span class="sxs-lookup"><span data-stu-id="45935-602">Setting</span></span> | <span data-ttu-id="45935-603">默认值</span><span class="sxs-lookup"><span data-stu-id="45935-603">Default value</span></span> | <span data-ttu-id="45935-604">含义</span><span class="sxs-lookup"><span data-stu-id="45935-604">Meaning</span></span> |
|---------|---------------|---------|
| <span data-ttu-id="45935-605">策略</span><span class="sxs-lookup"><span data-stu-id="45935-605">Policy</span></span> | <span data-ttu-id="45935-606">指数</span><span class="sxs-lookup"><span data-stu-id="45935-606">Exponential</span></span> | <span data-ttu-id="45935-607">指数退让。</span><span class="sxs-lookup"><span data-stu-id="45935-607">Exponential back-off.</span></span> |
| <span data-ttu-id="45935-608">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="45935-608">MaxRetryCount</span></span> | <span data-ttu-id="45935-609">5</span><span class="sxs-lookup"><span data-stu-id="45935-609">5</span></span> | <span data-ttu-id="45935-610">最大重试次数。</span><span class="sxs-lookup"><span data-stu-id="45935-610">The maximum number of retries.</span></span> |
| <span data-ttu-id="45935-611">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="45935-611">MaxDelay</span></span> | <span data-ttu-id="45935-612">30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-612">30 seconds</span></span> | <span data-ttu-id="45935-613">重试之间的最大延迟。</span><span class="sxs-lookup"><span data-stu-id="45935-613">The maximum delay between retries.</span></span> <span data-ttu-id="45935-614">此值不影响延迟序列的计算方式。</span><span class="sxs-lookup"><span data-stu-id="45935-614">This value does not affect how the series of delays are computed.</span></span> <span data-ttu-id="45935-615">它只定义上限。</span><span class="sxs-lookup"><span data-stu-id="45935-615">It only defines an upper bound.</span></span> |
| <span data-ttu-id="45935-616">DefaultCoefficient</span><span class="sxs-lookup"><span data-stu-id="45935-616">DefaultCoefficient</span></span> | <span data-ttu-id="45935-617">1 秒</span><span class="sxs-lookup"><span data-stu-id="45935-617">1 second</span></span> | <span data-ttu-id="45935-618">指数退避计算的系数。</span><span class="sxs-lookup"><span data-stu-id="45935-618">The coefficient for the exponential back-off computation.</span></span> <span data-ttu-id="45935-619">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="45935-619">This value cannot be changed.</span></span> |
| <span data-ttu-id="45935-620">DefaultRandomFactor</span><span class="sxs-lookup"><span data-stu-id="45935-620">DefaultRandomFactor</span></span> | <span data-ttu-id="45935-621">1.1</span><span class="sxs-lookup"><span data-stu-id="45935-621">1.1</span></span> | <span data-ttu-id="45935-622">用于添加每个条目的随机延迟的乘数。</span><span class="sxs-lookup"><span data-stu-id="45935-622">The multiplier used to add a random delay for each entry.</span></span> <span data-ttu-id="45935-623">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="45935-623">This value cannot be changed.</span></span> |
| <span data-ttu-id="45935-624">DefaultExponentialBase</span><span class="sxs-lookup"><span data-stu-id="45935-624">DefaultExponentialBase</span></span> | <span data-ttu-id="45935-625">2</span><span class="sxs-lookup"><span data-stu-id="45935-625">2</span></span> | <span data-ttu-id="45935-626">用于计算下一次延迟的乘数。</span><span class="sxs-lookup"><span data-stu-id="45935-626">The multiplier used to calculate the next delay.</span></span> <span data-ttu-id="45935-627">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="45935-627">This value cannot be changed.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="45935-628">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="45935-628">Retry usage guidance</span></span>
<span data-ttu-id="45935-629">访问使用 EF6 的 SQL 数据库时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="45935-629">Consider the following guidelines when accessing SQL Database using EF6:</span></span>

* <span data-ttu-id="45935-630">选择适当的服务选项（共享或高级）。</span><span class="sxs-lookup"><span data-stu-id="45935-630">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="45935-631">鉴于共享服务器的其他租户的使用情况，共享实例的连接延迟和限制可能比平常要长。</span><span class="sxs-lookup"><span data-stu-id="45935-631">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="45935-632">如果需要可预测的性能和可靠的低延迟操作，请考虑选择高级选项。</span><span class="sxs-lookup"><span data-stu-id="45935-632">If predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="45935-633">不建议结合使用固定间隔策略和 Azure SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="45935-633">A fixed interval strategy is not recommended for use with Azure SQL Database.</span></span> <span data-ttu-id="45935-634">请改用指数回退策略，因为服务可能已过载，更长的延迟则可以让服务有更长时间进行恢复。</span><span class="sxs-lookup"><span data-stu-id="45935-634">Instead, use an exponential back-off strategy because the service may be overloaded, and longer delays allow more time for it to recover.</span></span>
* <span data-ttu-id="45935-635">定义连接时，为连接和命令超时选择合适的值。</span><span class="sxs-lookup"><span data-stu-id="45935-635">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="45935-636">根据业务逻辑设计和全面测试来选择超时值。</span><span class="sxs-lookup"><span data-stu-id="45935-636">Base the timeout on both your business logic design and through testing.</span></span> <span data-ttu-id="45935-637">可能需要随着数据量或业务流程的变化，陆续修改此值。</span><span class="sxs-lookup"><span data-stu-id="45935-637">You may need to modify this value over time as the volumes of data or the business processes change.</span></span> <span data-ttu-id="45935-638">当数据库忙碌时，超时太短可能会导致连接过早失败。</span><span class="sxs-lookup"><span data-stu-id="45935-638">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="45935-639">超时太长则会导致在检测到失败的连接前等待太久，进而可能会妨碍重试逻辑正常运行。</span><span class="sxs-lookup"><span data-stu-id="45935-639">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="45935-640">超时值是端到端延迟的组成部分，尽管你无法轻松确定在保存上下文时会执行多少命令。</span><span class="sxs-lookup"><span data-stu-id="45935-640">The value of the timeout is a component of the end-to-end latency, although you cannot easily determine how many commands will execute when saving the context.</span></span> <span data-ttu-id="45935-641">可以设置 **DbContext** 实例的 **CommandTimeout** 属性，以此来更改默认超时值。</span><span class="sxs-lookup"><span data-stu-id="45935-641">You can change the default timeout by setting the **CommandTimeout** property of the **DbContext** instance.</span></span>
* <span data-ttu-id="45935-642">Entity Framework 支持在配置文件中定义的重试配置。</span><span class="sxs-lookup"><span data-stu-id="45935-642">Entity Framework supports retry configurations defined in configuration files.</span></span> <span data-ttu-id="45935-643">不过，为了实现 Azure 上的最大灵活性，应考虑在应用程序内以编程方式创建配置。</span><span class="sxs-lookup"><span data-stu-id="45935-643">However, for maximum flexibility on Azure you should consider creating the configuration programmatically within the application.</span></span> <span data-ttu-id="45935-644">重试策略的特定参数（如重试次数和重试间隔）可以存储在服务配置文件中，并在运行时用于创建相应的策略。</span><span class="sxs-lookup"><span data-stu-id="45935-644">The specific parameters for the retry policies, such as the number of retries and the retry intervals, can be stored in the service configuration file and used at runtime to create the appropriate policies.</span></span> <span data-ttu-id="45935-645">这样一来，无需重启应用程序，即可更改设置。</span><span class="sxs-lookup"><span data-stu-id="45935-645">This allows the settings to be changed without requiring the application to be restarted.</span></span>

<span data-ttu-id="45935-646">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="45935-646">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="45935-647">无法指定重试尝试之间的延迟（固定值且以指数序列生成）。</span><span class="sxs-lookup"><span data-stu-id="45935-647">You cannot specify the delay between retry attempts (it is fixed and generated as an exponential sequence).</span></span> <span data-ttu-id="45935-648">只能指定最大值（如下所示）；除非创建自定义重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-648">You can specify only the maximum values, as shown here; unless you create a custom retry strategy.</span></span> <span data-ttu-id="45935-649">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="45935-649">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="45935-650">**上下文**</span><span class="sxs-lookup"><span data-stu-id="45935-650">**Context**</span></span> | <span data-ttu-id="45935-651">**示例目标 E2E<br />最长延迟**</span><span class="sxs-lookup"><span data-stu-id="45935-651">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="45935-652">**重试策略**</span><span class="sxs-lookup"><span data-stu-id="45935-652">**Retry policy**</span></span> | <span data-ttu-id="45935-653">**设置**</span><span class="sxs-lookup"><span data-stu-id="45935-653">**Settings**</span></span> | <span data-ttu-id="45935-654">**值**</span><span class="sxs-lookup"><span data-stu-id="45935-654">**Values**</span></span> | <span data-ttu-id="45935-655">**工作原理**</span><span class="sxs-lookup"><span data-stu-id="45935-655">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="45935-656">交互式, UI,</span><span class="sxs-lookup"><span data-stu-id="45935-656">Interactive, UI,</span></span><br /><span data-ttu-id="45935-657">或 foreground</span><span class="sxs-lookup"><span data-stu-id="45935-657">or foreground</span></span> |<span data-ttu-id="45935-658">2 秒</span><span class="sxs-lookup"><span data-stu-id="45935-658">2 seconds</span></span> |<span data-ttu-id="45935-659">指数</span><span class="sxs-lookup"><span data-stu-id="45935-659">Exponential</span></span> |<span data-ttu-id="45935-660">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="45935-660">MaxRetryCount</span></span><br /><span data-ttu-id="45935-661">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="45935-661">MaxDelay</span></span> |<span data-ttu-id="45935-662">3</span><span class="sxs-lookup"><span data-stu-id="45935-662">3</span></span><br /><span data-ttu-id="45935-663">750 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-663">750 ms</span></span> |<span data-ttu-id="45935-664">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="45935-664">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="45935-665">第 2 次尝试 - 延迟 750 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-665">Attempt 2 - delay 750 ms</span></span><br /><span data-ttu-id="45935-666">第 3 次尝试 - 延迟 750 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-666">Attempt 3 – delay 750 ms</span></span> |
| <span data-ttu-id="45935-667">背景</span><span class="sxs-lookup"><span data-stu-id="45935-667">Background</span></span><br /> <span data-ttu-id="45935-668">或批处理</span><span class="sxs-lookup"><span data-stu-id="45935-668">or batch</span></span> |<span data-ttu-id="45935-669">30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-669">30 seconds</span></span> |<span data-ttu-id="45935-670">指数</span><span class="sxs-lookup"><span data-stu-id="45935-670">Exponential</span></span> |<span data-ttu-id="45935-671">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="45935-671">MaxRetryCount</span></span><br /><span data-ttu-id="45935-672">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="45935-672">MaxDelay</span></span> |<span data-ttu-id="45935-673">5</span><span class="sxs-lookup"><span data-stu-id="45935-673">5</span></span><br /><span data-ttu-id="45935-674">12 秒</span><span class="sxs-lookup"><span data-stu-id="45935-674">12 seconds</span></span> |<span data-ttu-id="45935-675">第 1 次尝试 - 延迟 0 秒</span><span class="sxs-lookup"><span data-stu-id="45935-675">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="45935-676">第 2 次尝试 - 约延迟 1 秒</span><span class="sxs-lookup"><span data-stu-id="45935-676">Attempt 2 - delay ~1 sec</span></span><br /><span data-ttu-id="45935-677">第 3 次尝试 - 约延迟 3 秒</span><span class="sxs-lookup"><span data-stu-id="45935-677">Attempt 3 - delay ~3 sec</span></span><br /><span data-ttu-id="45935-678">第 4 次尝试 - 约延迟 7 秒</span><span class="sxs-lookup"><span data-stu-id="45935-678">Attempt 4 - delay ~7 sec</span></span><br /><span data-ttu-id="45935-679">第 5 次尝试 - 约延迟 12 秒</span><span class="sxs-lookup"><span data-stu-id="45935-679">Attempt 5 - delay 12 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="45935-680">端到端延迟目标假设采用服务连接的默认超时。</span><span class="sxs-lookup"><span data-stu-id="45935-680">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="45935-681">如果指定更长的连接超时，则每次重试尝试都会将端到端延迟延长这一附加时间。</span><span class="sxs-lookup"><span data-stu-id="45935-681">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="45935-682">示例</span><span class="sxs-lookup"><span data-stu-id="45935-682">Examples</span></span>
<span data-ttu-id="45935-683">以下代码示例定义了使用 Entity Framework 的简单数据访问解决方案。</span><span class="sxs-lookup"><span data-stu-id="45935-683">The following code example defines a simple data access solution that uses Entity Framework.</span></span> <span data-ttu-id="45935-684">它通过定义可扩展 **DbConfiguration** 的 **BlogConfiguration** 类的实例来指定重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-684">It sets a specific retry strategy by defining an instance of a class named **BlogConfiguration** that extends **DbConfiguration**.</span></span>

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

<span data-ttu-id="45935-685">有关使用 Entity Framework 重试机制的更多示例，请参阅[连接复原/重试逻辑](http://msdn.microsoft.com/data/dn456835.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-685">More examples of using the Entity Framework retry mechanism can be found in [Connection Resiliency / Retry Logic](http://msdn.microsoft.com/data/dn456835.aspx).</span></span>

### <a name="more-information"></a><span data-ttu-id="45935-686">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-686">More information</span></span>
* [<span data-ttu-id="45935-687">Azure SQL 数据库性能和弹性指南</span><span class="sxs-lookup"><span data-stu-id="45935-687">Azure SQL Database Performance and Elasticity Guide</span></span>](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## <a name="sql-database-using-entity-framework-core"></a><span data-ttu-id="45935-688">使用 Entity Framework Core 的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="45935-688">SQL Database using Entity Framework Core</span></span>
<span data-ttu-id="45935-689">[Entity Framework Core](/ef/core/) 是一种对象关系映射程序，可方便 .NET Core 开发人员使用域特定对象处理数据。</span><span class="sxs-lookup"><span data-stu-id="45935-689">[Entity Framework Core](/ef/core/) is an object-relational mapper that enables .NET Core developers to work with data using domain-specific objects.</span></span> <span data-ttu-id="45935-690">开发人员无需再像往常一样编写大部分数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="45935-690">It eliminates the need for most of the data-access code that developers usually need to write.</span></span> <span data-ttu-id="45935-691">此版 Entity Framework 从头开始编写，不自动从 EF6.x 继承所有功能。</span><span class="sxs-lookup"><span data-stu-id="45935-691">This version of Entity Framework was written from the ground up, and doesn't automatically inherit all the features from EF6.x.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-692">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-692">Retry mechanism</span></span>
<span data-ttu-id="45935-693">在通过名为[连接复原能力](/ef/core/miscellaneous/connection-resiliency)的机制访问使用 Entity Framework Core 的 SQL 数据库时，提供重试支持。</span><span class="sxs-lookup"><span data-stu-id="45935-693">Retry support is provided when accessing SQL Database using Entity Framework Core through a mechanism called [Connection Resiliency](/ef/core/miscellaneous/connection-resiliency).</span></span> <span data-ttu-id="45935-694">连接复原能力在 EF Core 1.1.0 中引入。</span><span class="sxs-lookup"><span data-stu-id="45935-694">Connection resiliency was introduced in EF Core 1.1.0.</span></span>

<span data-ttu-id="45935-695">主抽象是 `IExecutionStrategy` 接口。</span><span class="sxs-lookup"><span data-stu-id="45935-695">The primary abstraction is the `IExecutionStrategy` interface.</span></span> <span data-ttu-id="45935-696">SQL Server（包括 SQL Azure）的执行策略可识别能够重试的异常类型，并为最大重试次数、两次重试间的延迟等提供合理的默认值。</span><span class="sxs-lookup"><span data-stu-id="45935-696">The execution strategy for SQL Server, including SQL Azure, is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, and so on.</span></span>

### <a name="examples"></a><span data-ttu-id="45935-697">示例</span><span class="sxs-lookup"><span data-stu-id="45935-697">Examples</span></span>

<span data-ttu-id="45935-698">以下代码在配置 DbContext 对象（表示与数据库的会话）时实现自动重试。</span><span class="sxs-lookup"><span data-stu-id="45935-698">The following code enables automatic retries when configuring the DbContext object, which represents a session with the database.</span></span> 

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(
            @"Server=(localdb)\mssqllocaldb;Database=EFMiscellanous.ConnectionResiliency;Trusted_Connection=True;",
            options => options.EnableRetryOnFailure());
}
```

<span data-ttu-id="45935-699">以下代码展示如何使用执行策略在自动重试机制下执行事务。</span><span class="sxs-lookup"><span data-stu-id="45935-699">The following code shows how to execute a transaction with automatic retries, by using an execution strategy.</span></span> <span data-ttu-id="45935-700">该事务在委托中定义。</span><span class="sxs-lookup"><span data-stu-id="45935-700">The transaction is defined in a delegate.</span></span> <span data-ttu-id="45935-701">如果出现暂时性失败，执行策略将再次调用委托。</span><span class="sxs-lookup"><span data-stu-id="45935-701">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

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

## <a name="azure-storage"></a><span data-ttu-id="45935-702">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="45935-702">Azure Storage</span></span>
<span data-ttu-id="45935-703">Azure 存储服务包括表和 blob 存储、文件以及存储队列。</span><span class="sxs-lookup"><span data-stu-id="45935-703">Azure storage services include table and blob storage, files, and storage queues.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="45935-704">重试机制</span><span class="sxs-lookup"><span data-stu-id="45935-704">Retry mechanism</span></span>
<span data-ttu-id="45935-705">重试在单独的 REST 操作一级发生，是客户端 API 实现的主要组成部分。</span><span class="sxs-lookup"><span data-stu-id="45935-705">Retries occur at the individual REST operation level and are an integral part of the client API implementation.</span></span> <span data-ttu-id="45935-706">客户端存储 SDK 使用可实现 [IExtendedRetryPolicy Interface](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx)（IExtendedRetryPolicy 接口）的类。</span><span class="sxs-lookup"><span data-stu-id="45935-706">The client storage SDK uses classes that implement the [IExtendedRetryPolicy Interface](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).</span></span>

<span data-ttu-id="45935-707">接口的实现各不相同。</span><span class="sxs-lookup"><span data-stu-id="45935-707">There are different implementations of the interface.</span></span> <span data-ttu-id="45935-708">存储客户端可以从专门用于访问表、blob 和队列的策略中进行选择。</span><span class="sxs-lookup"><span data-stu-id="45935-708">Storage clients can choose from policies specifically designed for accessing tables, blobs, and queues.</span></span> <span data-ttu-id="45935-709">每个实现使用不同的重试策略，此策略基本上定义了重试间隔和其他详细信息。</span><span class="sxs-lookup"><span data-stu-id="45935-709">Each implementation uses a different retry strategy that essentially defines the retry interval and other details.</span></span>

<span data-ttu-id="45935-710">内置类支持线性（固定延迟）和指数随机化重试间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-710">The built-in classes provide support for linear (constant delay) and exponential with randomization retry intervals.</span></span> <span data-ttu-id="45935-711">当其他进程在较高级别处理重试时，还有不重试策略可供使用。</span><span class="sxs-lookup"><span data-stu-id="45935-711">There is also a no retry policy for use when another process is handling retries at a higher level.</span></span> <span data-ttu-id="45935-712">不过，如果具有内置类未规定的特定要求，则可以实现自己的重试类。</span><span class="sxs-lookup"><span data-stu-id="45935-712">However, you can implement your own retry classes if you have specific requirements not provided by the built-in classes.</span></span>

<span data-ttu-id="45935-713">如果使用的是读取访问异地冗余存储 (RA-GRS)，且请求的结果是可重试错误，则备用重试会在主要和辅助存储服务位置之间切换。</span><span class="sxs-lookup"><span data-stu-id="45935-713">Alternate retries switch between primary and secondary storage service location if you are using read access geo-redundant storage (RA-GRS) and the result of the request is a retryable error.</span></span> <span data-ttu-id="45935-714">有关详细信息，请参阅 [Azure 存储冗余选项](http://msdn.microsoft.com/library/azure/dn727290.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-714">See [Azure Storage Redundancy Options](http://msdn.microsoft.com/library/azure/dn727290.aspx) for more information.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="45935-715">策略配置</span><span class="sxs-lookup"><span data-stu-id="45935-715">Policy configuration</span></span>
<span data-ttu-id="45935-716">重试策略是以编程方式进行配置。</span><span class="sxs-lookup"><span data-stu-id="45935-716">Retry policies are configured programmatically.</span></span> <span data-ttu-id="45935-717">典型的过程是创建并填充 **TableRequestOptions**、**BlobRequestOptions**、**FileRequestOptions** 或 **QueueRequestOptions** 实例。</span><span class="sxs-lookup"><span data-stu-id="45935-717">A typical procedure is to create and populate a **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions**, or **QueueRequestOptions** instance.</span></span>

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

<span data-ttu-id="45935-718">然后，可以在客户端上设置请求选项实例，客户端的所有操作都会使用指定的请求选项。</span><span class="sxs-lookup"><span data-stu-id="45935-718">The request options instance can then be set on the client, and all operations with the client will use the specified request options.</span></span>

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

<span data-ttu-id="45935-719">可以将填充的请求选项类实例作为参数传递到操作方法，以此来替代客户端请求选项。</span><span class="sxs-lookup"><span data-stu-id="45935-719">You can override the client request options by passing a populated instance of the request options class as a parameter to operation methods.</span></span>

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

<span data-ttu-id="45935-720">使用 **OperationContext** 实例可以指定在发生重试时以及在操作完成时要执行的代码。</span><span class="sxs-lookup"><span data-stu-id="45935-720">You use an **OperationContext** instance to specify the code to execute when a retry occurs and when an operation has completed.</span></span> <span data-ttu-id="45935-721">此代码可以收集供日志和遥测使用的操作的相关信息。</span><span class="sxs-lookup"><span data-stu-id="45935-721">This code can collect information about the operation for use in logs and telemetry.</span></span>

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

<span data-ttu-id="45935-722">除了指明故障是否适合重试，扩展的重试策略还会返回 **RetryContext** 对象，用于指明重试次数、上次请求结果、下次重试是在主要位置还是在辅助位置上发生（有关详细信息，请参阅下表）。</span><span class="sxs-lookup"><span data-stu-id="45935-722">In addition to indicating whether a failure is suitable for retry, the extended retry policies return a **RetryContext** object that indicates the number of retries, the results of the last request, whether the next retry will happen in the primary or secondary location (see table below for details).</span></span> <span data-ttu-id="45935-723">**RetryContext** 对象的属性可用于确定是否以及何时尝试进行重试。</span><span class="sxs-lookup"><span data-stu-id="45935-723">The properties of the **RetryContext** object can be used to decide if and when to attempt a retry.</span></span> <span data-ttu-id="45935-724">如需了解更多详情，请参阅 [IExtendedRetryPolicy.Evaluate 方法](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-724">For more details, see [IExtendedRetryPolicy.Evaluate Method](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).</span></span>

<span data-ttu-id="45935-725">下表显示了内置重试策略的默认设置。</span><span class="sxs-lookup"><span data-stu-id="45935-725">The following tables show the default settings for the built-in retry policies.</span></span>

<span data-ttu-id="45935-726">**请求选项**</span><span class="sxs-lookup"><span data-stu-id="45935-726">**Request options**</span></span>

| <span data-ttu-id="45935-727">**设置**</span><span class="sxs-lookup"><span data-stu-id="45935-727">**Setting**</span></span> | <span data-ttu-id="45935-728">**默认值**</span><span class="sxs-lookup"><span data-stu-id="45935-728">**Default value**</span></span> | <span data-ttu-id="45935-729">**含义**</span><span class="sxs-lookup"><span data-stu-id="45935-729">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="45935-730">MaximumExecutionTime</span><span class="sxs-lookup"><span data-stu-id="45935-730">MaximumExecutionTime</span></span> | <span data-ttu-id="45935-731">无</span><span class="sxs-lookup"><span data-stu-id="45935-731">None</span></span> | <span data-ttu-id="45935-732">请求的最长执行时间，包括所有可能的重试尝试。</span><span class="sxs-lookup"><span data-stu-id="45935-732">Maximum execution time for the request, including all potential retry attempts.</span></span> <span data-ttu-id="45935-733">如果未指定，则允许请求花费的时间没有限制。</span><span class="sxs-lookup"><span data-stu-id="45935-733">If it is not specified, then the amount of time that a request is permitted to take is unlimited.</span></span> <span data-ttu-id="45935-734">换而言之，请求可能会挂起。</span><span class="sxs-lookup"><span data-stu-id="45935-734">In other words, the request might hang.</span></span> |
| <span data-ttu-id="45935-735">ServerTimeOut</span><span class="sxs-lookup"><span data-stu-id="45935-735">ServerTimeout</span></span> | <span data-ttu-id="45935-736">无</span><span class="sxs-lookup"><span data-stu-id="45935-736">None</span></span> | <span data-ttu-id="45935-737">请求的服务器超时间隔（值四舍五入到秒）。</span><span class="sxs-lookup"><span data-stu-id="45935-737">Server timeout interval for the request (value is rounded to seconds).</span></span> <span data-ttu-id="45935-738">如果未指定，则会使用所有服务器请求的默认值。</span><span class="sxs-lookup"><span data-stu-id="45935-738">If not specified, it will use the default value for all requests to the server.</span></span> <span data-ttu-id="45935-739">通常情况下，最好忽略此设置，以便使用服务器默认值。</span><span class="sxs-lookup"><span data-stu-id="45935-739">Usually, the best option is to omit this setting so that the server default is used.</span></span> | 
| <span data-ttu-id="45935-740">LocationMode</span><span class="sxs-lookup"><span data-stu-id="45935-740">LocationMode</span></span> | <span data-ttu-id="45935-741">无</span><span class="sxs-lookup"><span data-stu-id="45935-741">None</span></span> | <span data-ttu-id="45935-742">如果创建的存储帐户设置了读取访问异地冗余存储 (RA-GRS) 复制选项，则可以使用位置模式来指明哪个位置应接收请求。</span><span class="sxs-lookup"><span data-stu-id="45935-742">If the storage account is created with the Read access geo-redundant storage (RA-GRS) replication option, you can use the location mode to indicate which location should receive the request.</span></span> <span data-ttu-id="45935-743">例如，如果指定的是 **PrimaryThenSecondary**，则请求总是会先发送到主要位置。</span><span class="sxs-lookup"><span data-stu-id="45935-743">For example, if **PrimaryThenSecondary** is specified, requests are always sent to the primary location first.</span></span> <span data-ttu-id="45935-744">如果某个请求失败，它将发送到辅助位置。</span><span class="sxs-lookup"><span data-stu-id="45935-744">If a request fails, it is sent to the secondary location.</span></span> |
| <span data-ttu-id="45935-745">RetryPolicy</span><span class="sxs-lookup"><span data-stu-id="45935-745">RetryPolicy</span></span> | <span data-ttu-id="45935-746">ExponentialPolicy</span><span class="sxs-lookup"><span data-stu-id="45935-746">ExponentialPolicy</span></span> | <span data-ttu-id="45935-747">有关每个选项的详细信息，请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="45935-747">See below for details of each option.</span></span> |

<span data-ttu-id="45935-748">**指数策略**</span><span class="sxs-lookup"><span data-stu-id="45935-748">**Exponential policy**</span></span> 

| <span data-ttu-id="45935-749">**设置**</span><span class="sxs-lookup"><span data-stu-id="45935-749">**Setting**</span></span> | <span data-ttu-id="45935-750">**默认值**</span><span class="sxs-lookup"><span data-stu-id="45935-750">**Default value**</span></span> | <span data-ttu-id="45935-751">**含义**</span><span class="sxs-lookup"><span data-stu-id="45935-751">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="45935-752">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="45935-752">maxAttempt</span></span> | <span data-ttu-id="45935-753">3</span><span class="sxs-lookup"><span data-stu-id="45935-753">3</span></span> | <span data-ttu-id="45935-754">重试的次数。</span><span class="sxs-lookup"><span data-stu-id="45935-754">Number of retry attempts.</span></span> |
| <span data-ttu-id="45935-755">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-755">deltaBackoff</span></span> | <span data-ttu-id="45935-756">4 秒</span><span class="sxs-lookup"><span data-stu-id="45935-756">4 seconds</span></span> | <span data-ttu-id="45935-757">不同重试之间的回退时间间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-757">Back-off interval between retries.</span></span> <span data-ttu-id="45935-758">将针对后续的重试使用多个这样的时间跨度（包括随机元素）。</span><span class="sxs-lookup"><span data-stu-id="45935-758">Multiples of this timespan, including a random element, will be used for subsequent retry attempts.</span></span> |
| <span data-ttu-id="45935-759">MinBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-759">MinBackoff</span></span> | <span data-ttu-id="45935-760">3 秒</span><span class="sxs-lookup"><span data-stu-id="45935-760">3 seconds</span></span> | <span data-ttu-id="45935-761">添加到从 deltaBackoff 计算的所有重试时间间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-761">Added to all retry intervals computed from deltaBackoff.</span></span> <span data-ttu-id="45935-762">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="45935-762">This value cannot be changed.</span></span>
| <span data-ttu-id="45935-763">MaxBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-763">MaxBackoff</span></span> | <span data-ttu-id="45935-764">120 秒</span><span class="sxs-lookup"><span data-stu-id="45935-764">120 seconds</span></span> | <span data-ttu-id="45935-765">如果计得的重试时间间隔大于 MaxBackoff，则使用 MaxBackoff。</span><span class="sxs-lookup"><span data-stu-id="45935-765">MaxBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> <span data-ttu-id="45935-766">不能更改此值。</span><span class="sxs-lookup"><span data-stu-id="45935-766">This value cannot be changed.</span></span> |

<span data-ttu-id="45935-767">**线性策略**</span><span class="sxs-lookup"><span data-stu-id="45935-767">**Linear policy**</span></span>

| <span data-ttu-id="45935-768">**设置**</span><span class="sxs-lookup"><span data-stu-id="45935-768">**Setting**</span></span> | <span data-ttu-id="45935-769">**默认值**</span><span class="sxs-lookup"><span data-stu-id="45935-769">**Default value**</span></span> | <span data-ttu-id="45935-770">**含义**</span><span class="sxs-lookup"><span data-stu-id="45935-770">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="45935-771">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="45935-771">maxAttempt</span></span> | <span data-ttu-id="45935-772">3</span><span class="sxs-lookup"><span data-stu-id="45935-772">3</span></span> | <span data-ttu-id="45935-773">重试的次数。</span><span class="sxs-lookup"><span data-stu-id="45935-773">Number of retry attempts.</span></span> |
| <span data-ttu-id="45935-774">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-774">deltaBackoff</span></span> | <span data-ttu-id="45935-775">30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-775">30 seconds</span></span> | <span data-ttu-id="45935-776">不同重试之间的回退时间间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-776">Back-off interval between retries.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="45935-777">重试使用指南</span><span class="sxs-lookup"><span data-stu-id="45935-777">Retry usage guidance</span></span>
<span data-ttu-id="45935-778">在使用存储客户端 API 访问 Azure 存储服务时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="45935-778">Consider the following guidelines when accessing Azure storage services using the storage client API:</span></span>

* <span data-ttu-id="45935-779">使用 Microsoft.WindowsAzure.Storage.RetryPolicies 命名空间中符合要求的内置重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-779">Use the built-in retry policies from the Microsoft.WindowsAzure.Storage.RetryPolicies namespace where they are appropriate for your requirements.</span></span> <span data-ttu-id="45935-780">在大多数情况下，这些策略就够用了。</span><span class="sxs-lookup"><span data-stu-id="45935-780">In most cases, these policies will be sufficient.</span></span>
* <span data-ttu-id="45935-781">对批处理操作、后台任务或非交互式方案使用 **ExponentialRetry** 策略。</span><span class="sxs-lookup"><span data-stu-id="45935-781">Use the **ExponentialRetry** policy in batch operations, background tasks, or non-interactive scenarios.</span></span> <span data-ttu-id="45935-782">在这种情况下，服务通常可以有更多的恢复时间。结果就是，提高了操作最终成功的可能性。</span><span class="sxs-lookup"><span data-stu-id="45935-782">In these scenarios, you can typically allow more time for the service to recover—with a consequently increased chance of the operation eventually succeeding.</span></span>
* <span data-ttu-id="45935-783">考虑指定 **RequestOptions** 参数的 **MaximumExecutionTime** 属性，以限制总执行时间；但在选择超时值时，请将操作的类型和大小考虑进去。</span><span class="sxs-lookup"><span data-stu-id="45935-783">Consider specifying the **MaximumExecutionTime** property of the **RequestOptions** parameter to limit the total execution time, but take into account the type and size of the operation when choosing a timeout value.</span></span>
* <span data-ttu-id="45935-784">如果需要实现自定义重试，请避免创建存储客户端类的包装器。</span><span class="sxs-lookup"><span data-stu-id="45935-784">If you need to implement a custom retry, avoid creating wrappers around the storage client classes.</span></span> <span data-ttu-id="45935-785">应使用这些功能通过 **IExtendedRetryPolicy** 接口扩展现有策略。</span><span class="sxs-lookup"><span data-stu-id="45935-785">Instead, use the capabilities to extend the existing policies through the **IExtendedRetryPolicy** interface.</span></span>
* <span data-ttu-id="45935-786">如果使用的是读取访问异地冗余存储 (RA-GRS)，则可以使用 **LocationMode** 指定在主要访问失败时重试尝试会访问存储空间的只读辅助副本。</span><span class="sxs-lookup"><span data-stu-id="45935-786">If you are using read access geo-redundant storage (RA-GRS) you can use the **LocationMode** to specify that retry attempts will access the secondary read-only copy of the store should the primary access fail.</span></span> <span data-ttu-id="45935-787">不过，使用此选项时，必须确保在尚未完成从主要存储空间进行复制的情况下，应用程序可以成功使用可能已过时的数据。</span><span class="sxs-lookup"><span data-stu-id="45935-787">However, when using this option you must ensure that your application can work successfully with data that may be stale if the replication from the primary store has not yet completed.</span></span>

<span data-ttu-id="45935-788">请考虑从下列重试操作设置入手。</span><span class="sxs-lookup"><span data-stu-id="45935-788">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="45935-789">这些都是通用设置，应监视操作，并对值进行微调以适应自己的方案。</span><span class="sxs-lookup"><span data-stu-id="45935-789">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>  

| <span data-ttu-id="45935-790">**上下文**</span><span class="sxs-lookup"><span data-stu-id="45935-790">**Context**</span></span> | <span data-ttu-id="45935-791">**示例目标 E2E<br />最长延迟**</span><span class="sxs-lookup"><span data-stu-id="45935-791">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="45935-792">**重试策略**</span><span class="sxs-lookup"><span data-stu-id="45935-792">**Retry policy**</span></span> | <span data-ttu-id="45935-793">**设置**</span><span class="sxs-lookup"><span data-stu-id="45935-793">**Settings**</span></span> | <span data-ttu-id="45935-794">**值**</span><span class="sxs-lookup"><span data-stu-id="45935-794">**Values**</span></span> | <span data-ttu-id="45935-795">**工作原理**</span><span class="sxs-lookup"><span data-stu-id="45935-795">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="45935-796">交互式, UI,</span><span class="sxs-lookup"><span data-stu-id="45935-796">Interactive, UI,</span></span><br /><span data-ttu-id="45935-797">或 foreground</span><span class="sxs-lookup"><span data-stu-id="45935-797">or foreground</span></span> |<span data-ttu-id="45935-798">2 秒</span><span class="sxs-lookup"><span data-stu-id="45935-798">2 seconds</span></span> |<span data-ttu-id="45935-799">线性</span><span class="sxs-lookup"><span data-stu-id="45935-799">Linear</span></span> |<span data-ttu-id="45935-800">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="45935-800">maxAttempt</span></span><br /><span data-ttu-id="45935-801">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-801">deltaBackoff</span></span> |<span data-ttu-id="45935-802">3</span><span class="sxs-lookup"><span data-stu-id="45935-802">3</span></span><br /><span data-ttu-id="45935-803">500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-803">500 ms</span></span> |<span data-ttu-id="45935-804">第 1 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-804">Attempt 1 - delay 500 ms</span></span><br /><span data-ttu-id="45935-805">第 2 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-805">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="45935-806">第 3 次尝试 - 延迟 500 毫秒</span><span class="sxs-lookup"><span data-stu-id="45935-806">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="45935-807">背景</span><span class="sxs-lookup"><span data-stu-id="45935-807">Background</span></span><br /><span data-ttu-id="45935-808">或批处理</span><span class="sxs-lookup"><span data-stu-id="45935-808">or batch</span></span> |<span data-ttu-id="45935-809">30 秒</span><span class="sxs-lookup"><span data-stu-id="45935-809">30 seconds</span></span> |<span data-ttu-id="45935-810">指数</span><span class="sxs-lookup"><span data-stu-id="45935-810">Exponential</span></span> |<span data-ttu-id="45935-811">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="45935-811">maxAttempt</span></span><br /><span data-ttu-id="45935-812">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="45935-812">deltaBackoff</span></span> |<span data-ttu-id="45935-813">5</span><span class="sxs-lookup"><span data-stu-id="45935-813">5</span></span><br /><span data-ttu-id="45935-814">4 秒</span><span class="sxs-lookup"><span data-stu-id="45935-814">4 seconds</span></span> |<span data-ttu-id="45935-815">第 1 次尝试 - 约延迟 3 秒</span><span class="sxs-lookup"><span data-stu-id="45935-815">Attempt 1 - delay ~3 sec</span></span><br /><span data-ttu-id="45935-816">第 2 次尝试 - 约延迟 7 秒</span><span class="sxs-lookup"><span data-stu-id="45935-816">Attempt 2 - delay ~7 sec</span></span><br /><span data-ttu-id="45935-817">第 3 次尝试 - 约延迟 15 秒</span><span class="sxs-lookup"><span data-stu-id="45935-817">Attempt 3 - delay ~15 sec</span></span> |

### <a name="telemetry"></a><span data-ttu-id="45935-818">遥测</span><span class="sxs-lookup"><span data-stu-id="45935-818">Telemetry</span></span>
<span data-ttu-id="45935-819">重试尝试记录到 **TraceSource** 中。</span><span class="sxs-lookup"><span data-stu-id="45935-819">Retry attempts are logged to a **TraceSource**.</span></span> <span data-ttu-id="45935-820">必须配置 **TraceListener**，才能捕获事件并将这些事件写入合适的目标日志。</span><span class="sxs-lookup"><span data-stu-id="45935-820">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span> <span data-ttu-id="45935-821">可以使用 **TextWriterTraceListener** 或 **XmlWriterTraceListener** 将数据写入日志文件、使用 **EventLogTraceListener** 将数据写入 Windows 事件日志，或使用 **EventProviderTraceListener** 将跟踪数据写入 ETW 子系统。</span><span class="sxs-lookup"><span data-stu-id="45935-821">You can use the **TextWriterTraceListener** or **XmlWriterTraceListener** to write the data to a log file, the **EventLogTraceListener** to write to the Windows Event Log, or the **EventProviderTraceListener** to write trace data to the ETW subsystem.</span></span> <span data-ttu-id="45935-822">此外，还可以配置缓冲区的自动刷新和即将记录的事件详细信息（例如，错误、警告、信息和详细内容）。</span><span class="sxs-lookup"><span data-stu-id="45935-822">You can also configure auto-flushing of the buffer, and the verbosity of events that will be logged (for example, Error, Warning, Informational, and Verbose).</span></span> <span data-ttu-id="45935-823">有关详细信息，请参阅 [Client-side Logging with the .NET Storage Client Library](http://msdn.microsoft.com/library/azure/dn782839.aspx)（使用 .NET 存储客户端库的客户端日志记录）。</span><span class="sxs-lookup"><span data-stu-id="45935-823">For more information, see [Client-side Logging with the .NET Storage Client Library](http://msdn.microsoft.com/library/azure/dn782839.aspx).</span></span>

<span data-ttu-id="45935-824">操作可以接收 **OperationContext** 实例，这会公开可用于附加自定义遥测逻辑的 **Retrying** 事件。</span><span class="sxs-lookup"><span data-stu-id="45935-824">Operations can receive an **OperationContext** instance, which exposes a **Retrying** event that can be used to attach custom telemetry logic.</span></span> <span data-ttu-id="45935-825">有关详细信息，请参阅 [OperationContext.Retrying 事件](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx)。</span><span class="sxs-lookup"><span data-stu-id="45935-825">For more information, see [OperationContext.Retrying Event](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).</span></span>

### <a name="examples"></a><span data-ttu-id="45935-826">示例</span><span class="sxs-lookup"><span data-stu-id="45935-826">Examples</span></span>
<span data-ttu-id="45935-827">以下代码示例展示了如何创建两个具有不同重试设置的 **TableRequestOptions** 实例；一个用于交互式请求，另一个用于后台请求。</span><span class="sxs-lookup"><span data-stu-id="45935-827">The following code example shows how to create two **TableRequestOptions** instances with different retry settings; one for interactive requests and one for background requests.</span></span> <span data-ttu-id="45935-828">然后，此示例在客户端上设置这两个重试策略，以便它们针对所有请求进行应用。此示例还会对特定请求设置交互式策略，以便替代应用于客户端的默认设置。</span><span class="sxs-lookup"><span data-stu-id="45935-828">The example then sets these two retry policies on the client so that they apply for all requests, and also sets the interactive strategy on a specific request so that it overrides the default settings applied to the client.</span></span>

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

### <a name="more-information"></a><span data-ttu-id="45935-829">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-829">More information</span></span>
* [<span data-ttu-id="45935-830">Azure 存储客户端库重试策略建议</span><span class="sxs-lookup"><span data-stu-id="45935-830">Azure Storage Client Library Retry Policy Recommendations</span></span>](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
* [<span data-ttu-id="45935-831">存储客户端库 2.0 - 实现重试策略</span><span class="sxs-lookup"><span data-stu-id="45935-831">Storage Client Library 2.0 – Implementing Retry Policies</span></span>](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## <a name="general-rest-and-retry-guidelines"></a><span data-ttu-id="45935-832">常规 REST 和重试指南</span><span class="sxs-lookup"><span data-stu-id="45935-832">General REST and retry guidelines</span></span>
<span data-ttu-id="45935-833">访问 Azure 或第三方服务时，请注意以下指南：</span><span class="sxs-lookup"><span data-stu-id="45935-833">Consider the following when accessing Azure or third party services:</span></span>

* <span data-ttu-id="45935-834">使用管理重试的系统化方法（可能作为可重用代码），以便跨所有客户端和解决方案应用一致的方法。</span><span class="sxs-lookup"><span data-stu-id="45935-834">Use a systematic approach to managing retries, perhaps as reusable code, so that you can apply a consistent methodology across all clients and all solutions.</span></span>
* <span data-ttu-id="45935-835">如果目标服务或客户端没有内置的重试机制，请考虑使用重试框架（如 [Polly][polly]）来管理重试。</span><span class="sxs-lookup"><span data-stu-id="45935-835">Consider using a retry framework such as [Polly][polly] to manage retries if the target service or client has no built-in retry mechanism.</span></span> <span data-ttu-id="45935-836">这会帮助你实现一致的重试行为，并可能会为目标服务提供合适的默认重试策略。</span><span class="sxs-lookup"><span data-stu-id="45935-836">This will help you implement a consistent retry behavior, and it may provide a suitable default retry strategy for the target service.</span></span> <span data-ttu-id="45935-837">不过，可能需要为具有非标准行为的服务创建自定义重试代码，无需根据异常来指明临时故障；或者，可能需要使用 **Retry-Response** 答复来管理重试行为。</span><span class="sxs-lookup"><span data-stu-id="45935-837">However, you may need to create custom retry code for services that have non-standard behavior, that do not rely on exceptions to indicate transient failures, or if you want to use a **Retry-Response** reply to manage retry behavior.</span></span>
* <span data-ttu-id="45935-838">临时检测逻辑视用于调用 REST 调用的实际客户端 API 而定。</span><span class="sxs-lookup"><span data-stu-id="45935-838">The transient detection logic will depend on the actual client API you use to invoke the REST calls.</span></span> <span data-ttu-id="45935-839">某些客户端（如较新的 **HttpClient** 类）不会引发包含失败 HTTP 状态代码的已完成请求的异常。</span><span class="sxs-lookup"><span data-stu-id="45935-839">Some clients, such as the newer **HttpClient** class, will not throw exceptions for completed requests with a non-success HTTP status code.</span></span> 
* <span data-ttu-id="45935-840">从服务返回的 HTTP 状态代码可以帮助指明故障是否是临时故障。</span><span class="sxs-lookup"><span data-stu-id="45935-840">The HTTP status code returned from the service can help to indicate whether the failure is transient.</span></span> <span data-ttu-id="45935-841">可能需要检查客户端生成的异常或重试框架，以访问状态代码或确定对等的异常类型。</span><span class="sxs-lookup"><span data-stu-id="45935-841">You may need to examine the exceptions generated by a client or the retry framework to access the status code or to determine the equivalent exception type.</span></span> <span data-ttu-id="45935-842">以下 HTTP 代码通常可指明重试是否合适：</span><span class="sxs-lookup"><span data-stu-id="45935-842">The following HTTP codes typically indicate that a retry is appropriate:</span></span>
  * <span data-ttu-id="45935-843">408 请求超时</span><span class="sxs-lookup"><span data-stu-id="45935-843">408 Request Timeout</span></span>
  * <span data-ttu-id="45935-844">429 请求过多</span><span class="sxs-lookup"><span data-stu-id="45935-844">429 Too Many Requests</span></span>
  * <span data-ttu-id="45935-845">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="45935-845">500 Internal Server Error</span></span>
  * <span data-ttu-id="45935-846">502 错误的网关</span><span class="sxs-lookup"><span data-stu-id="45935-846">502 Bad Gateway</span></span>
  * <span data-ttu-id="45935-847">503 服务不可用</span><span class="sxs-lookup"><span data-stu-id="45935-847">503 Service Unavailable</span></span>
  * <span data-ttu-id="45935-848">504 网关超时</span><span class="sxs-lookup"><span data-stu-id="45935-848">504 Gateway Timeout</span></span>
* <span data-ttu-id="45935-849">如果根据异常构建重试逻辑，则以下代码通常可指明出现了无法建立连接的临时故障：</span><span class="sxs-lookup"><span data-stu-id="45935-849">If you base your retry logic on exceptions, the following typically indicate a transient failure where no connection could be established:</span></span>
  * <span data-ttu-id="45935-850">WebExceptionStatus.ConnectionClosed</span><span class="sxs-lookup"><span data-stu-id="45935-850">WebExceptionStatus.ConnectionClosed</span></span>
  * <span data-ttu-id="45935-851">WebExceptionStatus.ConnectFailure</span><span class="sxs-lookup"><span data-stu-id="45935-851">WebExceptionStatus.ConnectFailure</span></span>
  * <span data-ttu-id="45935-852">WebExceptionStatus.Timeout</span><span class="sxs-lookup"><span data-stu-id="45935-852">WebExceptionStatus.Timeout</span></span>
  * <span data-ttu-id="45935-853">WebExceptionStatus.RequestCanceled</span><span class="sxs-lookup"><span data-stu-id="45935-853">WebExceptionStatus.RequestCanceled</span></span>
* <span data-ttu-id="45935-854">如果服务处于不可用状态，则服务可以指明在 **Retry-After** 响应头或其他自定义头中进行重试前的相应延迟。</span><span class="sxs-lookup"><span data-stu-id="45935-854">In the case of a service unavailable status, the service might indicate the appropriate delay before retrying in the **Retry-After** response header or a different custom header.</span></span> <span data-ttu-id="45935-855">服务还可以发送其他信息，既能以自定义头的形式，也能嵌入响应内容中。</span><span class="sxs-lookup"><span data-stu-id="45935-855">Services might also send additional information as custom headers, or embedded in the content of the response.</span></span> 
* <span data-ttu-id="45935-856">请勿针对表示客户端错误的状态代码（4xx 范围内的错误）进行重试，“408 请求超时”除外。</span><span class="sxs-lookup"><span data-stu-id="45935-856">Do not retry for status codes representing client errors (errors in the 4xx range) except for a 408 Request Timeout.</span></span>
* <span data-ttu-id="45935-857">在各种条件（如不同的网络状态和系统负载）下全面测试重试策略和机制。</span><span class="sxs-lookup"><span data-stu-id="45935-857">Thoroughly test your retry strategies and mechanisms under a range of conditions, such as different network states and varying system loadings.</span></span>

### <a name="retry-strategies"></a><span data-ttu-id="45935-858">重试策略</span><span class="sxs-lookup"><span data-stu-id="45935-858">Retry strategies</span></span>
<span data-ttu-id="45935-859">以下是典型的重试策略间隔类型：</span><span class="sxs-lookup"><span data-stu-id="45935-859">The following are the typical types of retry strategy intervals:</span></span>

* <span data-ttu-id="45935-860">**指数**。</span><span class="sxs-lookup"><span data-stu-id="45935-860">**Exponential**.</span></span> <span data-ttu-id="45935-861">此重试策略执行指定次数的重试，并使用随机指数回退方法来确定重试间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-861">A retry policy that performs a specified number of retries, using a randomized exponential back off approach to determine the interval between retries.</span></span> <span data-ttu-id="45935-862">例如：</span><span class="sxs-lookup"><span data-stu-id="45935-862">For example:</span></span>

    ```csharp
    var random = new Random();

    var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
                random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
                (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
    var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
                    this.maxBackoff.TotalMilliseconds);
    retryInterval = TimeSpan.FromMilliseconds(interval);
    ```

* <span data-ttu-id="45935-863">**增量**。</span><span class="sxs-lookup"><span data-stu-id="45935-863">**Incremental**.</span></span> <span data-ttu-id="45935-864">此重试策略具有指定的重试尝试次数以及增量的重试间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-864">A retry strategy with a specified number of retry attempts and an incremental time interval between retries.</span></span> <span data-ttu-id="45935-865">例如：</span><span class="sxs-lookup"><span data-stu-id="45935-865">For example:</span></span>

    ```csharp
    retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
                    (this.increment.TotalMilliseconds * currentRetryCount));
    ```

* <span data-ttu-id="45935-866">**线性重试**。</span><span class="sxs-lookup"><span data-stu-id="45935-866">**LinearRetry**.</span></span> <span data-ttu-id="45935-867">此重试策略执行指定次数的重试，并使用指定的固定重试间隔。</span><span class="sxs-lookup"><span data-stu-id="45935-867">A retry policy that performs a specified number of retries, using a specified fixed time interval between retries.</span></span> <span data-ttu-id="45935-868">例如：</span><span class="sxs-lookup"><span data-stu-id="45935-868">For example:</span></span>

    ```csharp
    retryInterval = this.deltaBackoff;
    ```

### <a name="transient-fault-handling-with-polly"></a><span data-ttu-id="45935-869">使用 Polly 处理暂时性错误</span><span class="sxs-lookup"><span data-stu-id="45935-869">Transient fault handling with Polly</span></span>
<span data-ttu-id="45935-870">[Polly][polly] 是一个库，用于以编程方式处理重试和[断路器][circuit-breaker]策略。</span><span class="sxs-lookup"><span data-stu-id="45935-870">[Polly][polly] is a library to programatically handle retries and [circuit breaker][circuit-breaker] strategies.</span></span> <span data-ttu-id="45935-871">Polly 项目隶属于 [.NET Foundation][dotnet-foundation]。</span><span class="sxs-lookup"><span data-stu-id="45935-871">The Polly project is a member of the [.NET Foundation][dotnet-foundation].</span></span> <span data-ttu-id="45935-872">对于客户端不对重试提供本机支持的服务，Polly 是一种有效的替代方法，它让用户无需编写可能难以正确实现的自定义重试代码。</span><span class="sxs-lookup"><span data-stu-id="45935-872">For services where the client does not natively support retries, Polly is a valid alternative and avoids the need to write custom retry code, which can be hard to implement correctly.</span></span> <span data-ttu-id="45935-873">Polly 还提供一种在出现错误时对其进行跟踪的方法，以便用户记录重试。</span><span class="sxs-lookup"><span data-stu-id="45935-873">Polly also provides a way to trace errors when they occur, so that you can log retries.</span></span>


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


### <a name="more-information"></a><span data-ttu-id="45935-877">详细信息</span><span class="sxs-lookup"><span data-stu-id="45935-877">More information</span></span>
* [<span data-ttu-id="45935-878">连接复原</span><span class="sxs-lookup"><span data-stu-id="45935-878">Connection Resiliency</span></span>](/ef/core/miscellaneous/connection-resiliency)
* [<span data-ttu-id="45935-879">数据点 - EF Core 1.1</span><span class="sxs-lookup"><span data-stu-id="45935-879">Data Points - EF Core 1.1</span></span>](https://msdn.microsoft.com/magazine/mt745093.aspx)


